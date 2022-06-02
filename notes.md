# understand affain
affine是用来做polyhedral变换的，affine中常用的两个属性，dimension和symbol都有很强的要求（dimension大概类似于induction variable，sym是loop bound），affine的变换必须维持线性，
那么有些op可以用，有些op就不能用。这样做的目的是为了保证对IR进行分析和变换，比如要对affine.for的IV进行相加操作：rewriter.create<AddIOp>(%i,%j)就会出错，
因为affine不支持对dim使用普通的算术操作，必须使用affine自己的affine.apply，这样才能保证变量留在affine系统内部。比如可以创建create<AffineApply>。一般来说，算术操作是在affine的
后面，用affine做各种变换，最后一步再lower到arith op。直接在affine层创建arith op会割裂各种affine op的联系。为了支持这种操作，affine支持了affine.apply来进行各种folding和canonicalization。

 # TVM
  
  host 和 kernel的融合，比如能够分析不同kernel的两个block的依赖关系。
 
 # oneflow
 
 split，broadcast，partial，三种操作，针对tensor，可以实现模型和数据并行
 
 Tile IR的抽象，帮助硬件上更优的执行。
