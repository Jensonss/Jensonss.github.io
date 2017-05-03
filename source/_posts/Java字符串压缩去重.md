---
title: Java字符串压缩去重
date: 2017-05-03 17:42:06
tags: Java
categories: Java
---

# 说在前面的话

去一家公司面试，开始就一道笔试题，做对了就继续，做不对就不用往下面了。内容是读取文件内容并压缩内容去重，压缩去重意思是如果内容为ABBCCCCAA压缩后字符串显示为A1B2C3A2。以此类推。其实这道题不难，但是我脑子转悠慢，读取文件和遍历字符做出来了，但是其余的没有做出了，或者说大概思路对了，但是卡在某一个点。总之有点废物就是了。。



# 我的做法

```
	public static String compress(String str){
		StringBuffer mystr = new StringBuffer();
		//我这里是从0开始的
		int count = 0;
		//而这也是初始为0，这样的设置导致下面判断时候一直
		//无所适从，对于何时该给count++，
		//何时该给mystr.append有点混乱
		char last = 0;
		int length = str.length();
		for(int i=0;i<length;i++){
			char c = str.charAt(i);
			if(last!=c){
				last = c;
				count++;
				mystr.append(last);
				mystr.append(count);
			}else{
				count++;
			}
		}
		return mystr.toString();
	}
```

# 正确地做法

- 直接使用string做法

  ```
  public String compressBad (String str)  
  {  
      String mystr = " ";  
      int count = 1;  
      char last = str.charAt(0);  
      for (int i = 1; i < str.length(); i++)  
      {  
          if (last == str.charAt(i))  
          {  
              count++;  
          }  
          else  
          {  
              mystr += last + count;  
              last = str.charAt(i);  
              count = 1;                
          }  
      }  
      return mystr += last + count;  
  }  
  ```

  ​



- 使用StringBuffer

  ```
  	public static  String compressBetter(String str) {
  		StringBuffer mystr = new StringBuffer();
  		int count = 1;
  		//取出第一个字符作为比较字符
  		char last = str.charAt(0);
  		for (int i = 1; i < str.length(); i++) {
  			//如果当前字符和之前的字符相同，说明是重复的，
  			//直接在数量值+1；
  			if (last == str.charAt(i)) {
  				count++;
  			} else {
  				//如果不相同，说明开始了一个新的字符，
  				//这时要做的只有两件事：
  				//1、结束上格字符计数并添加字符串，
  				mystr.append(last);
  				mystr.append(count);
  				//2、2、开始新的字符计数
  				last = str.charAt(i);
  				count = 1;
  			}
  		}
  		//最后一个字符计数并显示
  		mystr.append(last);
  		mystr.append(count);
  		return mystr.toString();
  	}
  ```

  ​

# 感想

作为IT从业者，大部分时间都在写业务逻辑，很多基本功已经近乎荒废了，这是非常不可取的。眼光放长远些，我们的技术不应该局限于某一种语言，而应该把重心放在跨语言的那些通用技术。比如：操作系统、算法和数据结构、网络及设计模式重构思想等，这些东西在任何语言中都会用到而且也会是重点。