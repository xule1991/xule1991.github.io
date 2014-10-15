---
layout: post
title: InverseAttributeInHibernate
tags: [LeetCode]
---

> Inverse is a attribute to decide which party to maintain the relationship of two objects.If one party assign 
>"inverse = false" (which is default),then the relationship is maintained by the opposite party.This attribute 
>will help hibernate to decide which sqls will be created

Example: One to many

Java code:

```java
Parent p = new Parent();  
Child c = new Child();  
c.setParent(p);  //child has a reference to parent
p.getChildren().add(c);  //parent has a set which contains its children
  
session.save(p);  
session.flush();   
```

Mapping file:

```java
Mapping relationship in parent:  
{set name="children" lazy="true" inverse="true"}  
      {key column="parent_id"/}  
      {one-to-many class="test.Child"/}  
{/set}  

Mapping relationship in child
{many-to-one name="parent" column="parent_id" not-null="true"/}   
```
	Because the inverse is true in parent's mapping party, so the many to one relationship is maintained by 
Child.According to this configuration,Hibernate will help us create two insert sentence.

```java
Hibernate: insert into parent (?) values (?)
Hibernate: insert into child (parent_id, id) values (?, ?)
```

	If set child's inverse = "true" then there will be three sql sentences,the first two are used to 
insert parent and child, the third is used to update child table to maintain the relationship.
	Another scenario, if we comment the c.setParent(p),Then hibernate will just create one sql as below:
	
	Hibernate: insert into parent (id) values (?)
	
	because the relationship is maintained by child and child do not have parent.
	
	乐此不疲～

2014-10-15 09:31:11









