---
title: suibi
date: 2016-06-19 10:16:13
tags: [随笔]
---

<!--more-->

## 测试prettify高亮

```java
@Test
public  void   test3() throws ParseException {
    SqlSession session = sqlSessionFactory.openSession();
    try {
        IUserDao userDao = session.getMapper(IUserDao.class);
        User user=new User(0,"赵六","zhaoliu",1,new SimpleDateFormat("yyyy-MM-dd").parse("1989-01-23"));
        int acceptCount= userDao.insert(user);
        session.commit();
        Assert.assertEquals(1,acceptCount);
    } finally {
        session.close();
    }
}
```
