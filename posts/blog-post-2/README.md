# Why we should use remote development workspaces!

For my work, I tend to move around a lot, every day I'll be working on a different computer, and I'll want to show students and colleges what I've been doing. Typically the projects I work on all have a different setup, some will need access to a local SQL server, some might need a Redis DB, some might be running on PHP or need a load balancer, or a variety of different services.

Point is, all projects are different. What I really want is to be able to jump on any computer, anywhere, and start coding.

In the past, I used the Cloud9 IDE before it was bought out and integrated into the AWS ecosystem. Cloud9 is and was an online development environment that ran directly within your web browser. You could create a new project, spin up a virtual development environment, and get to programming right away. It gave you access to a Linux VM, and an editor for programming and debugging. As far as I know, Its free and paid services didn't have any genuine competitors at the time.

I've found many benefits of having a remote development environment setup.

* **Code from anywhere**, on any device, at work, home, school, with your Chromebook, mac, or windows. All you need is a browser. 
* **Onboarding new staff**: Let's face it, all developers have preferred tools and editors. Providing a remote development environment allows new staff to get familiar quickly and the freedom to install whatever additional software they want locally, knowing that they will always have an environment ready to go. 
* **Coding interviews:** A remote dev environment allows us to set up a simple project. We can ask candidates to log in and complete a few quick programming tasks.

Today, there are alternative comparable solutions. Though there are some solutions that offer similar features... probably a few more features than cloud9 had too...

## Remote Development Offerings

### **GitPods**

> [Gitpod](https://www.gitpod.io/) is an open source platform for automated and ready-to-code development environments that blends into your existing workflow. It enables developers to describe their dev environment as code and start instant and fresh development environments for each new task directly from your browser.

Gitpod is open source and can be installed on your own servers. They also provide limited free / paid plans for their hosted service. Gitpod is built on top of the Theia IDE project. Its interface shares a lot of similarities with VS Code and is even compatible with most VS Code Extensions.  
  
Personally, I haven't used GitPod for anything substantial.  


### **Github CodeSpaces**

> Codespaces is an online development environment, hosted by GitHub and powered by Visual Studio Code, that allows you to develop entirely in the cloud.

CodeSpaces is a closed source project, previously VS Code CodeSpaces was offered by Microsoft, Its now migrating to the GitHub platform. Its currently in beta. Early access can be requested.

### **Code-Server**

[Available on GitHub](https://github.com/cdr/code-server/tree/master)

> Run VS Code on any machine anywhere and access it in the browser.

Unlike GitPod and CodeSpaces, Code Server doesn't manage your development environment. It doesn't create new VM's or sandbox your projects. Its goal is simple. Make VS Code available in the browser. It's easy to install on a VM, and you can access it straight away. It can also be installed alongside other services running on the VM.

Code server is what I've had the most experience with. For personal development, I find code-server to be the ideal solution, I've got access to the full VM, I can use Git, Docker, and any other software I like. Just as if I was developing on my local pc.

## Install Code Server on a GCP VM

So you want to install Code-Server on your own VM? Great! I've written a quick tutorial to show you how to get started.



