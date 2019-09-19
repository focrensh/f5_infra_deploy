## Description
Demonstrate BIG-IP app/infrastructure based on ansible inventory vars. The play book will pull Onboarding, Services, and Telemetry definitions from each host_var folder and deploy those to the BIG-IP.

#### Requirements
* Python env with following dependencies (Intsall all py reqs with `pip install -r requirements.txt`)
* Pre-made AWS sshkey, EIP, Security Group, VPC

#### Output
High level playbook flow:

* If current BIG-IP does not exist in AWS based on tags
  * Create EC2 BIG-IP
  * Move EIP in host_vars to new EC2
  * Install Automation Toolchain (DO, AS3, TS)
  * Deploy DO declaration
* After current/new box is online
  * Deploy AS3 Declaration

#### Setup
The ansible playbook has a hosts file with a group called **bigips**. The host listed under this group should have a corresponding folder under `host_vars` which holds the definitions of the BIG-IP.

* Update the python interpreter inside the **hosts** file to the appropriate location.
  * ie: `localhost ansible_python_interpreter=/sc/venvs/np/bin/python`
* Add vault file `../vaults/creds.yaml` with the following secrets and encrypt with your password
  * aws_access_key: `****`
  * aws_secret_key: `****`
  * vpass: `**Desired BIG-IP admin password**`
* **ansible.cfg** by default looks up the vault password at `../vaults/.vault_pass`. This is not required and the vault password may be added in a way which best fits your environment.
* For each BIG-IP `host_var` update the following:
  * **do.json** with desired Onboard settings (note AWS will reset the hostname based on dhclient).
  * Update **apps.json**  with desired AS3 Applications
* Update `group_vars/main.yaml` with AWS env information:
    ```
    ssh_keyfile: "~/.ssh/privatekey.pem"
    ami: ami-07b978ff1e31c60f9
    aws_ssh_key: 4est2
    aws_instance_type: t2.large
    aws_sg: 4est-bip
    aws_vpc_subnet: subnet-0ec6b63e44ba17aad
    region: us-east-2
    ```
    
## Run the Playbook

```shell
ansible-playbook deploy.yaml
```