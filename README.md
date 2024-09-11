# Lesson 1: Introduction to Ansible

## What is Ansible? Why do we need it?

### What is Ansible? 

Ansible is a powerful automation tool that simplifies managing and configuring your systems. It uses a simple, human-readable language (YAML) to define tasks, making it easy to learn and use.

### Why do We Need Ansible?

Ansible helps automate repetitive tasks, such as software installation and system configuration. This saves time, reduces errors, and ensures consistency across your environments.

### How It Can Help Us?

With Ansible, you can automate the setup of servers, manage software packages, and streamline deployments—all with minimal effort. Imagine deploying updates across hundreds of servers with a single command!

Ansible <b>playbooks</b> are YAML files used to define a set of tasks that you want to automate. Playbooks allow you to describe the desired state of your systems—like installing software, configuring services, or managing files—using simple, human-readable syntax.

Each playbook consists of:

* <b>Hosts</b>: Defines which machines the playbook will target (e.g., localhost, all servers).
* <b>Tasks</b>: A series of actions that Ansible will execute on the specified hosts.
* <b>Modules</b>: Pre-built scripts that perform the actual work, such as installing packages, managing files, and configuring services.

#### Basic Playbook Syntax

A playbook usually follows this structure:

```yml
---
- name: A descriptive name of what the playbook does
  hosts: localhost  # or a group of servers from inventory
  become: true      # Run tasks with elevated privileges (sudo)
  
  tasks:
    - name: Name of the first task
      module_name:
        key: value

    - name: Name of the second task
      module_name:
        key: value
```

## Practical Task: Automating Software Installation

Let’s start by automating the installation of Google Chrome. 

### Step 1: Install Ansible

Before we start, ensure Ansible is installed by running the following command:

```bash
pip install ansible
```

### Step 2: Create Your First Playbook

We will start with installing Google Chrome on an Ubuntu system.

* Create a playbook file called `install_chrome.yml`.
* Write the following YAML code:

```yml
---
- name: Install Google Chrome on Ubuntu
  hosts: localhost   # This playbook will run on the local machine
  become: true       # We need sudo privileges for package installation
  tasks:

    # Ensure that required dependencies are installed
    - name: Install prerequisites
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
        state: present


    # Add Google Chrome repository
    - name: Add Google Chrome repository
      apt_repository:
        repo: deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main
        state: present

    # Install Google Chrome
    - name: Install Google Chrome
      apt:
        name: google-chrome-stable
        state: present
```

#### What does the playbook do:

`become: true`: Ansible will run commands with sudo privileges since installing software requires root access.

`Install prerequisites`: Before installing Google Chrome, we make sure apt-transport-https, ca-certificates, and curl are installed. These are needed for package handling

`Add Google Chrome repository`: This adds the repository from which Google Chrome will be installed.

`Install Google Chrome`: Finally, we install the google chrome package using `apt`.

### Step 3: Run the Playbook

You can now run the playbook using this command:

```bash
ansible-playbook install_chrome.yml --ask-become-pass
BECOME password: 
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Install Visual Studio Code on Ubuntu] *********************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************
ok: [localhost]

TASK [Install prerequisites] ************************************************************************************************************
ok: [localhost]

TASK [Add Google Chrome repository] *****************************************************************************************************
changed: [localhost]

TASK [Install Google Chrome] ************************************************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************************************************************
localhost                  : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

## Home Exercise:

* <b>Add More Software</b>: Try adding Firefox, Slack, or any other tool you often use. Use the same pattern as the playbook above.
* <b>Create a Reusable Playbook</b>: Think about the tools you install when setting up a new machine. Can you automate the entire process? Next time, instead of manually installing everything, you can just run your Ansible playbook!
* <b>Test Across Multiple Machines</b>: If you have more than one machine, try running the same playbook on another PC. This will show how Ansible scales and how easily you can maintain consistency across systems.

With a bit more practice, you'll effortlessly deploy tools across multiple machines using Ansible. 

Installing software will become a breeze—just run your playbook and skip the tedious tasks. Keep going, and automation will become second nature! 🚀

Curious about how Ansible can transform your workflow? Stay tuned for more insights and tips!


# Lesson 2: Understanding Inventory Files

Today, we'll build on these foundations and dive deeper into using inventory files and automating tasks on multiple servers!

Ready? Let’s go! 👏

## What are Inventory Files? Why are they important?

### What are Inventory Files? 

Inventory files list the hosts (servers) that Ansible will manage. They can be simple text files or dynamic sources.

### Why They Are Important 

Inventory files are critical because they act as a map for Ansible, defining which servers it should interact with. They allow you to create groups of servers and organize your infrastructure logically. For example, you can create separate groups for your web servers and database servers across different cloud platforms. Without inventory files, Ansible wouldn't know which hosts to manage, making them essential for scaling your automation.

### How They Can Help Us 

By leveraging inventory files, you can scale your automation efficiently. Imagine managing hundreds of servers—grouped by role, function, or cloud provider—with just a few commands. This level of control streamlines processes like deploying applications, applying patches, or monitoring systems. Whether you're managing a single machine or a fleet across multiple clouds, inventory files make it easy to target specific hosts and perform complex tasks with precision.

## Structure of inventory.ini

The `inventory.ini` file follows a clear structure that groups hosts (servers) logically. Let's break it down:

* <b>Host Groups</b>: Hosts are grouped under specific categories. For example, you can group servers by cloud provider (AWS, Google Cloud, Azure) or role (web, database).

* <b>Host Entries</b>: Each host is defined with an ansible_host and optional variables like connection method or authentication details.

* <b>Localhost</b>: Ansible can also manage the local system running the playbook, often listed with a local group.

Here’s a more detailed look at an `inventory.ini` file with multiple groups and hosts:

```ini
[aws_servers]
aws_host1 ansible_host=ec2-3-123-45-67.compute-1.amazonaws.com ansible_user=ubuntu ansible_private_key_file=/path/to/aws_key.pem
aws_host2 ansible_host=ec2-3-123-45-68.compute-1.amazonaws.com ansible_user=ubuntu ansible_private_key_file=/path/to/aws_key.pem

