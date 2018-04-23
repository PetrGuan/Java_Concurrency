# 基础概念

## 什么是线程（thread）
线程是可以被操作系统调度的**最小**的执行单元。

## 什么是进程（process）
进程是一组同时执行的相关联的线程，并且有 shared environment。
也就是说在同一个进程内的线程们可以共享内存地址以及直接与各个线程直接交流。

## 什么是任务（task）
一个 task 是由一个线程完成的工作。（a single unit of work）
一个线程可以完成许多独立的 task，但是 only one task at a time。

![process-model](./img/p1.png)

## 线程的类型
系统线程（system thread） 由 JVM 创造并且在后台运行，一个很常见的例子就是 gc 线程。

用户线程（user-defined thread）是由程序开发者创造的，用来完成一个特殊的任务。

## 什么是 Concurrency
在同一时间执行多线程以及多进程的能力就被称为 concurrency。
The property of executing multiple threads and processes at the same time is referred to as concurrency.

## thread scheduler
一般而言线程数目是远远高于 cpu 处理器数目的，那么系统该怎么决定什么时候执行哪一个线程呢？这时候我们就需要 thread scheduler 来决定现在可以被 cpu “临幸”的线程。

## 上下文切换（context switch）
线程被分配使用 cpu 的时间是有限的，那么当一个线程时间用完，然而它的处理还没有完成时，这样的情景就被称为上下文切换（context switch）。系统需要保存这个线程的状态，为了以后能够恢复再度执行。需要记住的就是这个保存和再读的操作一定会有时间和资源开销。

## 线程优先级（thread priority）
一个线程可以打断另一个线程的执行，如果当前执行的线程优先级没有这个线程高的话。在 Java 中，优先级由 integer 来表示。

## Java 中的两种方法

    @FunctionalInterface public interface Runnable {
      void run();
    }

    public class PrintData implements Runnable {
      public void run() {
        for (int i = 0; i < 3; ++i)
          System.out.println("Printing record: " + i);
      }

      public static void main(String []args) {
        (new Thread(new PrintData())).start();
      }
    }

    public class ReadInventoryThread extends Thread {
      public void run() {
        System.out.println("Printing zoo inventory");
      }

      public static void main(String[] args) {
        (new ReadInventoryThread()).start();
      }
    }

以下的代码会输出什么呢？

    public static void main(String[] args) {
      System.out.println("begin");
      (new ReadInventoryThread()).start();
      (new Thread(new PrintDate())).start();
      (new ReadInventoryThread()).start();
      System.out.println("end");
    }

答案是这是不确定的，一种可能的情况是：

    begin
    Printing zoo inventory
    Printing record: 0
    end
    Printing zoo inventory
    Printing record: 1
    Printing record: 2

这个例子使用了 4 个线程， main() user thread 以及其他三个（由 main 创造）。关键在于线程的执行顺序是不确定的。

## Thread and Runnable

> * 如果你需要定义你自己的 Thread rules，比如 priority Thread，继承 Thread 更合适。
> * Java 本身不支持多继承，所以 implementing Runnable 更灵活。
> * Implementing Runnable 更面向对象，因为这样做分离了要做的 task 以及执行这个 task 的 Thread 对象。
> * Implementing Runnable 便于 Concurrency API 来使用。
