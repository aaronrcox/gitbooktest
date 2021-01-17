---
description: >-
  In this post, I will show you how I set up a C++ project with CMake and Visual
  Studio. We will compile to webasm with Emscripten via Docker and finally, we
  will automate the build and deploy with Git
---

# Build and deploy C++ for web with Emscripten, Docker and Github Actions

I first started programming around 2004 with C++, OpenGL, and SDL. I created all sorts of small little graphical prototypes like particle systems, custom UI libraries, 2d and 3d frameworks, random shader effects, data visualizations, and loads of game prototypes, etc... It's the kind of programming I can only describe as pure fun.

The problem with all of these projects is that they are closely tied to Microsoft Visual Studio, and Windows. A release build of the project outputs an `*.exe`... Fairly standard, but showcasing these projects is a little awkward. Sure I could show a screenshot and provide a GitHub link... but that's a bit lacking... I'd rather let the internet interact with my projects.

> Please, can you download my susipcious \*.exe

This pretty much sums up my motivation for this post, I'm going to show how we can build and publish a C++ project to GitHub pages so that it can run within a web browser. 

### Goals

* Compile C++ on windows using visual studio 2019
* Compile C++ with Emscripten for the web using Docker
* Automate Emscripten build with Github Actions
* Automate deploy to Github Pages

### Requirements

* Visual studio 2019 - C++ installed
* Docker \(optional\) - Helpful for local testing
* Github account

### Getting Started <a id="getting-started"></a>

This tutorial will be broken down into the following steps.

1. Create a CMake project with Visual Studio
2. Use Docker to build our project with Emscripten
3. Automate Build and Deploy with Github Actions

### Create a CMake project with Visual Studio <a id="create-a-cmake-project-with-visual-studio"></a>

{% hint style="warning" %}
 **Disclaimer** I’ve very new to CMake, so some setup may be considered less optimal or non-standard. As I learn more I’ll update the tutorial as necessary.
{% endhint %}

We can create a CMake project via Visual Studio’s CMake Project Template. There are a few changes that we will make to the default settings based on the following considerations.

1. Create release configuration
2. change default build output directory
3. Change projects location \(so it’s not in root\)
4. Separate header and source files into `inc` and `src` directories

Here’s a quick video to show the steps for creating the CMake project within visual studio.

{% embed url="https://www.youtube.com/watch?v=bKdDs2jygyo" %}

### Docker Build with Emscripten <a id="docker-build-with-emscripten"></a>

Now that we have an initial project setup, we are going to build the project with Emscriten using a docker container. 

Watch the video below for a quick walkthrough on how to set up our project, and keep reading for a written explanation of the provided scripts.

{% embed url="https://www.youtube.com/watch?v=\_SbUqOXf8Cs&feature=emb\_title" %}

### **Update CMakeLists for Emscripten**

Before we can build with Emscripten, there are a few additions need in our CMakeLists.txt files for our projects to build correctly.

`EmscritenProjects/CMakeLists.txt` \(root\)  
Add the following after we define the HelloWorld project

```text
if(EMSCRIPTEN)
	set(CMAKE_BUILD_TYPE "Emscripten")
endif()
```

`EmscritenProjects/projects/HelloWorld/CMakeLists.txt`   
Tell Emscripten to output a HTML file.

```text
if(EMSCRIPTEN)
	# Tell emscripten to output a html file
	set(CMAKE_EXECUTABLE_SUFFIX ".html")
endif()
```



### **Setup Docker Image**

With these changes made to our CMakeLists files, we are ready to setup docker to build our project with Emscripten.

{% hint style="info" %}
**Docker \(Optional\)**: Installing Docker will be helpful for local development and testing. Later, we will set up automatic builds with GitHub actions and deploy them to GitHub pages.
{% endhint %}

To test locally, install docker for windows.  
[https://hub.docker.com/editions/community/docker-ce-desktop-windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows) 

We will need to create 4 files in our project’s root directory:

1. **`Dockerfile:`** This is a text file that will describe a docker image. The Image will contain the build tooling needed for our project. Our Image will use the officially supported Emscripten docker image. We will additionally install**`ninja-build`**which is used by Visual Studio to generate our projects. And finally, we will run a custom script**`dockerbuild.sh`**when our docker container runs.

   ```text
   FROM emscripten/emsdk:latest
   RUN apt update && apt install -y ninja-build
   CMD ["/bin/sh", "/app/build.sh"]
   ```

2. **`.dockerignore`** When a Dockerfile is built into an image, the file content in the current directory is packaged. so that local files are copied and made available to the docker image. For our purpose, we don't want anything copied into our docker image. We will ignore everything. \(similar to how a .gitignore file works\)

   ```text
    # IGNORE EVERYTHING
    *
   ```

3.  **`build.sh`** A Bash script that runs the commands needed to build our CMake project with the Emscripten toolchain.

   ```text
   # make sure we are in the project directory
   cd /app

   # delete the files in the build folder
   # this is for when we run locially, on a fresh clone, build folder will not exist
   rm -rf /app/build/*

   # re-create the build folder (does nothing if already exists)
   mkdir build

   # Run CMake in build folder
   # this will populate the build folder
   cd ./build
   emcmake cmake ..

   # run make - this will compile our project
   make
   ```

4.  **`web_build.bat`** A Helper windows batch file script for running our docker image

   ```bash
    @echo off

    # Build the docker image described by our docker file
    docker build . -t build_emscripten

    # run the docker image, volume mount the current directory to
    # /app in the container. This is when our build.sh script will run.
    docker run -v %CD%:/app build_emscripten
   ```

### **Build and Run**

Now that the docker files are set up correctly, we can build the project by simply running the `web_build.bat` from the command prompt.

The output will be located in `/bin/Emscripten/HelloWorld/`

We will need to serve these files from a local webserver. I’ve found it easiest to use `Visual Studio Code's` - Live Server extension.

And we’re done. you should now see `Hello CMake` within the Emscripten terminal within your web browser.

### Automated Build and Deploy with Github Actions

The Last step is automating our build process with GitHub actions. To achieve this, we will need to create a new empty Repository on Github and push our files to that repo, and define a workflow that can be triggered when we push to the branch.

Here’s a quick walkthrough for the remaining steps, or continue reading below.

{% embed url="https://www.youtube.com/watch?v=aWw-LFLBlrc&feature=emb\_title" %}

To Get started we will first need to add a **`.gitignore`** file to our project.

```text
CMakeLists.txt.user
CMakeCache.txt
CMakeFiles
CMakeScripts
Testing
Makefile
cmake_install.cmake
install_manifest.txt
compile_commands.json
CTestTestfile.cmake
_deps

.vs/
bin/
build/
out/
```

 Once you’ve added the**`.gitignore`**file. we can push our project to GitHub.

### **Github Actions**

To use GitHub actions, we need to define a workflow file for GitHub. I’m going to create a new workflow file:`./github/workflows/EmscriptenBuildDeploy.yml`

The folder location here is important, as GitHub will only look in this **`.github/workflows/`** folder.

Here’s our YAML file

```yaml
name: EmscriptenBuildDeploy

on:
  workflow_dispatch:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Build
        run: "docker build . -t build_emscripten && docker run -v `pwd`:/app build_emscripten"

      - name: Deploy
        uses: JamesIves/github-pages-deploy-action@3.7.1
        with:
          GITHUB_TOKEN: $
          BRANCH: gh-pagesto.
          FOLDER: bin/Emscripten 
          CLEAN: true 


```

With these changes now in place. Push the action to your repo. The Action should automatically trigger.

You can view progress via the “Actions” navigation tab.

### **Enable Github Pages**

Finally, navigate to your project settings. scroll down to `Github Pages` and choose `gh-pages` branch then save.

### **Done**

We’re Done. A Simple HelloWorld C++ project Compiled and Deployed to Github pages with Github Actions. Although this was a simple “Hello World” project it has set the groundwork for future projects to be created and deployed.

