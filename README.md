## (Currently still working on the documentation)

What is DevSecOps?

DevSecOps stands for Development, Security, and Operations. It is an approach that integrates security practices into the DevOps process, ensuring that security is considered at every stage of the software development lifecycle (SDLC). The goal is to build a culture of shared responsibility for security among all participants in the development and operations processes.

The idea is to provide layered security, such that if attackers gain access to some part of the system they should not get access to your whole system i.e. reducing the blast surface. They include:

- Access Management
- Network security
- Application security
- Logging
- Monitoring

This project will be focused on CICD part of software development and embed security checks in our pipelines. The tools used for the projects are:

- CI: Gitlab-CI
- Secret Scanning: Gitleaks
- 

# Secret scanning
We will start scanning our application code for sensitive data, secrets, credentials etc. Different tools can be used to scan for sensitive data in our code. We will be using gitleaks to check any hardcoded data. 

We will start by installing the tool locally on our system and use pre-commit hook and ensure before we commit the code it flags off if there are any sensitive data in our code. The goals is to:

- Automatically run scan before code is pushed to remote Git repository
- Prevent any hard-coded secrets in the Git repository

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/6324b8bf-698d-438b-9c5a-cbd2f37c7b47)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ef1990fd-ef3c-41f0-a75f-54db2ba4bcbb)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/28e515a4-0d69-4023-a701-9a9a68f1c6e7)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e0053049-0399-45e8-ba4d-d02f44b69170)

<img src="image/image.png">

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/c43bbefc-2b0b-4a79-8f1b-c5f6bdd182d5)

The screenshot above shows that we have sensitive data in our code, as a result, you will not be able to commit the code as planned. We need to update this also in our pipeline


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/00b70251-dc12-4d75-b665-fa6317fcbd29)

Check the documentation on how to use [gitleaks](https://github.com/gitleaks/gitleaks) properly

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/6ae43913-7329-448c-b328-128cc5b2f978)

We can see the pipeline failed

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/487de8a3-d558-465d-8a01-f2475f59615f)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/62acb861-d7ac-4b03-a9fe-e7f5b0197c5e)

<!-- ![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/82cb2dda-cdb3-4c4c-810c-87a7c803c8c4) -->

Looking at the result we got from the scan, we have some false postives. False positive is when a scanning tool incorrectly flags a security vulnerability. As a result we need to go over the scan results to be sure, the result we are getting in line with what we are expecting. 

We have a folder named test which contains some dummy sensitive data, which we used for our yarn_test. We will exempt it from the scan, thereby creating a ``.gitleaks.toml`` file and specifying the folder or path to avoid while scanning. If we run the pipeline again, you will see we now have 15 warnings compare to 46 earlier.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/9d9fde89-fa4a-44c0-9685-dbc9729fb671)

![alt text](image-1.png)


# Static Code Security Testing (White Box Testing)
- Static code analysis (app is not running)
- Identifies security vulnerabilities in app’s source code, configuration files etc.
- Looks for common coding errors, deviations from secure coding practices etc.
- Can provide precise information about the security flaws. Also helpful for addressing issues related to code quality and design flaws.
- its linmitation does not capture vulnerabilities that only surface when interacting with the application (will be addressed in DAST)

In identifying vulnerabilities in source code, there is a need to use multiple tools to achieve an overall  view of vulnerabilities. we will be using [njsscan](https://hub.docker.com/r/opensecurity/njsscan) and [Semgrep](https://semgrep.dev/docs/semgrep-ci/sample-ci-configs#gitlab-cicd) respectively to do that

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/98ab1997-c9ed-4e2c-85fe-fde7cce5bb74)

The result of the njsscan is showing one vulnerability and indicating success when we have the severity showing warning, we need to adjust the code

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/3ce52c09-a67a-4934-bb86-3253eec51c95)

Semgrep scan is showing close to 23 vulnerabilities

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/aea80a10-f81a-4bda-b824-37424639c6ba)

