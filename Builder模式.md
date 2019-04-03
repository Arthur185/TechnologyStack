### 1.Builder模式介绍
Builder模式是一步一步创建一个复杂对象的创建型模式，可以让使用者在不知道内部构造细节的情况下精确控制对象的构造流程。该模式是为了将构建复杂对象的过程和它的部件解耦，使构建过程和部件的表示隔离开。
### 2.Builder模式定义
将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的展示。
### 3.Builder模式使用场景
* a.相同方法，不同执行顺序，产生不同事件结果
* b.多个部件和零件都可以装配到一个对象中，但产生的运行结果不相同
* c.产品类非常复杂，产品类中的调用顺序不同产生了不同的作用
* d.初始化对象特别复杂，参数众多，且很多参数都具有默认值
### 4.Builder模式UML类图

! [ Builder模式UML类图（摘自网络）](https://upload-images.jianshu.io/upload_images/6318561-9197aad5c39ac42f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

类图解析：
* Product : 产品抽象类
* Builder : 抽象Builder类，规范产品组建，一般是由子类实现具体的组建过程
* ConcreteBuilder : 具体的Builder类（实现Builder类）
* Director : 统一组装过程
### 5.实例解析
##### 5.1实例背景
组装计算机，过程简化为：构建主机、设置操作系统、设置显示器。
##### 5.2构建计算机类，即Product角色
```
//计算机抽象类
public abstract class Computer{
    protected String mBoard;
    protected String mDisplay;
    protected String mOs;
  
    protected Computer(){}
    //设置主板
    public void setBoard(String board){
        mBoard = board;
    }
    //设置显示器
    public void setDisplay(String display){
        mDisplay= display;
    }
    //设置操作系统
    public abstract void setOS();

    @Override
    public String toString() {
        return "Computer [mBoard=" + mBoard+ ", mDisplay=" + mDisplay +
                ", mOs=" + mOs+ "]";
    }
}
```
```
//具体的计算机类，Macbook
public class Macbook extends Computer {
    protected Macbook (){}
    
    @Override
    public void setOS() {
        mOs = "Mac OS X 10";    //设置默认操作系统
    }
}
```
##### 5.3构建抽象Builder类
```
public abstract class Builder {
    //设置主机
    public abstract void buildBoard(String board);
    //设置显示器
    public abstract void buildDisplay(String display);
    //设置操作系统
    public abstract void buildOS();
    //创建计算机类
    public abstract Computer create();
}
```
##### 5.4构建具体的Builder类
```
public class MacbookBuilder extends Builder{

    private Computer mComputer = new Macbook();

    @Override
    public void buildBoard(String board) {
        mComputer.setBoard(board);
    }
    @Override
    public void buildDisplay(String display) {
        mComputer.setDisplay(display);
    }
    @Override
    public void buildOS() {
        mComputer.setOS();
    }
    @Override
    public Computer create() {
        return mComputer;
    }
}
```
##### 5.5构建Director类 构造Computer
```
public class Director {
    Builder mBuilder =null;

    public Director(Builder builder){
        this.mBuilder =builder;
    }
    //构建对象
    public void construct(String board, String display){
        mBuilder.buildBoard(board);
        mBuilder.buildDisplay(display);
        mBuilder.buildOS();
    }
}
```
##### 5.6测试
```
public class Test {
    public static void main (String[] args) {
        //构造器
        Builder builder = new MacbookBuilder ();
        //Director
        Director mDirector = new Director ();
        mDirector.construct("英特尔主板"， "Dell显示器 ");
        System.out.println("Computer Info : "+ builder.create().toString);
    }
}
```
输出结果
```
Computer Info :Computer [mBoard=英特尔主板, mDisplay=Dell显示器, mOs=Mac OS X 10]
```
### 6.Builder模式精简
现实开发中，Director角色经常会被省略而直接用一个Builder来进行对象的组装，这个Builder通常为链式调用，关键点是每个set方法都返回自身，也就是return this,代码大致如下
```
  new TsetBuilder().setA("A").set("B").create();
```
通过这种形式不进去除了Director角色，整个结构也更加简单，也可以对Product对象的组装过程更加精细控制。
**建议查看Android源码AlertDialog实现，AlertDialog.Builder同时扮演了Builder、ConcreteBuilder、Director的角色，简化了Builder模式的设计！**
### 7.Builder模式总结
* 优点
   * （1）良好的封装性，使用建造者模式可以使调用者不必知道产品内部的组成的细节
  * （2）建造者独立，容易扩展
* 缺点
会产生多余的Builder对象以及Director对象，消耗内存。
