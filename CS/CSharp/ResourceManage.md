# 资源回收机制

### 1、定义

* GC，即垃圾回收。其工作原理是根据应用程序的root，探测应用程序中已经分配但是不再被使用的对象，并进行回收。
* 常用算法：Reference Counting，Mark Sweep，Copy Collection
* 主流算法：Mark Sweep（.Net,JavaVm)

### 2、作用

* 将程序员的精力集中在实际的问题上，不用手动管理内存，减少相关bug
* 减小模块间的耦合
* 内存管理更高效
* 提高软件开发速度，质量，安全性

### 3、托管资源和非托管资源

### 4、.net的GC缺陷

* GC不能自动释放非托管资源
* GC不是实时的，执行时间不确定。（游戏开发中集中执行GC会影响帧率，造成卡顿）
* 上一个问题的解决方式：让类继承IDisposable接口，调用Dispose方法主动释放资源

```c#
public class BaseResource : IDisposable
{
    // 指向外部非托管资源
	private IntPtr handle;
    // 此类使用的其它托管资源.
	private Component Components;
    // 跟踪是否调用.Dispose方法，标识位，控制垃圾收集器的行为
    privatebool disposed =false;
    // 构造函数
	public BaseResource()
    {
        // Insert appropriate constructor code here.
    }
    // 实现接口IDisposable.
    // 不能声明为虚方法virtual.
    // 子类不能重写这个方法.
    publicvoid Dispose()
    {
        Dispose(true);
        // 离开终结队列Finalization queue
        // 设置对象的阻止终结器代码
        //
        GC.SuppressFinalize(this);
    }
    // Dispose(bool disposing) 执行分两种不同的情况.
    // 如果disposing 等于 true, 方法已经被调用
    // 或者间接被用户代码调用. 托管和非托管的代码都能被释放
    // 如果disposing 等于false, 方法已经被终结器 finalizer 从内部调用过，
    //你就不能在引用其他对象，只有非托管资源可以被释放。
    protectedvirtualvoid Dispose(bool disposing)
    {
        // 检查Dispose 是否被调用过.
		if (!this.disposed)
        {
            // 如果等于true, 释放所有托管和非托管资源
			if (disposing)
            {
                // 释放托管资源.
                Components.Dispose();
            }
            // 释放非托管资源，如果disposing为 false,
            // 只会执行下面的代码.
            CloseHandle(handle);
            handle = IntPtr.Zero;
            // 注意这里是非线程安全的.
            // 在托管资源释放以后可以启动其它线程销毁对象，
            // 但是在disposed标记设置为true前
            // 如果线程安全是必须的，客户端必须实现。
        }
        disposed =true;
    }
    // 使用interop 调用方法
    // 清除非托管资源.
    [System.Runtime.InteropServices.DllImport("Kernel32")]
    private externstatic Boolean CloseHandle(IntPtr handle);
    // 使用C# 析构函数来实现终结器代码
    // 这个只在Dispose方法没被调用的前提下，才能调用执行。
    // 如果你给基类终结的机会.
    // 不要给子类提供析构函数.
	~BaseResource()
    {
        // 不要重复创建清理的代码.
        // 基于可靠性和可维护性考虑，调用Dispose(false) 是最佳的方式
        Dispose(false);
    }
    // 允许你多次调用Dispose方法,
    // 但是会抛出异常如果对象已经释放。
    // 不论你什么时间处理对象都会核查对象的是否释放,
    // check to see if it has been disposed.
	public void DoSomething()
    {
        if (this.disposed)
        {
            thrownew ObjectDisposedException();
        }
    }
    // 不要设置方法为virtual.
    // 继承类不允许重写这个方法
    public void Close()
    {
        // 无参数调用Dispose参数.
        Dispose();
    }
    public static void Main()
    {
        // Insert code here to create
        // and use a BaseResource object.
    }
}
```

### 5、.net中GC相关API

1、GC.Collect()方法

* 作用：强制进行垃圾回收