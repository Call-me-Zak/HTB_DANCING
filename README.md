# CTF Write Up for HTB_DANCING

### Briefing
In this CTF we'll be tackling Server Message Block (SMB) a file & printer sharing protocole you can think of it as a less secure version of Secure File Transfer Protocole (SFTP) which uses port 22 and built on (SSH2).

#### Recon
As always after connecting to the Virtual Machine on HTB platform, we can go ahead and scan for opern ports using nmap.

```sudo nmap -sV <ip_address_of_the_vm>```

this'll return the following output :

![image](https://github.com/user-attachments/assets/78c6d8b2-acef-4cff-b433-46a2c6c5b99d)

A simple google search would show that for modern contemporary systems SMB uses port 445 and in legacy systems uses port 139.

So we can easily verify which one is being used by trying to establish a connection by using the following command :

```smbclient -L <ip_address_of_the_vm> -p 139```

This command uses the smbclient -L flag to list the available shares, but in our case it returns an error :

```
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to ip_address failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
NT_STATUS_RESOURCE_NAME_NOT_FOUND suggests that specific resource or service name requirde for SMB1 workgroup listing is not available or not found. As smbclient is attempting to use SMB1 to list the workgroup but is failing.

So we can go ahead and try with port 445.

```smbclient -L <ip_address_of_the_vm> -p 445```

We're then prompted for a password, but since we don't know, we can go ahead and try a blank one, the shares still get listed but we get an error.

![image](https://github.com/user-attachments/assets/b6d09cb0-77d0-4c21-b182-8420199d58e7)

We can see 04 shares:

WorkShares being a custom share as it doesn't end with the "$" symbol.
So we can try to directly access that share by using the following command:

````smbclient //ip_address_of_HTB_vm/WorkShares -U ""``` to specify that the username is blank.

We're prompted for a password but when we try a blank password we're then granted access and in the terminal we can see that it now starts with ```>smb```

![image](https://github.com/user-attachments/assets/7772c81c-93e1-499e-aca1-91bd74a4dad3)

From here all that is left is to go through the directory till we find the flag.
We can type ```help``` to list all available commands.

We can already spot some pretty common linux commands

![image](https://github.com/user-attachments/assets/71c71432-7417-46ff-989e-5360de43fadf)

To start we can list all the existant directories by using ```dir```

![image](https://github.com/user-attachments/assets/944137a3-3ec0-4a06-95bb-9e32b245e108)

Then change directories by using ```cd``` and then ```ls``` to list all available files

![image](https://github.com/user-attachments/assets/8a0a2080-ce14-4c3f-bab7-c3cd20cab7e5)

Using ```more worknotes.txt``` allows us to read the contents of the file, as we can see it's not what we're looking for :

![image](https://github.com/user-attachments/assets/b2c5dec7-b4b2-426c-82f6-923887da3853)

We could also download the file into our system by using ```get``` for single files and ```mget``` for multiple files, but we don't need to download anything since we can directly view the contents of the file.

We can then procceed to the 2nd directory by using ```cd ..``` to back one directory and then ```cd James.P```

![image](https://github.com/user-attachments/assets/a023902e-8d4d-4697-8d0a-f9d69006a0e8)

There's our flag.txt file, using ```more flag.txt``` brings up the flag, and done !

![image](https://github.com/user-attachments/assets/43ba705b-3697-4eb0-b4a6-fd9da0ad540f)
