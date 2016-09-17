##泛型编程
###概论
###1 为什么使用泛型
1. 更好的可读性
2. 更加类型安全

####1.1 谁适合使用泛型
只有包含大量常规类型转换的代码才能受益于泛型。
###2 泛型类
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

###3 泛型方法
    public static <T> T getMiddle(T[] a)
    {
        return a[a.length / 2];
    }

###4 类型限定
	public static <T extends Comparable> T min(T[] a)

可以通过如下的方式进行多个限定

	T extends Comparable & Serializable

###5 Java虚拟机对泛型代码的处理
虚拟机不包含泛型，所有的对象都包含于普通类。
当定义泛型时，虚拟机自动提供raw类型。raw类型名就是泛型名，所有的类型参数都被擦除了，被替换为第一个限定类型（如果没有限定类型，被替换为Object类型）。

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

####5.1 泛型表达式的转化
当调用泛型时，如果返回值的类型被擦除，编译器自动加入强制类型转化。

	Pair<Employee> buddies = ...;
	Employee buddy = buddies.getFirst();
因为类型擦除的缘故，getFirst实际会返回Object类型，编译器会将方法调用编译为

####5.2 泛型方法的转化
	public static <T extends Comparable> T min(T[] a)

类型擦除后变为
	
	public static Comparable min(Comparable[] a)
方法的类型擦除可导致多态的不可用,为了解决这个问题编译器引入了bridge method(桥方法)的解决方案。

####5.3 调用遗留的非泛型代码
可以使用@SuppressWarning("unchecked")进行屏蔽
最不济的情况就是和非泛型时代相同

###6 泛型的约束和限制





























