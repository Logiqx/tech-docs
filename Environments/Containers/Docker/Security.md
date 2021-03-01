# Security - WIP

### I need to sort through this list of articles!



## Thoughts

Image builds

- Non-root user is important, especially for services
- Do not use environment variables for secrets because they are visible in the image history
- Do not include secrets in the images as they can easily be extracted, even if they're "deleted"
  - TODO: GitHub - Hmmm

Running Containers

- Do not run privileged containers
- Use --security-opt=no-new-privileges
- Do not mount docker.sock
  - TODO: I need an alternative approach for Jenkins - e.g. run ECS tasks

Host Configuration

- User namespaces are especially important on a shared host
  - e.g. They avoid anyone mounting the root filesystem of the host
- Container breakout is possible due to runC exploit prior to Docker 18.02



#### BLAH

Limit resources - CPU, Memory, Files (need to use Swarm) - <https://github.com/docker/compose/issues/4513>

Readonly file system? See OWASP cheat sheet

Protect Daemon socket <https://docs.docker.com/engine/security/https/>

User namespaces - <https://integratedcode.us/2015/10/13/user-namespaces-have-arrived-in-docker/>

​	<https://docs.docker.com/engine/security/userns-remap/>

Docker Bench - <https://github.com/docker/docker-bench-security>

​	Inspiration <https://www.cisecurity.org/benchmark/docker/>

AppArmor, SELinux, GRSEC



### BLAH

   <https://towardsdatascience.com/top-20-docker-security-tips-81c41dd06f57>

<https://docs.docker.com/engine/security/>

​	<https://docs.docker.com/engine/security/security/>

<https://github.com/OWASP/Docker-Security>

​	OWASP - [Docker_Security_Cheat_Sheet.md](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md)

<https://www.cimcor.com/blog/the-top-5-security-risks-in-docker-container-deployment>

<https://developers.redhat.com/blog/2016/02/24/10-things-to-avoid-in-docker-containers/>

​	Keep it small - <https://developers.redhat.com/blog/2016/03/09/more-about-docker-images-size/>

DETAILED - <https://sysdig.com/blog/7-docker-security-vulnerabilities/>

<https://nodramadevops.com/2019/06/running-docker-application-containers-more-securely/>

<https://techbeacon.com/security/get-real-container-security-4-rules-devops-teams-should-follow>

<https://towardsdatascience.com/top-20-docker-security-tips-81c41dd06f57>

<https://thenewstack.io/5-docker-security-best-practices/>

<https://www.equalexperts.com/blog/tech-focus/quick-wins-to-secure-your-docker-containers/>

<https://blog.aquasec.com/docker-security-best-practices>

<https://www.ontrack.com/uk/blog/the-world-of-data/using-docker-containers-beware-of-these-security-risks/>

<https://dev.to/petermbenjamin/docker-security-best-practices-45ih>

Why you don't need to run SSHd in your Docker containers - Docker Blog
https://blog.docker.com/2014/06/why-you-dont-need-to-run-sshd-in-docker/

Processes In Containers Should Not Run As Root - Marc Campbell - Medium
https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b

10 Docker Image Security Best Practices | Snyk
https://snyk.io/blog/10-docker-image-security-best-practices/

Is it possible to escalate privileges and escaping from a Docker container? - Information Security Stack Exchange
https://security.stackexchange.com/questions/152978/is-it-possible-to-escalate-privileges-and-escaping-from-a-docker-container

Abusing Privileged and Unprivileged Linux Containers
https://www.nccgroup.trust/uk/our-research/abusing-privileged-and-unprivileged-linux-containers/

The Dangers of Docker.sock
https://raesene.github.io/blog/2016/03/06/The-Dangers-Of-Docker.sock/

Isolate containers with a user namespace | Docker Documentation
https://docs.docker.com/engine/security/userns-remap/



### Sensitive Data / Secrets

[Manage sensitive data with Docker secrets](https://docs.docker.com/engine/swarm/secrets/)

[AWS Launches Secrets Support for Amazon Elastic Container Service](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-launches-secrets-support-for-amazon-elastic-container-servic/)

