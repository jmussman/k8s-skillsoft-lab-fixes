[//]: # (README.md)
[//]: # (Copyright © 2025 Joel A Mussman. All rights reserved.)
[//]: #

![Banner Light](https://raw.githubusercontent.com/jmussman/cdn-fun/main/banners/banner-k8s-skillsoft-lab-fixes-light.png#gh-light-mode-only)
![Banner Light](https://raw.githubusercontent.com/jmussman/cdn-fun/main/banners/banner-k8s-skillsoft-lab-fixes-dark.png#gh-dark-mode-only)

# K8s skillsoft Lab Fixes for Building Kubernetes Skills

## Overview

There are three separate issues with the virtual lab computers for the *skillsoft*
Building Kubernetes Skills course:

1. The Microsoft Azure computers behind the Learn On Demand lab environment do not have a
correct key map for Apple MacOS.
This blocks control-C (^C) from working.
This issue does not affect Microsoft Windows clients.
Follow the utilization instructions at this repo to install a .Xmodmap key map file
on the Ubuntu virtual computers if your desktop is a MacOS computer:
[https://github.com/jmussman/k8s-skillsoft-mac-xmodmap](https://github.com/jmussman/k8s-skillsoft-mac-xmodmap).

1. Google enforced end-of-life on the Kubernetes repositories it was hosting.
The lab instructions for installing Kubernetes reference these repositories and they cannot be found.
The instructions below identify where the repositories are used and replace them with the newer
public versions.

1. The three-computer Kubernetes cluster that is preinstalled for the bulk of the class has
expired self-signed TLS certificates, since current standards insist they must expire after 365 days.
Chrome is trying to reduce this to 90 days.
The instructions to update certificates are hard to find and poorly stated, although they do exist.
A script to replace the certificates, with improvements to the process, has been left at the
GitHub repository [https://github.com/jmussman/k8s-renew-certificates](https://github.com/jmussman/k8s-renew-certificates).
The README.md file at that repository will explain how to use it, and exactly how it works.

Instructors: see the [DEMO Instructions](#instructor-demo-fixes) for the corresponding fixes for the demonstrations.

## Instructions

These instructions replace selected steps in the lab instructions.
Check carefully to make sure they are applied at the correct place.

### Lab 1 - pre-lab setup

1. When the lab environment starts, you will be left on the virtual computer named "Ubuntu Desktop - 18.04 - vILT Lab: Kubernetes-Master".
On the right side click on the tab "Resources".
Make very sure you change the virtual computer and start in the virtual machine where the name ends with "vILT - Kubernetes Install Demo".
The lab will not work correctly on any of the other computers.

### Fixes

1. Task 1, page 2, step 2: Ignore this step, do not launch the root shell.

1. Task 2, Page 3, step 1, instruction 2: This command primes the computer with the signing key for the
Google Kubernetes packages, which were removed when end-of-life was reached.
Replace the curl command with these two commands for the keys for the new
packages at Google and https://pkgs.k8s.io:
```
curl -fsSL https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo apt-key add -
```

1. Task 2, page 3, step 1, instruction 3: Replace the echo command with this updated command for
the new package location:
```
echo "deb https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

1. Task 5, page 9, Step 10: The CIDR network is wrong, replace the kubeadm command with this:
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

1. Task 7, page 11, step 1: On the right-side "Resources" tab
switch to the computer that the name ends with
"vILT Lab: Kubernetes Install Demo - Wor".
The word "Worker" is cut off after the "r".

1. Task 8, page 12, step 1, instruction 2: This command primes the computer with the signing key for the
Google Kubernetes packages, which were removed when end-of-life was reached.
Replace the curl command with these two commands for the keys for the new
packages at Google and https://pkgs.k8s.io:
```
curl -fsSL https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo apt-key add -
```

1. Task 8, page 12, step 1, instruction 3: Replace the echo command with this updated command for
the new package location:
```
echo "deb https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

1. Proceed with the remainder of the lab.

### Lab 2 - Pre-lab instructions

1. In the right-side "Resources" tab launch and sign on to the three other computers:
"Ubuntu Desktop - 18.04 - vILT Lab: Kubernetes - Master", "... Kubernetes - Worker1", and
"... Kubernetes - Worker2".

1. Switch back to the "Master" comptuer.

1. Open a terminal window and execute the following command, *it should fail*:
```
labadmin@master:~$ kubectl cluster-info
```

1. The self-signed TLS certificates for the lab Kubernetes have expired and must be renewed for the Kubernetes to start.
The full repository for fixing this is at [https://github.com/jmussman/k8s-renew-certificates](https://github.com/jmussman/k8s-renew-certificates), but you just have to perform the next two steps.

1. Download the script that will fix the certificate issue:
```
curl --remote-name https://raw.githubusercontent.com/jmussman/k8s-renew-certificates/refs/heads/main/k8s-renew-certificates.sh
```

1. Execute the script as the labadmin user to renew the self-signed TLS certificates:
```
bash k8s-renew-certificates.sh
```

1. Verify that the cluster is up and running properly with the kubectl command,
you should see output that indicates all of the nodes are ready:
```
labadmin@master:~$ kubectl get nodes
NAME      STATUS   ROLES                  AGE    VERSION
master    Ready    control-plane,master   622d   v1.23.5
worker1   Ready    <none>                 622d   v1.23.5
worker2   Ready    <none>                 622d   v1.23.5
```

1. Proceed with Task 1 in Lab 2, but for all of following labs run the commands from the
master computer, not one of the worker computers.
The only reason for using the worker computer was to make the point that we should not be
doing work on the master, but right now the workers are not configured correctly to
run *kubectl* commands.
The /etc/kubernetes/admin.conf file would need to be copied to ~labadmin/.kube/config on the workers.

### Lab 28 - Assign and IP Address to the Pod

1. Task 1, page 189, steps 1 & 2: Ignore these steps, Flannel is already installed.

1. Task 1, page 190, step 3: Change the namespace to *kube-flannel*.

![Stop](./.assets/Stop.png) This is the end of the lab fixes!

## See Also

* The Kubernetes installation scripts for various platforms at https://github.com/jmussman/k8s-install-scripts

<br><br>

# Instructor DEMO fixes

## Instructions
### Demo 1 - pre-demo instructions

1. When the lab environment starts, you will be left on the virtual computer named "Ubuntu Desktop - 18.04 - vILT Lab: Kubernetes-Master".
On the right side click on the tab "Resources".
Make very sure you change the virtual computer and start in the virtual machine where the name ends with "vILT - Kubernetes Install Demo".
The lab will not work correctly on any of the other computers.

### Fixes

1. Page 2, step 2: Ignore this step, do not open a root shell.

1. Page 2, step 4, instruction 1: *apt-transport-https* is no longer necessary, these computers have apt
version 1.6.

1. Page 3, step 4 (starts on page 2), instruction 2:
This command primes the computer with the signing key for the
Google Kubernetes packages, which were removed when end-of-life was reached.
Replace the curl command with these two commands for the keys for the new
packages at Google and https://pkgs.k8s.io:
```
curl -fsSL https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo apt-key add -
```

1. Page 3, step 4, instruction 3: Replace the echo command with this updated command for
the new package location:
```
echo "deb https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

1. Page 3, stp 4, instruction 5: wget is not used anywhere in the labs.

1. Page 9, Step 18: The CIDR network is wrong, replace the kubeadm command with this:
```
root@Udesktop:~# kubeadm init --pod-network-cidr=10.20.0.0/12
```

1. Page 10, step 24: On the right-side "Resources" tab
switch to the computer that the name ends with
"vILT Lab: Kubernetes Install Demo - Wor".
The word "Worker" is cut off after the "r".

1. Page 11, step 27, instruction 2: This command primes the computer with the signing key for the
Google Kubernetes packages, which were removed when end-of-life was reached.
Replace the curl command with these two commands for the keys for the new
packages at Google and https://pkgs.k8s.io:
```
curl -fsSL https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo apt-key add -
```

1. Page 11, step 27, instruction 3: Replace the echo command with this updated command for
the new package location:
```
echo "deb https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

1. Proceed with the remainder of the demonstration.

### Demo 2 - Pre-task instructions

1. In the right-side "Resources" tab launch and sign on to the three other computers:
"Ubuntu Desktop - 18.04 - vILT Lab: Kubernetes - Master", "... Kubernetes - Worker1", and
"... Kubernetes - Worker2".

1. Switch back to the "Master" comptuer.

1. Open a terminal window and execute the following command, *it should fail*:
```
labadmin@master:~$ kubectl cluster-info
```

1. The self-signed TLS certificates for the lab Kubernetes have expired and must be renewed for the Kubernetes to start.
The full repository for fixing this is at [https://github.com/jmussman/k8s-renew-certificates](https://github.com/jmussman/k8s-renew-certificates), but you just have to perform the next two steps.

1. Download the script that will fix the certificate issue:
```
curl --remote-name https://raw.githubusercontent.com/jmussman/k8s-renew-certificates/refs/heads/main/k8s-renew-certificates.sh
```

1. Execute the script as the labadmin user to renew the self-signed TLS certificates:
```
bash k8s-renew-certificates.sh
```

1. Verify that the cluster is up and running properly with the kubectl command,
you should see output that indicates all of the nodes are ready:
```
labadmin@master:~$ kubectl get nodes
NAME      STATUS   ROLES                  AGE    VERSION
master    Ready    control-plane,master   622d   v1.23.5
worker1   Ready    <none>                 622d   v1.23.5
worker2   Ready    <none>                 622d   v1.23.5
```

1. Proceed with the demonstration, but for all of following demonstrations run the commands from the
master computer, not one of the worker computers.
The only reason for using the worker computer was to make the point that we should not be
doing work on the master, but right now the worker is not configured correctly
to run *kubectl* commands.
The /etc/kubernetes/admin.conf file would need to be copied to ~labadmin/.kube/config on the workers.

### Demo 7 - Using Labels and Selectors

1. Page 65, steps 11 & 12 (Page 66): Ignore these steps, they were cut and pasted from some other
demo, they don't don anything.

### Demo 12 - Monitoring the Cluster

1. Page 105, step 5: Ignore this instruction, it is a no-op.

### Demo 24 - Implementing Client Certificate Authentication

1. Page 187, step 3: This step is uneccesary, the step 4 duplicates creating the key.

1. Page 188, step 5: Since the user should be off of the master, the .csr file
needs to be sent to the master and this step performed there.
After the .crt file is signed and created, it should be returned to the computer with david.

### Demo 25 - Creating Keys, Certificates, and TLS Secrets

Nothing actually happens with this lab, we don't even ask the user to visit Kubernetes
to see the self-signed certificate warning.

1. Page 193, step 3: It says if you do not have the .rnd  issue then move to step 6, but
it is really step 9.
Besides, the random issue really doesn't matter for us.

### Demo 26 - Creating Roles and Role Bindings

1. Page 202, step 20: move back to the computer with david to perform this check; better yet
just get the pods in step 21.

### Demo 29 - Assigning an IP Address to the Pod

1. Page 220, steps 1 & 2: Ignore these steps, Flannel is already installed.

1. Page 221, step 3: Change the namespace to *kube-flannel*.

## License

The code is licensed under the MIT license. You may use and modify all or part of it as you choose, as long as attribution to the source is provided per the license. See the details in the [license file](./LICENSE.md) or at the [Open Source Initiative](https://opensource.org/licenses/MIT).

<hr>
Copyright © 2025 Joel A Mussman. All rights reserved.