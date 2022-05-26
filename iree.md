# build iree/integrations/tensorflow

build  ~/.cache/bazel/_bazel_zyc/ea50a75a999c115ad8f97eda78ae3b2f/external/llvm-project/llvm/BUILD.bazel error

error: 'remove_cv_t' in namespace 'std' does not name a template type; did you mean 'remove_cv'?

bazel build  --cxxopt='-std=c++14' iree_tf_compiler:iree-import-tf
