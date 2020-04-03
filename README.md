# ansible-update-amis
Ansible Playbook to update AWS AMI and replace it in the auto-scaling group

Often times in small AWS environments, there is a need to keep up with the updates on hosts that are in the auot-scalling groups.
While you can do this via userdata yum update command, often times these images need to be changed and updated with other things too.

This handy ansible playbook will do just that for you. It is intended to run on your Ansible server that will have access to AWS.
It will look for your latest AMI, launch a temporary instance, add this instances private IP to ansible in memory hosts, execute commands of your choosing on it, stop the instance, take a new AMI before terminating it.
Finally it will create a new launch configuration with this new AMI, update Auto Scalling group to use it (without replacing existing instances) and delete the old launch configuration. 

Ansible needs to have awscli installed with the right IAM privileges (ec2 full admin, S3 getobject from Config Bucket).
EC2 moduels in Ansible also depend upon boto3 python library (pip install boto3).

Script looks up your AMIs based on tags. It is intended to have AMI's with tags images:latest , images:1 and images:2 for the last 3 versions of your AMI. It will automatically delete the last AMI (images:3) and update tags on other ones so as to keep 3 copies.

On the ansible server in /etc/ansible/hosts you should set empty group [images] for which under /etc/ansible/group_vars you should setup images file that will setup how your ansible server will access your image.
Something like this:

ansible_ssh_user: ec2-user
ansible_ssh_private_key_file: /etc/ansible/testing.pem
ansible_become: yes


You should configure S3 bucket to conatin userdata script you want Launch Configuration to execute.

Other than that all variables that need to be changed are in the variables section in the top of the playbook.

Enjoy!

