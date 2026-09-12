# DevSecOps

## What is DevSecOps?

*   DevSecOps is the practice of integrating security into every stage of the DevOps lifecycle, including code, CI/CD pipelines, Infrastructure as Code (IaC), containers, and deployment configurations.

    It means security is not added at the end — it is built in from the beginning
* it's DevOps with Foucs on security&#x20;
* &#x20;traditionally,security was done in production enviroments suchs as firewalls,access lists,etc and we still do these things in production enviroment and insteading of thinking about security later what if we think about earlier like including security tools  in the developers enviroments like IDE that can help catchs security risks and it can helps catch bugs before a developer even commit thier code to github repo and having security checks in our pipeline because new security vulnerabilities are discoverd everyday&#x20;
* shifting security left: it's all about moving security as close to the developer as possible&#x20;
* Detecting  problems before shipping our service to production so if we are able to detect problems in our staging ,dev enviorments or even in Sw repo or in developer IDE&#x20;

## Why  DevSecOps?

* Early detection&#x20;
*   Cost savings because you detect early and get rid of that security vulnerability as soon as possible

    <figure><img src=".gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

## Implementing   DevSecOps  into pipeline&#x20;

Securing ci/cd pipelines&#x20;

<figure><img src=".gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

* So shifting the security left so   foucs on developer's IDE and how to secure this enviroment&#x20;
* so you can encourage the developers to use certian security tools that integrate into thier IDE so if they're using something like VSCode,There are a lot of adds-on that They can use like Static code analysis tools and you can also y do it in your souce code repos like running github actions that runs anytime developer submit pull  request you can have actions to run static code analysis and if it fails,it can prevent the pr from being merged &#x20;
* Static code analysis:  Check security vulnerabilities without executing or running  the code such as general coding errors like sql injections,buffer overflows
* Running static code analysis must happen periodically on your souce code repos because code was secure yesterday doesn't mean it's secure today because new security vulnerabilities are found everyday
* Implement secret scanners to scan your code for secerts like make sure you'haven't put an api key or database password into your code and push that to github repo so developers have to run this in thier IDE because once they submit the pull request especially if pull request is going to public repos even if it's not merged into the main branch of the code it's still inside the repo as commit so catching these errors before even pushing the code is really important amd enforcing this will happen by a tool called pre-commit so anytime developers are trying to commit code it's going to run a set of rules that  you have defined so you can define a set of role in your github repos so when the developers tries to commit to that pre commit will stop them if any of the rules are broken so you can have pre-commit run some sorts of secret scanner and if detects any secerts it will prevent the developers from commiting the code and pushing it the repos and you can aslo have precommit run linting rules which will basically  make sure the developers is conforming  to certain coding practices
* you can do mutiple static  code checks  you can run like software compostion analysis (SCA) ,static application security testing(SAST)
* &#x20;**software compostion analysis (SCA)** is all about checking the libraries that you're using and it's taking a look to all open souce libraries that your code uses and checks those for vulnerabilities
* **static application security testing(SAST):** checking your souce code for coding errors&#x20;
* These checks must heppen periodically in developer's IDE, Source code stage as well as build stage  why because you want to detect the problems earlier and catch them and also as your code moves through your  pipeline new securiy  vulnerabilities can be introduced so we inlcluding security stactic checks  into the source stage and build stage&#x20;
* Build stage related item which is **The software bill of materials (SBOM):** it's gonna be an artifact that you generate during the build stage and it's a list of all thired parties libararies that your application is using and this is important for application like government or healthcare because a lot of vulnerabilities that are found in software come from third party libraries that sw is using so having of this  list of different components that your sw dependent on   is a good security practice and you have  to generate for security teams&#x20;
* **The software bill of materials (SBOM)** works closly with your  software compostion analysis (SCA) tools insteading for running these tools  directly aganist your souce code you can run them aganist  your **e software bill of materials (SBOM) and** running this must  happen periodically because new vulnerabilities are found everyday&#x20;
* Take your SBOM store them in something like s3 buckets so you can have a look at them and aduit  them preiodically so and SBOM Act as single souce of truth that you can go to to find out which libraries you're using when the new vulnerabilities come out so you will go  SBOM and do search if're using this libaray if you're which version it is&#x20;
* During the test stage you're gonna to do Dynamic application security testing
* Dynamic application security testing: checking your code during its runtime to find out problems and there are lot of different tools that allows us to do this kind of test but it's really depend on your coding libraries and what your application does for exmaple if you're running something like an nginx server so there are certain libraries that you want to run to do your dynmic application security testing&#x20;
* Some exmaples about Dynamic application security testing that can check for things like sql injection,cross site scripting,session hijacking . all these things that we can't test by just looking at the code and we need the application running to run these tests&#x20;
* Common  Dynamic application security testing tool is OWASP ZAP
* release stage at that point we're shipping our software out and there is not really any security tasks t because everything happens before that but what happens after this release stage is checking your enviorments so running image scanners in these enviorments and running vulnerabilitiy scanners in your production, development ,staging enviorment are thing you will do as well as scanning your image repos because we're shipping our images to to something like dockerhub or AWS ECR And you can enable security on them  to scan your images periodically and in aws there is native image scanning enabled on ECR but there are lot of third party sw that you can point to your image repos and it will scan your image for  vulnerabilities
* there is a lot addtional thing you can do  and   implement

<figure><img src=".gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>
