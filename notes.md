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
 
 # llvm
 
 class InstrMapping
 {  string FilterClass;  list<string> RowFields = [];  list<string> ColFields = [];  list<string> KeyCol = [];  list<list<string> > ValueCols = []; } 

图2其中，FilterClass域规定了各指令类的基类，可减少使用关系模型的指令的搜索空间。RowFields域规定了关系表中在同一行的所有指令必须保持相同的属性。ColFields域规定了关系表中在同一列的所有指令必须保持相同的属性，这里仅规定了属性名称。KeyCol域规定了关键指令的ColFields域的属性值，这个属性值实际上就是关系表的关键指令，关键指令会被关系表转换成其它指令，这些指令在ValueCols域中规定。

作者：Frank Wang
链接：https://zhuanlan.zhihu.com/p/55807207

 把关系中的key转变为value代表的指令。
 
 AMD中定义的代码映射关系如下：
 
 ```
 
 def getMCOpcodeGen : InstrMapping {
  let FilterClass = "SIMCInstr";
  let RowFields = ["PseudoInstr"];
  let ColFields = ["Subtarget"];
  let KeyCol = [!cast<string>(SIEncodingFamily.NONE)];
  // These columns must be kept in sync with the SIEncodingFamily enumeration.
  let ValueCols = [[!cast<string>(SIEncodingFamily.SI)],
                   [!cast<string>(SIEncodingFamily.VI)],
                   [!cast<string>(SIEncodingFamily.SDWA)],
                   [!cast<string>(SIEncodingFamily.SDWA9)],
                   // GFX80 encoding is added to work around a multiple matching
                   // issue for buffer instructions with unpacked d16 data. This
                   // does not actually change the encoding, and thus may be
                   // removed later.
                   [!cast<string>(SIEncodingFamily.GFX80)],
                   [!cast<string>(SIEncodingFamily.GFX9)],
                   [!cast<string>(SIEncodingFamily.GFX10)],
                   [!cast<string>(SIEncodingFamily.SDWA10)],
                   [!cast<string>(SIEncodingFamily.GFX90A)],
                   [!cast<string>(SIEncodingFamily.GFX940)],
                   [!cast<string>(SIEncodingFamily.GFX11)]];
}
 
 ```
 
 通过inst mapping把KeyCol转换为ValueCols的指令。伪指令的opcode编码是 SIEncodingFamily.NONE
 