Updating njsscan script ```njsscan --exit-warning . ``` to ensure it fails. Now we have showing exit code 1

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1ca0768e-f877-4206-bbf4-d0067ebe8cf7)

# Software Composition Analysis
- Check third-party and open-source libraries and frameworks
- SCA tool goes through the dependencies of your application and checks whether any known vulnerabilities for that dependency and the specific version you use

We will be using ```retire.js```  to scan for all the dependencies and libraries in our code, to ensure there are no vulnerabilities in them. We will add another job to do SCA to scan codes of libraries in the node_modules folder where our dependencies are.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/03079c7b-14f1-4194-a417-fff337049fb5)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/9422449e-2e8f-4c36-aacf-1a42752e19d1)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1289a81b-9d11-4095-a5d4-40c66dad51fe)

From the screenshots above, we can see all the vulnerabilities.

# OPA (Open Policy Agent) Conftest
- The Open Policy Agent is an open source, general purpose policy engine that unifies policy enforcement across the stack.
- Using Conftest you can write tests for kubernetes Configuration, dockerfile, terraform code etc.
- Conftest uses the Rego language from Open Policy Agent for writing the assertion

Before we build our image, we need to ensure our dockerfile follow best practice when creating an image such as:
- Avoid using latest images
- Run as a Non-Root User etc.

We will use OPA Confest to run a static test on our dockerfile using Rego language from Open Policy Agent to confirm. We create a file called opa-docker-rego-security.rego and specified all the conditions we want our dockerfile to meet

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f482e056-64fb-4f52-a1cf-667c6628c7e8)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/b0b0bcd8-b488-4b95-b511-79a3cd786a38)

Running this rules against our Dockerfile

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/11b4e17c-3382-4ea0-b05a-042d4687efd2)

We will update our pipeline with OPA Conftest  

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/d9231f8a-0f7b-4241-849f-365336a68d99)

The result we got states we are using latest image and running the container as a root user, which are the conditions we specified in the policy.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/8deff12b-cee5-4df6-8ff5-1a9a1d7f29d3)


Now lets update the Dockerfile 

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f8bd947a-8433-466b-87fd-f4ab063e2444)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/356c5b22-26be-4885-b5aa-1fb193a8372e)


# Image Scanning (Trivy)
We will use a tool called [trivy](https://aquasecurity.github.io/trivy/v0.52/) for scan our images
- Scan every image layer for known vulnerabilities
- Scan files inside container images for vulnerabilities, exposed Secrets, misconfigurations
- Scans for known vulnerabilities (CVEs)
- Scans vulnerabilities in the application dependencies

Next, we need to build our image and scan them. 

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ba6624a4-a196-454b-9b85-7c4649fbc8a3)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/c3d583c4-21ba-40d6-96c2-4012c30f6756)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e26e754f-2ba1-41d0-a188-63a4cff211f4)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/2d4accd9-59ed-4a6b-8128-5c7381e7d1be)

From the screenshot above, we can see all the vulnerabilities in our images.

# Vulnerability Management (DefectDojo)
- Centrally manage vulnerability findings of different tool
- Triage vulnerabilities and push findings to other systems
- Enriches and refines vulnerability data

Now that we have done some tests, we should be able to upload all the results from the various scan to a centralised platform, where we can audit each vulnerability. This leads us to [Defectdojo](https://documentation.defectdojo.com/) which will be used to visualize all our scan results. Now we need to update our code to update results from each tool. We are updating the script to use a particular output format to save the result with a given name and save it as an artifact (which can be referenced in the pipeline) with a given name.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/777097d5-734d-4a80-92d7-4452807d266f)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/39220c74-2a7d-4bf1-957a-48410ae87f40)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e77d6d69-74f1-4049-9a80-fbc07b512554)

Making use of defectdojo demo server ```demo.defectdojo.org ``` . The credentials are ``` admin / 1Defectdojo@demo#appsec ```.

Now, lets setup DefectDojo to upload our scan results

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/392c3806-3313-46b5-924a-8c7c6de3979c)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/80b6701c-e92e-430f-b7cc-fe8ed32d64a2)


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/546fd26f-680b-4f68-872f-8bc8001c28f7)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e7b0d5c3-5aa3-48ee-ae2c-832081157c8b)

