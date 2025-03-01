# **Docker-Jenkins Workshop Report**

## **Miguel Gonzalez - A00395687**

## **First part: Jenkins installing and configuring**

It is necessary to create a network to communicate the jenkins container with the agent container:

```sh
docker network create jenkins
```

![alt text](images/image.png)

---

Now we create a new dockerfile where we leave the parameters to start the jenkins container:

![alt text](images/image-1.png)

---

We use the `docker build` command to create the image from the dockerfile:

![alt text](images/image-2.png)

---

Once the image has been created, we start a new container with this image, specifying also the network that was created in the first step:

![alt text](images/image-3.png)

---

Now we just go to the browser and access the link `localhost:8080`:

![alt text](images/image-4.png)

---

There are many methods to obtain the jenkins admin panel password. In my case, I decided to get it through the jenkins-ocean container logs:

![alt text](images/image-5.png)

![alt text](images/image-6.png)

---

Once the password has been obtained, all that remains is to install the recommended puglins and update the parameters to access the panel:

![alt text](images/image-7.png)
![alt text](images/image-8.png)
![alt text](images/image-9.png)

---

## **Second part: Our first agent**

First, an ssh key is created to establish a security layer with respect to the agents that can connect:

![alt text](images/image-10.png)

This key must now be added in the jenkins security panel:

![alt text](images/image-11.png)

Es necesario consultar la llave privada que creamos, esto se puede hacer con un comando `cat`.

---

Now we must create the container that will hold our agent. First we must consult the ssh public key:

![alt text](images/image-12.png)

The container is then created, but it is important to note that the ssh key must be complete and that the `--network ` parameter must be added to set the container on the network that was created at the beginning:

```sh
docker run -d --rm --name=agent1 --network jenkins -p 22:22 -e "JENKINS_AGENT_SSH_PUBKEY=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIfTyPvzNlZQ6HYrqTan8lIdpZNscZoQhYTrqc1ozC9p mag1305@LenovoMAG" jenkins/ssh-agent:alpine-jdk17
```

We can remove the `-rm` parameter since it deletes the container every time the docker service is stopped.

![alt text](images/image-13.png)

---

The agent must now be linked to the panel. It is important to highlight two things, you must assign an existing directory and in host you must put the name assigned at the time of creating the container, in my case `agent1`:
![alt text](images/image-14.png)

![alt text](images/image-15.png)

---

We check the agent status in the panel:

![alt text](images/image-16.png)

![alt text](images/image-17.png)

## **Third part: Assigning tasks to the agent**

### **Running a Github + Maven project**

Select “new task” and choose pipeline. When we configure the script a drop-down menu will appear from where we can select “Github + Maven”:

![alt text](images/image-18.png)

The script will appear by cloning to the repository 'https://github.com/jglick/simple-maven-project-with-tests', which has the branches to perform the following points.

![alt text](images/image-19.png)

An error appears, saying that maven is not configured in jenkins:

![alt text](images/image-20.png)

Quickly go to “manage jenkins -> tools”. Here, we will be able to add the maven dependency, that once selected we will save and that's it:

![alt text](images/image-21.png)

Now, we can execute the job:

![alt text](images/image-22.png)

### **Running task in a choosed branch**

For this we must modify the previous script adding a new parameter that will be the name of the branch:

![alt text](images/image-23.png)

Now when we modify the task, we will have a new entry, since jenkins detects this change automatically:

![alt text](images/image-24.png)

Finally, just execute it:

![alt text](images/image-25.png)

### **Script in a file**

First you must locate in which folder the jenkins workspace is saved. Once located we add a new file `build_script.groovy` and paste the script here:

![alt text](images/image-26.png)

We modify the script in the panel, to execute the script in the file:

![alt text](images/image-27.png)

And again, we execute:

![alt text](images/image-28.png)

---
