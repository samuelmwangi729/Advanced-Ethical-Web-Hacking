# Advanced Web Hacking for INT 406 Practical Activity
# _Authored By_ ![Author](https://img.shields.io/badge/Samuel-Mwangi-red)
## Student Details

|Field|Details|
|-----|-------|
|Student Name|Samuel Mwangi|
|Student ID|**2025/INT/12534**|
|Course Code| INT 406|
|Course Title|Advanced Ethical Hacking|
|Instructor|Mr Faisal Imran|
|Submitted On| 01/11/2026|

---
# Executive Summary
In this write up,a security assessment was carried out on the vulnerable **FAMJIS BANK** application is provided.The web application security test involved identifying, testing, and documenting any web application vulnerabilities in order to discover any hidden flag in an authorized environment. This security test was done to determine if the web application was vulnerable to common web application vulnerabilities which are:
- Insecure Direct Object Reference(IDOR)
- JSON Web Token Manipulation
- Stored XSS and 
- Unrestricted / Insecure File Upload which leads to **Remote command execution**. 

The assessment was carried out by using penetration testing utilities such as:
- Burp Suite (Where necessary)
- cURL used to make requests
- jwt_tool used to jwt manipulation
- other relevant utility(s).

The primary objective of the assessment was to uncover hidden web application vulnerabilities where the documentation is done on whether there was exploiitation of the vulnerability and determine whether they could be exploited to gain unauthorised access to sensitive data. Each vulnerability was investigated through controlled exploitation with a corresponding proof of concept with **flags** captured as evidence.
This report also gives recommendations on how to fix the vulnerabilities which is used to strengthen authentication and authorisation mechanisms.

In general,this assessment shows how weaknessses in web application security controls can expose sensitive information and compromise the confidentiality, integrity and the security of the web application.


## Introduction
The **FAMJIS** vulnerable web application was used to perform the security testing as part of INT 406 ICDFA Ethical hacking and Digital Forensics internship under the instructor **_Mr Faisal Imran_**. This activity provided a practical opportunity to apply web application penetration testing techniques in an authorised environment.

The assessment was focused in identifying and exploiting common web application vulnerabilities IDOR,JWT manipulation,XSS and Insecire file upload leading to Remote command execution which expose the user data to unauthorised access.

This lab activity clearly indicates how these vulnerabilities are expoloited together with a proof of concept captured as flags. The findings are documented together with its severity and the recommended actions to protect the web application from any attacks arising from these vulnerabilities.

## Lab Objectives

The primary objective of this lab activity was to develop field experience on web application penetration testing helping the student identify, document the findings and present them in a professional report. The specific objectives were:
- identify and exploit **IDOR Vulnerabilities** 
- Exploit weak JWT signing keys by cracking the key
- Test for stored XSS
- Set up a web server to collect stolen session keys
- Assess file upload Security
- Capture the flags as proof of exploitation
- Evaluate security implication of the vulnerabilities
- Recommend protective measures against weak security policies implementations
- Prepare a professional findings report

## Tools and Resources required
|Tool|Use| 
|---|---|
|Docker & Docker Compose| Hosts Containers to run the vulnerable application|
|FAMJIS Bank application|A vulnerable banking web application|
|Burp Suite| A web application toolkit for web penetration testing|
|Hashcat| Used to crack passwords based on hashes fed|
|curl|A commandline tool used to make HTTP requests|
|jwt_tool|A python tool used to tamper with a JWT|

## Rules of Engagement
The rules of engagement were set so that they would guide the penetration tester on how to engage with the target without affecting genuine users or even bringing the target offline. In this lab activity, the rules of engagements were:
- Attack your own **FAMJIS BANK** instance.
- Do not attack the student portal, the hosting or any other student instance or any system on the public internet.
- Do not run destructive Denial of Service attack.
- Automated scanning is allowed against the authorised environment only.
## Methodology
The steps followed to identify and exploit the vulnerabilities in this lab activities were clearly outlined in the laboratory guide. The solution and proof of exploitation follows a structured methodology where after the vulnerability has been identified, its only then exploitation and proof of exploitation can happen where the procedure to extract the flag documented.The steps followed to proof exploitation and capturing the flag were:
### 1. Installing **_FAMJIS Banking_** web application
This step involved acquiring the copy of the vulnerable application from [Github](https://github.com/Faisalauchan/famjis-bank-lab) where the installation and running is done under containers. Since the lab is a simulation and the flags are to be shown as proof of exploitation, the student and the password salts are set in the ```.env``` file created in the base of the project.
<img src="./Screenshots/environment_variables.png">
[Environment variables for the Banking application](./Screenshots/environment_variables.png) 

The command run is ```docker compose up -d``` which builds the project ready for access via the web browser.
<img src="./Screenshots/Building_Environment.png">
[A Screenshot building the application in the terminal](./Screenshots/Building_Environment.png)<br>
After the above steps were followed, the web application was now accessible via the browser ready for exploitation.

### 2. Web application Exploitation
In this phase, the web application is accessed via the browser on port ```8081``` after running the command ```sudo docker compose up``` to run the application as indicated in the screenshot below.
<img src="./Screenshots/running.png">
[A screenshot showing the containers running](./Screenshots/running.png). 

The application is then accessed via the browser and the credentials used to access the dashboard are ```you:password123``` as displayed in the login page below.
<img src="./Screenshots/login%20screen.png">
[A screenshot showing login credentials](./Screenshots/login%20screen.png)<br>
The credentials proof to be working and I was able to login as a demo customer **_Amara_** as shown in the screenshot below.
<img src="./Screenshots/dashboard.png">
[A screenshot showing Amara Dashboard](./Screenshots/dashboard.png). <br>
After the access to the dashboard has been confirmed, the hunting for the flags started based on what the demo user is actually allowed to do. These hunts were done in form of attacks which are described in details in <a href="#results-and-findings"> Results and Findings Section.</a>

## Results and Findings
This section explains in details how the vulnerabilities were identified and exploited together with the tools used to exploit them. Screenshots and commands used will be used as proof of exploitation to confirm that the exploitation happened. The attacks happened sequentially from **IDOR**,**Insecure File Uploads**, **JWT Manipulation** and lastly **Stored XSS** which allowed us to post the **admin** back to a listener running in a different port in the same host machine.