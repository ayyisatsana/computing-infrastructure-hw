# 1 - Dependencies management

## Theory [2]

- [0.5] What is Docker, and how it differs from dependencies management systems? From virtual machines?

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package.

Dependency management systems, such as pip for Python or npm for JavaScript, help a developer manage the libraries and dependencies required by an application. These systems allow a developer to specify the required dependencies in a configuration file and automatically install them when the application is deployed.

Virtual machines (VMs) provide a way to run an operating system within another operating system. This allows you to run multiple operating systems on the same physical hardware, each with its own set of applications and dependencies.

Docker differs from both dependency management systems and VMs in that it allows you to package and deploy an entire application as a single container, rather than just managing the dependencies of the application. This makes it easier to deploy applications because you don't have to worry about installing and configuring the dependencies on the host machine. Additionally, because Docker containers are lightweight and use the host operating system's kernel, they are more efficient than VMs, which require a full copy of the operating system and virtual hardware.

- [0.5] What are the advantages and disadvantages of using containers over other approaches?

There are several advantages to using containers:

1. Portability: Containers allow you to package an application and all of its dependencies in a single package, which makes it easier to move the application between different environments.

2. Isolation: Containers provide a level of isolation between the application and the host operating system, which can help reduce conflicts between applications and improve security.

3. Resource efficiency: Containers are lightweight and use the host operating system's kernel, which makes them more efficient than virtual machines, which require a full copy of the operating system and virtual hardware.

4. Consistency: Containers allow you to ensure that your application will run the same way in different environments, which can be helpful for testing and deployment.

There are also some potential disadvantages to using containers:

1. Limited customization: Because containers are designed to be lightweight and portable, they may not offer as much flexibility as a full virtual machine when it comes to customizing the operating system or installing additional software.

2. Complexity: Containers require a container engine, such as Docker, to run, and managing and deploying containers can be more complex than other approaches.

3. Security concerns: While containers can provide some level of isolation, they may not offer the same level of security as a full virtual machine, and there have been instances of vulnerabilities being discovered in container engines.

- [0.5] Explain how Docker works: what are Dockerfiles, how are containers created, and how are they run and destroyed?

Docker works by using Dockerfiles to build images and then creating and running containers from those images. A Dockerfile is a text file that contains instructions for building a Docker image. It specifies the base image to use, the dependencies to install, and any other commands needed to set up the image. When you build an image using a Dockerfile, Docker reads the instructions in the Dockerfile and creates an image that includes the specified dependencies and configurations.

To create a container from an image, you can use the `docker run` command, which creates a new container and runs a command in that container.

Containers are created from images, and each container is an isolated environment that runs a specific application or process. Containers can be stopped and started, and you can create multiple containers from the same image. 

To stop a container, you can use the `docker stop` command, which sends a signal to the container to stop running. To start a stopped container, you can use the `docker start` command. To destroy a container, you can use the `docker rm` command. This removes the container and any data stored in it, but it does not remove the image that the container was created from.

- [0.25] Name and describe at least one Docker competitor (i.e., a tool based on the same containerization technology).

One Docker competitor is LXD (Linux Containers). LXD is a container hypervisor that allows you to run multiple isolated Linux systems on a single host. It is based on the same containerization technology as Docker, but it offers a more powerful and flexible way to manage containers.

Like Docker, LXD uses images to create containers, but it also offers additional features such as the ability to snapshot and restore container states, live migration of containers between hosts, and support for running containers with non-root privileges. It also integrates with cloud platforms like OpenStack and allows you to manage multiple remote hosts from a single console.

LXD is designed to be more performant and scalable than Docker, and it is often used in production environments where high availability and resource efficiency are important. However, it may have a steeper learning curve and may not be as widely supported as Docker.

- [0.25] What is conda? How it differs from apt, yarn, and others?

Conda is a package, dependency, and environment management system for installing and managing scientific and data science packages in Python. It is widely used in the scientific and data science communities and is the package manager for the Anaconda distribution of Python, which is a popular distribution for data science and machine learning applications.

Conda differs from other package management systems like `apt` (Advanced Packaging Tool) in several ways:

1. Conda is language-agnostic, while `apt` is specific to the Debian Linux distribution and is used to manage packages written in a variety of programming languages.

2. Conda is designed to handle complex dependencies and can install packages from different sources, while `apt` is typically used to install packages from the official repositories of the Debian Linux distribution.

3. Conda allows you to create separate environments for different projects, which can help prevent conflicts between packages and make it easier to manage multiple projects with different dependencies. `apt` does not have this capability.

Overall, Conda is a specialized package manager that is designed to handle complex dependencies and environments for scientific and data science applications, while apt is a general-purpose package manager for the Debian Linux distribution and Yarn is a JavaScript package manager.

## Problem [6.5]

The problem itself is relatively simple.

Imagine that you developed an excellent RNA-seq analysis pipeline and want to share it with the world. Based on your experience, you are confident that the popularity of the pipeline will be proportional to its ease of use. So, you decided to help your future users and to pack all dependencies in a Conda environment and a Docker container.

Here is the list of tools and their versions that are used in your work:

- [fastqc](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), v0.11.9
- [STAR](https://github.com/alexdobin/STAR), v2.7.10b
- [samtools](https://github.com/samtools/samtools), v1.16.1
- [picard](https://github.com/broadinstitute/picard), v2.27.5
- [salmon](https://github.com/COMBINE-lab/salmon), commit tag 1.9.0
- [bedtools](https://github.com/arq5x/bedtools2), v2.30.0
- [multiqc](https://github.com/ewels/MultiQC), v1.13

**Anaconda**:

- [1] Install conda, create a new virtual environment, and install all necessary packages.

Installing on macOS from Anaconda .pkg file.

Creating and activating an environment: 

```
conda create --name rna-seq 
conda activate rna-seq
```

Adding channels:

```
conda config --add channels bioconda
conda config --add channels conda-forge
```

Installing packages in the environment:

```
conda install -c bioconda fastqc==0.11.9
conda install -c bioconda star==2.7.10b
conda install -c bioconda samtools==1.16.1
conda install -c bioconda picard==2.27.4
conda install -c bioconda salmon==1.9.0
conda install -c bioconda bedtools==2.30.0
conda install -c bioconda multiqc==1.13.0
```

- [0.75] You won't be able to install some tools - that's fine. List their names, and explain what should be done to make them conda-friendly ([conda-forge](https://conda-forge.org/docs/maintainer/adding_pkgs.html) channel, [bioconda](https://bioconda.github.io/contributor/workflow.html) channel).

Picard 2.27.5 — version is not avalible on bioconda, but it is possible to install the latest version manually from github. I could not do this (problem with gradle), so I installed the latest version available through bioconda (2.27.4).

- [0.25] [Export](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#exporting-the-environment-yml-file) the environment ([example](https://github.com/nf-core/clipseq/blob/master/environment.yml)) to the file and verify that it can be [rebuilt](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file) from the file without problems.

Exporting an environment file:

```
conda env export --from-history
name: rna-seq
channels:
  - conda-forge
  - bioconda
  - defaults
dependencies:
  - fastqc==0.11.9
  - star==2.7.10b
  - samtools==1.16.1
  - picard==2.27.4
  - salmon==1.9.0
  - bedtools==2.30.0
  - multiqc==1.13.0
```

Making .yml file:

```
conda env export > rna-seq.yml
```

Deactivating, removing and creating an environment from an rna-seq.yml file:

```
conda deactivate
conda env remove --name rna-seq 
conda env create -f rna-seq.yml 
conda activate rna-seq
```

**Docker**:

- [3] Create a Dockerfile for a container with all required dependencies. Conda usage is not allowed, don't forget about comments; test that all tools are accessible and work inside the container. Hints:
  - If needed, grant rights to execute downloaded/compiled binaries using chmod (`chmod a+x BINARY_NAME`)
  - Move all executables to $PATH folders (e.g.`/usr/local/bin`) to make them accessible without specifying the full path.
  - Typical command to run a container interactively (`-it`) and delete on exit(`--rm`): `docker run --rm -it name:tag`

Installing on macOS from Docker .dmg file.

Creating a Dockerfile:

```
touch dockerfile-hw
```

Building the container image:

```
sudo docker build -t dockerfile-hw
```

Start container:

```
sudo docker run --rm -it dockerfile-hw:latest bash
```

- [1] Use [hadolint](https://hadolint.github.io/hadolint/) and remove as many reported warnings as possible.

- [0.5] Add relevant [labels](https://docs.docker.com/engine/reference/builder/#label), e.g. maintainer, version, etc. ([hint](https://medium.com/@chamilad/lets-make-your-docker-image-better-than-90-of-existing-ones-8b1e5de950d))

```
FROM ubuntu:22.04
LABEL author=ayyisatsana
LABEL version="1.0.0"

RUN apt-get update \
&& apt-get -y --no-install-recommends install wget \
&& apt-get -y --no-install-recommends install unzip \
&& apt-get -y --no-install-recommends install perl \
&& apt-get -y --no-install-recommends install openjdk-11-jdk xvfb \
&& apt-get -y --no-install-recommends install python3-pip \
&& apt-get -y --no-install-recommends install libgomp1 \
&& apt-get -y --no-install-recommends install libtbb12 \
&& touch /.bashrc

#FastQC 0.11.9
RUN wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.9.zip \
&& unzip fastqc_v0.11.9.zip \
&& chmod a+x /FastQC/fastqc \
&& echo 'alias fastqc="/FastQC/fastqc"' >> /.bashrc

#STAR 2.7.10b
RUN wget https://github.com/alexdobin/STAR/releases/download/2.7.10b/STAR_2.7.10b.zip \
&& unzip ./STAR_2.7.10b.zip \
&& chmod a+x ./STAR_2.7.10b/Linux_x86_64_static/STAR \
&& mv ./STAR_2.7.10b/Linux_x86_64_static/STAR /bin/STAR \
&& rm -r ./STAR_2.7.10b

#samtools 1.16.1
RUN wget https://github.com/samtools/samtools/archive/refs/tags/1.16.1.zip -O ./samtools-1.16.1.zip \
&& unzip ./samtools-1.16.1.zip \
&& mv ./samtools-1.16.1/misc /samtools\
&& rm -r ./samtools-1.16.1 \
&& echo 'alias samtools="/samtools/samtools.pl"' >> /.bashrc

#picard 2.27.5
RUN wget https://github.com/broadinstitute/picard/releases/download/2.27.5/picard.jar -O /bin/picard.jar \
&& chmod a+x /bin/picard.jar \
&& echo 'alias picard="java -jar /bin/picard.jar"' >> /.bashrc

#bedtools 2.30.0
RUN wget https://github.com/arq5x/bedtools2/releases/download/v2.30.0/bedtools.static.binary -O /bin/bedtools.static.binary \
&& chmod a+x /bin/bedtools.static.binary\
&& echo 'alias bedtools="/bin/bedtools.static.binary"' >> /.bashrc

#MultiQC 1.13
RUN pip install multiqc==1.13

#salmon 1.9.0
RUN wget https://github.com/COMBINE-lab/salmon/releases/download/v1.9.0/salmon-1.9.0_linux_x86_64.tar.gz \
&& tar -zxvf ./salmon-1.9.0_linux_x86_64.tar.gz \
&& chmod a+x ./salmon-1.9.0_linux_x86_64/bin/salmon \
&& mv ./salmon-1.9.0_linux_x86_64/bin/salmon /bin/salmon \
&& rm -r ./salmon-1.9.0_linux_x86_64 

RUN rm ./*zip \
&& rm ./*tar.gz
```

- [0.75] Minimizing the size of the final Docker image. That is, removing all intermediates, unnecessary binaries/caches, etc. Don't forget to compare & report the final size before and after all the optimizations.

1. Delete all downloaded zip and tar archives along the way
2. Using the `--no-install-recommends` flag — it tells the package manager not to install any recommended packages along with the package that you are installing

The original docker size was over 2.5 gb. After optimization it was reduced to 2 gb.
