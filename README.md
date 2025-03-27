# SOC-Automation-Lab


# Part II

**1. Install Windows 10 VM** 

With Windows just set up a VirtualBox or any other and set up a Windows 10 VM.

We will need the VM later for our incident simulation.

**
3. Set-Up Cloud**

My recommendation:
www.digitalocean.com

200¢ free for usage

especially for everyone with a Mac with M1, M2, M3

**4. Set-Up Wazuh in the Cloud**

apt-get update && apt-get upgrade

Specifications
RAM: 8GB+
HDD: 50GB+
OS: Ubuntu 22.04 LTS

Install Wazuh 4.7
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a

Extract Wazuh Credentials
sudo tar -xvf wazuh-install-files.tar

**5. Set up the Hive in the Cloud**

