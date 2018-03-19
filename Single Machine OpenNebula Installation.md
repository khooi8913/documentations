# Single Machine OpenNebula Installation

Software version information used in the following guide:

> Ubuntu 16.04.3 LTS
>
> OpenNebula 5.4.7

For this summarized guide, we will configure all the software packages required for OpenNebula to function. As for the networking part, it will be discussed separately.

## Step One: Acquiring root access

1. You will need to be **root** in order to execute the commands. 

   ```bash
   sudo su
   ```


## Step Two: Add OpenNebula repositories as root

1. As root, execute the following to add OpenNebula to your repositories, if you are using other versions of Ubuntu, change the value `16.04` to your corresponding version (e.g. `14.04`, `17.04`)

   ```bash
   wget -q -O- https://downloads.opennebula.org/repo/repo.key | apt-key add -
   echo "deb https://downloads.opennebula.org/repo/5.4/Ubuntu/16.04 stable opennebula" > /etc/apt/sources.list.d/opennebula.list
   ```

2. As root, after adding the repositories, update your system's repositories.

   ```bash
   apt update
   ```

## Step Three: Installing OpenNebula Front-End (Sunstone)

1. As **root**, install the OpenNebula Sunstone by executing the following command:  

   ```bash
   apt install opennebula opennebula-sunstone opennebula-gate opennebula-flow
   ```

2. The installation size should be around 120MB. 

3. Upon completion, still, as **root**, execute `/usr/share/one/install_gems` the required dependencies for Sunstone.

## Step Four: Configuring OpenNebula Administrator account credentials 

1. Switch to **oneadmin** user, there should not be any password required for the user, as long as you are executing this as the root user.

   ```bash
   su - oneadmin
   ```

2. The `/var/lib/one/.one/one_auth` fill will have been created with a randomly-generated password. It should contain the following: `oneadmin:<password>`.  You may decide to change it or keep it as it is.

   > Note: Once you skipped this step and started OpenNebula services, you are unable to change the oneadmin password via this method. 

3. In this guide, we will change the password to ***mypassword***. 

   ```bash
   echo "oneadmin:mypassword" > ~/.one/one_auth
   ```

## Step Five: Starting OpenNebula services

1. Once completed the above steps, as the **oneadmin** user, we can now start up OpenNebula with the following commands. 

   ```Bash
   systemctl start opennebula
   systemctl start opennebula-sunstone
   ```

## Step Six: Verifying the Front-End installation 

1. By now, OpenNebula should be up and running and you can verify whether the daemon is up and running via the CLI with the command `oneuser show`.

   ```
   $ oneuser show
   USER 0 INFORMATION
   ID              : 0
   NAME            : oneadmin
   GROUP           : oneadmin
   PASSWORD        : 3bc15c8aae3e4124dd409035f32ea2fd6835efc9
   AUTH_DRIVER     : core
   ENABLED         : Yes

   USER TEMPLATE
   TOKEN_PASSWORD="ec21d27e2fe4f9ed08a396cbd47b08b8e0a4ca3c"

   RESOURCE USAGE & QUOTAS
   ```

2. Alternatively, you can also access the GUI `http://<your machine's IP address>:9869` by logging in as **oneadmin** with the credentials defined back in step four.



## Step Seven: Installing KVM Node 

1. Once completing the front end installation, we are only left with the KVM installation as our virtual machine's hypervisor.

   ```bash
   sudo apt-get install opennebula-node
   sudo service libvirt-bin restart # ubuntu
   ```

## Step Eight: Distributing SSH keys

1. You will need to distribute the SSH keys of each OpenNebula Node. However, in our case, our Front-End will be on the same host as the OpenNebula Node.

2. Switch to **oneadmin** user, we will have to copy the SSH keys into our known hosts directory.

   Example: 

   ```bash
   ssh-keyscan <frontend> <node1> <node2> <node3> ... >> /var/lib/one/.ssh/known_hosts
   ```

3. The `frontend`, `node1`, `node2`, `node3` refers to your machine's host name. 

   ```
   xzk@Optiplex-990:~$ 
   ```

   This is what you commonly see in your terminal. From here, we can retrieve the host name, which is `Optiplex-990`.

4. Hence, the command you should execute in order to distribute the SSH keys will be:

   ```
   ssh-keyscan Optiplex-990 >> /var/lib/one/.ssh/known_hosts
   ```

5. Upon completion, you can try SSH as **oneadmin** user to the `hosts` and there should not be any password required.

   ```
   ssh Optiplex-990
   ```

6. Once completed, we are almost done to get OpenNebula up and running!

## Step Nine: Adding an OpenNebula host

