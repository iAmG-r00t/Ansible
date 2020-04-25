# :genie: SSH Key Authentication Setup Ansible

This is the ansible automation for the SSH Public Key Authentication set up.

* This will create one SSH Key with a password and copy the Public Key to the servers.
- This means that you will now use one SSH key to authenicate with the multiple servers.
+ All the files (Password.txt, Private and Public Keys) will be available at the files directory in the public-key-setup_role directory.
* It also installs mosh on the remote hosts, if you aren't using mosh you are missing out some great tool visit [Mosh website](https://mosh.org/# to learn more about mosh).

# Note

- Do note that when you are running the playbook again, make sure you **SAVE** previous generated SSH Key and password because the will be **DELETED**.
+ Password generated is 30 strings long
* SSH Key generated is an RSA-4096

This project has also been restructured as an ansible role for inclusion in other ansible playbooks.

# Requirements 

It works on all platform.

## Install git, pwgen and mosh
```bash
sudo apt-get install git pwgen mosh
```

## Install ansible

```bash
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt-get update && sudo apt-get install ansible -y
```
For optimal use, use ansible version greater than 2.5

# Server set up

* Becuse my idea for this playbook was to replace the password authentication with SSH Keys, you just need a working root user and password for this to work smoothly.

# Quick setup

On the client:

```bash
git clone https://github.com/iAmG-r00t/Ansible/roles/public-key-auth-setup_ansible.git
cd public-key-auth-setup_ansible
```

Edit the hosts file in that folder and fill in the IP field with the  server IP's

Begin the setup process by running:

```bash
ansible-playbook public-key-setup_role.yml -u root -k -i hosts
```

Give it a few minutes and the server set up will be complete.

# What Next?

* Given that you want to confirm that the password is working really well with the SSH-Key created you can do the following:

1. Cat the password file:

```bash
cat public-key-setup_role/files/password.txt
```

2. Test the password against the private key:

```bash
ssh-keygen -f public-key-setup_role/files/SSH-Key -y
```
- If it displays you the private key then you are good to go, follow the below steps to complete.
* Now we proceed to add the private key to your ~/.ssh/ folder
+ Best practice is to rename the files to a different name, for easy tracking.

3. Move files to the .ssh folder on the home directory

```bash
mv public-key-setup_role/files/SSH-Key* ~/.ssh/
```

4. Test if ssh is now working with mosh

```bash
mosh user-name@remote-ip
```

# Resources

- [Ansible Documentation](https://docs.ansible.com/) :slightly_smiling_face:
- [Red Hat Ansible Essentials Course](https://www.redhat.com/en/services/training/do007-ansible-essentials-simplicity-automation-technical-overview) it was free during the :mask: period if you don't find it.
- [Setup SSH Key and initial user using Ansible Playbook](https://medium.com/@khandelwal12nidhi/setup-ssh-key-and-initial-user-using-ansible-playbook-61eabbb0dba4) this blog also helped me :relaxed: