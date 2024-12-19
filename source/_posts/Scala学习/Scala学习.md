---
layout: posts
title: Scala学习
date: 2022-10-29 22:25:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Scala学习"
tags:
- "scala"
---

简介 <!--more-->

简述Scala特点：

​		能用java 的包；		

​		类型推断，var变量，val常量；

​		支持并发和分布式；

​		方法体可实现可不实现；

​		强大的switch ...case；

​		虽然面向对象，但是可以函数式编程。



新建一个maven项目，在里面选择用 Scala 就行。



# 类和对象

![截屏2022-06-15 18.35.48](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-15%2018.35.48.jpg)

```scala
// 同一个包下不能有重名的类名。
class Person(xname:String,xage:Int){
  println("***** start class *****")
  private val name = xname
  var age = xage
  var gender = 'M'

  def this(yname:String,yage:Int,ygender:Char){
    this(yname,yage)
    this.gender = ygender
  }
  def syaName():Unit={
    println("hello "+ this.name)
  }
  println("***** end class *****")
}

object Person {
  println("===== start object =====")
  def apply(i:Int):Unit = {
    println("num is "+i)
  }
  def main(args: Array[String]): Unit = {
    val p = new Person(xname="zhangsan",xage = 28)
    println("gender is "+p.gender)
    p.gender = 'F'  // 实例化一个类，作为不可变对象，它的类内定义的属性 也是可以改变的。
    println("changed gender is "+p.gender)
    println(p.name)
    Person(100)
    val p2 = new Person(yname="lisi",yage=12,ygender='F')
    print("lisi gender is "+p2.gender)
  }
}
```

```scala
// output 
===== start object =====
***** start class *****
***** end class *****
gender is M
changed gender is F
zhangsan
num is 100
***** start class *****
***** end class *****
lisi gender is F
Process finished with exit code 0
```



# 简单计算

```scala
object eazy_math {
  def main(args:Array[String]):Unit = {
//    for (i <- 1.until(10)){
//      for(j <- 1 until(10)){
//        if(i >= j){
//          print(j+"*"+i+"="+i*j+"\t")
//        }
//        if(j == i){
//          println("\n")
//        }
//      }
//    }
    for (i <- 1.until(10);j <- 1 until(10)){
        if(i >= j){
          print(j+"*"+i+"="+i*j+"\t")
        }
        if(j == i){
          println("\n")
        }
    }
    for(ii <- 1 to 100 if(ii>50) if(ii%2==0)){
      print(ii)
    }
    println()
    val result = for(iii <- 1 to 100 if(iii>50) if(iii%2==0)) yield iii
    println(result)

    var a = 0
    while(a<3){
      println(a)
      a += 1
    }
  }
}

```

```scala
// output
1*1=1	

1*2=2	2*2=4	

1*3=3	2*3=6	3*3=9	

1*4=4	2*4=8	3*4=12	4*4=16	

1*5=5	2*5=10	3*5=15	4*5=20	5*5=25	

1*6=6	2*6=12	3*6=18	4*6=24	5*6=30	6*6=36	

1*7=7	2*7=14	3*7=21	4*7=28	5*7=35	6*7=42	7*7=49	

1*8=8	2*8=16	3*8=24	4*8=32	5*8=40	6*8=48	7*8=56	8*8=64	

1*9=9	2*9=18	3*9=27	4*9=36	5*9=45	6*9=54	7*9=63	8*9=72	9*9=81	

525456586062646668707274767880828486889092949698100
Vector(52, 54, 56, 58, 60, 62, 64, 66, 68, 70, 72, 74, 76, 78, 80, 82, 84, 86, 88, 90, 92, 94, 96, 98, 100)
0
1
2

Process finished with exit code 0
```



# 方法

![截屏2022-06-16 10.37.59](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-16%2010.37.59-7049242.jpg)

```scala
object func_test {
  def main(args: Array[String]): Unit ={
    val a = 1
    val b = a + 1
    def add( a:Int, b:Int): Int={
      a+b
    }
    var c = add(a, b)
    println(c)
    def reduce(a:Int,b:Int):Int={
      println(c+"is c")
      if(a>b) a-b else b-a
    }
    println(reduce(a,b))
    def multi(a:Int,b:Int) = a*b
    println(multi(a,b))
  }
}
```

```scala
object recursion_test {
  def main(args:Array[String]) {
    def recursion(num:Int):Int={
      if (num==1){
        1
      }
      else{
        num*(recursion(num-1))
      }
    }
    println(recursion(4))

    def arg_test(a :Int = 10,b :Int=20):Int={
      a+b
    }
    println("arg_test is "+arg_test(b=100)) 
  }
}
```

```scala
import java.util.Date

object lambda_func {
  def main(args:Array[String]): Unit ={
    def func(s:String*): Unit ={
      for(elem <- s){
        println(elem)
      }
    }
    func("a","b","c")

    def func_lambda(s:String*): Unit ={
      println("**** lambda is => **** ")
      s.foreach(elem => println(elem))
    }
    func_lambda("d","e","f")

    def func_lambda_easy(s:String*): Unit ={
      println("**** lambda eazy **** ")
      s.foreach(println(_))
    }
    func_lambda_easy("g","h","i")
//    对比一下 函数 和 方法 的区别
//    方法
    def fun1(a:Int,b:Int): Int ={
      a+b
    }
//    函数
    def fun2:(Int,Int) => Int = (a:Int,b:Int) =>{
      a+b
    }
    println(fun2(1,2))

//    偏应用函数（有点像python的修饰器，对原方法、函数加工。 ）
    def showLog(date : Date, log: String): Unit ={
      println(s"date is $date ,log is $log  ")
    }
    val date = new Date()
    showLog(date,"a")
    showLog(date,"b")
    showLog(date,"c")
    def fun_partial:String =>Unit = showLog(date,_:String)
    fun_partial("aa")
    fun_partial("bb")
    fun_partial("cc")

//    方法的参数 是 函数
    def fun_add(a:Int,b:Int): Int ={
      a+b
    }
    def fun_arg_fun(f:(Int,Int)=>Int,s:String):String={
      val i: Int = f(100, 200)
      i + "#" + s   // 因为声明了类型，所以这里会自定类型转换。
    }

    val result = fun_arg_fun(fun_add, "hello") //传递的是函数名，不带参数,本质是传递一个逻辑！
    println(result)

    //    方法的参数 是 函数  (匿名函数写法) ()=>{}
    val result2 = fun_arg_fun((a:Int,b:Int)=>{a*b},"scala" ) //本质是传递一个逻辑！
    println(result2)

    //    方法的返回是 函数
    def fun_out(s:String): (String,String)=>String ={
      def fun_in(s1:String,s2:String):String={
        s+"#"+s1+","+s2
      }
      fun_in
    }
    println(fun_out("a")("b","c"))
    //    方法的返回是 函数2
    def fun_out2(s:String)={
      def fun_in2(s1:String,s2:String):String={
        s+"#"+s1+","+s2
      }
      fun_in2 _ // 方法 convert to 函数
    }
    println(fun_out2("a")("e","f"))

//    方法的参数和返回都是函数
    def fun_outer(f:(Int,Int)=>Int):(String,String)=>String={
      val i = f(1, 2)
      def fun_inner(a:String,b:String):String={
        i+":"+a+","+b
      }
      fun_inner _ // _ 可加可不加
    }
    println(fun_outer((a,b)=>{a+b})("scala","python"))

//    柯里化函数
    def fun_klh(a:Int,b:Int)(c:Int,d:Int):Int={
      a+b+c+d
    }
    println(fun_klh(1,2)(3,4))
  }
}

// outer
a
b
c
**** lambda is => **** 
d
e
f
**** lambda eazy **** 
g
h
i
3
date is Fri Jun 17 03:00:53 PDT 2022 ,log is a  
date is Fri Jun 17 03:00:53 PDT 2022 ,log is b  
date is Fri Jun 17 03:00:53 PDT 2022 ,log is c  
date is Fri Jun 17 03:00:53 PDT 2022 ,log is aa  
date is Fri Jun 17 03:00:53 PDT 2022 ,log is bb  
date is Fri Jun 17 03:00:53 PDT 2022 ,log is cc  
300#hello
20000#scala
a#b,c
a#e,f
3:scala,python
10

Process finished with exit code 0

```



# 字符串和数组

```scala
import scala.collection.mutable.{ArrayBuffer, ListBuffer}

object str_list_array {
  def main(args: Array[String]):Unit={
//    默认不可变
    val arr = Array[String]("a","b","c","d")  // object use apply func ;
    val array0 = new Array[Int](3)
//    for(elem<-arr){
//      println(elem)
//    }
    array0(0) = 3  //[] 和 () 这里和python不一样。
    array0(1) = 2
    array0.foreach(println(_))
    val array1 = new Array[Array[Int]](3)
    array1(2) = Array[Int](1,2,3,4,5)
    array1(0) = Array[Int](6,7,8,9,10,11,12)
    array1(1) = Array[Int](13,14,15,16)
    array1.foreach( arr => {arr.foreach(println(_))})

    val arr1 = Array[String]("a","b")
    val arr2 = Array[String]("1","2")
    val arrays = Array.concat(arr1,arr2)
    arrays.foreach(println(_))

    val array:Array[String] = Array.fill(3)("hello")
    array.foreach(println(_))

//    可变的array
    val arr_var = ArrayBuffer[Int](1,2,3 )
    arr_var.+=(50)  // add into tail index
    arr_var.+=:(100) // add into head index
    arr_var.append(4,5,6)
    arr_var.foreach(println(_))

    val list = List[String]("scala spark", "python flask", "java spring","a")
    list.foreach(println(_))
      val result: List[Array[String]] = list.map(s => {
          s.split(" ")
      })
      result.foreach(arr=>{
          println("new word")
          arr.foreach(println(_))})
      val result2: List[String] = list.flatMap(s => {
          s.split(" ")
      })
      for(elem<-result2){
          println(elem)
      }
      val result3: List[String] = list.filter(s => {
          "scala spark" == s
      })
      result3.foreach(println(_))
      val num: Int = list.count(s => {
          s.length < 3
      })
      println(num)

//      可变的 list
      val list2 = ListBuffer[Int](1,2,3)
      list2.append(4,5)
      list2.+=:(100)
      list2.foreach(println(_))
  }
}
```



# set、map、tuple

```scala
object set_map_tuple {
  def main(srgs: Array[String]): Unit ={
    val set = Set[Int](1,2,2,3,3,4) // set 可以去重，但无序
    set.foreach(print(_))
    print("\n")

    val set2 = Set[Int](3,4,5,6)
    val result: Set[Int] = set2.intersect(set) // 交集
    result.foreach(print(_))
    print("\n")
    val value: Set[Int] = set & set2  // 交集
    value.foreach(print(_))
    print("\n")
    val value2 = set &~ set2   // 差集
    value2.foreach(print(_))
    print("\n")

//    可变set
    import scala.collection.mutable.Set
    val set_mutable =Set[Int](10,11,12)
    val ints = set_mutable.+(100)  //mutable
    ints.+=(99)
    ints.foreach(print(_))
      print("\n")
    set_mutable.+= (100)    // mutable
    set_mutable.foreach(print(_))
    print("\n")

    val map = Map[String,Int]("a"->100,"b"->200,("c",300),("c",400))
    println(map)
    val option: Option[Int] = map.get("a")
    println(option)
    println(option.get)
    val value_key = map.get("aa").getOrElse("no value")
    println(value_key)

    val keys: Iterable[String] = map.keys
    keys.foreach(key=>{
      val value1: Int = map.get(key).get
      println(s"key is $key :value is $value1 ")
    })
    val values: Iterable[Int] = map.values
    values.foreach(print)

    val map1: Map[String, Int] = Map[String, Int]("a" -> 1, ("b", 2), ("c", 3))
    val map2: Map[String, Int] = Map[String, Int]("a" -> 100, ("b", 2),("d",4))
    val map3: Map[String, Int] = map1.++(map2) //用map2 去覆盖 map1
    map3.foreach(println)
    val map4: Map[String, Int] = map1.++:(map2) //把map2 放到map1 的前面去覆盖 map1
    map4.foreach(println)

    //    可变map
    import scala.collection.mutable
    val map_mutable: mutable.Map[String, Int] = mutable.Map[String, Int]()
    map_mutable.put("a",100)
    map_mutable.put("b",200)
    map_mutable.foreach(print)
    print("\n")
    val result_filter: mutable.Map[String, Int] = map_mutable.filter(tp => {
      val key: String = tp._1
      val value: Int = tp._2
      value == 200
    })
    result_filter.foreach(print)
    print("\n")

    val tuple1 = new Tuple1("hello")
    val tuple2 = new Tuple2("a", 1)
    val tuple3 = new Tuple3(1, "scala", true)
    val tuple4: (Int, Int, String, Int) = (1, 2, "abc", 4)
//    val no_tuple: Nothing = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23)
//    元组不能直接遍历，要先变成迭代器。
    val tuple_iterator: Iterator[Any] = tuple4.productIterator
    tuple_iterator.foreach(print) //typle 1
    print("\n")
    val tuple_iterator2: Iterator[Any] = tuple3.productIterator
    while(tuple_iterator2.hasNext){ // typle 2
      print(tuple_iterator2.next())
    }
    print("\n")
    println(tuple4)
    println(tuple2.toString(),tuple2.swap)
  }
}
```



# trait、match、case 、偏函数、样例类

