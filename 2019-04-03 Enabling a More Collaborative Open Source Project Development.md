# 2019-04-03 Enabling a More Collaborative Open Source Project Development

Migrated from [Medium](https://medium.com/@vincentimo/enabling-a-more-collaborative-open-source-project-development-974b03ece712).

<!-- toc-gitlab:start mode=full -->
## Contents<br>
1. [Background](#background)
2. [How to ensure potential collaborators and users do not misinterpret the scope of the project?](#how-to-ensure-potential-collaborators-and-users-do-not-misinterpret-the-scope-of-the-project)
3. [How to ensure potential collaborators can properly setup the project environment regardless of their operating system?](#how-to-ensure-potential-collaborators-can-properly-setup-the-project-environment-regardless-of-their-operating-system)
4. [How to ensure potential collaborators can seamlessly contribute to the project regardless of their operating system?](#how-to-ensure-potential-collaborators-can-seamlessly-contribute-to-the-project-regardless-of-their-operating-system)
5. [Conclusion](#conclusion)
<!-- toc-gitlab:end -->

## Background

**Collaboration is the heart of an open source project.** It allows people from different backgrounds and experiences to work together to achieve a common goal by offering various perspectives, which ultimately benefits the project. However, it is not always easy; **there are** [**challenges**](https://www.researchgate.net/publication/278430664_Challenges_and_Issues_in_Collaborative_Software_Developments) **that might impede collaboration**. We need to ask _at least_ the following questions to ensure a smoother collaboration.

1. How to ensure potential collaborators and users do not misinterpret the scope of the project?
2. How to ensure potential collaborators can properly setup the project environment regardless of their operating system?
3. How to ensure potential collaborators can seamlessly contribute to the project regardless of their operating system?

This article aims to answer these questions to enable a more collaborative open source project development. It should be noted that this article provides high-level perspective of the solution it offers, and the low-level details is linked throughout the article.

## How to ensure potential collaborators and users do not misinterpret the scope of the project?

One way to ensure this is to create a [nice-looking and helpful README file](https://medium.com/@meakaakka/a-beginners-guide-to-writing-a-kickass-readme-7ac01da88ab3) for your open source project. **A README file is like the face of your project.** It is the first thing a potential collaborator or user will read to understand what your project is about, what your project tries to solve, and what features your project have. Writing a bad README file or even worse, not writing a README file at all, will definitely impede collaboration. Furthermore, high-quality README files also provide [additional benefits](https://medium.com/digitalcrafts/ways-to-up-your-game-with-a-readme-md-on-github-35eae99ee289).

Here's a resource you can use to create a high-quality README file.

> https://github.com/matiassingers/awesome-readme

Do this, and you are one step ahead in enabling smoother collaboration in your project.

## How to ensure potential collaborators can properly setup the project environment regardless of their operating system?

Your potential collaborators come from diverse backgrounds and they might use different operating systems (OS). **You can write the setup instruction for each OS you support in your README file, but it is inefficient.** You need to deep dive into each OS that you support and ensure your setup instruction works there. Furthermore, you will need to update your setup instruction each time there is an update to the OS or to the dependencies that would change the setup instruction. It is inefficient, and there should be a better way to do this.

One way to mitigate this issue is to abstract your project into a virtual machine (VM). **VM is an emulation of a computer system which runs guest OS on top of your host OS.** Using VM, you can run e.g. Ubuntu in your macOS machine. In this case, Ubuntu is the guest OS and macOS is the host OS. That means you can run guest OS-specific commands in your host machine.

![](https://i2.wp.com/www.onmsft.com/wp-content/uploads/2018/10/microsoft-virtual-pc.png)
*VM illustration: Windows VM runs on top of Windows VM, which runs on top of Windows.*

As a result, **you can streamline your development environment**: You can develop and configure your project in the OS of your choice, and your collaborators can setup your project in the OS of your choice as the guest OS in their host machine.

Let's provide an example to clear some confusion. You develop and configure your project in Ubuntu. Your collaborators can then prepare a virtual machine with Ubuntu as the guest OS and setup your project there, regardless of the host OS that your collaborators might use.

There are many VM providers you can use, two of which are [VirtualBox](https://www.virtualbox.org/) and [VMware](https://www.vmware.com/).

It begs a question: How to ensure your collaborator to prepare and setup their VM seamlessly? To answer this question, we will need a way to automate the preparation and setup of VMs. Enter [Vagrant](https://www.vagrantup.com/).

> Vagrant is a tool for building and managing virtual machine environments in a single workflow. With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past.
> 
> [Source: Vagrant Documentation](https://www.vagrantup.com/intro/index.html)

The automation process done by Vagrant centers around a [_Vagrantfile_](https://www.vagrantup.com/docs/vagrantfile/). If you are familiar with [Docker](https://www.docker.com/), Vagrantfile is to Vagrant as Dockerfile is to Docker. The primary function of the Vagrantfile is to describe the type of machine required for a project, and how to configure and provision these machines.

You can use Vagrantfile to configure your VM via shell script. If your project has dependencies, you can use Vagrantfile to install and configure those dependencies, also via shell script. The shell script can be directly embedded into the Vagrantfile, or saved in a separate file, typically called `bootstrap`, and be referred to in the Vagrantfile. You can read more [here](https://www.vagrantup.com/docs/provisioning/shell.html).

This way, you can make your project more collaborative by providing the Vagrantfile. Your collaborator just need to run Vagrant with the correct Vagrantfile for your project, and it will run in your collaborator's VM regardless of the host OS they use.

It is important to note that **Vagrant cannot work without a VM provider**, so your collaborator will need to install one of the [supported providers](https://www.vagrantup.com/docs/providers/).

## How to ensure potential collaborators can seamlessly contribute to the project regardless of their operating system?

OK, so now your project has been abstracted in a VM. Your collaborators have been able to setup the project environment in the VM. The last step is to configure your project so that your collaborators are able to seamlessly contribute to it.

If you use Vagrant to manage your VM, you can use Vagrant's [Synced Folders](https://www.vagrantup.com/docs/synced-folders/) feature to do this. **Synced folders enable Vagrant to sync a folder on the host machine to the guest machine**, allowing you to continue working on your project's files on your host machine, but use the resources in the guest machine to compile or run your project. Obviously, synced folders are configured in the Vagrantfile.

This way, any changes made in the host machine are reflected in the guest machine, and vice versa. Each time there is change, your collaborators need to [provision](https://www.vagrantup.com/docs/provisioning/) the VM using Vagrant.

**When you perform provisioning, the shell script embedded or referred to in the Vagrantfile is reexecuted.** This means there is a risk that the provisioning process would fail due the potential [unidempotency](https://medium.com/@ahmadfarag/idempotency-764f7bb6e4e2) nature of the shell script. For example, your shell script might contain `git clone`. The shell script will fail during reexecution due to the destination `git clone` folder has existed from the prior execution. **Therefore, to ensure your collaborators can seamlessly contribute to your project, your shell script must be idempotent.**

## Conclusion

This article explored ways to make your open source project more collaborative. Methods discussed in this article are summarized in the following points.

1. Create a high-quality README file.
2. Abstract your project in a VM to ensure you don't need to write the setup instruction for each OS you support in the README file.
3. Use VM management tool, e.g. Vagrant, to ensure your collaborators can properly setup the project environment regardless of their OS.
4. The automation process in Vagrant is centered around Vagrantfile, which can embed or refer to shell script which configures the VM.
5. Use synced folders feature in Vagrant to ensure any changes made in the host machine are reflected in the guest machine, and vice versa.
6. Each time there is change, your collaborators need to provision the VM using Vagrant, which will reexecute the shell script.
7. Ensure the shell script is idempotent to ensure your collaborators can seamlessly contribute to your project.