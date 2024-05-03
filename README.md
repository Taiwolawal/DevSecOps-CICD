## devsecops-project

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f02316ac-f2bf-494a-beee-7af59075ccac)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1a7f6359-76d1-473d-af39-0b888b724548)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/dc3a241a-9231-4cef-a3e1-53d6c4ed3b34)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/950896f3-f184-44fc-a43c-b5e94c31091c)



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
We need to scan for all the dependencies and libraries in our code, to ensure there are no vulnerabilities in them. We will add another job to do SCA on our code in the node_module folder where our dependencies and check for the CVE's

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/03079c7b-14f1-4194-a417-fff337049fb5)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/9422449e-2e8f-4c36-aacf-1a42752e19d1)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1289a81b-9d11-4095-a5d4-40c66dad51fe)

# Build Image
Before we build our image, we need to ensure our dockerfile follow best practice when creating an image such as:
- Avoid using latest images
- Run as a Non-Root User

We will use OPA/confest to run a static test on our dockerfile using rego to confirm. We create a file called opa-docker-rego-security.rego and specified all the conditions we want our dockerfile to meet

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f482e056-64fb-4f52-a1cf-667c6628c7e8)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/b0b0bcd8-b488-4b95-b511-79a3cd786a38)

Running this rules against our Dockerfile

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/11b4e17c-3382-4ea0-b05a-042d4687efd2)

We will update our code with 

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/d9231f8a-0f7b-4241-849f-365336a68d99)

And got this result stating we are using latest image and running the container as a root user, which are the conditions we specified in the policy

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/8deff12b-cee5-4df6-8ff5-1a9a1d7f29d3)


Now lets update the dockerfile with the right 

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f8bd947a-8433-466b-87fd-f4ab063e2444)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/356c5b22-26be-4885-b5aa-1fb193a8372e)

Next, we need to build our image and scan them

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/aa3d5c50-ee68-4f41-88ee-b9778a5c5672)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/b0508352-7b55-4182-ad8c-b6c8b862db10)

Update the necessary variables for the CICD pipeline

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/31d8d558-30dd-436c-8ca4-2cabbcf039d9)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/6f67a5ca-9bbe-4294-9509-c060e5388776)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ba6624a4-a196-454b-9b85-7c4649fbc8a3)


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/c3d583c4-21ba-40d6-96c2-4012c30f6756)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e26e754f-2ba1-41d0-a188-63a4cff211f4)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/2d4accd9-59ed-4a6b-8128-5c7381e7d1be)


# Vulnerability Management

Now that we have done some tests, we should be able to upload the results for a central post where we can audit each vulnerability. This leads us to Defectdojo which will be used to visualize all our scan results. Now we need to update our code to update results from each tool. We are updating the script to use a particular output format to save the result with a given name and save it as an artifact (which can be referenced in the pipeline) with a given name.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/777097d5-734d-4a80-92d7-4452807d266f)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/39220c74-2a7d-4bf1-957a-48410ae87f40)

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

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/dfc6a839-4d9a-4eee-9ebc-6bddc8104aa0)


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/17721afb-d0e0-4e8f-b6ac-54f6b119fc68)


We will use python script to upload results on defectdojo
- Get Token

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cc6faee3-2d74-429c-8d4e-9c70e06a3c18)

- Click on Generate New Key

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f98d7ad0-f14e-40b1-878d-84b35034f684)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/5ca49842-4c29-4085-a157-fc3fa83f49ff)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/96a3b21c-ef5d-4039-81f1-b377fbb211f6)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/a5ec6d62-3dea-4b69-99f5-703c88788e25)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e339c52d-78ee-4800-a2f8-55352d00d94a)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/bde5cad5-422c-4510-a868-57d07e8c612d)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/3bfda682-adf6-46b1-9137-922d9fd8c398)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e7573299-eef7-451a-b934-c95d8a9dfc0a)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/89eae17b-60b2-4479-b5ac-5d7987da5583)


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/fbe47c4c-7d21-4d7e-a2a7-e972dbdb978d)


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/80b48ad4-4bb1-4f87-8150-ef69ce45b541)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/045c5ee5-e3d7-4bb9-a63d-35802dc78ca4)

Gitlab runner instance

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/95a3fd8f-d940-4b61-ab39-8eb4e2e12431)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/4429c583-3939-41ea-b031-e80d88932193)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1fef15e6-ef7b-40ee-b878-77fc84951162)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cad9ea70-9a08-4770-9f1f-55a71479c937)

```
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
sudo apt-get install gitlab-runner


```

```
sudo apt update
sudo apt  install awscli -y
sudo apt  install docker.io -y
sudo usermod -aG docker ubuntu
sudo usermod -aG docker gitlab-runner
```

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/3257478b-f8f7-4618-961c-12830f50c462)


Restart the server

```
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/27e3c9e7-ff28-4d9c-bf93-3ee2e07d3e78)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/2143fffb-b8fc-42e7-86c3-193c98f75145)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/680fcc0f-5c80-4241-bbec-4471070d7677)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ab825f66-a8a2-4900-a308-614dd89d5dbd)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/70e97674-1f3a-4f0e-80ab-9a1e2eb25a21)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e439240d-1150-4bde-a406-44ae38542479)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1d3f6ea8-61b6-4d91-ac13-9fd6bb24c41d)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/de09bfed-30f0-41d0-9966-a5463123045b)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/82aa54a3-6311-4a0a-9aa0-137a3b522d51)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/042b2fdf-e273-4811-8229-90a648df5a6b)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cc5f7f32-e3a8-4b7d-964a-2895ec454479)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/d7ef2d83-7024-496c-b835-8764d045fab9)