```scala
// 特性中可以定义完方法，也可以不写方法体，让继承的类重写实现。
trait IsEquale{
  def isEqu(o:Any):Boolean
  def isNotEqu(o:Any):Boolean = !isEqu(o)
}

// 类继承 一个特性。
class Point(xx:Int,yy:Int) extends IsEquale {
  val x = xx
  val y = yy
  override def isEqu(o: Any): Boolean = {
    o.isInstanceOf[Point]&&o.asInstanceOf[Point].x==this.x
  }
}

object trait_match_case_biasfunc {
  def main(args: Array[String]):Unit={
    val p1 = new Point(1, 2)
    val p2 = new Point(1, 3)
    val result: Boolean = p1.isEqu(p2)
    println(result)
  }
}
```

![截屏2022-06-18 18.18.33](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-18%2018.18.33.jpg)

```scala
object match_test {
  def main(args: Array[String]):Unit={
    val tp: (Int, Double, String, String, Boolean) = (1, 1.2, "abc", "a", true)
    val iter: Iterator[Any] = tp.productIterator
    iter.foreach(MatchTest) // iter.foreach(s=>{MatchTest(s)}) ; iter.foreach(MatchTest(_)) 因为只调用了一次所以可以这样写
    
    def MatchTest(o:Any):Unit={
      o match {
        case i:Int=>{println(s"type is Int, value is $i")}
        case 1=>println("value is 1")
        case d:Double=>println(s"type is Double,value is $d")
        case s:String=>println(s"type is string,value is $s")
        case "a"=>println("value is a")
        case _=>{println("no match...")} // 用 _ 去匹配 剩下的任何内容
      }
    }
  }
}
// output
type is Int, value is 1
type is Double,value is 1.2
type is string,value is abc
type is string,value is a
no match...
```

​		偏函数 没有 match 只有case，偏函数定义时，不能使用括号传参，默认定义PartialFunction中传入一个值，匹配上了对应的case,返回一个值，只能匹配同种类型。	

```scala
object match_test {
  def main(args: Array[String]):Unit={
    def partial_fun :PartialFunction[Any,Int]={
      case "abc"=>2
      case "a"=>{1}
      case _ => 200 // 用 _ 去匹配 剩下的任何内容
    }
    val resul: Int = partial_fun("abcd")
    println(resul)
  }
}
```

​		当一个类被定义成为case类后，Scala会自动帮你创建一个伴生对象并帮你实现了apply， unapply，setter， getter 和toString,equals，copy和hashCode等方法。

​		当构造参数是声明为var类型的，它将帮你实现setter和getter方法。

```scala
case class Person1(var name:String,age:Int)
//class Person1(var name:String,age:Int)

object case_class_test {
  def main(args:Array[String]): Unit ={
    val p1 = new Person1("zhangsan",18 )
    val p2 = Person1("zhangsan",18 )
    println(p1.equals(p2))
  }
}
//output 
true
```



# 隐式转换

​		原来没有某种功能，通过隐式转换让它有，类似Java的反射、python的修饰器加工（不完全一样，但是目的类似）。

## 隐式值与隐式参数	

```scala
package scala_repository

object Implicit_trans {
  // 隐式参数
  def say(implicit s:String): Unit ={
    println(s"say is $s")
  }
  // 柯里化方式 应用部分隐式参数
  def sayName(i:Int=1)(implicit s:String):Unit={
    println(s"name is $s")
  }

  def main(args: Array[String]): Unit = {
    val name = "chenyushao"
    // 隐式值
    implicit val name1 = "miaoyang"
//    implicit val name2 = "lisi"  //ambiguous implicit values:
    // 隐式调用不用传值
    sayName(5)(name)
    say
  }
}
```

## 隐式转换函数

```scala
package scala_repository

class Animal(name:String){
  def canFly(): Unit ={
    println(s"$name can fly!")
  }
}
class robot(name:String){
  val xname = name
}

object implicit_trans1 {
  implicit def robot_to_animal(r:robot):Animal={
    new Animal(r.xname)
  }
//  they are ambiguous  
//  implicit def robot_to_animal2(r:robot):Animal={
//    new Animal(r.xname)
//  }
  def main(args: Array[String]): Unit ={
    val r:robot = new robot("chenyushao")
    r.canFly()
  }
}
```

## 隐式类

​		对象调用某个值或者方法，如果自己的里面找不到，就会去伴生类、伴生对象找，再找不着，就会去找同对象或者包内，有没有隐式转换的函数或者隐式转换类（都是把本类型作为参数传入），若有，就会用上里面的方法、值。