[google_cloud_servers]
gcloud_host1 ansible_host=35.123.45.67 ansible_user=gcpuser ansible_private_key_file=/path/to/gcloud_key.pem
gcloud_host2 ansible_host=35.123.45.68 ansible_user=gcpuser ansible_private_key_file=/path/to/gcloud_key.pem

[azure_servers]
azure_host1 ansible_host=52.123.45.67 ansible_user=azureuser ansible_private_key_file=/path/to/azure_key.pem
azure_host2 ansible_host=52.123.45.68 ansible_user=azureuser ansible_private_key_file=/path/to/azure_key.pem

[local]
localhost ansible_connection=local
```

### Key Parts of `inventory.ini`:

* <b>Group Names</b>: `[aws_servers]`, `[google_cloud_servers]`, and `[azure_servers]` or any name represent groups of hosts. You can apply tasks to these groups collectively.
* <b>Host Definitions</b>: Each host is defined with its IP or hostname (`ansible_host`).
* <b>Connection Details</b>: Ansible needs to know how to connect to the server. In this example, it connects using an SSH key and the ansible_user for authentication.
* <b>Localhost Group</b>: The `[local]` group defines the local machine which is managed directly.

## Practical Task: Create and Use an Inventory File

Let's dive into a hands-on task to understand the power of inventory files by defining several workspace on XploR Cloud, and using Ansible on localhost to install Google Chrome.
What's a XploR Workspace?
XploR workspaces are cloud-based development environments managed through infrastructure-as-code tools like Terraform. Every time you restart, reset, or update your XploR workspace, it returns to a fresh state, wiping out all manually installed software. This behavior is by design, ensuring that each workspace is clean and consistent.

However, this also means that any software you install manually, like Google Chrome or development tools such as gcc, qemu, quartus, vivado, python pip packages, npm packages ..., will be lost on a reset. That’s where Ansible shines. Instead of tediously reinstalling everything manually, Ansible automates the process, saving you time and energy so you can focus on your core tasks.

### Step 1: Set Up SSH Host Entries Using coder `config-ssh`

First, you need to configure SSH access to your XploR workspaces. XploR provides the coder config-ssh command, which creates SSH host entries for each workspace, allowing you to connect easily.

### Step 2: Define Inventory File with SSH Host Entries

With SSH configured, you can now define your inventory.ini file to target your workspaces by their SSH hostnames. Here’s an example

```ini
[ide_workspaces]
my_ide1_ws ansible_host=coder.ide1.my-agent
my_ide2_ws ansible_host=coder.ide2.my-agent

[dronava_workspaces]
demo1 ansible_host=coder.vivadows.my-agent
demo2 ansible_host=coder.flexricws.my-agent
```

#### Explanation of `inventory.ini`:

<b>Group Definitions</b>:

* `[ide_workspaces]` contains your workspaces for developing IDE.
* `[dronava_workspaces]` contains your workspaces for project releated to Dronava.
* `[all_workspaces:children]` includes both ide and dronava groups, so you can target all workspaces at once if needed.

<b>Host Entries</b>: Each workspace is referenced by its SSH host entry, which follows the format `coder.{workspace_name}.{agent_name}`.

### Step 3: Update the Ansible Playbook to Install Chrome
Now, update your Ansible playbook from lesson 01 named install_chrome.yml to install Google Chrome on your workspaces.

```yml
---
- name: Install Google Chrome on XploR Workspaces
hosts: all_workspaces
become: true
tasks:

# Ensure that required dependencies are installed
- name: Install prerequisites
apt:
name:
- apt-transport-https
- ca-certificates
- curl
state: present


# Add Google Chrome repository
- name: Add Google Chrome repository
apt_repository:
repo: deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main
state: present

# Install Google Chrome
- name: Install Google Chrome
apt:
name: google-chrome-stable
state: present
```

### Step 4: Run the Playbook from localhost
Now that everything is set up, run the following command from your local machine to install Google Chrome on the workspaces:

```bash
ansible-playbook install_chrome.yml
```

## Home Exercise: Expand Your Ansible Playbook to Install Development Tools

Now that you have learned the basics of Ansible, it's time to put your skills into practice! Your task is to expand the provided Ansible playbook to automate the installation of development tools on your XploR workspaces such Python, GCC, QEMU RISCV, OpenOCD, Nodejs and NPM, Quartus, Vivado...

This exercise will reinforce your understanding of automating software installation with Ansible, ensuring that you’re always ready to work without manual setup.

## Next Steps
Now that you’ve learned using `inventory.ini` to define hosts and groups for automation. In the next lesson, we’ll explore how to use variables to customize tasks and tailor automation even further to your specific workspace needs! Stay motivated—Ansible is a powerful tool that will keep simplifying your development workflow!