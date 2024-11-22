# Network-Packet-Indexer

## Objective

The Network Packer Indexer Implementation project aimed to design and implement a robust solution for capturing, indexing, and analyzing network packets in real-time using Arkime. The project aimed to enhance network traffic visibility, improve incident response capabilities, and support cybersecurity threat detection by leveraging scalable and efficient packet indexing.

### Skills Learned

- Setup and Configuration: Understanding the installation and configuration process of each tool
- Integration: Learning how these tools integrate and work together 
- Ability to analyze and upload PCAPs
- Practical Skills: Gaining hands-on experience in setting up a Network Packet Indexer

### Tools Used

- Arkime Packet Indexer: Primary tool for packet indexing and analysis.
- Elasticsearch: For indexing and searching packet data efficiently.
- Ubuntu Server: Operating system for hosting and managing indexing tools.
- MaxMind: For Country value information inside Arkime

### Step-By-Step Guide

1) To get started we will need to spin up Ubuntu Server on a Virtual Machine if you do not know how to do this please visit my <a href="https://github.com/wesgough/ubuntu-install">Ubuntu-Install</a> repository for a step-by-step guide
2) The first thing we want to do is log into our Ubuntu Server and perform the following command to update and upgrade all of our repositories
```
sudo apt-get update && sudo apt-get upgrade -y
```
![1](https://github.com/user-attachments/assets/2400d15f-2ab2-4dbb-9194-1f66b6d6b934)

*Ref 1: Update and Upgrade Repositories*

3) Next we will want to SSH into our Ubuntu Server from our personal computer which will allow us to copy and paste into our Virtual Machine
  - First type in "ip a" on your virtual machine to get the IP
```
ip a
```
![1](https://github.com/user-attachments/assets/71ee53bb-cd5d-4980-a4fb-a2dc8e293a14)

*Ref 2: IP Address*

  - Second type in "ssh username@ipaddress" on our personal computer to SSH into the virtual machine and then type our password to login
```
ssh <username>@<ipaddress>
```
![2](https://github.com/user-attachments/assets/8f528f6e-381c-4790-bb57-4c5acf9db4b9)

*Ref 3: SSH Login*

5) While that is downloading, head over to https://arkime.com on your personal computer (not your virtual machine) and click Download

![1](https://github.com/user-attachments/assets/187d5df2-00d9-4c37-aab6-4755c18e5791)

*Ref 4: Arkime Download Page*

5) Scroll down to Assets and find the download that matches our processor and current version of Ubuntu Server we installed on Step 1 | Right-click and select copy link address

![1](https://github.com/user-attachments/assets/789f4b83-8f19-4304-ae2f-59eeac34d9d5)

*Ref 5: Version Download*

6) Head back over to our Ubuntu machine and type "wget" followed by the link you just copied
```
wget https://github.com/arkime/arkime/releases/download/v5.5.1/arkime_5.5.1-1.ubuntu2404_amd64.deb
```

![1](https://github.com/user-attachments/assets/4cb2be53-2055-4162-a8ad-7c70a7d3f4bc)

*Ref 6: Download Arkime*

7) Install Arkime by running the following command: sudo dpkg -i arkime_5.5.1-1.ubuntu2404_amd64.deb
   - We will some dependency problems from missing packages preventing us from installing
   - Run the following command to install them:
```
sudo apt-get -f install
```
![1](https://github.com/user-attachments/assets/f499a7a9-023a-4f0a-9983-ea88e5267aa1)

*Ref 7: Arkime Install*

8) Once installed we want to begin configuring Arkime by navigating to the configuration file by running the following command: 
```
sudo /opt/arkime/bin/Configure
```
   - Type in your network adapter shown in "Found interfaces:" and press enter and type yes to continue
   - You will be prompted to create a password and then asked to Download GEO files which we will type yes and hit enter

![1](https://github.com/user-attachments/assets/900c784f-a525-4f04-811a-53fceba1129e)

*Ref 8: Arkime Configuration*

9) Next thing we want to do is start up and initialize our ElasticSearch SIEM
```
sudo systemctl start elasticsearch.service
```
```
sudo /opt/arkime/db/db.pl http://localhost:9200 init
```

![1](https://github.com/user-attachments/assets/c7735f51-b01b-4739-8d3d-df6819ef49ae)

*Ref 9: Start up and Initialize*

10) Add an admin user and then start everything with the following commands
```
sudo /opt/arkime/bin/arkime_add_user.sh admin "<Admin User>" <PASSWORD> --admin
```
```
systemctl start arkimecapture.service
```
```
systemctl start arkimeviewer.service
```
![1](https://github.com/user-attachments/assets/4c852b23-1a18-4ab4-939a-a82e7b6c26c6)

*Ref 10: Service Startup*

11) Arkime now should be capturing packets from the interface we provided and the service should be up and running. Let's open it up by typing in our Arkime Server IP address in a web browser followed by the port number
  - You will be prompted with Username and Password we just created

![1](https://github.com/user-attachments/assets/15a861a7-fa2b-43e0-b748-fe0455637f6f)

*Ref 11: Arkime Login*

12) Now that Arkime is up and running, let's head over to <a href="https://www.maxmind.com">MaxMind</a> and create an account to provide us with the Country value information inside Arkime
  - Click Sign In at the top right

![1](https://github.com/user-attachments/assets/983266ca-392d-465b-9153-0abb2062a6cd)

*Ref 12: MaxMind Sign In*
  - Click how to create one

![1](https://github.com/user-attachments/assets/861f593e-5c7b-4230-a89b-50056c971c46)

*Ref 13: MaxMind Create Account*

13) Once signed in we want to click on Managed License Keys then Generate new license key  

![1](https://github.com/user-attachments/assets/241227cb-1cd2-456c-9f9a-f691567ba63a)

*Ref 14: MaxMind License Keys*

14) Enter License key description and hit Confirm

![1](https://github.com/user-attachments/assets/697c3a15-58c4-478d-ba81-287dfa35179a)

*Ref 14: License Description*

15) Copy down License key information in a safe place 

![2](https://github.com/user-attachments/assets/6fcaf4ee-8ea9-475d-a8fa-2727e3bd5acc)

*Ref 15: License Key*

16) Head back over to our SSH Terminal and install our GeoUpdate Tool. This command will create a GeoIP.conf file in our /etc folder
```
sudo apt install geoipupdate
```
![1](https://github.com/user-attachments/assets/310077ad-a32e-4ca7-8e89-7ad6ee75728e)

*Ref 16: GeoIP Install*

17) Lets open the GeoIP.conf up in nano editor
```
sudo nano /etc/GeoIP.conf
```

18) Remove the pound sign of AccountID and LicenseKey then fill in those values we got from MaxMind license key creation page
  - Add GeoLite2-ASN after EditionIDs as well
  - Hit ctrl X then Y then Enter to save file

![1](https://github.com/user-attachments/assets/391d7026-1b86-4af9-8884-5db0ba802f4c)


*Ref 17: MaxMind Conf File*

19) Next we will want to run an update command to apply our configurations and restart the Arkime Capture Service

```
sudo geoipupdate
```

```
systemctl restart arkimecapture.service
```


### Accessing Arkime
  - After installation, Arkime will be accessible through your Ubuntu Server IP address over port 8005
  - Congratulations! You have successfully installed Arkime!
  - You can now head over to https://www.malware-traffic-analysis.net and downloads some malicious pcaps files onto the Ubuntu Server machine and open it up in Arkime to play around and sharpen up your skills!




