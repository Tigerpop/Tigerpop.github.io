---
layout: posts
title: "mysql笔记"
date: 2020-11-12 11:31:01
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "mysql"
tags:
- "mysql"
- "索引"
---​	

参考：<!--more-->

# 基础：

```sql
-- 进入mysql
mysql -uroot -p[密码]

-- 改用户名
-- use mysql;
-- update user set user='chenyushao' where user='root';
-- select user,host from mysql.user;
-- flush privileges;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| chenyushao       | localhost |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
+------------------+-----------+

create database test;  #创建新的名叫test的数据库

show databases;  #查看由哪些数据库

use test;    #选中数据库

show tables;    #查看数据库里的表

truncate table 表名 # 删除表中数据 delete 可以事物回滚，truncate不可以。

#创建数据表pet
create table pet(name VARCHAR(20),
   owner VARCHAR(20), 
   species 
   VARCHAR(20),
   sex CHAR(1),
   brith DATE,
   death DATE);  

describe pet;   #查看数据表pet结构

select * from pet #查看pet表中记录

#往数据表里添加数据
INSERT INTO pet
VALUES('Puffball','Diane','hamster','f','1999-03-30',NULL);


#删除数据
delete from pet where name=‘旺财’;

#修改数据，注意是update不是updata
update pet set name=‘旺旺财’ where owner=‘周星驰’;


#～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～#～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～
#mysql建表约束
1.#主键约束
   它能够唯一确定一张表中的一条记录，也就是给某个字段添加约束，使得该字段不重复且"不为空";
   create table user(id int primary key, name varchar(20)); #key确定了id为主键,也就是不能再有出现相同的id了,name没做主键约束，故可以插入一样的name;
   
   #联合主键（id和name组成的这一对字段，要不为空，且不能id和name同时相同，id或name单个出现相同的字段是可以的，同时id和name任意一个不能为空null。）
   create table user3(id int,name varchar(20), password varchar(20), primary key(id,name) );


2.#自增约束
   create table userthree(id int primary key auto_increment,   name varchar(20 )  );
   
   #仅仅添加name，id 会自动生成1，若再添加一个name（name里面的字段相同不相同都不影响），对应的id会变成2….
   create table userthree(id int primary key auto_increment, name varchar(20));

   #忘记创建主键约束了，怎么办？
   create table user4(       id int,
      name varchar(20)   );

   alter table user4 add primary key(id); #后续定义主键 （有时候会说找不到id这个field不知道为什么，换别的field就可以。）
   alter table user4 modify id int primary key;这样也可以
   alter table user4 drop primary key;#后续删除主键

3.#唯一约束
   约束修饰的字段值不可以重复。
   create table user5(id int,name varchar(20));
   alter table user5 add unique(name);
   #或者
   create table user6(id int,name varchar(20),unique(name));

   create table user7(id int,
      name varchar(20),
      unique(id,name) #类似联合主键，但是这样key会落到id上，后续要删除就alter table user6 drop index id;即可
   );
    #删除唯一约束
   alter table user6 drop index name;
   #modify添加唯一约束
   alter table user6 modify name varchar(20) unique;

#临时总结：
   一、建表的时候添加约束
   alter table ... add...
   alter table ... modify ...
    二、删除约束(key落到谁头上删除谁就可以了)
    alter table ... drop ...

4.#非空约束
    字面意思，
   create table user9(
      id int,
      name varchar(20) not null
   );
    insert into user9 (id) values(1);#错
    insert into user9 values(1,'张三');#对

   inser into user9 (name) values('李四');#对 right
5.#默认约束
   当插入字段值时，如果没有传值，就会使用默认值
   create table user10(
      id int,
      name varchar(20),
      age int default 10
   );
    #如果没有传入值，age就用10，传入9就变成了9；
   insert into user10 (id,name,age) values(1,'zhangsan',9);
   insert into user10 (id,name) values(1,'zhangsan');

6.#外键约束
   涉及：  父表、子表。
      主表、副表。

   #（班级）
   create table classes(
      id int primary key,
      name varchar(20)
   );
   #学生表
   create table students(
      id int primary key,
      name varchar(20),
      class_id int,
      foreign key(class_id) references classes(id)# 指明学生的class_id必须引用来自classes的id
   );
   insert into classes values(1,'一班');
   insert into classes values(2,'二班');
   insert into classes values(3,'三班');
   insert into classes values(4,'四班');

   insert into students values(1001,'张三',1);
   insert into students values(1002,'张三',2);
   insert into students values(1003,'张三',3);
   insert into students values(1004,'张三',4);

   insert into students values(1005,'李四',5);#报错 students`, CONSTRAINT `students_ibfk_1` FOREIGN KEY (`class_id`) REFERENCES `classes` (`id

   delete from classes where id=4; # 报错，已经被引用，父表此项就不能删除。

    #1、父表 中没有的数据值，在子表中是不可以使用的
   #2、子表已经引用了父表的数据后，父表的此这条记录不能被删除。

#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#数据库三大设计范式

#第一范式
--1NF
数据表中所有的字段都是不可分割的原子值。
create table students2(
   id int,
   name varchar(20),
   address varchar(30)
);

insert into students2 values(1,'张三','湖南省衡阳市雁峰区');
insert into students2 values(2,'李四','湖南省衡阳市蒸湘区');
insert into students2 values(3,'王五','湖南省衡阳市石鼓区');
   #字段还可以继续拆分的，就不满足第一范式,上面就是不满足的address，就是说要尽可能拆分的细。
   #范式，设计的越详细，对于某一些实际操作可能更好，但是不一定都是好处。

#第二范式
--2NF
--必须是满足第一范式的前提下，第二范式要求，除主键外的每一列都必须完全依赖主键。
--提示：不完全依赖，只可能发生在联合主键的情况下。
create table myorder(
   product_id int,
   customer_id int,
   product_name varchar(20),#只和product_id有关
   customer_name varchar(20),#只和customer_id有关
   primary key(product_id,customer_id)#联合主键
);
   #以上，除主键以外的其他列，只依赖于主键的部分字段。不满足第二范式。

--拆表
create table myorder2(
   order_id int primary key,
   product_id int,
   customer_id int
);#以上的product_id和customer_id完全依赖于order_id

create table product(
   id int primary key,
   name varchar(20)
);#以上的name 完全依赖于id

create table customer(
   id int primary key,
   name varchar(20)
);#以上的name 完全依赖于id

--分成三个表以后，就满足了第二范式的设计规范。

另一个解释案例：
讲师P 性别 班级P         教室 代课时间 开始 结束
韩忠康 Male php0331 102 30天 2013-03-31 2013-05-05
韩忠康 Male php0228 106 30天 2013-02-28 2013-03-30
韩顺平 male Php0228 106 15天 2013-03-31 2013-04-20

上面的设计可以用讲师P，班级，两个字段作为组合主键，但是问题来了，那么后面的教室仅仅由班级字段(组合主键中的一条)就可以确定，即所谓教室部分依赖于组合主键，那么就不符合2NF。

#第三范式
--3NF
--在满足第二范式的基础上，除开主键列的其他列之间，不能有传递依赖关系。

create table myorder(
   order_id int primary key,
   product_id int,
   customer_id int,
   customer_phone varchar(15)#和customer_id有关,customer_id非主键。所以不满足第三范式。
);

--拆分
create table myorder(
   order_id int primary key,
   product_id int,
   customer_id int,
);
create table customer(
   customer_id int primary key,
   name varchar(20),
   phone varchar(15)
);
#以上满足了第三范式。


#~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#这里的快速注释 ，是command+/;

-- 学生表
-- Student
-- 学号
-- 姓名
-- 性别
-- 出生年月日
-- 所在班级
create table student(
    sno varchar(20) primary key,
    sname varchar(20) not null,
    ssex varchar(10) not null,
    sbirthday datetime,
    class varchar(20)
);

--课程表
-- Course
-- 课程号
-- 课程名称
-- 教师编号
create table course(
    cno varchar(20) primary key,
    cname varchar(20) not null,
    tno varchar(20) not null, -- 因为这里的老师编号是来源于老师表的老师编号，所以需要外键约束。
    foreign key(tno) references teacher(tno)
);

-- 成绩表
-- Sorce
-- 学号
-- 课程号
-- 成绩
分数表的错误设计：
create table score(
    sno varchar (20) primary key, -- 这里有问题见下文解释。
    cno varchar(20) not null,
    degree decimal, -- 一种数值类型
    foreign key(sno) references student(sno),
    foreign key(cno) references course(cno)
);

-- 教师表
-- Teacher
-- 教师编号
-- 教师姓名
-- 教师性别
-- 出生年月
-- 职称
-- 所在部门
create table teacher(
    tno varchar(20) primary key,
    tname varchar(20) not null,
    tsex varchar (10) not null,
    tbirthday datetime not null,
    prof varchar(20) not null,
    depart varchar(20) not null
);
-- 往数据表中添加数据。
insert into student values('18416','陈宇韶','男','1993-4-6','184');
insert into student values('18438','董文勇','男','1993-4-6','184');
insert into student values('18435','宁鹏飞','男','1993-4-6','184');
insert into student values('18434','刘雯','男','1993-4-6','184');
insert into student values('18433','陶碧云','男','1993-4-6','184');
insert into student values('18432','曹杰','男','1993-4-6','184');
insert into student values('18417','秦壁辉','男','1993-4-6','184');
insert into student values('18410','秦诗','女','1995-4-6','183');
insert into student values('18422','秦静静','女','1992-4-6','181');

insert into teacher values('1841','李辉','男','1983-4-8','副教授','数学系');
insert into teacher values('1842','朱文','女','1983-7-9','副教授','语文系');
insert into teacher values('1843','何勇','男','1988-2-3','副教授','英语系');

insert into course values('1','数学','1841');
insert into course values('2','语文','1842');
insert into course values('3','英语','1843');

drop table score;
分数表的正确设计：
create table score(
    sno varchar (20) not null, -- 像上文，若学生编号作为score的主键，则一个分数对应的学生学号是唯一的，也就是score和sno一一对应了，也就是一个学生只能有一个成绩了，实际上学生每个course都有一个成绩。
    cno varchar(20) not null,
    degree decimal, -- 一种数值类型
    foreign key(sno) references student(sno),
    foreign key(cno) references course(cno),
    primary key(sno,cno)   -- score与 （学生编号、课程号）的整体才是一一对应的关系，（学生编号、课程号）对应一个唯一的分数。
);

insert into score values('18416','1',120);
insert into score values('18438','1',111);
insert into score values('18435','2',119);
insert into score values('18434','3',99);
insert into score values('18433','2',109);
insert into score values('18432','1',88);
insert into score values('18417','3',115);
insert into score values('18417','2',115);
insert into score values('18416','2',120);
insert into score values('18416','1',121);-- 报错，（学号、课程）对应唯一分数。

-- 查询练习
-- ～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～
-- 1、查询student表的所有记录。
select * from student;  -- * 表示所有的字段，就是各个属性。

-- 2、查询student表的sname，ssex，class字段（列）
select sname ,ssex,class from student; -- 查这三个字段。

-- 3、查询教师所有单位，即不重复的depart字段（列）。
select depart from teacher;
select distinct depart from teacher; --去掉depart字段中重复 。

-- 4、查询score表中成绩在110到120之间的记录。
-- 查询区间。between ..小. and ..大.
-- where 引出条件
select * from score where degree between 110 and 120;
select * from score where degree > 110 and degree < 121;

-- 5、查询score表中成绩为85，86或88的记录。
-- 表示或或者关系。
select * from score where degree in(120,115,112);

-- 6、查询student表中183班或者为'女'的记录。
select * from student where class='183' or ssex='女';

-- 7、以class降序\升序查询student表的所有记录。
select * from student order by class desc; -- 降序
select * from student order by class asc;-- 升序

-- 8、以cno升序、degree降序查询score表的所有记录。
select * from score order by cno asc , degree desc; -- 先以cno升序排列，再在cno排序好的基础上，对所有cno同值行以degree降序排列。

-- 9、查询184班的学生人数。
-- 统计 count
select count(*) from student where class='184';

-- 10、查询score表中的最高分学生学号和课程号。（子查询或排序）
select sno,cno from score where degree=(select max(degree) from score);
--（1）找到最高分
select max(degree) from score;
-- (2) 找最高分的sno和cno
select sno,cno from score where degree=(select max(degree) from score);
-- 排序做法
select sno,cno from score order by degree;-- 默认是升序
select sno , cno from score order by degree desc limit 0,1;-- 降序排列，取最大值。但是这样会出现bug，重分只会留一个。
-- limit 第一个数字表示从多少开始，第二个数字表示查多少条。

-- 查询每门课的平均成绩
select * from course;

-- avg()
select avg(degree) from score where cno='2';
select avg(degree) from score where cno='1';
select avg(degree) from score where cno='3';
-- 以上也可以写成
-- group by 分组，就是有多少种。
select cno,avg(degree) from score group by cno;

-- 12、查询score表中至少2名学生选修的并以3开头的课程平均分。
select cno from score group by cno -- 这里group by和having 一起使用。
having count(cno)>=3; -- having 是group 后进行的，where 是group 前。

select cno from score group by cno
having count(cno)>=2 and cno like '3%'; -- cno like '3%' 是以3开头的意思。

select cno, avg(degree) from score group by cno
having count(cno)>=2 and cno like '3%';

select cno, avg(degree),count(*) from score group by cno --count(*)满足所有这样条件的计数一共有多少个。
having count(cno)>=2 and cno like '3%';

-- 13、 查询分数大于70，小于90的sno列。
select sno ,degree from score
where degree between 90 and 122;

select sno ,degree from score
where degree >70 and degree <90;

-- 14 查询所有学生的 sname、cno 和 degree 列。(多表查询)
select sname ,cno ,degree from student,score
where student.sno = score.sno;  -- mysql 的等于是单等于号。

-- 15、查询所有学生学号、课程名、和成绩。
select cno ,cname, from course;
select cname,sno,degree from course,score
where course.cno = score.cno;  -- 这里需要注意，cno=cno充当粘合的列 不能在select后面。

-- 16、查询所有学生的sname、cname和degree列。（三表查询）
select sname,cname,degree from student,course,score
where student.sno=score.sno and course.cno=score.cno; -- 粘合列不能当作select后面的列。

-- 若想把粘合的列select，则需要以下写法。
select sname,cname,degree,student.sno,course.cno from student,course,score
where student.sno=score.sno and course.cno=score.cno;

-- 把student.sno 换个名称 stu_s
select sname,cname,degree,student.sno as stu_s,course.cno as cou_c from student,course,score
where student.sno=score.sno and course.cno=score.cno;

-- 17、查询"184班"学生，每门课的平均分。
select * from student where class='184';
select sno from student where class='184';

select avg(degree) from score
where sno in(select sno from student where class='184');

select cno, avg(degree) from score
where sno in(select sno from student where class='184') group by cno;-- 按课程编号求平均分

-- 18、查询课程1 的成绩高于18432号同学，的所有同学的记录。 
select * from score
where cno='1' and degree >(select degree from score where sno='18432' and cno='1');

-- 19、查询成绩高于学号18432、课程号为2的成绩的所有记录。
select degree from score where sno='18432' and cno='1'
select * from score where degree>(select degree from score where sno='18432' and cno='1');

-- 20、查询和学号为18416、18433 的同学同年出生的所有学生的sno、sname和sbirthday。
select year(sbirthday) from student where sno in('18416','18433')
select sno,sname,sbirthday from student
where year(sbirthday) in
(select year(sbirthday) from student where sno in('18416','18433'));

-- 21、 查询'李辉'任课的学生的成绩。
select tno from teacher where tname='李辉'
select cno from course where tno=(select tno from teacher where tname='李辉')
select degree , sno  from score where cno=(select cno from course where tno=(select tno from teacher where tname='李辉')
) ;

-- 22、查询选修课程的人数多于2人的教师姓名。
select cno from score group by cno having count(sno)>2;
select cno from score group by cno having count(cno)>2;
select cno from score group by cno having count(*)>2;-- 以上三者等价。
select tno from course where cno=(select cno from score group by cno having count(*)>2);
select tname from teacher where tno=(select tno from course where cno=(select cno from score group by cno having count(*)>2));
-- 以上会有bug， count(*)>2，一旦有两以上个就不行，应该用in
select tname from teacher where tno in (select tno from course where cno in (select cno from score group by cno having count(sno)>2));

-- 23、查询184班和185班全体学生的记录。
select * from student where class in ('184','185');

-- 24、查询有120分以上成绩的cno。
select cno ,degree from score where degree>='120';

-- 25、查询数学系教师所教课程的成绩。
select * from teacher where depart='数学系';
select * from course where tno in (select * from teacher where depart='数学系')
select degree from score where cno in (select cno from course where tno in (select tno from teacher where depart='数学系'));

-- 26、查询'数学系'与'英语系'不同职称的教师的tname和prof。
select * from teacher;
select prof from teacher where depart='数学系';
select * from teacher where depart='数学系' and prof not in (select prof from teacher where depart='英语系')
union -- 求并集。
select * from teacher where depart='英语系' and prof not in (select prof from teacher where depart='数学系');

-- 27、查询选修1号课程，且成绩至少高于选修编号为2号课程的同学的cno、sno、和degree，并按degree降序排列。
select * from score where cno='1';
select * from score where cno='2';-- 大于2课程的最小分数就行
select * from score where cno='1' and degree > any(select degree from score where cno='2') order by degree desc;

-- 28、查询课程号为1且成绩高于3课程的cno、sno、degree。
-- 大于2课程所有的分数。
select * from score where cno='1' and degree > all(select degree from score where cno='3') order by degree desc;

-- 29、 查询所有的教师和同学的name、sex和birthday。
select tname,tsex,tbirthday from teacher
union
select sname,ssex,sbirthday from student;

-- 30、查询所有女教师女同学的name、sex和birthday。
select tname as name,tsex as sex,tbirthday as birhday from teacher where tsex='女'
union
select sname,ssex,sbirthday from student where ssex='女';

-- 31、查询成绩比该课程平均成绩低的同学的成绩。注意！！！
select cno ,avg(degree) from score group by cno;
select * from score a  where a.degree >(select avg(b.degree) from score b group by cno);
-- 以上的代码会报错，以cno分组会出现多个avg（degree），会导致第一个where 无法选中目标。

-- 容易理解的版本：
select Score.courseNo,Score.studentNo 
from (select avg(Score.score) as avgScore,courseNo
	  from Score
      group by courseNo
      ) a
left join Score on a.courseNo = Score.courseNo
where score < avgScore
group by Score.courseNo,Score.studentNo

select * from score a where degree > (select avg(degree) from score b where b.cno=a.cno );
-- 以上代码解读： 
-- 先明确优先级是 from > where > group by > order by > limit > select > having ; 程序读完已经有了a、b表只是还没用到。select b.cno, a.cno from score a,score b; 实际效果如下：
-- b.cno    a.cno
-- 数学			 数学
-- 数学			 数学
-- 数学			 数学
-- 语文      语文
-- 语文      语文
-- 英语      英语       (cno已经排好序了。)
-- 这是 相关子查询（由于子查询中用到了主查询的东西）。
-- 要遍历完 表中所有元素，由于子查询中用到了主查询的东西，并不是先彻底完成子查询再去彻底完成主查询，而是先进行子查询，用掉调用的主查询的from的一部分值，回到主查询，再接着主查询from后面的继续，子查询，用掉调用的主查询的from的一部分值，回到主查询... 知道主查询的from 遍历完毕。
-- 子查询中（ 首先明确：子查询中用到的主查询的任何值都是固定的！相当于固定了主查询中的一些对应的行。 固定a.cno字段其中一种元素,找到b表中与选中的a.cno元素等值的b.cno元素（事实上形成了一个分组），去算b.cno满足条件元素组成的分组 对应的平均值，）
-- 主查询中（ 再去拿a表cno之前固定的那些行 去与b表此cno平均值比较。）
-- 此处不要group by 是因为，" Subquery returns more than 1 row"子查询是不允许返回多个列的。

-- 32、查询所有任课教师的tname和depart
select tname,depart from teacher where tno in (select tno from course);
-- 课程表中要有课程

-- 33、查询至少有2名男生的班号。
select class,count(*) as '男生数量' from student where ssex='男' group by class having count(*)>=2;

-- 34、查询student表中不姓王的同学记录。
select * from student;
select * from student where sname not like '王%';

-- 35、 查询student 表中每个学生的姓名和年龄。
select year(now());
select year(sbirthday) from student;
select year(now())-year(sbirthday),sname from student;

-- 36、 查询student 表中最大和最小的sbirthday的日期值。
select sbirthday from student order by sbirthday;
select max(sbirthday) as '最小', min(sbirthday) as '最大' from student;

-- 37、以班号和年龄从大到小的顺序查询student表中的全部记录。
select * from student order by class desc,sbirthday;
-- 优先把班级按降序排列，然后在每个班级内按生日升序排列，年龄小生日数值大。

-- 38、查询男教师及其所上的课程。
select * from teacher where tsex='男';
select cname from course where tno in (select tno from teacher where tsex='男');
select cname,tname,tsex from course,teacher where course.tno=teacher.tno and tsex='男';

-- 39、查询最高分同学的sno、cno、和degree列。
select sno,cno,degree from score where degree = (select max(degree) from score);
# 按学科分
select sno,cno,degree from score a where degree=(select max(degree) from score b where b.cno=a.cno);

-- 40、查询和陈宇韶同性别的所有同学的sname。
select ssex from student where sname='陈宇韶';
select sname,ssex from student where ssex = (select ssex from student where sname='陈宇韶');

-- 41、查询和陈宇韶同性别并且同班的同学sname。
select sname,ssex from student where ssex = (select ssex from student where sname='陈宇韶')
and class = (select class from  student where sname='陈宇韶');

-- 42、查询所有选修数学的男同学的成绩。
select cno from course where cname = '数学';
select degree ,sname from  score,student where ssex='男'
and cno=(select cno from course where cname = '数学');
select student.sno,ssex,degree,cname from score,student,course where score.sno=student.sno and score.cno=course.cno and ssex='男'and cname='数学';

-- 43、假设使用如下命令建立一个grade表。grade意思是等级。
create table grade(
low int(3),
upp int(3),
grade char(1)
);
insert into grade values (90,100,'A');
insert into grade values (80,89,'B');
insert into grade values (70,79,'C');
insert into grade values (60,69,'D');
insert into grade values (0,59,'E');
-- 查询所有同学的sno、cno和grade列。
select  sno ,cno,degree,grade from score,grade where (degree between low and upp) ;
# 这里相当于用另一种方法进行了多表的粘合。



# 查看分组的内部信息。group_concat(字段)
select sell_date,count(distinct(product))as num_sold,group_concat(distinct(product))as products from Activities group by sell_date order by sell_date;
输入：
Activities 表：
+------------+-------------+
| sell_date  | product     |
+------------+-------------+
| 2020-05-30 | Headphone   |
| 2020-06-01 | Pencil      |
| 2020-06-02 | Mask        |
| 2020-05-30 | Basketball  |
| 2020-06-01 | Bible       |
| 2020-06-02 | Mask        |
| 2020-05-30 | T-Shirt     |
+------------+-------------+
输出：
+------------+----------+------------------------------+
| sell_date  | num_sold | products                     |
+------------+----------+------------------------------+
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |
+------------+----------+------------------------------+

--  ～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～
-- ～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～



SQL 的四种连接查询

内连接
inner join 或者 join


外连接
1.左连接 left join 或者 left outer join

2.右连接 right join 或者 right outer join

3.完全外连接 full join 或者 full outer join

-- 创建testjoin数据库
create database testJoin;

-- 创建表
-- person 表
-- id,
-- name,
-- cardId
create table person(
id int,
name varchar(20),
cardId int
);

-- card 表
-- id,
-- name
create table card(
id int,
name varchar(20)
);

insert into card values(1,'工行卡');
insert into card values(2,'建行卡');
insert into card values(3,'农行卡');
insert into card values(4,'中行卡');
insert into card values(5,'交行卡');
insert into card values(6,'饭卡');

insert into person values(1,'陈宇韶',1);
insert into person values(2,'周建宇',3);
insert into person values(3,'罗志康',6);

-- 并没有创建外键，
-- (内连接)inner join 查询
select * from person inner join card on person.cardId=card.id;
+------+-----------+--------+------+-----------+
| id   | name      | cardId | id   | name      |
+------+-----------+--------+------+-----------+
|    1 | 陈宇韶    |      1 |    1 | 工行卡    |
|    2 | 周建宇    |      3 |    3 | 农行卡    |
+------+-----------+--------+------+-----------+
-- 内连接查询，其实就是数据表中的数据，通过某个字段相对，查询相关数据(就是查询都有的部分)。
select * from person join card on person.cardId=card.id; -- 和上面式子等价。



-- (左外连接)left join
select * from person left join card on person.cardId=card.id;
+------+-----------+--------+------+-----------+
| id   | name      | cardId | id   | name      |
+------+-----------+--------+------+-----------+
|    1 | 陈宇韶    |      1 |    1 | 工行卡    |
|    2 | 周建宇    |      3 |    3 | 农行卡    |
|    3 | 罗志康    |      7 | NULL | NULL      |
+------+-----------+--------+------+-----------+
-- 左外连接会把左边表里面的所有数据都取出来，而右边表中的数据，如果有相等的，就显示出来
-- 如果没有就补null。



-- (右外连接)right join
select * from person right join card on person.cardId=card.id;
+------+-----------+--------+------+-----------+
| id   | name      | cardId | id   | name      |
+------+-----------+--------+------+-----------+
|    1 | 陈宇韶    |      1 |    1 | 工行卡    |
|    2 | 周建宇    |      3 |    3 | 农行卡    |
| NULL | NULL      |   NULL |    2 | 建行卡    |
| NULL | NULL      |   NULL |    4 | 中行卡    |
| NULL | NULL      |   NULL |    5 | 交行卡    |
| NULL | NULL      |   NULL |    6 | 饭卡      |
+------+-----------+--------+------+-----------+
-- 右外连接会把右边表里面的所有数据都取出来，而左边表中的数据，如果有相等的，就显示出来
-- 如果没有就补null。

-- （全外连接）full join
select * from person full join card on person.cardId=card.id;
-- mysql 不支持 full join ，智能写成以下形式。
select * from person left join card on person.cardId=card.id
union
select * from person right join card on person.cardId=card.id;
+------+-----------+--------+------+-----------+
| id   | name      | cardId | id   | name      |
+------+-----------+--------+------+-----------+
|    1 | 陈宇韶    |      1 |    1 | 工行卡    |
|    2 | 周建宇    |      3 |    3 | 农行卡    |
|    3 | 罗志康    |      7 | NULL | NULL      |
| NULL | NULL      |   NULL |    2 | 建行卡    |
| NULL | NULL      |   NULL |    4 | 中行卡    |
| NULL | NULL      |   NULL |    5 | 交行卡    |
| NULL | NULL      |   NULL |    6 | 饭卡      |
+------+-----------+--------+------+-----------+

-- 连接的好处是我们不用创建外键，在利用某个字段相等的条件来求表之间的交集、并集相关关系。





-- ～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～
-- ～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～～·






-- mysql 事务
mysql 中，事务其实是一个最小的不可分割的工作单元。事务能保证一个业务的完整性。
比如 银行转账。a转给b 1000元钱。
a -> 1000
update user set money=money-1000 where name='a';

b
update user set money=money+1000 where name='a';

-- 若实际程序中，以上语句只有一条执行成功
-- 出现数据前后不一致。
-- 多条sql语句，可能会有同时成功的要求，要么就同时失败。

-- mysql中如何控制事务？
1、mysql是默认开启事务的。autocommit （自动提交）
 select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+
-- 默认事务开启的作用是什么？
-- 当我们去执行一个sql语句时，效果会立刻体现出来，不能回滚。

create database bank;
create table user(
    id  int primary key,
    name varchar(20),
    money int
);
insert into user values(1,'陈宇韶',1000);

-- 事务回滚：撤销sql语句执行效果（提交了就没法回滚了，回滚是在本地的操作！,一次回滚直接是把一整个事务全部撤销了！）。
rollback;
rollback;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
+----+-----------+-------+
1 row in set (0.00 sec)    -- 虽然显示rollback生效了，但是数据却还是有在里面。

-- 设置mysql 自动提交为false。
set autocommit=0;select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            0 |
+--------------+
1 row in set (0.00 sec)
-- 以上关闭了mysql的自动提交。
insert into user values(2,'陈文君',1);
 select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
|  2 | 陈文君    |     1 |
+----+-----------+-------+
2 rows in set (0.00 sec)

rollback ;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
+----+-----------+-------+
1 row in set (0.00 sec)
-- 可见被撤销了。
-- 但是 手动commit提交，就不可撤销（事务回滚）了。
insert into user values(2,'陈文君',1);
commit;
rollback ;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
|  2 | 陈文君    |     1 |
+----+-----------+-------+
2 rows in set (0.00 sec)

-- 自动提交 @@commit
-- 手动提交 commit
-- 事务回滚 rollback

-- 如果发现事件A 发生 而事件B不发生，可以事务回滚。确认没问题则 手动提交commit。

-- begin
-- 或者
-- start transaction
-- 都可以手动开启事务。

set autocommit=1;
update user set money=money-1000 where name='陈宇韶';
update user set money=money+1000 where name='陈文君';
rollback ; -- 发现没有被撤销。
-- 但是以下三条语句一起执行的话。是可以被事务回滚的（撤销）。
begin; -- 手动开启事务。 或者用start transaction
update user set money=money-1000 where name='陈宇韶';
update user set money=money+1000 where name='陈文君';

-- 事务开启之后，一旦commit,就不可回滚.因为commit 后，事务就结束了。

事务的四大特征：
A 原子性：事务是最小单位，不可再分。
C 一致性：事务要求，同一事务中的sql语句，必须保证同时成功或者同时失败。
I 隔离性：事务1 和事务2 之间是具有隔离性的。
D 持久性：事务一旦结束（commit，rollback），就不可以返回。

事务开启：
    1、修改默认不提交 set autocommit=0;
    2、begin;
    3、start transaction ;
事务手动提交（只对增删改有效，对查无效。）：
    commit;
事务回滚：
    rollback;

事务的隔离性（以下为隔离级别）：
1、read uncommitted;    -- 读未提交的
2、read committed;       -- 读已经提交的
3、repeatable read ;    -- 可以重复读
4、serializable;      -- 串行化

1、read uncommitted;

-- 脏读
如果有事务a，和事务b，
a 事务对数据进行操作，在操作的过程中，事务没有被提交，但是b 可以看见a 的操作结果。这样导致了脏读。

insert into user values(3,'邓建楚',1000);
insert into user values(4,'淘宝店',1000);

-- 如何查看数据库的隔离级别？
mysql 8.0:
-- 系统级别
select @@global.transaction_isolation;

-- 会话级别
select @@transaction_isolation

-- 修改隔离级别
set global transaction isolation level read uncommitted;
select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| READ-UNCOMMITTED               |
+--------------------------------+

-- 转账：邓建楚在淘宝买鞋子500元。
start transaction;
update user set money=money-500 where name='邓建楚';
update user set money=money+500 where name='淘宝店';

-- 给淘宝店打电话说你去查一下，是不是到账了。
 select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |     0 |
|  2 | 陈文君    |  1001 |
|  3 | 邓建楚    |   500 |
|  4 | 淘宝店    |  1500 |
+----+-----------+-------+

-- 发货
-- 晚上请女朋友吃好吃的
-- 1000元

-- 结账的时候发现钱不够
rollback; -- 撤销买鞋的事务。
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |     0 |
|  2 | 陈文君    |  1001 |
|  3 | 邓建楚    |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+

-- 淘宝店主默认开启事务
-- select * from user;
-- 发现没钱过来了，

-- 如果两个不同的地方，都在进行操作，如果事务a 开启之后，他的数据可以被其他事务读取到。
-- 这样就会出现（脏读）
-- 脏读：一个事务读到了另外一个事务没有提交的数据，就叫作脏读。
-- 实际开发是不允许脏读出现的。


2、read committed;  -- 读已经提交的

set global transaction isolation level read committed;
select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| READ-COMMITTED                 |
+--------------------------------+

bank 数据库 user 表
小陈：银行员工
start transaction;
select * from user;

小陈 去厕所
小王：在小王这终端打开user表 ，新建事务。
start transaction;
insert into user values(5,'罗志康',100);
commit;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
|  2 | 陈文君    |  1000 |
|  3 | 邓建楚    |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | 罗志康    |   100 |
+----+-----------+-------+

小陈上完厕所回来。算了以下user表的平均数。此时由于终端还停留着以下显示：
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
|  2 | 陈文君    |  1000 |
|  3 | 邓建楚    |  1000 |
|  4 | 淘宝店    |  1000 |
+----+-----------+-------+
select avg(money) from user; -- 小陈却发现平均值变化了
+------------+
| avg(money) |
+------------+
|   820.0000 |
+------------+

-- 虽然我只能读到你提交的数据，但是还是会出现问题。
-- 读取同一表的数据还是会前后不一致。
-- 这就是不可重复读现象：read committed

3、repeatable read     -- 可以重复读

set global transaction isolation level repeatable read;
 select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| READ-COMMITTED                 |
+--------------------------------+

-- 在REPEATABLE-READ 隔离级别下又会出现什么问题？

-- 王尼玛 -北京
begin;
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
|  2 | 陈文君    |  1000 |
|  3 | 邓建楚    |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | 罗志康    |   100 |
+----+-----------+-------+

-- 张全蛋 -成都
begin;
-- 张全蛋-成都
insert into user values(6,'暴走',1000);
select * from user;
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
|  2 | 陈文君    |  1000 |
|  3 | 邓建楚    |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | 罗志康    |   100 |
|  6 | 暴走      |  1000 |
+----+-----------+-------+

-- 王尼玛-北京  由于终端显示还停留在以下显示上：
+----+-----------+-------+
| id | name      | money |
+----+-----------+-------+
|  1 | 陈宇韶    |  1000 |
|  2 | 陈文君    |  1000 |
|  3 | 邓建楚    |  1000 |
|  4 | 淘宝店    |  1000 |
|  5 | 罗志康    |   100 |
+----+-----------+-------+
-- 王发现 插入下行显示已经存在，无法插入。
insert into user values(6,'暴走',1000);

-- 这种现象叫做幻读。
-- 事务a 和事务b 同时操作一张表，事务a提交的数据，也不能被事务b读到，就可以造成幻读。

4、serializable;   串行化
set global transaction isolation level serializable;
select @@global.transaction_isolation;

-- 张全蛋- 成都
start transaction;
insert into user values(7,'李铁柱',1000);
insert into user values(8,'王小花',1000);

-- 王尼玛- 北京
start transaction;
insert into user values(8,'王小花',1000); -- 一直运行，不会出结果

-- 张全蛋- 成都
commit; -- 张全蛋提交瞬即，王尼玛那边的插入操作出结果————报错提示重复插入。


-- 当user 表被另外一个事务操作的时候，其他事务里面的写操作是不可以进行的。
-- 进入排队状态（串行化），指导王尼玛那边的事务结束之后，张全蛋这个写入操作才会被执行。
-- 在没有等待超时的情况下。
-- 串行化的问题是，性能特别差。

read-uncommitted > read-committed > repeatable-read > serializable;
-- 隔离级别越高，性能越差。
-- mysql的默认隔离级别是repeatable-read。
```

# 以下为补充：

小提示：mysql清屏 ctrl + L ;

![截屏2022-02-16 16.41.48](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-16%2016.41.48.jpg)

## 脏读

​		如下图所示，有 emails 和 mailboxes 两张表，emails 表中 unread_flag 字段表示是否已读，mailboxes 表中 unread 字段则是未读的email数量。

当用户1执行 insert 操作和 update 操作中间，用户2进行了读取，这时会获得一个矛盾的结果，明明有1封未读的 email，但是 unread 计数器却是0。

![img](mysql%E7%AC%94%E8%AE%B0.assets/51EB2AC5-758D-42CD-947E-6CDE8D1A7654_1_105_c-20221029171307386.jpeg)

​		防止保证：读数据库的时候，只能看到已成功提交的数据。

## 更新丢失

​		如下图所示，用户1和用户2都分别尝试获取 counter，然后对 counter 进行加1并保存，理论结果应该是44，但是实际上结果为43。

![img](mysql%E7%AC%94%E8%AE%B0.assets/077E886C-0AE1-4A76-9B4F-3C7F69A5EB72_1_105_c-20221029171307492.jpeg)

​		防止保证：通过原子化写操作、加锁等方式。

## 脏写

​		Alice 和 Bob 同时购买一辆车，买车同时也会更新发票，下图的场景中会出现， Bob 买到了车，但是 Alice 收到了发票的情况。

​		注意，这种情况和 **丢失更新** 不一样，丢失更新中第二次写入是在第一个事务提交后才执行的。

![img](mysql%E7%AC%94%E8%AE%B0.assets/F51A6AD6-9621-44BB-909F-23752F7CC9CD_1_105_c-20221029171307600.jpeg)

​		防止保证：写数据库的时候，只能覆盖已经成功提交的数据。

## 不可重复读（读倾斜）

​		不可重复读是指一个事务先后执行同一条 SQL，，虽然只读提交的，但两次读取到的数据还是不同，就是不可重复读。

​		"读取到了其他事物提交的数据，针对 update 、delete。"

​		Alice有两个子账户，分别有500，一共有1000，转账者从账户2转了100到账户1，转账有两个SQL操作，两个操作在同一个事务中。如果 Alice 在同一个事务中读取了多次账户1，最初读取到了账户1是500，而后面会读取到600，两次读取数据不一致。

![img](mysql%E7%AC%94%E8%AE%B0.assets/2070BC05-6770-4BE7-BAA3-5C88761D03C3_1_105_c-20221029171307786.jpeg)

​		防止保证：读数据库的时候，只能看到已成功提交的数据。

## 幻读（写倾斜）

​		“读取了其他并发事务提交的数据，针对 insert。”

​		下图中的表是一个 oncall 排班系统，如果有两个医生 oncall 的情况下，允许一个医生退出 oncall。Alice 和 Bob 分别读取了 oncall 的人数，判断当前 oncall 的人数多于两人，然后分别退出了 oncall，导致这个时间段无人 oncall。

![img](mysql%E7%AC%94%E8%AE%B0.assets/76024979-5358-4E84-AE98-6CEB66AB4ECE_1_105_c-20221029171307875.jpeg)

​		事务再去读取表时,发现表中还有没有修改的数据行，就好象发生了幻觉一样。

​		防止保证：如果只有在事务完全提交后才能读取数据，则可以避免该问题。

## 事务隔离如何通过锁来实现？

读写锁、范围锁。

### serializable 

​		有两种方式：

- 使用基于锁的并发控制实现，可串行性要求在事务结束时释放读写锁（在选定数据上获取）。此外，当SELECT查询使用带范围的WHERE子句时，必须获取范围锁，尤其是要避免幻像读取现象。
- 当使用基于非锁的并发控制时，不会获取任何锁。但是，如果系统在多个并发事务中检测到写冲突，则仅允许其中之一提交。这种方式称之为快照隔离。

### repeatable-read

​		基于锁的并发控制实现将保持读写锁（在选定数据上获取），直到事务结束为止。但是，范围锁不受管理，因此可能发生幻像读取。

### read-commited

​		基于锁的并发控制实现将写锁（在选定数据上获取）保持到事务结束，但是一旦执行SELECT操作，就会释放读锁（因此出现不可重复的读现象）。与重复读取一样，不管理范围锁。

### read-uncommited

​		这是最低的隔离级别。在此级别，允许进行脏读取，因此一个事务可能会看到其他事务尚未提交的更改。



# mysql 提高

![image-20220217164752601](mysql%E7%AC%94%E8%AE%B0.assets/image-20220217164752601.png)

```sql
show variable like '%char';

show engines;

-- 指定引擎为 myisam（性能优先）
create table tb(
id int(4) auto_increment,
name varchar(20),
primary key(id)
)
engine=MyISAM AUTO_INCREMENT=1
DEFAULT CHARSET=utf8;

```

## 索引

​		sql的优化主要就是优化索引。

​		索引就是一个树的数据结构，index来帮助提高效率。可以回顾一下算法与数据结构。

​		可以加索引搜索，也可以不加索引搜索。索引最好建立在 空间占用小的字段上。 

### 分类：

​		聚集索引（数据完整存放在叶结点）、非聚集索引；

​		主键索引、辅助索引（二级索引）；

![截屏2022-10-13 12.08.27](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-13%2012.08.27.jpg)

​	

![截屏2022-10-13 12.09.46](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-13%2012.09.46.jpg)

​		联合索引（覆盖索引）

![截屏2022-10-13 12.34.01](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-13%2012.34.01.jpg)



### 索引下推(icp)：

​		ICP（index condition pushdown）5.6版本加入。explain 的extra 可查。

​		![截屏2022-10-13 12.54.53](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-13%2012.54.53.jpg)

​		原本 在二级索引中，不会 有取交并这样的过滤行为，只会都查出来 主键index ，然后一股脑都去回表查询，icp索引下推之后，在二级索引中就会直接进行过滤，过滤出的 主键索引index ，一次回表查询即可。

​		因此，多建立联合索引，可以提高效率！

​		ICP，只存在于二级索引中；

​		一旦 引用了子查询的条件，就不能索引下推了。



### 索引失效：

![截屏2022-10-14 11.52.30](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2011.52.30.jpg)

![截屏2022-10-14 11.54.40](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2011.54.40.jpg)



![截屏2022-10-14 12.25.38](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2012.25.38.jpg)

![截屏2022-10-14 12.34.30](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2012.34.30.jpg)

![截屏2022-10-14 12.54.57](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2012.54.57.jpg)

​		mysql 默认一页 是 16kb（也就是b+ 树的一个结点 16kb）。以页为单位（`Data_length` ）从内存读取。

​		上面的 io 是写进 内存的消耗 ，和`Data_length` 有关，默认是 1.0，cpu 是cpu读取内存并计算的消耗，和记录数量 `Rows`有关，默认0.2； 

​		1、如果是走 聚集索引的 全表查询：

​		通过 `show table status like '表名'` 可以 查看到 `Data_length`， 如果是20512768，则 为20512768/1024= X kb，X kb/16 = 1252 页。

​		通过 `show table status like '表名'` 可以 查看到 `Rows`，如果是442486，则 直接为 4482486 条数据。

​		2、如果是走二级索引，包括可能回表查询的情况：

​		通过b+树的 查询到叶结点的 时间复杂度是 常数级别，查询两头可以定位出区间，我们这里直接就用 1 来代表定位区间 的内存消耗；

​		通过 二级索引中定位区间 ，io 消耗 为 1；

​		通过二级索引 树的回溯 可以查出 一共有多少个 记录。每一个记录都要回一次表。  假如 走二级索引 查出 范围内有100个叶子结点，要把 100个记录 的页 加载进内存 中，然后去回表，io消耗 是 100 * 1；

​		通过 cpu 从 二级索引中查询出的 区间 把 id 读取出来，为cpu消耗，100条数据就是 100 * 0.2；

​		通过 cpu 回表查询还要读一次。消耗为 100 * 0.2；



​		可见，如果 通过索引查询，查询出的记录 太大，有10000条， 索引查询开销 > 全表查询，mysql 的优化器 就会自动选择 全表查询！！！



### 索引优化 ：

![截屏2022-10-14 12.01.15](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2012.01.15.jpg)

​		解释：

​		bool 字段 会变成二叉树，分叉太少，深度会很深。

​		char(1) 就比 archer(20) 小多了，更加适合作index。

​		![截屏2022-10-14 12.10.51](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2012.10.51.jpg)

​		![截屏2022-10-14 12.17.47](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-14%2012.17.47.jpg)

​	

​	

### 索引缺点：

​		1、树结构本身可能很大；

​		2、索引适用场景少，针对少量数据、频繁更新、很少使用的字段，都不适用索引；

​		3、索引会降低增、删、改的效率，可以回顾算法与数据结构里树改结构的时间复杂度。

### 索引优点：

​		1、提高查询效率；

​	    2、降低CPU使用率，如order by 排序，搜索树本身就是排好序的。

### B树

​		数据都放在叶子结点。

![截屏2022-02-18 16.10.15](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2016.10.15.jpg)

### 索引创建、显示与删除

![截屏2022-02-18 16.17.46](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2016.17.46.jpg)

![截屏2022-02-18 16.21.58](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2016.21.58.jpg)

```sql
-- 删除索引
drop index name_index on user;
drop index id_index on user;
-- 显示索引（主键会被识别为索引）
show index from user;
```



## sql性能问题

​		1、分析sql执行计划：explain 来模拟sql优化器执行sql语句。

​		2、优化器会干扰我们的优化。

​		在mysql官网对应版本的手动参考 reference manual 中找到 优化optimization。![截屏2022-02-18 17.04.16](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2017.04.16.jpg)

![截屏2022-02-18 17.06.43](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2017.06.43.jpg)

### （id）		

​		explain结果里 id 相同，从上往下顺序执行。

![截屏2022-02-18 17.19.24](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2017.19.24.jpg)

​		数据小的优先查询（因为笛卡尔积，中间的乘积尽可能小）。

​		explain结果里id不同的话，id越大越优先。

![截屏2022-02-18 17.36.10](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2017.36.10.jpg)

​		其本质就是 嵌套子查询时，先查最内层，再查最外层，类似于括号嵌套的数学运算。

​		explain结果里id有相同也有不同，id大的优先，然后在id值相同的从上往下顺序执行。

### （select_type）

![截屏2022-02-18 18.00.33](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2018.00.33.jpg)

以上截图有没截图到的地方：

```sql
explain select cr.name from (select * from course where tid in (1,2)) cr;
explain select cr.name from (select * from course where tid=1 union select * from course where tid=2) cr;
```

![截屏2022-02-18 18.10.49](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2018.10.49.jpg)

### （type）索引类型、类型

​		![截屏2022-02-18 18.24.19](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-18%2018.24.19.jpg)

const : 仅仅能够查到一条数据，用于primary key 或者 unique索引。

```sql
create table test01(
    tid int(3),
    tname varchar(20)
);
insert into test01 value(1,'a');
commit;
alter table test01 add constraint tid_pk primary key(tid);

explain select * from (select * from test01) t where t.tid=1;
explain select tid from test01 where tid=1;
# 都会显示 type 是 const
```

eq_ref: 唯一性索引，对于每一个索引键的查询，返回匹配唯一行数据。

```sql
-- 对于每一个索引键的查询，返回匹配唯一行数据。
alter table teacherCard add primary key(tcid);
alter talbe teacher add unique index (tcid);

explain select t.tcid from teacher t,teacherCard tc where t.tcid=tc.tcid;
# 在teacher和teacherCard 数量一致且不为零的时候，才有可能是eq_ref。（例如 teacher和teacherCard两个表的 根据tcid索引字段匹配后，对应的元素个数都是3，且都没有重复的，实际是很难保证这样。如：甲的索引id是1，2，乙的索引id是1，2，正好可以匹配上，且不重复。）

```

ref: 非唯一性索引，对于每个“索引键”的查询，返回匹配的所有行 （0或者多个）。 

```sql
# 接上上面代码框
alter table test01 drop primary key ;
create index test01_index on test01(tid);
# alter table test01 add index tid_index(tid);
explain select tid from test01 where tid=1;
# 会显示是ref索引类型。
insert into test01 value(1,'b');
explain select tid from test01 where tid=1;
# 还是会显示是ref索引类型。
# 如：甲的索引id是1，2，乙的索引id是1，1，乙的索引有重复。）
```

range：检索指定范围的行，where后面是一个范围查询（between、<、>、in有时候会失效转为无索引情况。）

```sql
explain select * from teacher t where t.tid in (1,2);
explain select * from teacher t where t.tid<3;
```

index:把所有索引都查一遍。

```sql
explain select cid from course ; 
-- cid 是索引，只需要扫描索引表。
```

all:把所有字段都查一遍。

```sql
explain select cid from course;
-- cid 不是索引，需要扫描全部表。
```

### （possible_keys）可能用到的索引

### （key）实际用到的索引

### （key_len）索引的长度，用于判断复合索引是否被完全使用。

![截屏2022-02-19 22.12.35](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.12.35.jpg)

​		复合索引一级一查，就是说复合索引的后面一个索引被查的时候也会用到前面一个索引，计算后面一个索引长度时，要加两个索引的长度来算。如下：

![截屏2022-02-19 22.17.41](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.17.41-7034786.jpg)

![截屏2022-02-19 22.21.55](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.21.55.jpg)

int（4） 这样的 就不用 4*3=12 了，直接用 4 就行 。不用考虑  一个字符 3字节，这是对char 和varchar 来说的。



### （ref）表明当前表所参照的字段。

![截屏2022-02-19 22.29.30](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.29.30.jpg)

![截屏2022-02-19 22.30.14](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.30.14.jpg)

### （rows）实际通过索引扫描的 数据个数。

![截屏2022-02-19 22.33.47](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.33.47.jpg)

![截屏2022-02-19 22.34.22](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.34.22.jpg)

c 是2个 但是 t 是1。

![截屏2022-02-19 22.35.03](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.35.03.jpg)

​		rows用来表示在SQL执行过程中会被扫描的行数。

​		举个例子来说，假如有一个语句 select * from t where column_a = 1 and column_b = 2;
全表假设有100条记录，column_a字段有索引（非联合索引），column_b没有索引。
column_a = 1 的记录有20条， column_a = 1 and column_b = 2 的记录有5条。

那么最终查询结果应该显示5条记录。 explain结果中的rows应该是20. 因为这20条记录mysql引擎必须逐行检查是否满足where条件。

### （Extra）

#### 1、using filesort：

​		 性能消耗大，需要“额外”一次排序（或者 查找），常见于order by 出现时。

![截屏2022-02-19 22.45.15](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.45.15.jpg)

单索引避免file sort：where哪些字段，就order by 哪些字段。

![截屏2022-02-19 22.51.31](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-19%2022.51.31.jpg)

​		复合索引避免file sort：where和order by 按照复合索引顺序使用，不要跨列或者无序使用。

​		**注意：1、where 后的 索引=常量 才会算，索引=范围 是不被记入 最佳左前缀的；2、WHERE子句中必须包含索引中最左的字段！但不一定是常量，因为最左前缀可以只出现在order by子句中，where只是起到一个选择索引的作用。**

​		可以参考 ：`https://blog.csdn.net/Bronze5/article/details/113817719?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-113817719-blog-125004353.pc_relevant_3mothn_strategy_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-113817719-blog-125004353.pc_relevant_3mothn_strategy_recovery&utm_relevant_index=2`



```sql
# 举例说明。
# 联合索引 （1，2，3，4） 以下是 符合最佳左前缀 不会触发 using filesort 文件重排序的 组合：

# 自己到自己不会触发；
where 1='' order by 1
where 2='' order by 2
where 3='' order by 3
where 4='' order by 4
# 最佳左前缀不会触发；
where 1='' order by 2
where 2='' order by 1
where 3='' order by 1
where 4='' order by 1

	↑ →→ ↓
  1  	 2	 	3		 4
  ↑ ←← ↓    ↓    ↓
  ↑         ↓    ↓
  ↑ ←←←←←←  ↓    ↓
  ↑              ↓
  ↑  ←←←←←←←←←←  ↓
 
# 保证最左的 往后走一个，其他的都汇聚到最左的一个。
(3,4,2)1 
(4,2)1
# 这些也会优化好。
(1,4)4
(1,3)3
(1,2)2
(1,3)1
# 就连 （1，3）2 也会被优化.
# a1 不需要回表查询，a3出现了跨列，导致了a3索引失效，需要回表查询
# 因为 把where 和 order by涉及的索引拼接，（a3索引因为跨列已经失效了）剩下的顺序是a1、a2，是索引顺序，所以不需要额外一次排序filesort。
(1,3)2
(1,4)2
(2,4)1
(2,3)1
(2,3)2   # 我还 不明白下面这些为什么会被优化 的不需要 filesort。
(2,3)3
(4,3)3
(3,4)3
(3,4)4
... 
都是会被 优化为 不触发 filesort 。


```



#### 2、using temporary：

​		性能损耗大，用到了临时表。常见于 group by 出现时。

![截屏2022-02-20 13.16.12](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2013.16.12.jpg)

​		需要额外再多用一张表，已经有了，但是不适用，必须再来一张表，就会using temporary。

![截屏2022-02-20 13.24.22](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2013.24.22.jpg)

#### 3、using index

​		性能提升。索引覆盖。（不读区原文件，只从索引中获取数据，不需要回表查询。）只要使用到的列 全部都在索引中，就是索引覆盖using index。

![截屏2022-02-20 13.27.58](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2013.27.58.jpg)

​		![截屏2022-02-20 13.30.27](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2013.30.27.jpg)

​		![截屏2022-02-20 13.34.32](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2013.34.32.jpg)

#### 4、using where

​		需要回表查询

​		![截屏2022-02-20 13.36.59](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2013.36.59.jpg)

#### 5、impossible where

​		不可能出现的情况。

![截屏2022-02-20 13.38.19](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2013.38.19.jpg)



### 优化事例	

​		SQL的优化是一种概率层面的优化，至于是否实际使用了我们的优化，需要通过explain 配合结果的possible key、key 、extra等来推测。

#### （一）复合索引

```sql
create table test03(
    a1 int(4) not null,
    a2 int(4) not null,
    a3 int(4) not null,
    a4 int(4) not null
);
alter table test03 add index idx_a1_a2_a3_a4(a1,a2,a3,a4);
```

```sql
# 推荐写法，索引的使用顺序和索引的顺序一致。
explain select a1,a2,a3,a4 from test03 where a1=1 and a2=2 and a3=3 and a4=4;
# 但是改一下顺序，由于mysql服务层的优化器自动优化，也会时一样的结果。
explain select a1,a2,a3,a4 from test03 where a4=1 and a3=2 and a2=3 and a1=4;
```

​		![截屏2022-02-20 14.00.51](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2014.00.51.jpg)

```sql
explain select a1,a2,a3,a4 from test03 where a1=1 and a2=2 and a4=4 order by a3;
```

![截屏2022-02-20 14.01.56](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2014.01.56.jpg)

​		可见有一部分需要where回表查询了。a1、a2 不需要回表查询，a4出现了跨列，导致了a4索引失效，需要回表查询，因此using where ，可以通过key_len 验证。

```sql
explain select a1,a2,a3,a4 from test03 where a1=1 and a4=4 order by a3;
```

![截屏2022-02-20 14.08.44](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2014.08.44.jpg)

​		出现file sort，多了一次额外排序/查询。因为 把where 和 order by涉及的索引拼接，（a4索引因为跨列已经失效了）剩下的顺序是a1、a3，一样不是索引顺序，所以需要额外一次排序filesort。

​		总结：

​		复合索引（a、b、c）从左往右，一个失效会引起后面都失效。

​		复合索引如果在全部使用时顺序与定义时一致，且不跨列使用，则复合索引全部使用；如果部分一致，且不夸列使用，则使用部分索引。

​		原则：

​		a.不要夸列或者无序使用；

​		b. 尽量使用全索引匹配；

​		

#### （二）单表优化

```sql
create table book(
    bid int(4) primary key,
    name varchar(20) not null, 
    authorid int(4) not null,
    publicid int(4) not null,
    typeid int(4) not null
);
insert into book value(1,'tjava',1,1,2);
insert into book value(2,'tc',2,1,2);
insert into book value(3,'wx',3,2,1);
insert into book value(4,'math',4,2,3);
commit;
```

```sql
select bid from book where typeid in (2,3) and authorid=1;
# where 后的条件，实际会锁定 4行，只是之后取了交集，所以 rows=4
```

![截屏2022-02-20 20.16.59](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2020.16.59.jpg)

查询优化，加上索引。

```sql
alter table book add index idx_bta(bid,typeid,authorid);

explain select bid from book where typeid in (2,3) and authorid=1;
# 用到了 2号、3号索引，而索引像 一二三级 目录一样，所以一定会使用到 1号 索引，所以 key_len=4*3=12 。
# 而 typeid 用了in 而不是 =‘常数’，我们就忽略它。
 explain select bid from book where typeid in (2,3) and authorid=1 order by typeid desc;
 # 而 typeid 用了in 而不是 =‘常数’，我们就忽略它。
 # 3号位到和2号位 ，不符合 最佳左前缀，触发 文件再排序。
 # 这里如果是 typeid=1 而不是 用in， 就不会出现 using filesort。
```

![截屏2022-02-20 20.17.17](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2020.17.17.jpg)

​		![截屏2022-02-20 20.20.03](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2020.20.03.jpg)

根据查询的实际顺序，定义一个新顺序的复合索引。（原索引记的删除，以免干扰）。

```sql
# 虽然bid不在where和order by后，可以回表查询bid，但是bid放入索引中可以提升索引使用率，所以我们在复合索引中依然保留bid。
alter table book add index idx_tab(typeid,authorid,bid);
# 原索引记的删除，以免干扰）。
drop index idx_bta on book;

explain select bid from book where typeid in (2,3) and authorid=1 ;
# 只用到了 联合索引 目录的前两级 ，所以 key_len=2*4=8。而 联合索引中1号索引 typeid 限定了扫描范围只有两个，所以 rows=2。

# Backward index scan 是 MySQL-8.0.x 针对性的一个专用优化项，它可以从索引的后面往前面读，性能上比加索引提示要好的多。
explain select bid from book where typeid in (2,3) and authorid=1 order by typeid desc;
# 因为 desc 降序，自动 用上了Backward index scan。
```

![截屏2022-02-20 20.28.57](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2020.28.57.jpg)

![截屏2022-02-20 20.39.47](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2020.39.47.jpg)

由于in 可以会失效使得索引类型直接变为all，而复合索引中typeid是第一个索引一旦它失效了后面跟着失效，所以我们改变where后查询顺序，并且相应改变复合索引顺序，。

```sql
drop index idx_tab on book;
alter table book add index idx_atb(authorid,typeid,bid);

explain select bid from book where authorid=1 and typeid in (2,3) order by typeid desc;

# 试一试去掉in。达到了效果最好。
explain select bid from book where authorid=1 and typeid=3 order by typeid desc;
```

![截屏2022-02-20 21.23.14](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2021.23.14.jpg)

![截屏2022-02-20 21.32.18](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2021.32.18.jpg)

总结：

​		a.尽量保持 索引的定义和使用的顺序一致性。

​		b.索引逐步优化。

​		c.将含有in的范围查询 放到where条件的最后。 

#### （三）两表优化	

```sql
create table teacher2(
    tid int(4) primary key,
    cid int(4) not null
);
insert into teacher2 values(1,2);
insert into teacher2 values(2,1);
insert into teacher2 values(3,3);
create table course2(
    cid int(4) ,
    cname varchar(20)
);
insert into course2 values(1,'java');
insert into course2 values(2,'python');
insert into course2 values(3,'kotlin');
commit;
```

左连接查询

```sql
explain select * from teacher2 t left join course2 c on t.cid=c.cid where c.cname='java'; 
```

![截屏2022-02-20 22.11.02](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2022.11.02.jpg)

索引往哪张表上加？原则：

​		a.数据量小的表驱动数据量大的表；（程序设计中，小的循环套大的循环效率>大的循环套小的循环效率）

​		b.索引建立在经常使用的字段上；（左链接是以满足左表为根本，左表必然使用频繁，索引加在左表对应的链接字段上。同理右链接，右表使用频繁，索引加在右表对应的链接字段上。）本题 t.cid=c.cid , t.cid 被设定为索引。	

```sql
alter table teacher2 add index index_teacher2_cid(cid);

explain select * from teacher2 t left join course2 c on t.cid=c.cid where c.cname='java'; 
```

![截屏2022-02-20 22.11.32](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2022.11.32.jpg)

```sql
# 给剩下的查找字段加上索引。
alter table course2 add index index_course2_cname(cname);

explain select * from teacher2 t left join course2 c on t.cid=c.cid where c.cname='java'; 
```

![截屏2022-02-20 22.12.58](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-20%2022.12.58.jpg)

补充：

using join buffer : extra 中一个选项，mysql引擎 使用了链接缓存来优化。

#### （四）三表查询

原则：

​		a.小表驱动大表；

​		b.索引建立在经常查询的字段上；

#### （避免索引失效）

​		1、复合索引情况：

​				复合索引（a、b、c）从左往右，一个失效可能会引起后面都失效。而且，如果索引使用了 !=  、<> 、 is null、 is not null 也“可能”会使得索引失效。

​				a.不要夸列或者无序使用；

​				b. 尽量使用全索引匹配；

​		2、不要在索引上进行任何操作（计算、函数、类型转换），否则索引失效。  如：select .. where A.x*3= .. 。

​		3、尽量索引覆盖，让索引要用到的字段。

​		4、like尽量以常量开头，不要以%开头。 select * from xx where name like '%x';  name如果是索引，那name就可能失效了。挽救措施是：尽量索引覆盖，查询的字段不要出现回表查询，效果会好一些，比如 select tname from teacher where name like '%x'; 

​		5、尽量不要用类型转换（显示、隐式），否则索引可能失效。

​		6、尽量不要用or，or的出现可能连左边的索引也一起弄失效了。

#### （其它优化方法）

##### 		1、exists和 in 

```sql
select .. from table where exists 子查询;
# exists 是把主查询的结果放到子查询中校验，相当于先遍历了主循环。由于小包大的循环嵌套性能更好，所以主查询大就用exists。

select .. from table where 字段 in 子查询;
```

​		如果主查询的数据集大，则使用In；

​		如果子查询的数据集大，则使用exist；

```sql
 1 SELECT
 2     `user`.*
 3 FROM
 4     `user`
 5 WHERE
 6     EXISTS (
 7         SELECT
 8             `order`.user_id
 9         FROM
10             `order`
11         WHERE
12             `user`.id = `order`.user_id
13     )
```



##### 		2、order by 优化

​		using filesort 有两种算法：双路排序、单路排序（根据IO的次数）。 

​		双路排序：

​				1、磁盘先读取排序字段，对排序字段排序（在buffer中进行排序）；2、扫描其它字段。

​		单路排序：

​				一次性扫描所有字段，在buffer中排序，但是单路排序不一定真的是“单路”。 

​		双路排序io用的多，消耗资源，就有了单路排序。但是单路排序不一定真的是“单路”，因为数据量太大了，无法一次性读取全部数据，就会“分片读取”也就会多次io读取了，同时单路排序一次性读完字段会使得buffer更大。 可以如下调整buffer大小：

```sql
set max_length_for_sort_data=1024;
# 1024单位是byte，可以自己调。
# max_length_for_sort_data值太低，mysql会自动从 单路变双路。
```

order by 查询优化：

​		a、选择单双路；调整buffer大小；

​		b、避免select * ；* 难以被索引覆盖；

​		c、避免复合索引夸列使用；

​		d、保证都是升序或者降序；order by a asc，c desc； 这样就不可取；		

​		

## 慢日志查询

​		默认10秒都没有查出来就属于慢查询了。慢查询日志默认关闭，建议开发时打开，最终部署时关闭。	

```sql
# 检查是否开启了慢查询日志。
show variables like '%slow_query_log%';

# 临时开启(内存中开启,重启mysql服务失效。)
set global slow_query_log=1;

# Linux中永久开启 (可以回顾一下Linux学习笔记。)
/etc/my.cnf 
【mysqld】# 追加 slow_query_log=1
slow_query_log_file=想要存放的位置 如 /var/lib/mysql/localhost-slow.log
```

​		设置慢查询阈值

```sql
# 临时设置阈值，重新登录mysql即可起效，无需重启服务。
set global long_query_time=5;

# Linux中永久设置
/etc/my.cnf 
【mysqld】# 追加 
long_query_time=3
# 以上是设置为5秒，可以自己定。

# 用主动延时来检查
select sleep(4);
show global status like '%slow_queries%';
```

​		用Linux的各种工具cat等查看慢日志文件，或者用mysql自带的慢查询工具，专业的应该更方便一些。

```sql
cat /usr/local/mysql/data/MacBook-Pro-slow.log 
# 我这里用的是Mac，和Linux的默认慢日志位置不一样。

# 用mysql内的工具来看,按提示操作,记得退出mysql界面在Linux命令框里操作。
mysqldumpslow --help

# 获取返回记录最多的3个sql
mysqldumpslow -s r -t 3 /usr/local/mysql/data/MacBook-Pro-slow.log
# 获取访问次数最多的3个sql
mysqldumpslow -s c -t 3 /usr/local/mysql/data/MacBook-Pro-slow.log
# 按照时间排序，前10条包含left join 查询语句的sql
mysqldumpslow -s t -t 10 -g 'left join' /usr/local/mysql/data/MacBook-Pro-slow.log
```

![截屏2022-02-21 15.51.22](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-21%2015.51.22.jpg)



## 模拟海量数据优化

### 插入海量数据

​		存储过程（无return）存储函数（有return）。	

```sql
create database testdata;
use testdata;
create table dept(
    dno int(5) primary key default 0,
    dname varchar(20) not null default '',
    loc varchar(30) default ''
)engine=innodb default charset=utf8;
create table emp(
    eid int(5) primary key,
    ename varchar(20) not null default '',
    job varchar(20) not null default '',
    deptno int(5) not null default 0
)engine=innodb default charset=utf8;
drop table emp;

```

​		通过存储函数插入海量数据。

​		创建存储函数：

​				randstring(6) 用于模拟员工名字，由5个字母组成。

```sql
# 在mysql中创建函数有几个注意的点。
# 1、用 delimiter $ ... $ 来让  # ; 不再是语句终点，让两个$包裹的中间部分成为一个整体。
# delimiter $
# ...
# $
# 2、定义函数的形参 要定义形参类型，以及返回值的类型，注意不要 ；。中间的函数体用begin 和 end 包裹。
# create function randstring(n int) return varchar(255)
# begin
# ...
# end
#	3、定义变量 用declare ，需要声明类型，以及默认值。
# 4、mysql的字符串索引不同于其它语言，是从1开始的。
# 5、rand()随机函数是【0，1）的闭开区间；FLOOR() 向下取整；substring(目标，索引，取的个数)切片；concat(stringA,stringB)粘合两个字符串;
# 6、赋值语句需要set开头 。set i=i+1;

# 以防none of deterministic 报错。
set global log_bin_trust_function_creators=1;

delimiter $

create function randstring(n int) returns varchar(255)
begin
declare all_str varchar(100) default 'abcdefghijklmn';
declare return_str varchar(255) default '';
declare i int default 0;
while i<n
do
set return_str = concat(return_str,substring(all_str,floor(rand()*14+1),1 ));
set i = i+1;
end while;
return return_str;
end $
```

​		产生随机整数

```sql
delimiter $
create function ran_num() returns int(5)
begin
declare i int default 0;
set i = floor(rand()*100);
return i;
end 
$
```

​		通过存储过程插入海量数据：emp表中。

```sql
# 利用存储过程海量插入需要提前注意：
# 存储过程参数要 in 表示输入类型还是什么类型；关闭自动提交，不然io浪费过大；repeat ... until ... end repeat 中until后无; ； 

create procedure insert_dept(in dno_start int(10),in data_time int(10))
begin
    declare i int default 0;
    set autocommit = 0;
    repeat
        insert into dept values(dno_start+i,randstring(6),randstring(8));
        set i = i+1;
    until i=data_time end repeat;
    commit;
end $

create procedure insert_emp(in eid_start int(10),in data_times int(10))
begin
    declare i int default 0;
    set autocommit=0;
    repeat
        insert into emp values(eid_start+i,randstring(5),'other',ran_num());
        set i = i+1;
    until i=data_times end repeat;
    commit;
end $


```

​		调用存储过程插入数据

```sql
# 把语句结束符号改回分号
delimiter ;
call insert_emp(1000,800000);
call insert_dept(10,30);

select count(*) from emp;
select count(*) from dept;
# 可以看见已经成功插入80万条员工数据和30条部门数据。慢日志中可见插入80万条数据耗时了58秒。
```

### 分析海量数据

​		用profiles	

```sql
show variables like '%profiling%';
set profiling = on; # 开启
# show profiles; 可以记录开启 profiles 后所有操作的时间。但是只能看见总共时间，看不见cpu、io各自的耗时。

-- 1、总的检查
show profiles;

-- 2、精确分析 sql诊断。
show profile all for query 2;
# 查看详细耗时。
show profile cpu,block io for query 上一步查询的query_id;

-- 3、查全局日志（调优开发时用，实际部署关闭。）不关会浪费大量存储空间。
show variables like '%general_log%';
set global general_log=1;
set global log_output='table';
# 以上将记录导入mysql自带的mysql库的general_log表中。也可以自己定地方存日志文件。
# set global log_output='file';
# set global general_log=1;
# set global general_log_file='自己想放的地方/general.log';

select * from mysql.general_log; # 在全局日志里取查。
```



## MySQL的锁

操作类型：

​		a、读锁（共享锁）：对同一个数据，多个读操作可以同时进行，互不干扰；

​		b、写锁（互斥锁）：如果当前写操作没有完毕，则无法进行其它的读操作、写操作；

操作范围：

​		a、表锁，MyISAM引擎就是用表锁，开销小，加锁快，无死锁，但是范围大容易发生锁冲突，并发低；

​		b、行锁，一次性对一条数据加锁，InnoDB引擎就是用行锁。开销大，加锁慢，容易死锁，锁范围小不容易冲突，不容易高并发问题如（脏读、幻读、不可重复、丢失更新）；

​		c、页锁 

### 表锁示例	

```sql
-- 会话0
create table tablelock(
id int primary key auto_increment,
name varchar(20)
)engine myisam;

insert into tablelock(name) values('a1');
insert into tablelock(name) values('a2');
insert into tablelock(name) values('a3');
insert into tablelock(name) values('a4');
insert into tablelock(name) values('a5');
commit;
```

​		上锁用法：

​		lock  tables 表名1  read/write，表名2 ... read/write ...

```
	查锁：
```

​		show  open  tables;  

​		释放锁：

​		unlock tables;

```sql
-- 会话0
show open tables;
lock tables tablelock read;
select * from tablelock where id =1;
delete from tablelock where id =2; # Table 'tablelock' was locked with a READ lock and can't be updated
select * from emp; # Table 'emp' was not locked with LOCK TABLES
delete from emp where eid =1; # Table 'emp' was not locked with LOCK TABLES

-- 会话1
select * from dept;
delete from dept where dno=16;# Query OK, 1 row affected (0.01 sec)
select * from tablelock;
delete from tablelock where id=1; # 一直等待会话0释放read锁，释放后才会写操作。
```

#### read锁总结：

（一）

​		会话0对a表加了read锁，则该会话只能对a表进行读操作、不能写操作；该会话对其它表既不能读也不能写。（ 加锁的本会话仅保留了对a表读操作的能力。）

（二）

​		会话0 给a表加了read锁，其它会话：1、可以读、写其它表；2、对a表只能读，写则需要等待会话0释放read锁。

write锁的实验类似，这里略去。

#### write锁总结：

（一）

​		会话0对a表加的write锁，则可以对a表进行任何操作（增删改查）；不能对其它表进行任何操作（增删改查）。

（二）

​		其它会话对会话0设置write锁的a表，可以增删改查的前提，是要等会话0释放write锁。

补充：MySQL表级锁MyISAM引擎的工作模式如下。

![截屏2022-02-22 22.08.13](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-22%2022.08.13.jpg)

#### 分析表锁：		

```sql
# 查看枷锁情况
show open talbes; # 1是加锁。
# 查看表锁严重程度：
show status like 'table%'; # table_locks_immediate 是立即能获得的锁的数量；table_locks_waited 是需要等待的表锁数量（数值越大锁竞争越激烈）；
```

![截屏2022-02-22 22.22.03](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-22%2022.22.03.jpg)

### 行锁示例	

```sql
-- 会话0
set autocommit=0; # 关闭自动提交。用begin 或者 transaction 手动声明也是可以的。
create table linelock(
    id int(5) primary key auto_increment,
    name varchar(20)
)engine=innodb;
insert into linelock(name) values('1');
insert into linelock(name) values('2');
insert into linelock(name) values('3');
insert into linelock(name) values('4');
insert into linelock(name) values('5');
commit;
```

```sql
-- 会话0
insert into linelock(name) values('a6');
-- 会话1（此时会话0关闭了自动提交，会话1还是自动提交的。）
update linelock set name='ax' where id=6;
# 此时会话1会进入等待，等会话0释放锁。

-- 会话0
insert into linelock value(7,'a7');
-- 会话1
update linelock set name='ax' where id=5;
# 行级锁，一次锁一行，操作不同行数据，互不不干扰。
# 此时，会话0因为事务还没有结束，因为没有被commit，还是只能看见原表到自己这步操作，看不见会话1已经提交的改动；会话1自动提交了，能看见原表到自己这的改动，但是由于会话0的事务还没有结束，看不见会话0还未提交的改动。如下：
```

会话1

![截屏2022-10-10 23.36.58](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2023.36.58.jpg)

会话0

![截屏2022-10-10 23.39.38](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2023.39.38.jpg)

```sql
-- 会话0 
commit;
# 会话0提交后结束事务，便可查询到会话1所做的改动。
```

![截屏2022-02-23 17.47.03](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-23%2017.47.03.jpg)

#### 行锁总结（关闭了自动提交）：

​		1、如果会话x对某条数据a进行增删改(DML)操作，则其它会话必须等待会话x 结束事务（commit/rollback）后，才能对a操作。

​		2、表锁通过unlock tables或者结束事物；行锁只能通过结束事务解锁。

#### 行锁注意事项：

```sql
-- 会话0
alter table linelock add index idx_linelock_name(name);
update linelock set name='ai' where name ='3';

-- 会话1 
update linelock set name='aix' where name='4';
# 改不同数据行不受影响。

-- 会话0
update linelock set name=11 where name like '%1%' ;

-- 会话1 
update linelock set name=22 where name like '%2';
# 发现会话1的操作被阻塞了，直到会话0commit结束会话才执行。这是因为name用了模糊搜索，导致了name索引失效了，行锁自动转换为了表锁！！！
# 而由行锁放大而来的表锁把整个linelock表都锁住了，依然遵守行锁规范只是范围变大了，可以参考前文表锁，只要上了表锁，不管是read锁还是write锁，都是别的会话不能DML(增删改)此表，直到释放锁，或者结束事务commit；
```

​		a、如果没有索引，则行锁会转为表锁（主键也是一个索引，别忘了）；可以show index from 表 去查看一下索引情况。

​		b、开了一堆会话，有时候种种原因导致了互相锁，也分不清了，一些表连删都删不掉，可以关闭一些会话，只保留一个会话再操作。

​		c、行锁的特殊情况——间隙锁、临键锁。本题如id=1～9之间 7号位置是个空缺，--会话0 输入 update linelock set name='x' where id>1 and id<9  但是不提交，mysql会自动给id=7的数据加临键锁（行锁）下面有说到，此时会话1若要DML id=7的数据要等待临键锁锁释放。       可以理解为：（如果有where，实际加锁范围  就是where后面的范围，而不是实际的值。）

```sql
-- 会话0
delete from linelock where id =7;
commit;
update linelock set name='x' where id>1 and id<9;

-- 会话1
insert into linelock values(7,'y');
# 由于会话0没commit，间隙行锁没有被释放，会话1被迫等待。
```

![截屏2022-02-23 19.06.04](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-23%2019.06.04.jpg)	

```sql
# 行锁分析
show status like '%innodb_row_lock%';

```

​		![截屏2022-02-23 19.08.42](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-23%2019.08.42.jpg)

![截屏2022-02-23 19.10.55](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-02-23%2019.10.55.jpg)

补充：

​		在select时，在select语句末尾加for update，可以一样实现加行锁的目的。一下是 读锁（共享锁）锁一行的案例。但是这个不会只能读这锁住的一行，本表它表都能读，只是此行不能增改删。

```sql
-- 会话0
select name from linelock where id = 2 for update;

-- 会话1
select * from linelock where id=2; # 会话1查询时没有问题的。
update linelock set name='y' where id=2; # dml增删改 就要等会话0释放行锁了。
```



### 意向锁

​		意向 共享锁、意向 排它锁。

​		![截屏2022-10-10 17.34.11](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2017.34.11.jpg)

​		目的： 告诉你 这个表是否已经加锁了，加了什么锁。 不必再去每一个行去试了。

​		innodb 引擎有个问题，在 表没设置 主键的情况下， update 原本这样的自动上 排它锁的 操作，innodb引擎会自动 将 行锁 变成 表锁，此时对其他行 update 是无法操作。所以要加主键。

​		innodb 引擎 用模糊搜索 也会让 行锁升级表锁。



### 行锁的区间

​		假设表中的 id = 1、5、9、11 四个位置储存有数据。

![截屏2022-10-10 17.45.47](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2017.45.47.jpg)

![截屏2022-10-10 17.48.12](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2017.48.12.jpg)

![截屏2022-10-10 17.54.04](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2017.54.04.jpg)

​		间隙锁 一个非常易错的点： 

​				如果 `where` 后面 书写的范围是 （13，正无穷） 那实际触发的间隙锁 还是 （11，正无穷） 这段间隙！！！

​		![截屏2022-10-10 17.59.03](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2017.59.03.jpg)

​		

## MVCC 快照读（不用锁）实现隔离

![截屏2022-10-10 18.16.26](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2018.16.26.jpg)

​		不是给整个库 快照，而是对修改的数据 快照，以后挑最大的transaction_id 组成一套数据 来读。

​		但是 开始了事务以后，开启事务 时候的 transaction_id 分界，比它大的我们就不读了！！！



### “快照读” 结合 “当前读”

​		![截屏2022-10-10 18.29.15](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2018.29.15.jpg)

​		innodb默认 可重复读，所以 事务a 读两次都是 18；

​		![截屏2022-10-10 18.37.47](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2018.37.47.jpg)

​		每一个事务开启时就要 确定已经提交的 最大 快照号，三个事务都是 999;

![截屏2022-10-10 18.38.37](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2018.38.37.jpg)

​		事务c 修改了数据，记录新的事务号 row_trx_id = 1002，age 的最新版本是1002 版本的19 了;

![截屏2022-10-10 18.42.02](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2018.42.02.jpg)

​		事务b 要修改 age 数据时，又会去 数据库中找 最新版本的 age，找到 最新的age 对应 row_trx_id 版本号是 1002，所以用 1002 版本的age 数据。

​		事务b 修改完 age 数据后，会给自己的新的age数据 打上自己 1001版本 的标记 row_trx_id=1001，此时1001版本的age 是最新的age 了；

![截屏2022-10-10 18.49.58](mysql%E7%AC%94%E8%AE%B0.assets/%E6%88%AA%E5%B1%8F2022-10-10%2018.49.58.jpg)

​		事务b 读 age 时 看见 最新的age 对应的版本是1001，正好 自己的 trx_id=1001 ，1001没有大于 1001，所以 事务b 读的age 就是 20。

​		事务a 读age， 因为事务a 没有对age 进行修改， 所以  读 一堆age版本时，用自己的 up_limit_id =999 来划分，从新到旧，遇到小于等于999版本的age 读取，所以还是 18。
