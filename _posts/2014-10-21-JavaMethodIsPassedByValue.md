---
layout: post
title: Refactoring:ImprovingTheDesignOfExistingCode1
tags: [Reading]
---

>
	

```	
public class Test {
	int a;

	public Test(int a){
		this.a = a;
	}
	
	public void aPlusOne(Test test) {
		test = new Test(3);
	}
	
	public static void main(String[] args){
		Test test = new Test(1);
		test.aPlusOne(test);
		System.out.println(test.a);
	}
}

```

The out put will be 1;

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```



	乐此不疲～

2014-10-21 15:00:11









