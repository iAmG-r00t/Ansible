# :genie: SSH Key Authentication Setup Ansible

**This is the ansible role for automation for the SSH Public Key Authentication set up.**

## Goals

- [x] Create an SSH Key with passphrase.
- [x] Create a new user and add to target host.
- [x] Deploy SSH Key under the new user on the target host.
- [ ] Deploy different SSH Keys for each inventory target host groups. **`To be done`**
- [x] Disable `root` and `passwordauthentication` for SSH.
- [ ] Allow password authentication to specif users who either have or don't have sudo rights. **`To be done`**
- [x] Install some packages ([`mosh`](<(https://mosh.org/#)>), `figlet` and `lolcat`)on the target host.
- [ ] How to escalate privilege **`become - module`** for some certain tasks, to enable you to execute them without using the `root` user. **`To be done`**
- [x] Create a banner for the target host showing `hostname` and `username` of the logged in user as below.

### Banner

<!-- ![image](https://user-images.githubusercontent.com/56562226/99379486-cac73800-28d9-11eb-8041-4c2088816938.png)-->

<p align="center">
<img src="https://user-images.githubusercontent.com/56562226/99391530-5bf2da80-28eb-11eb-8f84-5fd938d465d3.png" />

<img src="https://user-images.githubusercontent.com/56562226/99379486-cac73800-28d9-11eb-8041-4c2088816938.png" />
</p>

## Benefits

- The first version had one option of only creating an SSH Key, but I got the need of creating a second user and I wanted this to be easier without the need for me placing the username and password.
  - It generates a random **`username`** of string length `5` and **`password`** of string length `20`.
  - It also has the option of creating a user with your **preferred** `username` and `password`.
- It creates an SSH Key with a passphrase and deploys it to the target host under the new user created.
  - SSH Key generated is an **RSA-4096**.
  - It has the option of deploying your own ssh-key to a user of your choice.
- It creates a directory named `SSH-Keys`, then creates another directory inside it, where name is the `current date stamp` and another directory inside it where name is `current time stamp`. The benefit with this is that you can run the role multiple times and you won't loose your data.

  - Inside the directory you will find the target `hostname:ip` in a file, `SSH Keys`, `SSH Key Passphrase`, `User Name` in a file and `User Password` in a file.
  - See Image below.

  <p align="center">
  <img src="https://user-images.githubusercontent.com/56562226/99391930-f81ce180-28eb-11eb-9d7d-a43432511c24.png" />
  </p>

## Requirements

It works on all platforms.

### Install git, pwgen, gpw and mosh

```sh
sudo apt install git pwgen gpw mosh
```

### Install ansible

```bash
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt-get update && sudo apt-get install ansible git -y
```

**NB:** For optimal use, use ansible version greater than 2.5

## Set up

- Becuse my idea for this playbook was to replace the password authentication with SSH Keys, you just need a working `root` user and password for this to work smoothly.

### Quick Setup

On the client:

```bash
git clone https://github.com/iAmG-r00t/Ansible.git
cd Ansible/roles/public-key-auth-setup_ansible
```

- Edit the hosts file in that folder and fill in the IP field with the target host/hosts IP's.

Begin the setup process by running:

```bash
ansible-playbook public-key-setup_role.yml -u root -k -i hosts
```

Give it a few minutes and the server/servers set up will be complete.

### Advance Use

- Given that you have your own SSH Key and user. You can opt to deploy the ssh key alone to the user.

- On the client:
  - Edit the hosts file in that folder and fill in the IP field with the server IP's

```bash
cd Ansible/roles/public-key-auth-setup_ansible
ansible-playbook public-key-setup_role.yml --tags own_key -u root -k -i hosts --extra-vars="user=username sshkey=/path/to/public/ssh/key"
```

Give it a few minutes and the server set up will be done.

This will deploy your `sshkey` under the `user` you provided. If you come by any errors try tweaking it on sudo previleges.

- Read the main file under the tasks directory, it contains alot of functionality that I will work on improving as time goes by.

## What Next?

- Given that you want to confirm that the password is working really well with the SSH-Key created you can do the following:

1. Cat the password file:

```bash
cat public-key-setup_role/SSH-keys/****/***/ssh-key_passphrase.txt
```

2. Test the password against the private key:

```bash
ssh-keygen -f public-key-setup_role/SSH-keys/****/***/SSH-Key -y
```

- If it displays the private key then you are good to go, follow the below steps to complete.
- Next we proceed to add the private key to your ~/.ssh/ folder
- Best practice is to rename the files to a different name, for easy tracking.

3. Move files to the .ssh folder on the home directory

```bash
mv public-key-setup_role/SSH-keys/****/***/SSH-Key* ~/.ssh/
```

4. Test if ssh is now working with mosh

```bash
mosh user-name@remote-ip
```

### Use as an ansible role

- This project has been structured as an ansible role. You can therefore include it in other ansible playbooks.

```bash
- name: Setup SSH Key Authentication
  hosts: all
  gather_facts: true
  roles:
    - {role: 'public-key-setup_role', tags: 'ssh-auth'}
```

## Resources

- [Ansible Documentation](https://docs.ansible.com/) :slightly_smiling_face:
- [Red Hat Ansible Essentials Course](https://www.redhat.com/en/services/training/do007-ansible-essentials-simplicity-automation-technical-overview) it was free during the :mask: period if you don't find it.
- [Setup SSH Key and initial user using Ansible Playbook](https://medium.com/@khandelwal12nidhi/setup-ssh-key-and-initial-user-using-ansible-playbook-61eabbb0dba4) this blog also helped me :relaxed:
