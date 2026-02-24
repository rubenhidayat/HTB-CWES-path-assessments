# File Upload Attack Skill Assessment

## Scenario

You are contracted to perform a penetration test for a company's e-commerce web application. The web application is in its early stages, so you will only be testing any file upload forms you can find.

Try to utilize what you learned in this module to understand how the upload form works and how to bypass various validations in place (if any) to gain remote code execution on the back-end server.

## Question

Try to exploit the upload form to read the flag found at the root directory "/". 

## Answer

1. Browse the web application and find the file upload functionality, it is located in `http://154.57.164.75:31990/contact/` ![alt text]({3EE18FE1-C292-4062-AA80-A9C2631BBD48}-1.png)

2. Send a legitimate file/image to see the positive response