Click on the product type "DevSecOps"

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f45d0fd1-3298-4aa6-ae64-0aabb93ca9a4)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/eda57fe0-f5c7-478a-8688-1855b824cda3)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e476fbeb-c16b-4a10-b5f2-9205755c51f6)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/8a5607b3-6b8f-4503-a2e7-b4aa27ccdcac)

<!-- ![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/22758e1a-818a-4ebb-b72b-3320c814067a) -->

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cfb73db2-dfb1-4add-a7ed-580b99bf9e8c)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/408ce951-5fd0-411e-b22d-d1e4d2e3cde8)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/b30e7f7e-0ef3-485c-8746-123b850f4d9d)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/96a3b21c-ef5d-4039-81f1-b377fbb211f6)

<!-- ![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/b294b4c7-bb68-410c-b5f8-be39beb4639d) -->


# Automate Uploading Security Scan Results

<!-- Now lets write a python code to upload the results. 

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/dfc6a839-4d9a-4eee-9ebc-6bddc8104aa0) -->


<!-- ![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/17721afb-d0e0-4e8f-b6ac-54f6b119fc68) -->


We will use python code to upload results on defectdojo
- Get Token

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cc6faee3-2d74-429c-8d4e-9c70e06a3c18)

- Click on Generate New Key

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f98d7ad0-f14e-40b1-878d-84b35034f684)

We have our python code ```upload-reports.py```consisting of the API KEY token and updating the engagement value to 14 in the data field

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/96a3b21c-ef5d-4039-81f1-b377fbb211f6)

![alt text](image-4.png)

<!-- ![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/17721afb-d0e0-4e8f-b6ac-54f6b119fc68) -->

Update the pipeline to upload scan reports

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/dfc6a839-4d9a-4eee-9ebc-6bddc8104aa0)

<!-- ![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/5ca49842-4c29-4085-a157-fc3fa83f49ff)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/96a3b21c-ef5d-4039-81f1-b377fbb211f6) -->


Now we have uploaded all our scan results on DefectDojo

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/a5ec6d62-3dea-4b69-99f5-703c88788e25)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e339c52d-78ee-4800-a2f8-55352d00d94a)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/bde5cad5-422c-4510-a868-57d07e8c612d)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/3bfda682-adf6-46b1-9137-922d9fd8c398)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e7573299-eef7-451a-b934-c95d8a9dfc0a)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/89eae17b-60b2-4479-b5ac-5d7987da5583)


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/fbe47c4c-7d21-4d7e-a2a7-e972dbdb978d)


Now, we can easily audit all the vulnerabilities and have a very good understanding on common ones you are faced with and address ways of minimizing it.

# Provision Server (App-Server & Gitlab-Runner)

Next, we will deploy the application on a server (App-Server) to run DAST and also provision a server (Gitlab-Runner) to have our Self-managed Gitlab runner to run our work loads. The idea of running your pipeline workloads on a dedicated Gitlab runner is to allows for better control over security policies and compliance with internal security requirements. You can ensure that sensitive data and credentials are handled securely.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/80b48ad4-4bb1-4f87-8150-ef69ce45b541)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/045c5ee5-e3d7-4bb9-a63d-35802dc78ca4)

Gitlab runner instance

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/95a3fd8f-d940-4b61-ab39-8eb4e2e12431)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/4429c583-3939-41ea-b031-e80d88932193)




![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1fef15e6-ef7b-40ee-b878-77fc84951162)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/cad9ea70-9a08-4770-9f1f-55a71479c937)




