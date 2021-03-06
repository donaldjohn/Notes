## 泛型编程
### 概论
### 1 为什么使用泛型
1. 更好的可读性
2. 更加类型安全

#### 1.1 谁适合使用泛型
只有包含大量常规类型转换的代码才能受益于泛型。
### 2 泛型类
	  
```Java	  
	  package com.donaldjohn;
	    
	    import java.util.regex.Pattern;
	    
	   	 /**
	     * Created by zhihui on 2016/9/17.
	     */
	    public class Pair<T>
	    {
	        private T first;
	        private T second;
	    
	        public Pair()
	        {
	            first = null;
	            second = null;
	        }
	    
	        public Pair(T first, T second)
	        {
	            this.first = first;
	            this.second = second;
	        }
	    
	        public T getFirst()
	        {
	            return first;
	        }
	    
	        public T getSecond()
	        {
	            return second;
	        }
	    
	        public void setFirst(T newValue)
	        {
	            this.first = newValue;
	        }
	    
	        public void setSecond(T newValue)
	        {
	            this.second = newValue;
	        }
	    }
```
### 3 泛型方法
```Java
    public static <T> T getMiddle(T[] a)
    {
        return a[a.length / 2];
    }
```    

### 4 类型限定
	public static <T extends Comparable> T min(T[] a)

可以通过如下的方式进行多个限定

	T extends Comparable & Serializable

### 5 Java虚拟机对泛型代码的处理
虚拟机不包含泛型，所有的对象都包含于普通类。
当定义泛型时，虚拟机自动提供raw类型。raw类型名就是泛型名，所有的类型参数都被擦除了，被替换为第一个限定类型（如果没有限定类型，被替换为Object类型）。

例如 Pair<T>被将会变成这样：

```Java
        public class Pair
        {
            private Object first;
            private Object second;

            public Pair()
            {
                first = null;
                second = null;
            }

            public Pair(Object first, Object second)
            {
                this.first = first;
                this.second = second;
            }

            public Object getFirst()
            {
                return first;
            }

            public void setFirst(Object first)
            {
                this.first = first;
            }

            public Object getSecond()
            {
                return second;
            }

            public void setSecond(Object second)
            {
                this.second = second;
            }
        }
```	
Raw Type 使用第一个限定(bound)的类型，默认是Object
#### 5.1 泛型表达式的转化
当调用泛型时，如果返回值的类型被擦除，编译器自动加入强制类型转化。

```Java
	Pair<Employee> buddies = ...;
	Employee buddy = buddies.getFirst();
```	
因为类型擦除的缘故，getFirst实际会返回Object类型，编译器会将方法添加强制类型转化

**访问泛型成员变量的时候也会添加类型转化**

#### 5.2 泛型方法的转化
```Java
	public static <T extends Comparable> T min(T[] a)
```
类型擦除后变为

```Java
	public static Comparable min(Comparable[] a)
```	
方法的类型擦除可导致多态的不可用,为了解决这个问题编译器引入了bridge method(桥方法)的解决方案。

```Java
		public void setSecond(Object second){setSecond(Date)second;}
```		
DateInterval的例子（P710）：如果DateInterval重写了Pair的getSecond()的方法,情况变得更奇怪。此时DateInterval会有两个参数和名称相同,只有返回值不同的getSecond()方法。这种状态对虚拟机是允许的且虚拟机可以区分不同的方法调用。
Date getSecond()
Object getSecond()
##### 总结
* 虚拟机中没有泛型方法，只有普通类和方法。
* 所有的类型参数都会被限定类型替换。
* 桥方法用来保持多态特性。
* 虚拟机插入强制类型转换以保证类型安全

#### 5.3 调用遗留的非泛型代码
可以使用@SuppressWarning("unchecked")进行屏蔽
最不济的情况就是和非泛型时代相同

### 6 泛型的约束和限制
#### 6.1 原生类型不能作为类型参数
#### 6.2 运行时的类型检测只能检测到RAW类型
例如：
	
```Java	
	Pair<String> stringPair = ...;
	Pair<Employee> employeePair = ...;
	if(stringPair.getClass() == employeePair.getClass())//they are equal
```
比较会返回true,因为两次getClass都返回Pair.class
#### 6.3 不能创建参数化类型的数组
不能定义如下数组:

```Java
	Pair<String>[] table = new Pair<String>[10];//ERROR
```	
为什么不行呢?类型擦除后table的类型是Pair[], 可以转化为Object[]，数组能够记住它的元素类型，如果视图存储一个错误类型会抛出ArrayStoreException。类型擦除会使该机制失效。
**objarray[0] = new Pair<Employee>()**将可以通过检测,因为这个原因参数化的数组是非法的.
#### 6.4 变长参数使用泛型的警告
	变长参数使用泛型时, JVM实际会创建泛型数组，与6.3的规则冲突，然而此处并不会报错，会产生一个警告，可以使用@SuppressWarning("unchecked")或者@SafeVarargs进行屏蔽。
#### 6.5 不能实例化类型参数变量
	new T(), new T[...], T.class都是非法的
	可以使用反射实例化变量:
	first = T.class.newInstance();//错误	
#### 6.6 泛型类中不能用泛型定义静态内容
	private static T singleInstance; //ERROR
	private static T getSingleInstance() //ERROR
	{

	}
#### 6.7 不能抛出或捕捉泛型类
* 泛型类不允许继承Throwable
* catch语句中不允许使用泛型
但是在约定异常时,可以用泛型
例如`public static <T extends Throwable> void doWork(T t) throws T//OK`是合法的

通过这种方式可以抛出检查异常不处理

#### 6.8 类型擦除后出现冲突
* equals(T value)会与equals(Object value)冲突
* 类或类型不能是同一个接口的不同泛型形式
	可能在同一个类中出现两个相同的桥方法（Bridge Method），进而产生冲突

### 7 泛型的继承原则
* Pair<T> 和 Pair<S> 没有任何关系，不管S和T有什么关系。



