```scala
package scala_repository

class Robot(xname:String){
  val name = xname
}

object implicit_trans2 {
  // 隐式类只能定义在 类、对象中。
  implicit class Robot_explend_func(r:Robot){
    def showNames(): Unit ={
      println(s"${r.name} is my name.")
    }
  }
  def main(args:Array[String]): Unit ={
    val r = new Robot("chen")
    r.showNames()
  }
}
```



# sparkwordcount

​		我们用maven 来搞。

​		先从mvnrepository 中选一个要用到的spark版本。

![截屏2022-06-21 11.58.06](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-21%2011.58.06.jpg)

 		在pom.xml中添加依赖

![截屏2022-06-21 12.22.21](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-21%2012.22.21.jpg)

​		在idea中查看 maven的配置信息和repository位置，点击override，然后取消，其实是为了刷新pom.xml。

![截屏2022-06-21 12.20.41](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-21%2012.20.41.jpg)

​		自动下载好了。![截屏2022-06-21 12.24.03](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-21%2012.24.03.jpg)

​		scala 的简写方式是真的简单啊。

```scala
package scala_spark_wordcount
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object SparkWC {
  def main(args:Array[String]): Unit ={
    val conf = new SparkConf()
    conf.setAppName("WordCount")
    conf.setMaster("local")
    val sc = new SparkContext(conf)

    val lines: RDD[String] = sc.textFile("./././data/words")
//    val words: RDD[String] = lines.flatMap(line => {
//      line.split(" ")
//    })
//    val pair_word: RDD[(String, Int)] = words.map(word => {
//      (word, 1)
//    })
//    val result: RDD[(String, Int)] = pair_word.reduceByKey((a: Int, b: Int) => {
//      a + b
//    })
    val result: RDD[(String, Int)] = lines.flatMap(_.split(" ")).map((_, 1)).reduceByKey(_ + _)
    result.foreach(one=>{println(one)})
    sc.stop()
  }
}
```

```scala
// output
(scala,1)
(spark,3)
(hive,1)
(hadoop,1)
(python,1)
(hello,9)
(kafka,2)
```



# 左折叠、右折叠

​		左折叠`/:`   

​		右折叠 `:\`

  右折叠形象化表示 `op(a, op(b, op(c, z)))`

![截屏2022-06-24 15.32.24](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-24%2015.32.24.jpg)

左折叠形象化表示` op(op(op(z, a), b), c)`

![截屏2022-06-24 15.33.34](Scala%E7%AE%80%E5%8D%95%E5%AD%A6.assets/%E6%88%AA%E5%B1%8F2022-06-24%2015.33.34.jpg)

```scala
package scala_spark_wordcount

import scala.collection.mutable

object hello {
  def main(args:Array[String]): Unit ={
    println("hello world")

    val list1: List[Int] = List(3, 5, 9, 10, 11)
    println(list1.foldRight(6)(_ - _))  //2

    val map1: mutable.Map[String, Int] = mutable.Map("a" -> 1, "b" -> 2, "c" -> 3)
    val map2: mutable.Map[String, Int] = mutable.Map("a" -> 2, "b" -> 3, "d" -> 4)

    val res: mutable.Map[String, Int] = map1.foldLeft(map2) { (m2, kv) => {
      val k: String = kv._1
      val v: Int = kv._2
      // 判断map2是否有这个key，有就取出来对应的v和map1的v相加，没有就是0
      m2(k) = m2.getOrElse(k, 0) + v
      m2
    }
    }
    println(res)
  }
}
```

```scala
// output
hello world
2
HashMap(a -> 3, b -> 5, c -> 3, d -> 4)
```





# 生成带有比重的随机值

```scala
package kafka_to_spark_test

import scala.collection.mutable.ListBuffer
import scala.util.Random

object mock_data {
  def apply[T](opts: (T,Int)*):mock_data[T]={
    val randomoptions = new mock_data[T]()
    randomoptions.totalWeight = (0 /: opts)(_+_._2)
    opts.foreach {                         // 偏函数 没有 match 只有case
      case (value,weight)=>{               // 制作一张齐全的大表option，从中随机取值。
        randomoptions.options ++= (1 to weight).map(_ => value )}}
    randomoptions
  }
  def main(args:Array[String]): Unit ={
    val opts: mock_data[String] = mock_data(("zhangsan", 1), ("lisi", 10))
    println(opts.getRandomOption())
    println(opts.getRandomOption())
  }
}
class mock_data[T]{
  var totalWeight:Int = _ // var tatalWeight:Int = null
  private val options: ListBuffer[T] = ListBuffer[T]()

  def getRandomOption(): T ={
    val random = new Random()
    options(random.nextInt(options.length))
  }
}

// output
lisi
lisi
```

