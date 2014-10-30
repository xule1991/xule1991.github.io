---
layout: post
title: EscapeCharacter
tags: [Tips]
---

>When we use Spring template, we need use escape character for '<', 


```
where
  ((select superadmin from LOGIN_PROFILE where profile_id = :<profileId>) = 1 
   or
   f.manager_id in
     (select party_id
      from PROFILE_AUTHORIZED_PARTY 
      where profile_id = :<profileId>
  	     or profile_id in (select group_profile_id from GROUP_PROF_INDIVIDUAL_PROF where user_profile_id = :<profileId>)
     )
  )
 <if(fundManagerMeiPrefix)> and f.manager_id like :<fundManagerMeiPrefix>||'%' <endif>
 <if(fundNamePrefix)> and LOWER(p.party_name) like LOWER(:<fundNamePrefix>||'%') <endif>
 <if(tradeDate)> and to_char(f.effective_date_from,'yyyy-mm-dd') \<= to_char(:<tradeDate>,'yyyy-mm-dd') and to_char(f.effective_date_to,'yyyy-mm-dd') >= to_char(:<tradeDate>,'yyyy-mm-dd') <endif>
```

for the last line, we need use \<, instead of <, or the project will throw an error like : org.stringtemplate.v4.compiler.STException;

	
	乐此不疲～

2014-10-24 13:41:11









































































