---
title: Hibernate Session中createQuery()和createSQLSquery()方法的区别
date: 2014-06-04 18:37:29
categories: Hibernate
tags: ['hibernate','java']
---

本文将介绍Hibernate的Session类createQuery, createSQLSquery方法两个区别及如何使用

### createQuery

可以使用"FROM Student"这样的HQL语句进行查询，其中"Student"是持久化Student类的名称

<!--more-->
例如：
```Java
Session session = factory.openSession();
Query query = session.createQuery("FROM Student where studentId="+ stuId);
Student ss = (Student) query.list().get(0);
System.out.println(ss.getName() + "    " + ss.getAge());
session.close();
```

### createSQLQuery

可以像"select * from student"这样使用原生数据库语法编写查询语句，其中student是一个表的名称

例如：

```Java
Session session = factory.openSession();
SQLQuery query = session.createSQLQuery("select * from student where student_id="+stuId);
query.addEntity(Student.class);
List list = query.list();
for (Object ss : list) {
  System.out.println(((Object)ss).getName() + "    " + ((Object)ss).getAge());
  session.close();
}    
session.close();
```
query.addEntity(Student.class)这句是指定将数据库查询的数据映射到指定的实体类，如果不指定将会以Object[Object[]]返回行数据

### get()

用给定的对象标识返回给定的持久化实体类（Student.hbm.xml为实体类映射文件，实体标识id映射数据库的主键student_id）
例如
```Java
Session session = factory.openSession();
Student ss = (Student) session.get(Student.class,student_id);
System.out.println(ss.getName() + "    " + ss.getAge());
session.close();
```
