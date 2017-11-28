
---
title: Log工具封装Tag显示类方法和行数
date: 2017-11-28 23:23:02
tags: Android
categories: Android
---

<!-- more -->


```java
import android.text.TextUtils;
import android.util.Log;

/**
 * 二次封装的log打印工具
 */
public class LogUtils {

	public static String customTagPrefix = "";

	private LogUtils() {
	}

	public static boolean allowD = true;
	public static boolean allowE = true;
	public static boolean allowI = true;
	public static boolean allowV = true;
	public static boolean allowW = true;
	public static boolean allowWtf = true;

	public static LogListener logListener;//如果要处理Log信息，实现listener即可

	/**
	 * @Description log输出监听（简易观察者）
	 */
	public interface LogListener {

		void handleLog( String level, String tag, String content );
	}
  
    public static StackTraceElement getCurrentStackTraceElement() {
        return Thread.currentThread().getStackTrace()[4];
    }
  
	private static String generateTag( StackTraceElement caller ) {
		String tag = "%s[%s, %d]";
		String callerClazzName = caller.getClassName();
		callerClazzName = callerClazzName.substring(
				callerClazzName.lastIndexOf( "." ) + 1 );
		tag = String.format( tag, callerClazzName, caller.getMethodName(), caller.getLineNumber() );
		tag = TextUtils.isEmpty( customTagPrefix ) ?
				tag :
				customTagPrefix + ":" + tag;
		return tag;
	}

	public static void d( String content ) {
		if (!allowD)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.d( tag, content );
		handleLog( "d", tag, content );
	}

	public static void d( String content, Throwable tr ) {
		if (!allowD)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.d( tag, content, tr );
	}

	public static void e( String content ) {
		if (!allowE)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.e( tag, content );
		handleLog( "e", tag, content );
	}

	public static void e( String content, Throwable tr ) {
		if (!allowE)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.e( tag, content, tr );
	}

	public static void i( String content ) {
		if (!allowI)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.i( tag, content );
		handleLog( "i", tag, content );
	}

	public static void i( String content, Throwable tr ) {
		if (!allowI)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.i( tag, content, tr );
	}

	public static void v( String content ) {
		if (!allowV)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.v( tag, content );
		handleLog( "v", tag, content );
	}

	public static void v( String content, Throwable tr ) {
		if (!allowV)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.v( tag, content, tr );
	}

	public static void w( String content ) {
		if (!allowW)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.w( tag, content );
		handleLog( "w", tag, content );
	}

	public static void w( String content, Throwable tr ) {
		if (!allowW)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.w( tag, content, tr );
	}

	public static void w( Throwable tr ) {
		if (!allowW)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.w( tag, tr );
	}

	public static void wtf( String content ) {
		if (!allowWtf)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.wtf( tag, content );
		handleLog( "wtf", tag, content );
	}

	public static void wtf( String content, Throwable tr ) {
		if (!allowWtf)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.wtf( tag, content, tr );
	}

	public static void wtf( Throwable tr ) {
		if (!allowWtf)
			return;
		StackTraceElement caller = getCurrentStackTraceElement();
		String tag = generateTag( caller );
		Log.wtf( tag, tr );
	}

	/**
	 * 处理Log信息
	 *
	 * @param level
	 * @param tag
	 * @param content
	 */
	private static void handleLog( String level, String tag, String content ) {
		if (null != logListener) {
			logListener.handleLog( level, tag, content );
		}
	}
}
```

