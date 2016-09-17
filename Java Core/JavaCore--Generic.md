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
































