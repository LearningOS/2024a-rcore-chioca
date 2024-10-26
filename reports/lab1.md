# 实验内容
- add TaskControlBlock fields
- update tasks initialization
- impl function ```get_task_control_block  update_task_info```
- impl funciton ```impl sys_task_info```
- add update task info to syscall
  
# 习题
1.
程序产生如下报错
 ```
[kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x804003a4, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
```
rust sbi版本 ```[rustsbi] RustSBI version 0.3.0-alpha.2, adapting to RISC-V SBI v1.0.0```
2.
(1)
刚进入 __restore 时，a0 代表了系统调用的第一个参数
__restore 使用情景：
- 任务切换时，恢复任务上下文信息
- 从系统调用返回时，恢复用户态的上下文信息
  
(2)
```
ld t0, 32*8(sp)       # 加载32*8偏移处的数据到t0
ld t1, 33*8(sp)       # 加载33*8偏移处的数据到t1
ld t2, 2*8(sp)        # 加载2*8偏移处的数据到t2
csrw sstatus, t0      # 将t0的值写入sstatus寄存器
csrw sepc, t1         # 将t1的值写入sepc寄存器
csrw sscratch, t2     # 将t2的值写入sscratch寄存器

```
(3)
x2 已经用于指向当前保存的栈顶，因此无需在此重复加载。
x4 通常由线程上下文的管理代码设置，恢复用户态时不需要重新加载。

(4)
通过交换 sp 和 sscratch 中的值，系统可以有效地在用户态和内核态之间切换栈指针，确保每次进入对应态时都有正确的栈指针指向。

(5)
发生在指令```sret```
因为它在执行过程中会根据 sstatus 寄存器的 SPP 位来确定返回的特权级，并使用 sepc 中的地址作为返回点，从而切换到用户态并继续执行用户态的指令。

(6)
sp 在指令后指向内核栈地址，供内核态使用。
sscratch 存储用户态的栈地址，以备后续返回用户态时恢复。

(7)
一般发生在系统调用和异常或者中断中，常用指令为```ecall ```
# 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 以下各位 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

参考了微信交流群里同学以及助教的交流信息，以及与队长黄宇浩交流了关于包导入以及全局变量管理的问题

  

2. 此外，我也参考了 以下资料 ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

    [实验手册](https://learningos.cn/rCore-Camp-Guide-2024A/chapter3/index.html)     [RUST圣经](https://course.rs/about-book.html)

  
  
  

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

  

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。