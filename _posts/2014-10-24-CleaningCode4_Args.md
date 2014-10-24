---
layout: post
title: CleaningCode4_Args
tags: [Reading]
---

>An example about parsing command line.


```
	public static void main(String[] args) {
		try{
			Args arg = new Args("l,p#,d*", args);
			boolean logging = arg.getBoolean('1');
			int port = arg.getInt('p');
			String directory = arg.getString('d');
			executeApplication(logging, port, directory);
		}catch (ArgsException e) {
			System.out.printf("Argument error:%s\n",e.errorMessage());
		}
	}
```
In this code, we use two arguments to create an Args object, the first argument is format string
,which difine three command line params,first is -l, a boolean argument, second is -p, an integer,
third is -d,string.Then pass args array to Args's constructor.

```
	public class Args{
		private Map<Character, ArgumentMarshaler> marshalers;
		private Set<Character> argsFound;
		private ListIterator<String> currentArgument;
		
		public Args(String schema, String[] args) throws ArgsException {
			marshalers = new HashMap<Character, ArgumentMarshaler>();
			argsFound = new HashSet<Character>();
			
			parseSchema(schema);
			parseArgumentStrings(Arrays.asList(args));
		}
		
		private void parseSchema(String schema) throws ArgsException {
			for(String element : schema.split(","))
				if(element.length() > 0)
					parseSchemaElement(element.trim());
		}
	}
	
	
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
```

```
```

	
	乐此不疲～

2014-10-23 15:38:11









































































