## devsecops-project

Setting up our CI pipeline, we will want to embed security checks in our software development lifecycle (SDLC) such as our CICD pipeline

# Secret scanning
Different tools can be used to scan for sensitive data in our code. We will be using gitleaks to check any hardcoded data 

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/8acfde66-ee93-440e-905e-865d0b48bc23)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/4d7e3545-1896-4f48-992a-3ed8f8bf1ec3)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/c43bbefc-2b0b-4a79-8f1b-c5f6bdd182d5)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/00b70251-dc12-4d75-b665-fa6317fcbd29)

Check the documentation on how to use gitleaks properly

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/6ae43913-7329-448c-b328-128cc5b2f978)

It also checks git commits to see if you have committed any sensitive data.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/6324b8bf-698d-438b-9c5a-cbd2f37c7b47)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ef1990fd-ef3c-41f0-a75f-54db2ba4bcbb)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/28e515a4-0d69-4023-a701-9a9a68f1c6e7)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e0053049-0399-45e8-ba4d-d02f44b69170)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/b9f5d833-169b-4848-8880-e0fdd6e3cb1c)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/487de8a3-d558-465d-8a01-f2475f59615f)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/62acb861-d7ac-4b03-a9fe-e7f5b0197c5e)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/82cb2dda-cdb3-4c4c-810c-87a7c803c8c4)

Rectifying false positives

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/9d9fde89-fa4a-44c0-9685-dbc9729fb671)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e024b377-e809-4716-9417-82994f819ef2)

# Static Code Security Testing
In identifying vulnerabilities in source code, there is a need to use multiple tools to achieve an overall  view of vulnerabilities. we will be using njsscan and Semgrep respectively to do that

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/98ab1997-c9ed-4e2c-85fe-fde7cce5bb74)

The result of the njsscan is showing one vulnerability and indicating success when we have the severity showing warning, we need to adjust the code

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/3ce52c09-a67a-4934-bb86-3253eec51c95)

Semgrep scan is showing close to 23 vulnerabilities

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/aea80a10-f81a-4bda-b824-37424639c6ba)

Updating njsscan script ```njsscan --exit-warning . ``` to ensure it fails. Now we have showing exit code 1

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1ca0768e-f877-4206-bbf4-d0067ebe8cf7)

# Software Composition Analysis
We need to scan for all the dependencies in our code, to ensure there are not vulnerabilities in them

# Vulnerability Management

Now that we have done some tests, we should be able to upload the results for a central post where we can audit each vulnerability. This leads us to Defectdojo which will be used to visualize all our scan results. Now we need to update our code to update results from each tool. We are updating the script to use a particular output format to save the result with a given name and save it as an artifact (which can be referenced in the pipeline) with a given name.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/777097d5-734d-4a80-92d7-4452807d266f)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e77d6d69-74f1-4049-9a80-fbc07b512554)

Making use of defectdojo demo server to ```demo.defectdojo.org ``` . The credentials are ``` admin / 1Defectdojo@demo#appsec ```.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/392c3806-3313-46b5-924a-8c7c6de3979c)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/80b6701c-e92e-430f-b7cc-fe8ed32d64a2)

Now to upload our results to defectdojo

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/546fd26f-680b-4f68-872f-8bc8001c28f7)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e7b0d5c3-5aa3-48ee-ae2c-832081157c8b)

Click on the product type "DevSecOps"

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f45d0fd1-3298-4aa6-ae64-0aabb93ca9a4)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/eda57fe0-f5c7-478a-8688-1855b824cda3)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e476fbeb-c16b-4a10-b5f2-9205755c51f6)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/8a5607b3-6b8f-4503-a2e7-b4aa27ccdcac)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/22758e1a-818a-4ebb-b72b-3320c814067a)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cfb73db2-dfb1-4add-a7ed-580b99bf9e8c)

# Automate Uploading Security Scan Results

We will use python script to upload results on defectdojo
- Get Token

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cc6faee3-2d74-429c-8d4e-9c70e06a3c18)

- Click on Generate New Key

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f98d7ad0-f14e-40b1-878d-84b35034f684)

































