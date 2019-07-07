# 反射

### 1、定义

指程序可以访问，检测和修改它本身状态或行为的一种能力。

### 2、用途

* 使用Assembly定义和加载程序集，获取程序集信息以及创建相应实例

* 使用Module获取包含模块的程序集以及模块中的类，方法

* 使用Constructor Info了解构造函数信息

* 使用MethodInfo了解方法信息

* 使用FieldInfo了解字段信息，可以获取和设置字段值

* 使用EventInfo了解事件信息，可以添加或移除事件处理程序

* 使用PropertyInfo了解属性信息，获取和设置属性值

* 使用ParameterInfo了解参数信息

  

  反射用到的命名空间：

  * System.Reflection
  * System.Type
  * System.Reflection.Assembly

### 3、System.Type类

```c#
public class Person{
    public string str;
    public int num;
    private string Num{get;set;}
    public int Age{get;set;}
    
    public Person(){
        Console.WriteLine("No Params");
    }
    
    public Person(string _str, int _num){
        str = _str;
        num = _num;
        Console.WriteLine("Have Params")
    }
    
    public void show(){
        Console.WriteLine("Show: name={0}, Age={1}",this.Name,this.Age);
    }
}

class MainClass{
    public static void Main(string[] args){
        Person p = new Person();
        Type t= typeof(p);
        
        /*
        1. 获取类中所有字段
        Output:
        	System.String str
        	System.Int32 num
        */
        FieldInfo[] fis = t.GetFields();
        foreach(var item in fis){
            Console.WriteLine("{0}", item);
        }
        
        /*
        2. 获取类中所有公有属性
        Output：
        	Int32 Age
        */
        
        PerpertyInfo[] pis = t.GetProperties();
        foreach(var item in pis){
            Console.WriteLine("{0}", item);
        }
        
        /*
        3. 获取类中所有的公有构造函数
        Output：
        	Void .ctor()
        	Void .ctor(String, Int32)
        */
        ConstructorInfo[] cis = t.GetConstructors();
        foreach(var item in cis){
            Console.WriteLine("{0}", item);
        	
            //获取参数列表
            ParameterInfo[] pis = item.GetParameters();
            foreach(var param in pis){
                Console.WriteLine(param);
            }
        }
       
        /*
        4. 获取类中所有方法
        Output：
 			System.String get_Name()
 			Void set_Name(System.String)
 			Int32 get_Age()
 			Void set_Age(Int32)
 			...........
        */
        
        MethodInfo[] mis = t.GetMethods();
        foreach(var item in mis){
            Console.WriteLine("{0}", item);
        }
    }
}
```

