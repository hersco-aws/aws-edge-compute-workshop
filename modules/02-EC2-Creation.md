# Workshop - Module 2

## 1. EC2 Creation

We are now creating an EC2 instance and install the necessary software on that instance in order to be abel to create the Amazon Machine Image (AMI) which we can use for deplyoing to the Snwoball Edge device.

### 1.1 Launching a new instance

1. Use the workshop [**portal**](https://portal.awsworkshop.io/) to login to the AWS Management Console

1. Navigate to *EC2* service console.

1. Select **Launch Instance**.

  ![11_1](/api/workshops/sbe-workshop-2018/content/assets/images/11_1.png)

1. **Select** to launch `Ubuntu Server 16.04 LTS (HVM), SSD Volume Type` from the list.

	_NOTE_: if you cannot locate the `Ubuntu Server 16.04 LTS` AMI you could also select the `Ubuntu Server 18.04 LTS` version of the AMI.

  ![11_2](/api/workshops/sbe-workshop-2018/content/assets/images/11_2.png)

1. For this workshop we are using a smaller instance type to build our Amazon Machine Image (AMI). Choose the `t3.micro` instance type and click **Configure Instance Details** 

   _NOTE_: If you were actually building an AMI for the new Snowball Edge device with GPU support, you would want to select the `p3.2xlarge` instance type. Unfortunately, we don't have that instance available for the workshop. 

1. Use the `Network` drop down box to select **Demo VPC**. 

1. Once you choose your network, the subnet should populate automatically (there is only one). Then choose **Enable** in the Auto-assign Public IP drop down box. Click **Add Storage**.

1. Click **Add Tags**.

1. Click **Configure Security Group**.

1. Add a `Custom TCP` rule to the instance's security group by selecting to **Add Rule**.

   ![11_4](/api/workshops/sbe-workshop-2018/content/assets/images/11_4.png)
   
   **NB**: The configuration should be as follows:

   * *Type*: `Custom TCP`
   * *Protocol*: `TCP`
   * *Port Range*: `8883`
   * *Source*: `Anywhere`
   * *Description*: `MQTT Communications`
   
   This change to the security group is necessary in order to enable *Greengrass* to communicate with *AWS IoT Core* via MQTT using certificate based authentication.
   
   	_NOTE_: in a live environment we would not recommend to leave the any ports open to all IP addresses if possible.

1. Review the selected configuration and confirm by pressing the **Launch** button.
	
	![11_5](/api/workshops/sbe-workshop-2018/content/assets/images/11_5.png)

1. Create a new keypair, call it `SBE_Workshop` and download the private key, select to **Launch Instance**.

   ![11_6](/api/workshops/sbe-workshop-2018/content/assets/images/11_6.png)

1. Once you have created the instance and downloaded the private key click to **View Instances** to confirm that everything is running fine.
	
	![11_7](/api/workshops/sbe-workshop-2018/content/assets/images/11_7.png)

### 1.2 Connecting to the new instance from your computer

If you do not want or cannot use your own computer for these steps you can also go through these steps using the embedded console in your *Cloud9* environment.

1. On your computer make sure that the private key you downloaded has the correct permissions set.

  Command: `chmod 600 ~/Downloads/SBE_Workshop.pem`

  ![12_1](/api/workshops/sbe-workshop-2018/content/assets/images/12_1.png)

1. Look up the connection information for your instance in the EC2 console.
	
	![12_2](/api/workshops/sbe-workshop-2018/content/assets/images/12_2.png)
	
1. Use the connection information to create an SSH session to your running instance.

  <details>
  	<summary>Command: `ssh -i "~/Downloads/SBE_Workshop.pem" ubuntu@ec2-34-222-222-74.us-west-2.compute.amazonaws.com`</summary>

		Output:
	  	
	  	The authenticity of host 'ec2-34-222-222-74.us-west-2.compute.amazonaws.com (34.222.222.74)' can't be established.
	  	ECDSA key fingerprint is SHA256:JyJwNbtJYB4GPJCQHZCnxsdtKIv4cKM596uum8TFhBY.
	  	Are you sure you want to continue connecting (yes/no)? yes
	  	Warning: Permanently added 'ec2-34-222-222-74.us-west-2.compute.amazonaws.com,34.222.222.74' (ECDSA) to the list of known hosts.
	  	Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.4.0-1067-aws x86_64)
	  	
	  	 * Documentation:  https://help.ubuntu.com
	  	 * Management:     https://landscape.canonical.com
	  	 * Support:        https://ubuntu.com/advantage
	  	
	  	  Get cloud support with Ubuntu Advantage Cloud Guest:
	  	    http://www.ubuntu.com/business/services/cloud
	  	
	  	0 packages can be updated.
	  	0 updates are security updates.
	
	  ​	
	  ​	
	  ​	The programs included with the Ubuntu system are free software;
	  ​	the exact distribution terms for each program are described in the
	  ​	individual files in /usr/share/doc/*/copyright.
	  ​	
	  	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	  	applicable law.
	  	
	  	To run a command as administrator (user "root"), use "sudo <command>".
	  	See "man sudo_root" for details.
  </details>

  ![12_3](/api/workshops/sbe-workshop-2018/content/assets/images/12_3.png)
  ![12_4](/api/workshops/sbe-workshop-2018/content/assets/images/12_4.png)

1. Once connected to the *EC2* instance check whether there are any updates available.

	<details>
		<summary>Command: `sudo apt-get update`</summary>

		Output:
		
		Hit:1 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial InRelease
		Get:2 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates InRelease [109 kB]
		Get:3 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-backports InRelease [107 kB]
		Get:4 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/main Sources [868 kB]
		Get:5 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/restricted Sources [4,808 B]
		Get:6 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/universe Sources [7,728 kB]
		Get:7 http://security.ubuntu.com/ubuntu xenial-security InRelease [107 kB]
		Get:8 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/multiverse Sources [179 kB]
		Get:9 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [7,532 kB]
		Get:10 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/universe Translation-en [4,354 kB]
		Get:11 http://security.ubuntu.com/ubuntu xenial-security/main Sources [136 kB]
		Get:12 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/multiverse amd64 Packages [144 kB]
		Get:13 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial/multiverse Translation-en [106 kB]
		Get:14 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main Sources [323 kB]
		Get:15 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/restricted Sources [2,528 B]
		Get:16 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/universe Sources [225 kB]
		Get:17 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/multiverse Sources [8,384 B]
		Get:18 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [869 kB]
		Get:19 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main Translation-en [353 kB]
		Get:20 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [697 kB]
		Get:21 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/universe Translation-en [282 kB]
		Get:22 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [16.4 kB]
		Get:23 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/multiverse Translation-en [8,344 B]
		Get:24 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-backports/main Sources [4,868 B]
		Get:25 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-backports/universe Sources [6,740 B]
		Get:26 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-backports/main amd64 Packages [7,304 B]
		Get:27 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-backports/main Translation-en [4,456 B]
		Get:28 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [7,804 B]
		Get:29 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-backports/universe Translation-en [4,184 B]
		Get:30 http://security.ubuntu.com/ubuntu xenial-security/restricted Sources [2,116 B]
		Get:31 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [78.8 kB]
		Get:32 http://security.ubuntu.com/ubuntu xenial-security/multiverse Sources [2,088 B]
		Get:33 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [573 kB]
		Get:34 http://security.ubuntu.com/ubuntu xenial-security/main Translation-en [240 kB]
		Get:35 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [393 kB]
		Get:36 http://security.ubuntu.com/ubuntu xenial-security/universe Translation-en [151 kB]
		Get:37 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [3,460 B]
		Get:38 http://security.ubuntu.com/ubuntu xenial-security/multiverse Translation-en [1,744 B]
		Fetched 25.6 MB in 4s (5,986 kB/s)
		Reading package lists... Done
	</details>

1. Now upgrade the installed packages on your *EC2* instance.

  <details>
  	<summary>Command: `sudo apt-get upgrade -y`</summary>

	  	Output:
	  	
	  	Reading package lists... Done
	  	Building dependency tree
	  	Reading state information... Done
	  	Calculating upgrade... Done
	  	The following packages have been kept back:
	  	  linux-aws linux-headers-aws linux-image-aws
	  	The following packages will be upgraded:
	  	  apparmor apt apt-transport-https apt-utils bind9-host
	  	  cloud-initramfs-copymods cloud-initramfs-dyn-netconf curl dnsutils git
	  	  git-man initramfs-tools initramfs-tools-bin initramfs-tools-core
	  	  libapparmor-perl libapparmor1 libapt-inst2.0 libapt-pkg5.0 libbind9-140
	  	  libcurl3-gnutls libdns-export162 libdns162 libglib2.0-0 libglib2.0-data
	  	  libisc-export160 libisc160 libisccc140 libisccfg140 liblwres141
	  	  libpam-systemd libsystemd0 libudev1 open-iscsi openssh-client openssh-server
	  	  openssh-sftp-server overlayroot python3-requests python3-update-manager
	  	  systemd systemd-sysv tzdata udev update-manager-core
	  	44 upgraded, 0 newly installed, 0 to remove and 3 not upgraded.
	  	Need to get 16.4 MB of archives.
	  	After this operation, 24.6 kB of additional disk space will be used.
	  	Get:1 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libapt-pkg5.0 amd64 1.2.29 [707 kB]
	  	Get:2 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libapt-inst2.0 amd64 1.2.29 [55.5 kB]
	  	Get:3 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apt amd64 1.2.29 [1,041 kB]
	  	Get:4 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apt-utils amd64 1.2.29 [196 kB]
	  	Get:5 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libsystemd0 amd64 229-4ubuntu21.5 [204 kB]
	  	Get:6 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libpam-systemd amd64 229-4ubuntu21.5 [115 kB]
	  	Get:7 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 systemd amd64 229-4ubuntu21.5 [3,635 kB]
	  	Get:8 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 udev amd64 229-4ubuntu21.5 [993 kB]
	  	Get:9 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libudev1 amd64 229-4ubuntu21.5 [54.2 kB]
	  	Get:10 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 initramfs-tools all 0.122ubuntu8.13 [8,936 B]
	  	Get:11 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 initramfs-tools-core all 0.122ubuntu8.13 [44.7 kB]
	  	Get:12 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 initramfs-tools-bin amd64 0.122ubuntu8.13 [9,742 B]
	  	Get:13 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 systemd-sysv amd64 229-4ubuntu21.5 [11.7 kB]
	  	Get:14 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libapparmor1 amd64 2.10.95-0ubuntu2.10 [29.7 kB]
	  	Get:15 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libglib2.0-0 amd64 2.48.2-0ubuntu4.1 [1,120 kB]
	  	Get:16 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 open-iscsi amd64 2.0.873+git0.3b4b4500-14ubuntu3.6 [334 kB]
	  	Get:17 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 tzdata all 2018f-0ubuntu0.16.04 [166 kB]
	  	Get:18 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libisc-export160 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [153 kB]
	  	Get:19 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libdns-export162 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [667 kB]
	  	Get:20 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libapparmor-perl amd64 2.10.95-0ubuntu2.10 [31.6 kB]
	  	Get:21 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apparmor amd64 2.10.95-0ubuntu2.10 [451 kB]
	  	Get:22 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 curl amd64 7.47.0-1ubuntu2.9 [138 kB]
	  	Get:23 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libcurl3-gnutls amd64 7.47.0-1ubuntu2.9 [184 kB]
	  	Get:24 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apt-transport-https amd64 1.2.29 [26.2 kB]
	  	Get:25 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 bind9-host amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [38.4 kB]
	  	Get:26 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 dnsutils amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [89.2 kB]
	  	Get:27 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libisc160 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [215 kB]
	  	Get:28 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libdns162 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [881 kB]
	  	Get:29 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libisccc140 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [16.3 kB]
	  	Get:30 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libisccfg140 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [40.4 kB]
	  	Get:31 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 liblwres141 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [33.7 kB]
	  	Get:32 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libbind9-140 amd64 1:9.10.3.dfsg.P4-8ubuntu1.11 [23.6 kB]
	  	Get:33 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libglib2.0-data all 2.48.2-0ubuntu4.1 [132 kB]
	  	Get:34 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 openssh-sftp-server amd64 1:7.2p2-4ubuntu2.5 [38.6 kB]
	  	Get:35 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 openssh-server amd64 1:7.2p2-4ubuntu2.5 [335 kB]
	  	Get:36 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 openssh-client amd64 1:7.2p2-4ubuntu2.5 [588 kB]
	  	Get:37 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 python3-update-manager all 1:16.04.14 [33.1 kB]
	  	Get:38 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 update-manager-core all 1:16.04.14 [5,504 B]
	  	Get:39 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 git-man all 1:2.7.4-0ubuntu1.5 [736 kB]
	  	Get:40 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 git amd64 1:2.7.4-0ubuntu1.5 [2,714 kB]
	  	Get:41 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 python3-requests all 2.9.1-3ubuntu0.1 [55.8 kB]
	  	Get:42 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 cloud-initramfs-copymods all 0.27ubuntu1.6 [4,380 B]
	  	Get:43 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 cloud-initramfs-dyn-netconf all 0.27ubuntu1.6 [6,892 B]
	  	Get:44 http://us-west-2.ec2.archive.ubuntu.com/ubuntu xenial-updates/main amd64 overlayroot all 0.27ubuntu1.6 [15.7 kB]
	  	Fetched 16.4 MB in 0s (41.4 MB/s)
	  	Extracting templates from packages: 100%
	  	Preconfiguring packages ...
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../libapt-pkg5.0_1.2.29_amd64.deb ...
	  	Unpacking libapt-pkg5.0:amd64 (1.2.29) over (1.2.27) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	Setting up libapt-pkg5.0:amd64 (1.2.29) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../libapt-inst2.0_1.2.29_amd64.deb ...
	  	Unpacking libapt-inst2.0:amd64 (1.2.29) over (1.2.27) ...
	  	Preparing to unpack .../archives/apt_1.2.29_amd64.deb ...
	  	Unpacking apt (1.2.29) over (1.2.27) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	Processing triggers for man-db (2.7.5-1) ...
	  	Setting up apt (1.2.29) ...
	  	Installing new version of config file /etc/apt/apt.conf.d/01autoremove ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../apt-utils_1.2.29_amd64.deb ...
	  	Unpacking apt-utils (1.2.29) over (1.2.27) ...
	  	Preparing to unpack .../libsystemd0_229-4ubuntu21.5_amd64.deb ...
	  	Unpacking libsystemd0:amd64 (229-4ubuntu21.5) over (229-4ubuntu21.4) ...
	  	Processing triggers for man-db (2.7.5-1) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	Setting up libsystemd0:amd64 (229-4ubuntu21.5) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../libpam-systemd_229-4ubuntu21.5_amd64.deb ...
	  	Unpacking libpam-systemd:amd64 (229-4ubuntu21.5) over (229-4ubuntu21.4) ...
	  	Preparing to unpack .../systemd_229-4ubuntu21.5_amd64.deb ...
	  	Unpacking systemd (229-4ubuntu21.5) over (229-4ubuntu21.4) ...
	  	Processing triggers for man-db (2.7.5-1) ...
	  	Processing triggers for dbus (1.10.6-1ubuntu3.3) ...
	  	Processing triggers for ureadahead (0.100.0-19) ...
	  	Setting up systemd (229-4ubuntu21.5) ...
	  	addgroup: The group `systemd-journal' already exists as a system group. Exiting.
	  	[/usr/lib/tmpfiles.d/var.conf:14] Duplicate line for path "/var/log", ignoring.
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../udev_229-4ubuntu21.5_amd64.deb ...
	  	Unpacking udev (229-4ubuntu21.5) over (229-4ubuntu21.4) ...
	  	Preparing to unpack .../libudev1_229-4ubuntu21.5_amd64.deb ...
	  	Unpacking libudev1:amd64 (229-4ubuntu21.5) over (229-4ubuntu21.4) ...
	  	Processing triggers for man-db (2.7.5-1) ...
	  	Processing triggers for systemd (229-4ubuntu21.5) ...
	  	Processing triggers for ureadahead (0.100.0-19) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	Setting up libudev1:amd64 (229-4ubuntu21.5) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../initramfs-tools_0.122ubuntu8.13_all.deb ...
	  	Unpacking initramfs-tools (0.122ubuntu8.13) over (0.122ubuntu8.12) ...
	  	Preparing to unpack .../initramfs-tools-core_0.122ubuntu8.13_all.deb ...
	  	Unpacking initramfs-tools-core (0.122ubuntu8.13) over (0.122ubuntu8.12) ...
	  	Preparing to unpack .../initramfs-tools-bin_0.122ubuntu8.13_amd64.deb ...
	  	Unpacking initramfs-tools-bin (0.122ubuntu8.13) over (0.122ubuntu8.12) ...
	  	Preparing to unpack .../systemd-sysv_229-4ubuntu21.5_amd64.deb ...
	  	Unpacking systemd-sysv (229-4ubuntu21.5) over (229-4ubuntu21.4) ...
	  	Processing triggers for man-db (2.7.5-1) ...
	  	Setting up systemd-sysv (229-4ubuntu21.5) ...
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../libapparmor1_2.10.95-0ubuntu2.10_amd64.deb ...
	  	Unpacking libapparmor1:amd64 (2.10.95-0ubuntu2.10) over (2.10.95-0ubuntu2.9) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	Setting up libapparmor1:amd64 (2.10.95-0ubuntu2.10) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	(Reading database ... 51284 files and directories currently installed.)
	  	Preparing to unpack .../libglib2.0-0_2.48.2-0ubuntu4.1_amd64.deb ...
	  	Unpacking libglib2.0-0:amd64 (2.48.2-0ubuntu4.1) over (2.48.2-0ubuntu4) ...
	  	Preparing to unpack .../open-iscsi_2.0.873+git0.3b4b4500-14ubuntu3.6_amd64.deb ...
	  	Unpacking open-iscsi (2.0.873+git0.3b4b4500-14ubuntu3.6) over (2.0.873+git0.3b4b4500-14ubuntu3.5) ...
	  	Preparing to unpack .../tzdata_2018f-0ubuntu0.16.04_all.deb ...
	  	Unpacking tzdata (2018f-0ubuntu0.16.04) over (2017c-0ubuntu0.16.04) ...
	  	Preparing to unpack .../libisc-export160_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking libisc-export160 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libdns-export162_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking libdns-export162 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libapparmor-perl_2.10.95-0ubuntu2.10_amd64.deb ...
	  	Unpacking libapparmor-perl (2.10.95-0ubuntu2.10) over (2.10.95-0ubuntu2.9) ...
	  	Preparing to unpack .../apparmor_2.10.95-0ubuntu2.10_amd64.deb ...
	  	Unpacking apparmor (2.10.95-0ubuntu2.10) over (2.10.95-0ubuntu2.9) ...
	  	Preparing to unpack .../curl_7.47.0-1ubuntu2.9_amd64.deb ...
	  	Unpacking curl (7.47.0-1ubuntu2.9) over (7.47.0-1ubuntu2.8) ...
	  	Preparing to unpack .../libcurl3-gnutls_7.47.0-1ubuntu2.9_amd64.deb ...
	  	Unpacking libcurl3-gnutls:amd64 (7.47.0-1ubuntu2.9) over (7.47.0-1ubuntu2.8) ...
	  	Preparing to unpack .../apt-transport-https_1.2.29_amd64.deb ...
	  	Unpacking apt-transport-https (1.2.29) over (1.2.27) ...
	  	Preparing to unpack .../bind9-host_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking bind9-host (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../dnsutils_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking dnsutils (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libisc160_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking libisc160:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libdns162_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking libdns162:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libisccc140_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking libisccc140:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libisccfg140_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking libisccfg140:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../liblwres141_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking liblwres141:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libbind9-140_1%3a9.10.3.dfsg.P4-8ubuntu1.11_amd64.deb ...
	  	Unpacking libbind9-140:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) over (1:9.10.3.dfsg.P4-8ubuntu1.10) ...
	  	Preparing to unpack .../libglib2.0-data_2.48.2-0ubuntu4.1_all.deb ...
	  	Unpacking libglib2.0-data (2.48.2-0ubuntu4.1) over (2.48.2-0ubuntu4) ...
	  	Preparing to unpack .../openssh-sftp-server_1%3a7.2p2-4ubuntu2.5_amd64.deb ...
	  	Unpacking openssh-sftp-server (1:7.2p2-4ubuntu2.5) over (1:7.2p2-4ubuntu2.4) ...
	  	Preparing to unpack .../openssh-server_1%3a7.2p2-4ubuntu2.5_amd64.deb ...
	  	Unpacking openssh-server (1:7.2p2-4ubuntu2.5) over (1:7.2p2-4ubuntu2.4) ...
	  	Preparing to unpack .../openssh-client_1%3a7.2p2-4ubuntu2.5_amd64.deb ...
	  	Unpacking openssh-client (1:7.2p2-4ubuntu2.5) over (1:7.2p2-4ubuntu2.4) ...
	  	Preparing to unpack .../python3-update-manager_1%3a16.04.14_all.deb ...
	  	Unpacking python3-update-manager (1:16.04.14) over (1:16.04.13) ...
	  	Preparing to unpack .../update-manager-core_1%3a16.04.14_all.deb ...
	  	Unpacking update-manager-core (1:16.04.14) over (1:16.04.13) ...
	  	Preparing to unpack .../git-man_1%3a2.7.4-0ubuntu1.5_all.deb ...
	  	Unpacking git-man (1:2.7.4-0ubuntu1.5) over (1:2.7.4-0ubuntu1.4) ...
	  	Preparing to unpack .../git_1%3a2.7.4-0ubuntu1.5_amd64.deb ...
	  	Unpacking git (1:2.7.4-0ubuntu1.5) over (1:2.7.4-0ubuntu1.4) ...
	  	Preparing to unpack .../python3-requests_2.9.1-3ubuntu0.1_all.deb ...
	  	Unpacking python3-requests (2.9.1-3ubuntu0.1) over (2.9.1-3) ...
	  	Preparing to unpack .../cloud-initramfs-copymods_0.27ubuntu1.6_all.deb ...
	  	Unpacking cloud-initramfs-copymods (0.27ubuntu1.6) over (0.27ubuntu1.5) ...
	  	Preparing to unpack .../cloud-initramfs-dyn-netconf_0.27ubuntu1.6_all.deb ...
	  	Unpacking cloud-initramfs-dyn-netconf (0.27ubuntu1.6) over (0.27ubuntu1.5) ...
	  	Preparing to unpack .../overlayroot_0.27ubuntu1.6_all.deb ...
	  	Unpacking overlayroot (0.27ubuntu1.6) over (0.27ubuntu1.5) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	Processing triggers for systemd (229-4ubuntu21.5) ...
	  	Processing triggers for ureadahead (0.100.0-19) ...
	  	Processing triggers for man-db (2.7.5-1) ...
	  	Processing triggers for ufw (0.35-0ubuntu2) ...
	  	Setting up libapt-inst2.0:amd64 (1.2.29) ...
	  	Setting up apt-utils (1.2.29) ...
	  	Setting up libpam-systemd:amd64 (229-4ubuntu21.5) ...
	  	Setting up udev (229-4ubuntu21.5) ...
	  	addgroup: The group `input' already exists as a system group. Exiting.
	  	update-initramfs: deferring update (trigger activated)
	  	Setting up initramfs-tools-bin (0.122ubuntu8.13) ...
	  	Setting up initramfs-tools-core (0.122ubuntu8.13) ...
	  	Setting up initramfs-tools (0.122ubuntu8.13) ...
	  	update-initramfs: deferring update (trigger activated)
	  	Setting up libglib2.0-0:amd64 (2.48.2-0ubuntu4.1) ...
	  	No schema files found: doing nothing.
	  	Setting up open-iscsi (2.0.873+git0.3b4b4500-14ubuntu3.6) ...
	  	Setting up tzdata (2018f-0ubuntu0.16.04) ...
	  	
	  	Current default time zone: 'Etc/UTC'
	  	Local time is now:      Fri Oct 26 14:46:19 UTC 2018.
	  	Universal Time is now:  Fri Oct 26 14:46:19 UTC 2018.
	  	Run 'dpkg-reconfigure tzdata' if you wish to change it.
	  	
	  	Setting up libisc-export160 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up libdns-export162 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up libapparmor-perl (2.10.95-0ubuntu2.10) ...
	  	Setting up apparmor (2.10.95-0ubuntu2.10) ...
	  	Installing new version of config file /etc/apparmor.d/abstractions/private-files ...
	  	Installing new version of config file /etc/apparmor.d/abstractions/private-files-strict ...
	  	Installing new version of config file /etc/apparmor.d/abstractions/ubuntu-browsers.d/user-files ...
	  	update-rc.d: warning: start and stop actions are no longer supported; falling back to defaults
	  	Skipping profile in /etc/apparmor.d/disable: usr.sbin.rsyslogd
	  	Setting up libcurl3-gnutls:amd64 (7.47.0-1ubuntu2.9) ...
	  	Setting up curl (7.47.0-1ubuntu2.9) ...
	  	Setting up apt-transport-https (1.2.29) ...
	  	Setting up libisc160:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up libdns162:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up libisccc140:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up libisccfg140:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up libbind9-140:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up liblwres141:amd64 (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up bind9-host (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up dnsutils (1:9.10.3.dfsg.P4-8ubuntu1.11) ...
	  	Setting up libglib2.0-data (2.48.2-0ubuntu4.1) ...
	  	Setting up openssh-client (1:7.2p2-4ubuntu2.5) ...
	  	Setting up openssh-sftp-server (1:7.2p2-4ubuntu2.5) ...
	  	Setting up openssh-server (1:7.2p2-4ubuntu2.5) ...
	  	Setting up python3-update-manager (1:16.04.14) ...
	  	Setting up update-manager-core (1:16.04.14) ...
	  	Setting up git-man (1:2.7.4-0ubuntu1.5) ...
	  	Setting up git (1:2.7.4-0ubuntu1.5) ...
	  	Setting up python3-requests (2.9.1-3ubuntu0.1) ...
	  	Setting up cloud-initramfs-copymods (0.27ubuntu1.6) ...
	  	Setting up cloud-initramfs-dyn-netconf (0.27ubuntu1.6) ...
	  	Setting up overlayroot (0.27ubuntu1.6) ...
	  	Processing triggers for libc-bin (2.23-0ubuntu10) ...
	  	Processing triggers for initramfs-tools (0.122ubuntu8.13) ...
	  	update-initramfs: Generating /boot/initrd.img-4.4.0-1067-aws
	  	W: mdadm: /etc/mdadm/mdadm.conf defines no arrays.
  </details>

  ![12_5](/api/workshops/sbe-workshop-2018/content/assets/images/12_5.png)

1. Well done! You now have an up-to-date Ubuntu instance. Move on to the next module to add AWS Greengrass.

