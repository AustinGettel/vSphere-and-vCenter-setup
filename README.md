# vSphere-and-vCenter-setup
Configuring vSphere plus installing a vCenter Server as your first VM

You'll need to start by downloading VMWare vSphere and vCenter from VMware's wewbsite. Google download vmware vsphere to find the downloads page. Download VMware vSphere Hypervisor (ESXi) 7.0U3d; this is the installer for ESXi, the hypervisor that we need to install first. Also download VMware vCenter Server 7.0U3e, we'll use this later.

Now we need to create bootable hardware for the ISO of ESXi that we just installed. I like to Rufus. Select your USB device from the dropdown if it's not already selected, then under boot section click on select and choose the VMware-VMvisor-installer. Lastly click start. This will take a few minutes, but once complete you'll have a bootable usb for VMware ESXi.

The nextg step is to install it in your hardware. I'm running an older workstation, so I get a hardware warning telling me that my hardware is supported for now, but won't be in the future. If you're running unspported hardware you may driver problems. Finish going through the installation, and while it's rebooting at the end unplug the installation USB. Once rebooted we're going to set the IP to static. Press F2, log in, go to Configure Management Netork, then IPv4 Configuration, and select Set static IPv4 address and network configuration. You can either set your own address, subnet mask, and default gateway from here, or the leave them blank.

I'm going to start by changing the name. I've already set it to VirtualHome, so lets change it to virtualAustin. Start by clicking on Networking.

From here flip to the TCP/IP stacks tab and select Default TCP/IP stack.

We can change our name here under Host name. On this screen we can also set our Domain name, DNS server, and IP gateways. I set up a mock Domain, vsphere.local, and used [Cloudflare's DNS](https://www.cloudflare.com/).

The next thing to do is set up time synchronization. So on the Navigator on the left select the Manage option directly under the Host drop down.

<!---I love you <3 wifey--->

By default we should be on the system tab, so select Time & Date. Go to the Edit NTP Settings page. We want it set to Use Network Time Protocol (enable NTP client). From the drop down select Start and stop from host. Now we need to set our NTP servers. You can use your own, but I'm going to use servers from the [pool.ntp.org project](https://www.ntppool.org/en/). To do so in the NTP servers box type the following in complete with commas.

0.pool.ntp.org, 1.pool.ntp.org, 2.pool.ntp.org, 3.pool.ntp.org  

<!---WHY ISN'T IT WORKING?!--->

Okay, but NTP Service status still says stopped, what gives? We've got this. Switch to  the services tab, find and click on ntp (it'll say ntpd for NTP Daemon), and then press the Start button. <!---Don't forget to check you're work by hitting refresh to check that it's running--->

Okay, that last part was something you wanted to do if you were planning on having multiple physical machines running the ESXi hypervisor, or if you're planning on setting up vCenter.

This is a good time to update your licensing for VMWare while we're already in the Manage menu. Just hit the Licensing tab, click Actions, and Assign license is the first, and only, option.

If you skip this step, just remember all these features are only enabled because you're in Evaluation mode, and that might get hairy once it expires for you.

<!---here comes something really hard, oh boy--->
Okay, real quick, lets talk about networking. Select Networking, and go to the VMkernel NICs tab. VMkernels are the way we can set up, and control, virtual Network Interface Cards, or the virtual IP, for our virtual machines that we're going to run, but they can do so much more. For a home lab, you might not ever want to set up more than 1, and if you'd like to enable all the features, just highlight it, and click Edit settings. 

Now in this window you can enable all of the avaiable services, and you'd be able to use all of them on this 1 vKernal. You can also change port grouping, IP version and settings, and TCP/IP stack. Now lets say further down the line you decide to set up a vertual router in you're homelab. Now you would want to set up a sperate vKernel, and only enable the services needed, or in a work environment you may have 4 physical NICs and may use 2 set up to a specific virtual switch set up for ISCUSSI for storage. Today, I'm just leaving the one vKernel and 1 virtual switch, but I'm enabling all the services.

<!---here comes the fun part--->

