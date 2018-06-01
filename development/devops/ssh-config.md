<!-- TITLE: Ssh Config -->
<!-- SUBTITLE: Setting up .ssh/config -->

# Introduction

> **Who should be doing this:** Developers and designers working on client websites
{.is-info}

> **What will  you be doing:** Setting up SSH shortcuts for our servers
{.is-info}

> **Why you should be doing this:** Allows for easy access to connect to servers
{.is-info}

> **Who should you seek help from if necessary:** Lead developer
{.is-info}

> **How long should this take:** 5 - 10 minutes
{.is-info}

-----
# Process
This will allow you to use an alias to ssh into a client server. A password will be required if your SSH key has not been added to the authorized_keys file on the server.

## Setting up config

1. Open up terminal
2. Use the command `vim ./ssh/config` to access the configuration file
3. Create a new alias: (press the 'a' key to enter edit mode in vim)


```batchfile
HOST CLIENT
  User username
  HostName client.domain
  ForwardAgent yes
```

4. Save the file by hitting `escape` followd by `shift`+`;`
5. Use the command `wq` to `write` then `quit` vim
6. Add your Private key to your path with the command `ssh-add ./ssh/id_rsa`
	* This will allow you to use your own key to push and pull on the server if the server does not have access to the repo it self
7. Once out of vim, you can use `ssh CLIENT` to connect to the server.
	* If your `id_rsa.pub` is not in the servers `authorized_keys` you will be required to provide a password

## Config List
This list is up-to-date as of 06/01/2018. It is the list used by Kyle Jasso

```batchfile
# Brolik.net server
HOST BRO
  User brolik.net
  HostName s99349.gridserver.com

# Brolik.com server
HOST BROCOM
  User brolik.com
  HostName s48996.gridserver.com

# EC2 Wiki Server
HOST BROW
  User ec2-user
  ForwardAgent yes
  HostName ec2-54-145-41-104.compute-1.amazonaws.com

HOST CSA
  User christinaseixacademy.org
  HostName s209757.gridserver.com

HOST FISH
  User fishtown
  HostName chi14.stablehost.com

HOST JHP
  User ubuntu
  ForwardAgent yes
  HostName ec2-34-207-210-37.compute-1.amazonaws.com 

HOST JTPL
  User clickitclam.com
  HostName s199603.gridserver.com

HOST MAV
  User mavenagency.com
  HostName s71154.gridserver.com

HOST MLCS
  User cosmetic-eyes.com
  HostName s208364.gridserver.com

# Load Balancer 1
Host MLSPA
  User ec2-user
  HostName ec2-52-91-200-46.compute-1.amazonaws.com
  ForwardAgent yes

# Load Balancer 2
Host MLSPAB
  User ec2-user
  HostName ec2-35-171-8-241.compute-1.amazonaws.com
  ForwardAgent yes

HOST NUV
  User nuvanna-brolik
  HostName nuvanna.sftp.wpengine.com
  Port 2222

HOST PWR
  User powerhouse.com
  HostName s212252.gridserver.com

HOST RFG
  User rehabfinancialgroup.com
  HostName s197969.gridserver.com

HOST ROAM
  User ec2-user
  HostName ec2-34-236-150-182.compute-1.amazonaws.com
  ForwardAgent yes

HOST SSM
  User summitsteelinc.com
  HostName s168214.gridserver.com

HOST STI
  User stocktoninn.com
  HostName s178548.gridserver.com

HOST GIC
  User globeinternationalcorp.com
  HostName s208811.gridserver.com

HOST RPT
  User bereact.com
  HostName s177960.gridserver.com

# Digital Ocean
HOST UIC
  User root
  HostName 138.197.3.0

HOST USV
  User upstagevideo.com
  HostName s160604.gridserver.com

HOST WTR
  User worldtravelinc.com
  HostName s206783.gridserver.com

HOST WTRP
  User portal.worldtravelinc.com
  HostName s181464.gridserver.com

HOST VRS
  User vet.vrshealth.com
  HostName s211135.gridserver.com

```


