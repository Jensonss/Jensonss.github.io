---
title: UML--类图详解
date: 2017-04-21 21:22:50
tags: 设计模式
categories: 设计模式
---
类图是面向对象系统建模中很常用也很重要的图，是定义其它图的基础。类图主要是用来显示系统中的类、接口间关系的一种模型。
类之间的关系主要有泛化、实现、聚合、组合、依赖、关联6种关系。

# 泛化关系(generalization)

uml中的泛化关系也就是继承关系。继承关系的2个类可以使用 is-a来表示。继承关系使用实线空心箭头来表示，箭头从子类指向父类。

![屏幕快照 2017-03-02 下午3.39.00.png](http://upload-images.jianshu.io/upload_images/1796052-be6493449edd5912.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

PS：泛化关系中的父类为非抽象类,泛化关系为面向对象中耦合度最大的一种关系

#  实现关系(Realization)

实现关系使用空心三角箭头的虚线表示，箭头从实现类指向接口。

![屏幕快照 2017-03-02 下午3.51.25.png](http://upload-images.jianshu.io/upload_images/1796052-92a163450b82adc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不知道为什么在我的osx版的startuml中实现关系只是一条线，不晓得是不是bug

# 聚合关系(Aggregation)

聚合关系表示has-a的关系，是一种不稳定的包含关系。较强于一般关联,有整体与局部的关系,如果没有了整体,局部仍然可单独存在。如公司和员工的关系，公司包含员工，但如果公司倒闭，员工依然可以换公司。在类图使用空心的菱形的实线表示，菱形从局部指向整体。

![屏幕快照 2017-03-02 下午3.56.37.png](http://upload-images.jianshu.io/upload_images/1796052-3018f76e4b869260.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 组合关系(Composition)

组合关系表示contains-a的关系，是一种强烈的包含关系。组合类负责被组合类的生命周期。是一种更强的聚合关系。部分不能脱离整体存在。如公司和部门的关系，没有了公司，部门也不能存在了。在类图使用实心菱形的实线表示，菱形从局部指向整体。

![屏幕快照 2017-03-02 下午3.59.43.png](http://upload-images.jianshu.io/upload_images/1796052-cf5edcf29e224e3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 依赖关系(Dependency)

依赖关系是对象关系最弱的一种关联方式，是临时性的关联。代码中一般指由局部变量、函数参数、返回值建立的对于其他对象的调用关系。一个类调用被依赖类中的某些方法而得以完成这个类的一些职责。在类图使用带箭头的虚线表示，箭头从使用类指向被依赖的类。

![屏幕快照 2017-03-02 下午4.03.48.png](http://upload-images.jianshu.io/upload_images/1796052-77d40f8a95f1f73d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 关联关系(Association)

关联关系是对象之间一种引用关系，比如吃饭时客户与餐具类之间的关系。这种关系通常使用类的属性表达。关联又分为一般关联、聚合关联与组合关联。在类图使用带箭头的实线表示，箭头从使用类指向被关联的类。可以是单向和双向。

![屏幕快照 2017-03-02 下午4.43.15.png](http://upload-images.jianshu.io/upload_images/1796052-78619e44991e9581.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 聚合和组合如何区别？

直白点的理解就是聚合关系的两个类，局部类的生命周期不受整体类的影响，能够剥离整体单独存在。
而组合关系的两个类，局部类的生命周期受限于整体类，整体类不存在时，局部类也将消亡。
举个栗子：
学生去学校上学被分配到x年级：
其中学生和学校属于聚合关系，学生可以脱离学校独立存在，县里学校不好可以去市里学校。而学校和班级是组合关系，就是说班级存在的前提是要有个学校。

![屏幕快照 2017-03-02 下午4.31.06.png](http://upload-images.jianshu.io/upload_images/1796052-a3572272c2413345.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)