Earlier we set up [Cloudflare's DNS](https://www.cloudflare.com/), but in a future lab I'll be setting up Active Directy and then adjusting DNS to point to the DNS. So, since we're skipping AD for now, that means....

<b>WE'RE READY TO DEPLOY SOME VIRTUAL MACHINES!</B>

<!---that felt good--->

Because this lab is about getting vSphere and vCenter set up, we're going to start with vCenter. Without AD on vCenter will have all local accounts at first. If you don't already have vCenter downloaded, you'll need to do that now, or skip ahead if you want to skip vCenter. Do you have it downloaded? Good. Go to your downloads folder, and find the VMware-VCSA-all-7.0.3-19717403 file. Now, the easiest method to install this is by using the GUI installer. I'm running Windows 11, but the following steps should be the same for windows 10. Right the file, and select Mount. If Mount is not an option, click show more options, and Mount should be under this list. It may take few minutes to mount. Once complete it will show as a CD/DVD disc under This PC in Windows Explorer.

Now, to use the GUI installer, double click on the folder titled vcsa-ui-installer, on the next screen double click the folder for the OS you're using, for me that is win32, and double click on installer to run the installation wizard.

Now just walk through the wizard, nothing hard or confusing here. In a production environment, we would want to do this with domain name, but we're starting our home lab here and don't have any of that set up [YET]. So select install, hit Deploy vCenter Server, and point it towards your ESXi host with your IP address. You're not making a new account here, it's asking for root and the password you set when first installing ESXi.

Hit yes for this untrusted cert warning.

You could leave the default VM name for your new vCenter server, or change it to whatever you'd like

On deplyment size, this is going to depened on what you're planning on running, and more importantly, what resources your hardware has available. Most home labs are probably never going to have more than a 1000 VMs, but you'd be surprised how easy it is to get up to 100. I'm going with small.

On the next screen you want to install on the existing datastore, becasue that's all we have. But what's that option to Enable Thin Disk Mode? With out thin disk, when you install a VM you have to allocate how much storage space you want, so you might choose 100GB, and all of that space is now used by that VM. If you enable thin disk, each VM will just use how much storage space it needs, and will increase as needed. I prefor using thin disk.

Now more network configuring. Don't stress too hard, we've already addressed a lot of this. Choose your network, probably VM Network by default. For your IP address, just choose the next address on the table, for instance my ESXi is 192.168.50.169 so for vCenter I'll use 192.168.50.170. Subnet is 250.250.250.0. My default gateway is 192.168.50.1, yours is probably something similar. We're still using [Cloudflare's DNS](https://www.cloudflare.com/), so it's 1.1.1.1,1.0.0.1. Don't change ports. 

Review the information and if it looks right select finish. This a big install, and will take a few minutes. Once the install is done, stage 1 deploying the vCenter is done. On to stage 2! Set up!

Time synchronization; we dealt with this earlier.  If you followed along and set up your ESXi host to the NTP server, you can select either the host ESXi or NTP server. Since we already did that earlier, I'm just going to have vCenter sync with my host ESXi hypervisor. Now it's asking about SSH access. In a production setting, this is almost always going to be disabled, but this is my home lab, and I would like to have remote access, so I'm enabling, and will show the set up process in a future lab.

Single Sign-On Configuration. This is asking you to set up an account information for the purpose of signing into vCenter. You do have to create a SSO domain at this time, to keep it simple for now just use the default vsphere.local, and, yes, you do have to type that in. Then finish setting up your account and <b>DON'T FORGET YOUR PASSWORD!</B>

Do you want VMWare collecting data from your homelab? Yeah, me neither. Uncheck that box. Next. Review your info, hit setup, and it's time to hurry up and wait some more.

When it's done, open a new tab in your browser and type in the IP address you set. don't forget when you sign in it's not just asking for you type your user name in, you need the whole domain, so if you used administrator and vsphere.local it's administrator@vsphere.local.

Did it work? Great! That's it! You now have a homelab with a VM server on it!

Now real quick, maybe you have more than 1 ESXi hypervisor, and want them all to work together? I here you. We need to make a cluster to combine them together. This only take a moment. Right click on the vCenter IP on the left, and hit New DataCenter. Name it whatever you'd like, and leave the IP address alone. 

You're new datacenter in listed on the left under your vCenter IP address. Right click the datacenter, and hit new cluster.

Name your cluster, and choose your resources. vSphere DRS is distributed resources. this can automatically move, or vMotion, your VMs. vSphere HA is for High Availability. This means if you have a VM running, but the host isn't heartbeating, or can't be found, vCenter will relaunch the VM on another machine. vSAN is ESXi's version on Storage Area Networks, so as you add more hosts and more storage, it's available to all the machines. Review your info and finish.

Now you have a cluster and it's time to add your hosts to it. Right click the cluster, and select Add Hosts.

Type in the hosts IP address, root, and your password. Hit the add hosts button to type in more hosts, and do this for all the hosts you want to add. If you gave them all the same password just hit the box for use the same creditionals. Once done, you'll get a security alert. Check all the boxes. now hit next, and finish. It will take a few minutes, but they will start adding the hosts to the cluster, and you can watch the progress at the bottum. The host with vCenter will not be in maintence mode, but the rest will go into maintance. Once the machines are reconfigured, right click on the hosts in maintence mode, hover over maintance mode, and select exit maintenance mode.

On a side note, I would make a cluster now even if you do not have multiple machines, or plan on adding any more machines. vCenter is a server designed to control multiple ESXi hypervisor hosts, and clusters is the way we do that, so this is the easiest way to utilize vCenter. You could also just add the host directly to the DataCenter, without making a cluster.

Now, would you like to deploy a bonus VM? Yeah, lets do it. On the left above the IP for vCenter, click the icon that is 2nd to the left; this is VM View. Right click on the data center, New Folder, and New VM and Template Folder. I'm going to call this folder Template. 

You may have to refresh to see this new folder on the left. Right click the folder, and hit New Virtual Machine. Select create a new virtual machine. I'm installing Ubuntu 22.04 so I'm going to name this Ubuntu 2204.

On compute resource you choose which host or cluster you want to use. Select storage you have to select the data store. If you set up a cluster with more than one machine and only have local datastores you'd choose which machine to use here. If you have a vSAN set up you could select that at this time.

Don't change compatibility. Select the guest OS your using, again I'm installing Ubuntu 64-bit, so I choose Linux for Guest OS Family, and OS Version I'll select Ubuntu 64-bit.

Under customize you can select how much hardware to allocate to the VM. You can set everything to max understanding that not every VM is going to be using that much all the time, or limit it. If earlier you enabled thin disk, then make sure under the drop down for New Hard Disk you change the default disk provisioning from thick provision to thin provision. Click next, review, and finish.

The template is now complete, but we haven't actually installed ubuntu yet. So select your template, and power it on by hitting the green play button up top. Ignore any error messages you may get. Refresh if you need to. You could use a remote console, which you may need to download seperatly. I'm going to launch the web concole.

Wait, what? It's not doing anything? Oh, right, we still need to load the ISO to install the OS. Now, again, we could use the remote console, or we can upload the ISO. Two ways to to do that. One, create a bootable CD/DVD. .... Yeah, agreed. Two, jump back to the vSphere UI. Click on storage, right click on the datastore you're using, and select Browse. Now you can either upload to here, or make another directory to neatly store your ISO in. If you did that too, select your new directory, and hit the upload button.

Now find your ISO, and upload it. Done? Good. Back to vCenter. Go to the template you made. Hit actions at the top, edit settings, and from the CD/DVD drop down select Datastore ISO File. Now, the cavemen can find that bootable disc they had lying around, while the rest of us only need find the ISO file we upload. 

Now we install Ubuntu. Go back to your web console, and have at it.
<!---if you got this far, I don't need to hold your hand for this part---> 
Once done, power off your VM. Now right click the VM, hoover over template, and hit convert to template.

<!---recap time!--->
So now you have succesfully installed VMWare ESXi 7 vSphere HyperVisor, made a VM vCenter server to manage your host machine (and any others you may add down the line), and not only installed an Ubuntu VM, but also made a template so you can easly deploy as many as you need in the future!
<!---Congratulations to us--->:confetti_ball:
