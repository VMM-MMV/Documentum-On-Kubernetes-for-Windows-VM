# Documentum-On-Kubernetes-for-Windows-VM
This is a tutorial on how to deploy the enterprise content management (ECM) platform Documentum on Kubernetes for a Windows Virtual Machine.

Before doing any configurations your computer/VM should at least have 32GB of RAM.

## Prerequisites for Docker on Windows VM:
  - System requirmets for Docker: [System requirements](https://docs.docker.com/desktop/install/windows-install/#system-requirements)
  - Enable BIOS SVM, because we are running WSL on a VM:
    [What Is SVM Mode in BIOS & Should You Enable it?](https://www.partitionwizard.com/partitionmagic/svm-mode.html)
  - After doing this, run `wsl --install`, in an elevated powershell, to install Windows Subsystem for Linux, that is needed so that docker runs on windows.
### Install docker desktop, and enable kubernetes.
You can download it from here: [Docker Desktop On Windows](https://docs.docker.com/desktop/install/windows-install/)

### Configure .wsl
Go under `C:\Users\<your_username>`, and make a file `.wsl`, paste this in it, then restart the computer.
```
[wsl2]

# Limits VM memory to use no more than 16 GB, this can be set as whole numbers using GB or MB
memory=16GB

# Comment (or do not include) the following line so VM uses all available Logical Processors
# processors=6
```

### Download Helm
  - Helm will be used to automatically deploy our workload to the k8s cluster using Helm charts. 
  - Easiest way to install Helm is via Chocolatey package manager (on Windows) Chocolatey can be downloaded from: [Chocolatey Software | Installing Chocolatey](https://chocolatey.org/install)
  - Once Chocolatey is installed, issue the following command from an elevated Powershell: `choco install Kubernetes-Helm`
  - Then run `helm version` to see if it’s installed.

### Login to opentext image registry
```
docker login registry.opentext.com
```

### Pull the needed docker images
```
docker pull postgres:15.1
docker tag postgres:15.1 registry.opentext.com/cs/pg:15.1
docker pull registry.opentext.com/dctm-d2pp-rest-ol:23.2
docker pull registry.opentext.com/dctm-tomcat:23.2
docker pull registry.opentext.com/dctm-server:23.2
docker pull registry.opentext.com/otds-server:23.1.1
docker pull registry.opentext.com/dctm-rest:23.2
docker pull registry.opentext.com/dctm-admin:23.2
docker pull registry.opentext.com/dctm-content-connect:23.2
docker pull registry.opentext.com/dctm-content-connect-dbinit:23.2
docker logout
```

Or just run the `docker_pull.cmd` file, in the Documentum-On-Kubernetes-for-Windows-VM folder.

###  `cd` to Documentum-On-Kubernetes-for-Windows-VM folder, and run
  ```
  kubectl apply -f ingress_deploy.yaml
  ```
  To deploy ingress-nginx.

###  Make a namespace where our content server will be deployed
  ```
  kubectl create namespace d2
  ```

###  `cd` to the d2 folder that's in the Documentum-On-Kubernetes-for-Windows-VM folder, and run
  ```
  helm install d2 . --values=dockerimages-values.yaml --values=d2-resources-values-test-small.yaml --namespace d2
  ```
  Now wait for about 90 minutes, because that's about the time it takes to start everything the first time, after that it takes about 10-20 minutes.

If any problems arise, ask me, or follow the original tutorial: [Learning to deploy Documentum on Kubernetes- UNSUPPORTED — OpenText - Forums](https://forums.opentext.com/forums/discussion/123456/learning-to-deploy-documentum-on-kubernetes-unsupported)
Just keep in mind it has the configuration for d2 (that's why some folders have the name d2, so if a problem arises you could see how it would be done in the original tutorial and get a different perspective) and xplore, which take a lot of additional computer storage (around 100GB of storage), so I removed them.