Set-up Gitlab-runner on our Gitlab-CI repo

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/f02316ac-f2bf-494a-beee-7af59075ccac)

Click on New Project Runner

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1a7f6359-76d1-473d-af39-0b888b724548)

In the tags section, specify ec2, shell. Which will be what we use to reference the runner when we want to use it in the pipeline.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/dc3a241a-9231-4cef-a3e1-53d6c4ed3b34)


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/950896f3-f184-44fc-a43c-b5e94c31091c)

SSH into Gitlab-runner server to run the [installation commands](https://docs.gitlab.com/runner/install/linux-repository.html) . Copy the following command below on the Gitlab-Runner server.

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

Now we have setup our gitlab runner to handle workloads of our pipeline.


We can install trivy also on the Gitlab-Runner server to run the trivy pipeline stage. Restart the server and lets install trivy on it to do some heavy lifting for us.

```
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

# Secure access to our servers (AWS SSM)
To secure access to our servers (App-server & Gitlab-runner server), we need to close SSH port. To connect to our instances securely we will be using using AWS system manager to provide a safe and secure access to our servers. 

Connect to the Gitlab-Runner server via SSH for now and run the following command ``` sudo systemctl status snap.amazon-ssm-agent.amazon-ssm-agent.service ``` to confirm if we have amazon-ssm-agent running on the server, which it is.


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/27e3c9e7-ff28-4d9c-bf93-3ee2e07d3e78)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/1d3f6ea8-61b6-4d91-ac13-9fd6bb24c41d)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/de09bfed-30f0-41d0-9966-a5463123045b)


Attach SSM Role to the servers, so that the servers will be allowed to be managed by SSM.

Create Role for both Gitlab-Runner and App-Server instances. Below is Gitlab-runner setup (create role for App-Server also)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/2143fffb-b8fc-42e7-86c3-193c98f75145)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/680fcc0f-5c80-4241-bbec-4471070d7677)

Attach AmazonSSMFullAccess and AmazonEC2ContainerRegistryFullAccess policy

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ab825f66-a8a2-4900-a308-614dd89d5dbd)

Attach the role to the Gitlab-Runner server

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/70e97674-1f3a-4f0e-80ab-9a1e2eb25a21)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e439240d-1150-4bde-a406-44ae38542479)


Perform the same for App-server. Moving forward we should remove port 22 from both servers.

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/d7ef2d83-7024-496c-b835-8764d045fab9)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e675a8b9-198f-4727-b376-2da99eeb0b2d)

Now lets connect to the Gitlab-Runner via session-manager.

![alt text](image-5.png)

![alt text](image-6.png)

# Dynamic Application Security Testing (Black Box Testing)
- Testing the app’s running instance or deployed version
- Simulating security attacks and analyzing behavior and responses in real-time
- Does not require access to the code

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/3c7ffc88-7caf-403e-9b9c-d9000402792f)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/69daab95-24a6-4d55-a673-c900a2318d51)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/e888f82c-9318-4fb1-b364-813cd9820603)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ced8a73f-c1ad-4977-85d3-e16077216e95)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/48ba9ceb-898d-43b9-93b9-31e16c2c2d3d)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/ff63201d-af05-4760-823a-a68d01f566bb)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/fe7ed087-a2d6-4d3b-aa78-140bb95a8e73)






<!-- ![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/8acfde66-ee93-440e-905e-865d0b48bc23)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/4d7e3545-1896-4f48-992a-3ed8f8bf1ec3) -->


![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/aa3d5c50-ee68-4f41-88ee-b9778a5c5672)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/b0508352-7b55-4182-ad8c-b6c8b862db10)

Update the necessary variables for the CICD pipeline

![alt text](image-3.png)

![image](https://github.com/Taiwolawal/devsecops-project/assets/50557587/6f67a5ca-9bbe-4294-9509-c060e5388776)