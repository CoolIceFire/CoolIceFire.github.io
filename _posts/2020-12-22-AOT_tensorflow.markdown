代码在这里：https://github.com/CoolIceFire/aot_demo

1. 运行 train_model.py 得到 saved_model

2. 运行```saved_model_cli aot_compile_cpu --dir ./saved_model --tag_set serve --output_prefix graph --cpp_class Graph --multithreading true```得到graph.h , graph.o , graph_makefile.inc , graph_metadata.o
   
3. 编写graph.cc 和 main.cc

4. ```bazel build -c opt libmodel.so``` 编译生成libmodel.so

5. ```bazel build -c opt tf_serving``` 编译生成tf_serving

6. 运行tf_serving即可


附：

1. multithreding问题

比如对于矩阵乘法，如果使用multi_thread，那么就调用runtime_matmul.cc里面的实现，否则调用single_thread_runtime_matmul.cc里面的实现。
```c++
// https://github.com/tensorflow/tensorflow/blob/r2.4/tensorflow/compiler/xla/service/cpu/dot_op_emitter.cc#L728
    case F32:
      fn_name = multi_threaded
                    ? (use_mkl_dnn ? runtime::kMKLMatMulF32SymbolName
                                   : runtime::kEigenMatMulF32SymbolName)
                    : (use_mkl_dnn
                           ? runtime::kMKLSingleThreadedMatMulF32SymbolName
                           : runtime::kEigenSingleThreadedMatMulF32SymbolName);
      float_type = b_->getFloatTy();
      break;
```

看一下两者实现的区别：

```c++
// 这是runtime_matmul里面的实现，可以看到通过run_options，使用的thread_pool信息，也就是可以多线程进行矩阵乘法运算
// https://github.com/tensorflow/tensorflow/blob/r2.4/tensorflow/compiler/xla/service/cpu/runtime_matmul.cc#L36
template <typename T, Eigen::AlignmentType Alignment>
void MatMul(const void* run_options_ptr, T* out, T* lhs, T* rhs,
            tensorflow::int64 m, tensorflow::int64 n, tensorflow::int64 k,
            tensorflow::int32 transpose_lhs, tensorflow::int32 transpose_rhs) {
  const xla::ExecutableRunOptions* run_options =
      static_cast<const xla::ExecutableRunOptions*>(run_options_ptr);

  tensorflow::int64 lhs_rows = m;
  tensorflow::int64 lhs_cols = k;
  if (transpose_lhs) {
    std::swap(lhs_rows, lhs_cols);
  }

  tensorflow::int64 rhs_rows = k;
  tensorflow::int64 rhs_cols = n;
  if (transpose_rhs) {
    std::swap(rhs_rows, rhs_cols);
  }

  const Eigen::TensorMap<Eigen::Tensor<const T, 2>, Alignment> A(lhs, lhs_rows,
                                                                 lhs_cols);
  const Eigen::TensorMap<Eigen::Tensor<const T, 2>, Alignment> B(rhs, rhs_rows,
                                                                 rhs_cols);
  Eigen::TensorMap<Eigen::Tensor<T, 2>, Alignment> C(out, m, n);

  typedef typename Eigen::Tensor<T, 2>::DimensionPair DimPair;
  int lhs_contract_dim = transpose_lhs ? 0 : 1;
  int rhs_contract_dim = transpose_rhs ? 1 : 0;
  const Eigen::array<DimPair, 1> dims(
      {DimPair(lhs_contract_dim, rhs_contract_dim)});

  // Matrix multiply is a special case of the "contract" operation where
  // the contraction is performed along dimension 1 of the lhs and dimension
  // 0 of the rhs.
  XLA_LIGHTWEIGHT_CHECK(run_options->intra_op_thread_pool() != nullptr);
  C.device(*run_options->intra_op_thread_pool()) = A.contract(B, dims);
}
```
再来看一下single_thread_matmul的实现
```c++
// 这里面没有用到run_options信息，没有用到thread_pool，速度慢
// https://github.com/tensorflow/tensorflow/blob/r2.4/tensorflow/compiler/xla/service/cpu/runtime_single_threaded_matmul.cc
template <typename T, Eigen::AlignmentType Alignment>
void MatMul(const void* run_options_ptr, T* out, T* lhs, T* rhs,
            tensorflow::int64 m, tensorflow::int64 n, tensorflow::int64 k,
            tensorflow::int32 transpose_lhs, tensorflow::int32 transpose_rhs) {
  tensorflow::int64 lhs_rows = m;
  tensorflow::int64 lhs_cols = k;
  if (transpose_lhs) {
    std::swap(lhs_rows, lhs_cols);
  }

  tensorflow::int64 rhs_rows = k;
  tensorflow::int64 rhs_cols = n;
  if (transpose_rhs) {
    std::swap(rhs_rows, rhs_cols);
  }

  const Eigen::TensorMap<Eigen::Tensor<const T, 2>, Alignment> A(lhs, lhs_rows,
                                                                 lhs_cols);
  const Eigen::TensorMap<Eigen::Tensor<const T, 2>, Alignment> B(rhs, rhs_rows,
                                                                 rhs_cols);
  Eigen::TensorMap<Eigen::Tensor<T, 2>, Alignment> C(out, m, n);

  typedef typename Eigen::Tensor<T, 2>::DimensionPair DimPair;
  int lhs_contract_dim = transpose_lhs ? 0 : 1;
  int rhs_contract_dim = transpose_rhs ? 1 : 0;
  const Eigen::array<DimPair, 1> dims(
      {DimPair(lhs_contract_dim, rhs_contract_dim)});

  // Matrix multiply is a special case of the "contract" operation where
  // the contraction is performed along dimension 1 of the lhs and dimension
  // 0 of the rhs.
  C = A.contract(B, dims);
}
```

