# Pentester's Mindset!
## Get out of the limited OWASP TOP-10/SANS TOP-25/Bug Bounty mindset
by Ravikumar Paghdal - *ravi at net-square.com*, [@_RaviRamesh](https://twitter.com/_raviramesh) <BR/> November 2019

![](https://i.ibb.co/pZPPBRQ/4c0d0a1d7a9e9cae51a4e75edac1b032.png)

### TL;DR:
"Get out of the limited [OWASP TOP-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)/[SANS TOP-25](https://www.sans.org/top25-software-errors/)/[Bug Bounty](https://en.wikipedia.org/wiki/Bug_bounty_program) mindset". This paper discusses multiple case studies to convey the message that *if you think limited, you will be limited.* Bug bounty approach has degraded the quality of penetration testing, for both the customers as well as the practitioners. It is hard for the customer to differentiate between a good penetration testing and a quick and dirty top-10 or top-25 approach. <BR/>This paper will help those who have newly entered the infosec field and the bug bounty hunters.

>**Caution:** *This article can and will change the mindset and habit of typical pentester. Proceed with caution. This may give serious heartache to your feelings.*

### 1. Introduction

કેમ છો? મજામાં. I'm Ravikumar Paghdal, currently working as a senior manager at [Net Square Solutions Pvt. Ltd](https://net-square.com). In my past experience, I have reviewed hundreds of web/mobile application vulnerability assessment and penetration testing reports of multiple organizations and there is one thing that is always common in all the reports, guess what?

**"The most common thing is the well-known vulnerabilities."**

Yes, I’m talking about SQL injection, XSS, CSRF, IDOR, missing security headers, etc. 

The shocking part is according to a survey conducted by HackerOne in 2019 ["The survey and statistic of the ethical hacker community"](https://www.hackerone.com/sites/default/files/2019-02/the-2019-hacker-report_3.pdf) ( page 39), more than 50% of BugBounty hunters are focusing on XSS and SQLi only.

> MORE AND MORE HACKERS NAME XSS THEIR FAVORITE ATTACK VECTOR

>When asked about their favorite attack vector, technique or method, over 38% of hackers surveyed said they prefer searching for cross-site scripting (XSS) vulnerabilities. That’s up from just 28% last year, and puts XSS significantly ahead of all other attack vector preferences. SQL injection placed second at 13.5%, while fuzzing, business logic, and information gathering rounded out the top five. In 2017, neither business logic nor information gathering placed in the top 10 last year.- ["The survey and statistic of the ethical hacker community"](https://www.hackerone.com/sites/default/files/2019-02/the-2019-hacker-report_3.pdf) ( page 38)

<kbd>
 
![](https://i.ibb.co/xLVcpDy/aa9f3cbad995571a1423f5c8a06c2062.png)

</kbd>
<BR/><BR/>
 
After giving sufficient time thinking about why these findings are most common in every security report. I finally concluded the answer and it is so simple. 

**"Because a security analyst or a penetration tester only focuses on well-known vulnerabilities."**

From my experience, I can tell that this is the [habitual behaviors or patterns of practice](https://www.tandfonline.com/doi/abs/10.1080/14792779943000035). This is what I have observed based on most of the analyst's testing mechanism or mindset towards testing, the basic strategy is to intercept HTTP request and inject single quotes (```'```), double quotes (```"```), greater than sign (```>```) and less than sign (```<```) to identify vulnerabilities. 
<BR/>While injecting those special characters, the mind of an analyst has a thought process that eventually leads to finding such as XSS and SQLi only ;) 

I have always wondered about this strategy, why they do not inject characters like [backtick](http://tldp.org/LDP/abs/html/commandsub.html) (``` ` ```), [pipe](http://www.linfo.org/pipes.html) (```|```), [Null character](https://en.wikipedia.org/wiki/Null_character) (```%00```), [Zalgo text](https://zalgo.org/) (```N̯̱̣͇̖̦̦̣ͥͮͩͪ̐͑͂̈̅ͦ͋̆̔͆̀̆̀̚̚̕  ```), [multibyte character](https://en.wikipedia.org/wiki/Variable-width_encoding) (```﷽```, ```𒐫```), [Carriage Return](https://en.wikipedia.org/wiki/Carriage_return) (ASCII 13, ```\r```), [Line Feed](https://en.wikipedia.org/wiki/Newline) (ASCII 10, ```\n```) or different characters, why??  

Let’s understand the concept of **pattern of practice** in the most simple way.

Recall those days when you have started with your first ever web application testing or you can say your initial learning phase, during that time you had the practice to find common bugs like SQL Injection, XSS, CSRF, etc. But now we have grown and there are many new bugs in the market (according to Common Weakness Enumeration ([CWE List version 3.4](https://cwe.mitre.org/data/index.html)) the total number of software weaknesses is [808](https://cwe.mitre.org/data/index.html)), *so why do we focus only on common vulnerability?*

I strongly believe that after reviewing the following case studies, you will be able to figure out what is wrong with the current mindset of pentester.  

Let's take one HTTP request which contains two parameters ```'uid'``` and ```'pass'```.

<kbd>
 
![](https://i.ibb.co/prRwxTZ/Picture-1.png)

</kbd>
<BR/><BR/>

Now, if you know how to find vulnerabilities in a web application then provide an answer to few simple questions of mine.

 1. Which part of the request is vulnerable?
 2. Which vulnerability will affect the application and on which part?
 
The most common answer would be two parameters ```'uid'``` and ```'pass'``` or further someone will answer including cookie parameters ```'_a'```, ```'_b'``` and ```'_c'``` also. Right ??!!

Let's continue with following case studies to change your point of view about web app security testing.

### 2. Case Studies

In the below first five case studies, ```B``` is banking and financial services company. They use web server ```W```, app server ```A```, SQL database ```S```, NoSQL database ```N```, file server ```F```, log servers ```L1```(Oracle based DB) and ```L2``` (linux machine) and domain controller server ```AD``` for authentication.

Keep one thing in your mind, in all following case studies; you only have a single login page on the client side for testing, the architecture is given for your understanding only, about possible server side operations. We have not covered [Business logic vulnerability](https://www.owasp.org/index.php/Business_logic_vulnerability) in the below case studies because it varies with different applications. So we will focus on technical vulnerabilities only.


#### 2.1 A case study with typical web application architecture
We have been provided with a single page to perform blackbox testing on a banking application ```B```, once we enter the *userid* and *password* then this two parameters ```'uid'``` and ```'pass'``` will traverse to the web server ```W``` and ```W``` will build dynamic SQL queries to retrieve user details from MySQL database ```S```.

<kbd>
 
![](https://i.ibb.co/ZYXyjPk/Screenshot-2019-10-31-at-7-38-27-PM.png)

</kbd>
<BR/>

Steps involved:
1. Browser will send two parameters ```'uid'``` and ```'pass'``` to the web server ```W```.
2. Web server ```W``` will receives parameter and build dynamic SQL query.
3. SQL Query will triger on database server ```S``` and provide desired output to web server ```W```.

Our first request with ```'uid'``` and ```'pass'``` traverse the same way and it will build a dynamic SQL query. So our login page is affected by which vulnerability? SQL Injection right?!! 

#### 2.2 A case study with WebDAV enabled file server 
Same as earlier, we have a single login page for testing but the database server is not implemented on server side.
All the data is stored in file formate on the file server ```F```. Once the parameters are received by the web server ```W```, the web server will connect to the file server using [WebDAV](https://www.ietf.org/rfc/rfc4918.txt) methods ([PUT](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PUT), [PATCH](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PATCH), etc.) and those methods will update the user data. 
So in this case study; parameters are the same ```'uid'``` and ```'pass'``` but the vulnerability will differ.

<kbd>

![](https://i.ibb.co/7YdxD1B/Screenshot-2019-10-31-at-7-38-38-PM.png)

</kbd>
<BR/>

Steps involved:
1. Browser will send two parameters ```'uid'``` and ```'pass'``` to the web server ```W```.
2. Web server ```W``` will connect to the file server ```F``` using [WebDAV](https://www.ietf.org/rfc/rfc4918.txt) methods.
3. File server ```F``` will perform file operation based on received [WebDAV](https://www.ietf.org/rfc/rfc4918.txt) method.

In case study 2.2, you don’t have any control over server to server calls or methods so don’t think about [PUT method exploit](https://portswigger.net/kb/issues/00100900_http-put-method-is-enabled). You will only have control on two parameters ```'uid'``` and ```'pass'``` so think carefully, what can you do with this two parameters and which kind of vulnerability can you discover?

Here we have observed that user file name is created based on the user name and when the user will try to access the file then the file server will respond with user data from the file system, so what if you insert the following payload in ```'uid'``` parameter.

```uid=../../../../../../../../../../../../../../etc/passwd&pass=xyz```

Yes, you are on the right path... there is a possibility of [file path traversal vulnerability](https://portswigger.net/web-security/file-path-traversal).

#### 2.3 A case study with NoSQL database and XML processing 

We have the same login page but the server architecture is changed this time, in this architecture web server's responsibility is to only receive parameters and convert them into XML formate and forward them to app server ```A```. 

App server ```A``` can only understand XML or JSON request. If app server ```A``` receives any request from the web server ```W``` then it will parse it into XML request and if the request is received from Android app ```AN``` then it will parse the request in JSON. 

In-short all the business logic coded on app server ```A``` and DB server ```N``` is based on mongoDB . Once the app server receives an login request then it will process it and build dynamic queries and pass it on the DB server ```N```. If app server receives a valid response from the DB server ```N```, then it will pass the response to the web server ```W``` which will then build a HTML page.

<kbd>

![](https://i.ibb.co/4TjyWjc/Screenshot-2019-10-31-at-8-16-51-PM.png)

</kbd>
<BR/><BR/>

Steps involved:
1. Browser will send two parameters ```'uid'``` and ```'pass'``` to the web server ```W``` and if you are using mobile application ```AN``` then it will send JSON request ```{uid:"<userid>",pass:"<password>"}``` to the app server ```A```.
2. Web server ```W``` will receives parameter; convert them into XML formate and forward them to the app server ```A```.
3. App server ```A``` will parse the request and make a login call to the Mongo DB server ```N```.

In this case, our parameter value will be converted into XML request. Therefore, you can inject XML payloads in ```'uid'``` and ```'pass'``` parameters and then it will be parsed on the app server ```A```. So the attacker can perform all the possible XML attacks like [XML Injection](https://portswigger.net/kb/issues/00100700_xml-injection), [XXE](https://portswigger.net/web-security/xxe), [XInclude attack](https://portswigger.net/web-security/xxe#xinclude-attacks) , [XSLT injection](https://portswigger.net/daily-swig/edge-side-includes-abused-to-enable-rce) and [XPATH injection](https://portswigger.net/kb/issues/00100600_xpath-injection) [if XPath queries are involved in this process.] 

Another observation is that mongoDB does not accept any SQL statements. So you have to inject NoSQL statements and ```'uid'``` is vulnerable to [NoSQL Injection](https://www.owasp.org/index.php/Testing_for_NoSQL_injection).

#### 2.4 A case study with log server based vulnerabilities and exploitation

Again, we have the same login page with two parameters ```'uid'``` and ```'pass'``` also we have the same architecture as case study 2.3 but in addition to the above case study we have two log servers. 

One log server ```L1``` is based on ORACLE DB and the second log server ```L2``` is based on linux command line process. Every single request received by the app server ```A``` will be copied and send to both log servers ```L1```,```L2``` and then app server ```A``` will process the request.

<kbd>

![](https://i.ibb.co/y85mvg2/Screenshot-2019-10-31-at-7-37-58-PM.png)

</kbd>
<BR/><BR/>

Steps involved:
1. Browser will send two parameters ```'uid'``` and ```'pass'``` to the web server ```W``` and if you are using mobile application ```AN``` then it will send JSON request ```{uid:"<userid>",pass:"<password>"}``` to the app server ```A```.
2. Web server ```W``` will receives parameter; convert them into XML formate and forward them to the app server ```A```.
3. Every request received by the app server ```A``` will be copied and send to both log servers ```L1```,```L2```. 
If app server ```A``` receives JSON request i.e. ```{uid:"ravi",pass:"P@ssw0rd"} ``` then the app server will send this JSON to log server ```L1``` and log server ```L2```.

- In log server ```L1```, the request will be generated as following SQL query

>```INSERT INTO LOG_2019 VALUES('{uid:"ravi",pass:"P@ssw0rd"}');``` <BR/>
```INSERT INTO LOG_2019 VALUES('{uid:"ravi",pass:"Wr0ng_P@55w0rd"}');``` <BR/>
```INSERT INTO LOG_2019 VALUES('{uid:"admin",pass:"admin"}');``` <BR/>
```INSERT INTO LOG_2019 VALUES('{uid:"test",pass:"test@123"}');```

- In log server ```L2```, the request will be generated as following command to append the request into the log file.

>```echo '{uid:"ravi",pass:"P@ssw0rd"}' >> LOG_2019.log```<BR/>
```echo '{uid:"ravi",pass:"Wr0ng_P@55w0rd"}' >> LOG_2019.log``` <BR/>
```echo '{uid:"admin",pass:"admin"}' >> LOG_2019.log``` <BR/>
```echo '{uid:"test",pass:"test@123"}' >> LOG_2019.log```
4. App server ```A``` will parse the request and make a login call to the Mongo DB server ```N```.


In this case, all possible vulnerabilities are similar to case study 2.3, in addition our parameter's values are directly invoked in log servers request so the attacker can inject their payload in log process. So log server ```L1``` is vulnerable to [SQL Injection](https://portswigger.net/web-security/sql-injection) and Log server ```L2``` is vulnerable to [OS command injection](https://portswigger.net/web-security/os-command-injection).  

>*Funny part is you can inject your payload anywhere in the JSON request which is not limited to parameter value only, because the whole JSON request will be logged into the log server.* ☺

One more thing, if you observe the architecture deeply, all server-to-server communication is bi-directional but only the log server ```L1/L2``` calls are in one/single direction. So if you want to identify this blind injection you need to know **out-of-band** techniques, how it works and you need to learn about [BURP collaborator](https://portswigger.net/burp/documentation/collaborator) to detect this kind of hidden vulnerabilities [hidden because you can’t observe any errors or time delay in the response, you have to exploit this kind of vulnerabilities using OOB techniques].

#### 2.5 A case study with LDAP protocol and JSON Web Token

Again we have the same login page with two parameters  ```'uid'``` and ```'pass'``` and we have the same architecture as case study 2.4 but in addition to the above case study we have one authentication process to validate a user using [LDAP protocol](https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol). 

Once the app server ```A``` receives the login request, it will forward the request to active directory or domain controller ```AD``` to validate the user; based on windows authentication and this process is implemented using dynamic LDAP strings. If the user and password in the request is valid then the server ```AD``` will issue [JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519) and further all authentication will take place using verifying JWT token. 

<kbd>

![](https://i.ibb.co/SyDL5pz/Screenshot-2019-10-31-at-7-39-04-PM.png)

</kbd>
<BR/><BR/>

Steps involved:
1. Browser will send two parameters ```'uid'``` and ```'pass'``` to the web server ```W``` and if you are using mobile application ```AN``` then it will send JSON request ```{uid:"<userid>",pass:"<password>"}``` to the app server ```A```.
2. Web server ```W``` will receives parameter.
3. Web server ```W``` will convert them into XML formate and forward them to the app server ```A```.
4. Every request received by the app server ```A``` will be copied and send to both log servers ```L1```,```L2```.
5. App server ```A``` will build a dynamic LDAP query to validating a user id and password and forward request to active directory or domain controller ```AD``` to validate the user.
6. If user is valid then the ```AD``` server will issue [JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519).
7. If user request have JWT token then app server ```A``` will parse the request and start data processing on the Mongo DB server ```N```.

All possible attacks for this case study are similar as case study 2.4, in addition to [LDAP Injection](https://portswigger.net/kb/issues/00100500_ldap-injection) and possibility to [JWT misconfiguration](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bcp-07) related issues.

#### 2.6 Do not trust on HTTP headers - A case study with CVE-2019-5418

In this sceanrio, we will review some of the vulnerability discovered in HTTP headers.

> **CVE-2019-5418** : [File Content Disclosure on Rails](https://github.com/mpgn/CVE-2019-5418) found by [John Hawthorn](https://twitter.com/jhawthorn)

<kbd>

![](https://i.ibb.co/KmtPxTc/54474938-95f10c80-47eb-11e9-9a7c-8fa7c72ccd9e.png)

</kbd>
<BR/><BR/>

In this case, **Accept** HTTP header is vulnerable to file content disclosure.

The images below displays **Referer** HTTP header is vulnerable to SQL Injection.

<kbd>

![](https://i.ibb.co/Y7qkB9M/Referer2.png)
![](https://i.ibb.co/pRVKt9h/Refere1.png)

</kbd>
<BR/><BR/>
So, my question for all the testers is, "how frequently do you check different vulnerabilities in such HTTP headers?".

#### 2.7 Out of the Box testing or weird testing - a case study with CVE-2018-4124

 >[**CVE-2018-4124**](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-4124) 
*జ్ఞా This Telugu Character Makes Apple iPhones Crash (Update: Bug Fixed with iOS 11.2.6)*

Someone noticed that displaying a string written in India’s Telugu language (```జ్ఞా```) caused many apps on iOS and macOS to crash. 

Apple became aware of the issue after news of the bug started to spread on social media networks and trolls and pranksters started exploiting it. 
One individual apparently showed how he could crash the Uber app on drivers’ phones by setting his name to the problematic string and then requesting a ride.

While initially only a certain Telugu string appeared use to work, some later noticed that a specific string using characters of India’s **Bengali** language also caused apps on iOS and macOS to crash. 
There are several theories on what may be causing the crash, including from Mozilla research engineer Manish Goregaokar and Philippe Verdy of the Unicode Consortium.

The company patched the flaw with the release of macOS High Sierra 10.13.3 Supplemental Update, iOS 11.2.6, watchOS 4.2.3 and tvOS 11.2.6.

Another case is [Breaking Parser Logic - Take Your Path Normalization Off and Pop 0days Out](https://i.blackhat.com/us-18/Wed-August-8/us-18-Orange-Tsai-Breaking-Parser-Logic-Take-Your-Path-Normalization-Off-And-Pop-0days-Out-2.pdf) by [@**orange_8361**](https://twitter.com/orange_8361)

|| CVE           
| ------------- |:-------------:| 
 Spring Framework| CVE-2018-1271 | 
| Spark Framework| CVE-2018-9159      | 
| Jenkins| CVE-2018-1999002      |
|Mojarra|CVE-2018-14371|
|Ruby on Rails|CVE-2018-3760|
|Sinatra|CVE-2018-7212|
|Next.js|CVE-2018-6184|
|resolve-path|CVE-2018-3732|

So after one long and one short story !!

**What do we learn as a pentester based on case study 2.7 Out of the Box testing or weird testing ?**

Do not believe on any single unicode characters, you need to check your web server with all possible unicode. Right ?? 
<BR/>You don't know which unicode will crash your server. ;)

<BR/>To play more with unicode, you need to check [Unraveling Unicode: A Bag of Tricks for Bug Hunting](https://www.blackhat.com/presentations/bh-usa-09/WEBER/BHUSA09-Weber-UnicodeSecurityPreview-SLIDES.pdf) and [Exploiting Unicode-enabled Software](https://cansecwest.com/csw09/csw09-weber.pdf) by [Chris Weber](https://twitter.com/w3be).

----
It will always be a debate topic, [Why Johnny Can’t Pentest: An Analysis of Black-box Web Vulnerability Scanners](https://sefcom.asu.edu/publications/black-box-scanners-dimva2010.pdf) and automated vs manual testing.
Scanner will do the same thing, set all injection points and schedule and fuzz with predefined payloads. Based on server response scanner will decide, there are vulnerabilities or not. 

In many test cases, the automated scanner will fail, the following are a few examples. 

- We have sent one request for automated scanning and during scan the session is terminating.
- During scan server respond slowly and rest of testing response will be delayed, scanner detect time based vulnerability ( false positive findings ).
- Server throws error message which don’t have in scanner's signature DB.
- Web pages contain those words which are listed in signature DB.
- During scan - Server renews the session token.
- New released vulnerability is not part of the scanner !!
- Application URLs created in runtime are based on DOM or JS
...

### Conclusion

In above all 5 case studies have one thing in common, it is the same login page with ```uid``` and ```pass``` parameter.

As Black Box Tester, we do not know about server side architecture and configuration. So you have to follow two statements during testing.

>**Each user controllable part of HTTP request is vulnerable to every possible vulnerability.**<BR/> 
**So think like an attacker and test like an attacker.**

>**Attackers don't follow compliance standards and certifications.** - [Saumil Shah](https://conference.hitb.org/hitbsecconf2017ams/materials/KEYNOTE%201%20-%20Saumil%20Shah%20-%20Redefining%20Defense.pdf)

Based on all the above 5 case studies, we have concluded that you can not judge vulnerabilities based on parameter's name, we have seen single flaw with two parameters ```uid``` and ```pass``` having possibility to exploit multiple vulnerabilities (SQLi, RCE, LDAPi, NoSQLi, XML attacks etc.) in the same application/request.

In case study 2.6, clearly mentioned that do not ignore HTTP headers either, it is as important as parameters. 

You never know, in which request and in which process developer is involving HTTP header during coding of the application. So do not judge the headers blindly and conduct a proper pentesting.

In case study 2.7, we have learned that always do out of the box testing during pentest to discover more abnormal bugs (maybe even a 0 days).  

Now, you have same HTTP request.
 
<kbd>
 
![](https://i.ibb.co/prRwxTZ/Picture-1.png)

</kbd>
<BR/><BR/>

and you have same question.

 1. Which part of the request is vulnerable?
 2. Which vulnerability will affect the application and on which part?

You have your anaswer. Right? 

> Each corner of the request includes [HTTP methods](https://tools.ietf.org/html/rfc7231#section-4.3), [Message Headers](https://www.iana.org/assignments/message-headers/message-headers.xhtml) and all parameters are vulnerable to every possible vulnerability.

Even, we have reviewed few test cases when the automated scanner will fail.
*So you have to create such testing process or methodology to involve human mind in automated process, kind of mixed approach to reach a satisfactory level of your testing process.*  

**"The biggest drawback for the pentester is a constant fear of ‘missing the vulnerability’ and lacks the enthusiasm of finding a zero day or performing weird testing due to deadline. We can always do smart work but if we miss out on zero day then that would be our biggest regret."**

Ohh Yes!!. Last but not least, I have one last case study for you and you have to do yourself. 

#### Black Box - do yourself.

<kbd>
 
![](https://i.ibb.co/3RK1RCN/Screenshot-2019-11-04-at-12-38-17-AM.png)

</kbd>
<BR/><BR/>

----

### Appendix A: Bibliography

#### Tools/Code:
1. Online Zalgo Text Generator [https://zalgo.org/](https://zalgo.org/)
2. Burp Collaborator [https://portswigger.net/burp/documentation/collaborator](https://portswigger.net/burp/documentation/collaborator)

#### Articles/Blog posts:

1. Edge Side Includes abused to enable RCE [https://portswigger.net/daily-swig/edge-side-includes-abused-to-enable-rce](https://portswigger.net/daily-swig/edge-side-includes-abused-to-enable-rce)
2. Apple News [https://www.pbwcz.cz/Articles of english/apple.html](https://www.pbwcz.cz/Articles%20of%20english/apple.html)

#### Reference Documentation:

1. OWASP Top Ten Project [https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
2. CWE/SANS TOP 25 Most Dangerous Software Errors [https://www.sans.org/top25-software-errors/](https://www.sans.org/top25-software-errors/)
3. The 2019 Hacker Report [https://www.hackerone.com/sites/default/files/2019-02/the-2019-hacker-report_3.pdf](https://www.hackerone.com/sites/default/files/2019-02/the-2019-hacker-report_3.pdf)
4. Habit, Attitude, and Planned Behaviour [https://www.tandfonline.com/doi/abs/10.1080/14792779943000035](https://www.tandfonline.com/doi/abs/10.1080/14792779943000035)
5. CWE List Version 3.4 [https://cwe.mitre.org/data/index.html](https://cwe.mitre.org/data/index.html)
6. HTTP Extensions for Web Distributed Authoring and Versioning (WebDAV) [https://www.ietf.org/rfc/rfc4918.txt](https://www.ietf.org/rfc/rfc4918.txt)
7. HTTP PUT request method [https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PUT](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PUT)
8. HTTP PATCH request method [https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PATCH](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PATCH)
9. JSON Web Token (JWT) [https://tools.ietf.org/html/rfc7519](https://tools.ietf.org/html/rfc7519)
10. JSON Web Token Best Current Practices [https://tools.ietf.org/html/draft-ietf-oauth-jwt-bcp-07](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bcp-07)
11. Why Johnny Can’t Pentest: An Analysis of Black-box Web Vulnerability Scanners[https://sefcom.asu.edu/publications/black-box-scanners-dimva2010.pdf](https://sefcom.asu.edu/publications/black-box-scanners-dimva2010.pdf)

#### Exploits:

1. File Content Disclosure on Ruby on Rails [https://github.com/mpgn/CVE-2019-5418](https://github.com/mpgn/CVE-2019-5418)
2. CVE-2018-4124 [https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-4124](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-4124)

#### Conference Presentations:

1. Breaking Parser Logic - Take Your Path Normalization Off and Pop 0days Out - Orange Tsai,Blackhat USA 2018 [https://i.blackhat.com/us-18/Wed-August-8/us-18-Orange-Tsai-Breaking-Parser-Logic-Take-Your-Path-Normalization-Off-And-Pop-0days-Out-2.pdf](https://i.blackhat.com/us-18/Wed-August-8/us-18-Orange-Tsai-Breaking-Parser-Logic-Take-Your-Path-Normalization-Off-And-Pop-0days-Out-2.pdf)
2. Unraveling Unicode: A Bag of Tricks for Bug Hunting - Chris Weber [https://www.blackhat.com/presentations/bh-usa-09/WEBER/BHUSA09-Weber-UnicodeSecurityPreview-SLIDES.pdf](https://www.blackhat.com/presentations/bh-usa-09/WEBER/BHUSA09-Weber-UnicodeSecurityPreview-SLIDES.pdf)
3. Redefining Defense - Saumil Shah [https://conference.hitb.org/hitbsecconf2017ams/materials/KEYNOTE 1 - Saumil Shah - Redefining Defense.pdf](https://conference.hitb.org/hitbsecconf2017ams/materials/KEYNOTE%201%20-%20Saumil%20Shah%20-%20Redefining%20Defense.pdf)

#### Personal Mentions:

1. Saumil Shah @therealsaumil [https://twitter.com/therealsaumil](https://twitter.com/therealsaumil)
2. John Hawthorn @jhawthorn [https://twitter.com/jhawthorn](https://twitter.com/jhawthorn)
3. Cheng-Da Tsai @orange_8361 [https://twitter.com/orange_8361](https://twitter.com/orange_8361)
4. Chris Weber @w3be [https://twitter.com/w3be](https://twitter.com/w3be)
5. Binni Shah @binitamshah [https://twitter.com/binitamshah](https://twitter.com/binitamshah)
6. Dafydd Stuttard @dafyddstuttard [https://twitter.com/dafyddstuttard](https://twitter.com/dafyddstuttard)

#### Special Thanks
- Saumil Shah
- Hiren Shah
- Jigar Soni
- Aditya Modha
- Net Square Team :)

##### EOF
