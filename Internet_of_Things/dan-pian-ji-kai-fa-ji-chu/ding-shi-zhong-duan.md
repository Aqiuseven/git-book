# 定时中断

## 一、**提要**

在前述中，我们提到了可以用延时来控制LED灯按秒关/亮。这样的开发似乎简单优雅也没有任何问题。**但是**，在后续章节中我们会提到**多进程**的应用，<mark style="color:orange;">micropython</mark> 继承了 <mark style="color:orange;">python</mark> 的特性，在**多进程**任务时，time.sleep()，会释放控制权，以让其他进程完成任务。**但是在C开发的单片机中，一些延时需要通过无用循环的空转来实现**，例如这样：

```c
void delay(int time){
    for(int i=0;i<=time;i++){
        for(int item=0;item<=500;item++)
    }
} // 做无用循环，让单片机空转，以实现延时功能
int main(){
    printf("h");
    delay(100);
    printf("i");
}
```

在上面的代码里，我们就可以看到，单片机在延时时，会做没有意义的空转，这时候这个**进程始终占用**cpu，**其他进程就不能利用cpu**完成任务。**但是**<mark style="color:orange;">**micropython**</mark>**，同**<mark style="color:orange;">**python**</mark>**&#x20;一样，在延时时会释放出cpu的控制权**。

那如果micropython同 c 一样无法在我有延时需求时完成控制权的转换，那应该怎么实现优雅的延时需求呢？



## 二、定时器定时中断

同名字一样，定时器会定时，定时到了，会中断(打断)一下程序的运行。完整的理解就是：**定时结束时，单片机会暂停当前进程，优先完成定时中断中需要完成的事情，**&#x4EE5;下是示例参考：

```python
import machine

def callback(timer):
    print("定时器周期性触发")

'''
提供 4 个定时器 id 为 0-3
'''
timer = machine.Timer(0) # 创建ID 0 的计时器

'''
init的3个参数
period ： 定时时长，单位ms
mode ： 中断类型
    - machine.Timer.ONE_SHOT ：中断只触发一次
    - machine.Timer.PERIODIC ：重复触发
callback ： 回调函数
'''
timer.init(period=1000, mode=machine.Timer.PERIODIC, callback=callback)
```



## 三、拓展

上述已经介绍了，如何使用定时中断，现在可以改造第一个例程，使用定时中断的方式实现LED一秒开关一次。

请先自主设计，以下是参考代码：
