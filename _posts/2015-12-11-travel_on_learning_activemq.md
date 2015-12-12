---
layout: post
title: LearningActiveMq (1)
tags: [activemq_learning]
---

>First day on continuing to study source code, need to track every issues met and then blog them.


```
	1. the maven repo china:
			http://maven.oschina.net/content/groups/public/
			
	2. create a new gitignore on windows, we need to use editor to create a new file then save as ".gitignore", then it can be saved.
	
	3. create a new project on github:
		create a new one on github.com
		git add README
		# Stages your README file, adding it to the list of files to be committed
		git commit -m 'first commit'
		# Commits your files, adding the message "first commit"
		git remote add origin https://github.com/username/Hello-World.git
		# Creates a remote named "origin" pointing at your GitHub repository
		git push origin master
		# Sends your commits in the "master" branch to GitHub
		
	4. instead of usding maven dependencies to import the jar needed, we can also add all the libs under binary package's lib directory to the classpath in 
	IDE, then all the dependencies is added, much more convenient if you start from scratch.
	
	5. configure maven mirrors:
	the follows are the mirrors which can be used at home without vpn
		<mirrors>
			<mirror>
			  <id>mirrorId</id>
			  <mirrorOf>repositoryId</mirrorOf>
			  <name>Human Readable Name for this Mirror.</name>
			  <url>http://my.repository.com/repo/path</url>
			</mirror>
			 -->
			 <mirror>  
			  <id>repo2</id>  
			  <mirrorOf>central</mirrorOf>  
			  <name>Human Readable Name for this Mirror.</name>  
			  <url>http://repo2.maven.org/maven2/</url>  
			</mirror> 
			<mirror>    
			  <id>ui</id>    
			  <mirrorOf>central</mirrorOf>    
			  <name>Human Readable Name for this Mirror.</name>    
			 <url>http://uk.maven.org/maven2/</url>    
			</mirror> 
			<mirror>    
			  <id>jboss-public-repository-group</id>    
			  <mirrorOf>central</mirrorOf>    
			  <name>JBoss Public Repository Group</name>    
			 <url>http://repository.jboss.org/nexus/content/groups/public</url>    
			</mirror>  
			 <mirror>  
			  <id>CN</id>  
			  <name>OSChina Central</name>                                                                                                                         
			  <url>http://maven.oschina.net/content/groups/public/</url>  
			  <mirrorOf>central</mirrorOf>  
			</mirror>  
  </mirrors>
		btw, maven.oschina.net is not stable always 500error during transfer .jar packages can not find out what is happening. just remove this mirror, everything is fine.
		
		6. 
```



	
	乐此不疲～

2015-12-11







































































