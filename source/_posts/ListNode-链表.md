---
title: ListNode-链表
date: 2021-06-22 11:40:00
type: artitalk
categories:
- JAVA基础知识
tags:
- 数据结构
cover: https://cdn.pixabay.com/photo/2021/06/13/07/33/mountain-pass-6332476_960_720.jpg
---

# 基本概念

链表是一种数据结构，由数据和指针构成，链表的指针指向下一个节点。

![链表结构](https://pic.imgdb.cn/item/60d15d47844ef46bb2385734.jpg)

`java ListNode`链表 就是用`Java`自定义实现的链表结构。

# 快速入门

## 基本结构



```java
class ListNode {        //类名 ：Java类就是一种自定义的数据结构
    int val;            //数据 ：节点数据 
    ListNode next;      //对象 ：引用下一个节点对象。在Java中没有指针的概念，Java中的引用和C语言的指针类似
}
```

 

## 初始化 

添加构造方法方便初始化。



```java
class ListNode {        //类名 ：Java类就是一种自定义的数据结构
    int val;            //数据 ：节点数据 
    ListNode next;      //对象 ：引用下一个节点对象。在Java中没有指针的概念，Java中的引用和C语言的指针类似
    
    ListNode(int val){  //构造方法 ：构造方法和类名相同   
        this.val=val;     //把接收的参数赋值给当前类的val变量
    }
}
```

 

> 范型写法：使用范型可以兼容不同的数据类型
>
> 
>
> ```java
> class ListNode<E>{                //类名 ：Java类就是一种自定义的数据结构
>     E val;                        //数据 ：节点数据 
>     ListNode<E> next;             
>     //对象 ：引用下一个节点对象。在Java中没有指针的概念，Java中的引用和C语言的指针类似
>     
>     ListNode(E val){              //构造方法 ：构造方法和类名相同   
>         this.val=val;             //把接收的参数赋值给当前类的val变量
>     }
> }
> ```
>
>  

创建链表及遍历链表：



```java
class ListNode {        //类名 ：Java类就是一种自定义的数据结构
    int val;            //数据 ：节点数据 
    ListNode next;      //对象 ：引用下一个节点对象。在Java中没有指针的概念，Java中的引用和C语言的指针类似
    
    ListNode(int val){  //构造方法 ：构造方法和类名相同   
        this.val=val;   //把接收的参数赋值给当前类的val变量
    }
}

class Test{
    public static void main(String[] args){
        
        ListNode nodeSta = new ListNode(0);    //创建首节点
        ListNode nextNode;                     //声明一个变量用来在移动过程中指向当前节点
        nextNode=nodeSta;                      //指向首节点
        //这句话就相当于 ListNode nextNode = nodeSta = new ListNode(0);

        //创建链表
        for(int i=1;i<10;i++){
            ListNode node = new ListNode(i);  //生成新的节点
            nextNode.next=node;               //把新节点连起来
            nextNode=nextNode.next;           //当前节点往后移动
        } //当for循环完成之后 nextNode指向最后一个节点，
        
        nextNode=nodeSta;                     //重新赋值让它指向首节点
        print(nextNode);                      //打印输出
      
    }
    
    //打印输出方法
    static void print(ListNode listNoed){
        //创建链表节点
        while(listNoed!=null){
            System.out.println("节点:"+listNoed.val);
            listNoed=listNoed.next;
        }
        System.out.println();
    }
   
}
```

 

# 更多使用

## 插入节点

![插入节点](https://pic.imgdb.cn/item/60d15dd9844ef46bb23c2f9a.jpg)

 

 

 



```Java
class ListNode {        //类名 ：Java类就是一种自定义的数据结构
    int val;            //数据 ：节点数据 
    ListNode next;      //对象 ：引用下一个节点对象。在Java中没有指针的概念，Java中的引用和C语言的指针类似
    
    ListNode(int val){  //构造方法 ：构造方法和类名相同   
        this.val=val;   //把接收的参数赋值给当前类的val变量
    }
}

class Test{
    public static void main(String[] args){
        
        ListNode nodeSta = new ListNode(0);          //创建首节点
        ListNode nextNode;                           //声明一个变量用来在移动过程中指向当前节点
        nextNode=nodeSta;                            //指向首节点
        
        //创建链表
        for(int i=1;i<10;i++){
            ListNode node = new ListNode(i);         //生成新的节点
            nextNode.next=node;                      //把新节点连起来
            nextNode=nextNode.next;                  //当前节点往后移动
        } //当for循环完成之后 nextNode指向最后一个节点，
        
        nextNode=nodeSta;                            //重新赋值让它指向首节点
        print(nextNode);                             //打印输出
     
        //插入节点
        while(nextNode!=null){
            if(nextNode.val==5){
                ListNode newnode = new ListNode(99);  //生成新的节点
                ListNode node=nextNode.next;          //先保存下一个节点
                nextNode.next=newnode;                //插入新节点
                newnode.next=node;                    //新节点的下一个节点指向 之前保存的节点
            }
            nextNode=nextNode.next;
        }//循环完成之后 nextNode指向最后一个节点
         nextNode=nodeSta;                            //重新赋值让它指向首节点
         print(nextNode);                             //打印输出
      
    }
    
    static void print(ListNode listNoed){
        //创建链表节点
        while(listNoed!=null){
            System.out.println("节点:"+listNoed.val);
            listNoed=listNoed.next;
        }
        System.out.println();
    }
}
```

 

## 替换节点

![替换节点](https://pic.imgdb.cn/item/60d15e1a844ef46bb23ddf8d.jpg)



```java
class ListNode {        //类名 ：Java类就是一种自定义的数据结构
    int val;            //数据 ：节点数据 
    ListNode next;      //对象 ：引用下一个节点对象。在Java中没有指针的概念，Java中的引用和C语言的指针类似
    
    ListNode(int val){  //构造方法 ：构造方法和类名相同   
        this.val=val;   //把接收的参数赋值给当前类的val变量
    }
}

class Test{
    public static void main(String[] args){
        
        ListNode nodeSta = new ListNode(0);          //创建首节点
        ListNode nextNode;                           //声明一个变量用来在移动过程中指向当前节点
        nextNode=nodeSta;                            //指向首节点
        
        //创建链表
        for(int i=1;i<10;i++){
            ListNode node = new ListNode(i);         //生成新的节点
            nextNode.next=node;                      //把新节点连起来
            nextNode=nextNode.next;                  //当前节点往后移动
        } //当for循环完成之后 nextNode指向最后一个节点，
        
        nextNode=nodeSta;                            //重新赋值让它指向首节点
        print(nextNode);                             //打印输出
     
        //替换节点
        while(nextNode!=null){
            if(nextNode.val==4){
                ListNode newnode = new ListNode(99);  //生成新的节点
                ListNode node=nextNode.next.next;     //先保存要替换节点的下一个节点
                nextNode.next.next=null;              //被替换节点 指向为空 ，等待java垃圾回收
                nextNode.next=newnode;                //插入新节点
                newnode.next=node;                    //新节点的下一个节点指向 之前保存的节点
            }
            nextNode=nextNode.next;
        }//循环完成之后 nextNode指向最后一个节点
         nextNode=nodeSta;                            //重新赋值让它指向首节点
         print(nextNode);                             //打印输出
      
    }
    
    //打印输出方法
    static void print(ListNode listNoed){
        //创建链表节点
        while(listNoed!=null){
            System.out.println("节点:"+listNoed.val);
            listNoed=listNoed.next;
        }
        System.out.println();
    }
}
```

 

## 删除节点

![删除节点](https://pic.imgdb.cn/item/60d15e46844ef46bb23f00f6.jpg)



```java
class ListNode {        //类名 ：Java类就是一种自定义的数据结构
    int val;            //数据 ：节点数据 
    ListNode next;      //对象 ：引用下一个节点对象。在Java中没有指针的概念，Java中的引用和C语言的指针类似
    
    ListNode(int val){  //构造方法 ：构造方法和类名相同   
        this.val=val;   //把接收的参数赋值给当前类的val变量
    }
}

class Test{
    public static void main(String[] args){
        
        ListNode nodeSta = new ListNode(0);          //创建首节点
        ListNode nextNode;                           //声明一个变量用来在移动过程中指向当前节点
        nextNode=nodeSta;                            //指向首节点
        
        //创建链表
        for(int i=1;i<10;i++){
            ListNode node = new ListNode(i);         //生成新的节点
            nextNode.next=node;                      //把新节点连起来
            nextNode=nextNode.next;                  //当前节点往后移动
        } //当for循环完成之后 nextNode指向最后一个节点，
        
        nextNode=nodeSta;                            //重新赋值让它指向首节点
        print(nextNode);                             //打印输出
     
        //删除节点
        while(nextNode!=null){
            if(nextNode.val==5){
                ListNode listNode=nextNode.next.next;     //保存要删除节点的下一个节点
                nextNode.next.next=null;                  //被删除节点 指向为空 ，等待java垃圾回收
                nextNode.next=listNode;                   //指向要删除节点的下一个节点
            }
            nextNode=nextNode.next;
        }//循环完成之后 nextNode指向最后一个节点
         nextNode=nodeSta;                            //重新赋值让它指向首节点
         print(nextNode);                             //打印输出
    }
    
    //打印输出方法
    static void print(ListNode listNoed){
        //创建链表节点
        while(listNoed!=null){
            System.out.println("节点:"+listNoed.val);
            listNoed=listNoed.next;
        }
        System.out.println();
    }
}
```

 

# 补充说明

在对节点进行替换或删除的时候，被替换或被删节点的next引用需不需要设置为null？


答案是： 不需要，因为一个对象被回收的前提是因为没有任何地方持有这个对象的引用（引用计数器为0）也就是说它不在被引用，那么那么它将被回收，至于它引用什么对象无关紧要，因为对于它所引用的对象来说依然是看引用计数器是否为0；
