# ACIT 2420 Assignment 3 - Tutorial

This guide presents a step-by-step tutorial to walk you through the entire process.

- [Setting Up a Debian 12 Server on DigitalOcean](#setting-up-a-debian-12-server-on-digitalocean)
- [Creating a New Regular User](#creating-and-setting-up-a-new-regular-user)
- [Prevent Root SSH Access](#prevent-root-ssh-access)
- [Installing and Configuring Nginx](#installing-and-configuring-nginx)


Let’s get started! 

## Setting Up a Debian 12 Server on DigitalOcean

Before we create a droplet, we need to create and add a SSH key to your DigitalOcean Account which make you securely access your account.

### Step 1: **Create an SSH key pair:**

Follow the steps and run the command below in the terminal on your local machine, in our case, we will use PowerShell in the windows terminal.

1. Create a new .ssh directory, run: 
    
    ```bash
    mkdir .ssh
    ```
    

2. Now, create your new ssh key pair, run:
    
    ```bash
    ssh-keygen -t ed25519 -f .ssh/do-key -C "your-email-address"
    ```
    
Once the key pair is generated, the private key remains on your local machine, while the public key will be used in next step.

### Step 2: Adding a SSH key to your DigitalOcean Account:

1. Visit the DigitalOcean website: https://www.digitalocean.com/
2. Click “**Log in**” to your DigitalOcean account. **Note: If you don’t have an account yet, click “Sign up” to create a new account.*
    
    ![Untitled](/images/1.png)
    
3. Click “**Settings**” section → “**Security**” menu, and then click the "**Add SSH Key**" button.
    
    
4. In PowerShell terminal, run the following command, and the public key will be copied: 
    
    ```bash
    Get-Content C:\Users\user-name\.ssh\do-key.pub | Set-Clipboard
    ```
    
5. Go back to your DigitalOcean page, and do:
    1. paste the public key your just copied to the SSH key content field,
    2. type your SSH key, and 
    3. click “**Add SSH key**”.
    
    ![Untitled](/images/2.png)
    

### Step 3: **Create a New Droplet:**

In this step, we are going to create a new droplet using DigitalOcean.

1. Go to the **homepage** of your DigitalOcean account.
    
    
2. Click “Create” and select “Droplets” to start create a new server.
    
    ![Untitled](/images/3.png)
    
3. Next, set up your server. There are several sections you can set up depends on your needs, in this tutorial, we will update the features below and leave the remaining default:
    1. **Choose an image:** Click “**Debian**” and you will see version 12 as default.
        
        ![Untitled](/images/4.png)
        
    2. **Choose Size - CPU options:** Click “**Regular**” and select “$4/mo” plan.
        
        ![Untitled](/images/5.png)
        
    3. **Choose Authentication Method:** Select “**SSH Key**” and mark the SSH key we just created in Step 1.
        
        ![Untitled](/images/6.png)
        
    4. ****Finalize Details - Hostname:**** name your new droplet, let’s type “**Debian2**” in our case, then click “**Create Droplet**”.
        
        ![Untitled](/images/7.png)
        

Now you successfully created a new droplet, let’s move to next step!

### Step 4: **Access the Server:**

Once the droplet is created, you would be able to access your new server.

1. Once you finished step 3, you will be redirected to the project page, and you can find the information of your new server, **copy** the entire address.
    
    ![Untitled](/images/8.png)
    
2. In PowerShell terminal, run the command to use SSH to connect to your Debian2 server as a root user:
    
    ```bash
    ssh -i .ssh/do-key [root@64.23.130.120](mailto:root@64.23.130.120)
    ```
    
    ![Untitled](/images/9.png)
    
3. Set up your password, run:
    
    ```bash
    passwd
    ```
    
    ![Untitled](/images/10.png)
    
    *Note: you will need to enter your password twice to set up.
    

Now you are successfully connected to your new server! 

## ****Creating and Setting up a New Regular User****

In this section, follow the steps to create a new regular user for your server.

### Step 1: Create a new user:

1. While logged in as root, run the command to create a new user:
    
    ```bash
    sudo useradd -ms /bin/bash newuser
    ```
    
    *Note: replace "newuser" with your preferred username for all following steps.
    
2. Set up the password for the user by running:
    
    ```bash
    sudo passwd newuser
    ```
    
    *Note: you will need to enter your password twice to set up.
    
3. Next, let’s add the new user to the sudo group to grant administrative privileges, so the new user can perform administrative tasks:
    
    ```bash
    sudo usermod -aG sudo newuser
    ```
    
4. Now, to ensure bash is set as the login shell, check the user's shell with the command:
    
    ```bash
    grep newuser /etc/passwd
    ```
    
    If the shell is not set to `/bin/bash`, run the following command to change it:
    
    ```bash
    chsh -s /bin/bash newuser
    ```
    

### **Step 2: Make your new user can access the server via SSH**

1. First, as a root users, copy the `.ssh` directory from home directory to the new user's home directory:
    
    ```bash
    sudo cp -r /root/.ssh /home/newuser
    ```
    
    *Note: including any files in the directory.
    
2. Now, change ownership of the directory and files in the directory, so that the copy in your new user's directory is owned by the new user and the new user's primary group:
    
    ```bash
    sudo chown -R newuser:newuser /home/newuser/.ssh
    ```
    
3. Test to connect to the server with your new user:
    
    ```bash
    ssh -i .ssh/do-key newuser[@64.23.130.120](mailto:root@64.23.130.120)
    ```
    
4. Run the following command to update the package information from the repositories:
    
    ```bash
    sudo apt update
    ```
    
    *Note: you will need to enter password for this action.
    

Perfect! You have successfully create a new regular user for your server!

## ****Prevent Root SSH Access****

In this section, we are going to set up preventing root user SSHaccess. The reason is preventing root login enhances security by mitigating the risk of unauthorized access and potential exploitation.

### **Step 1: Edit ssh configuration so that the root user can no longer connect to the server via ssh**

 

1. First, locate the `sshd_config` file and open the file in vim:
    
    ```bash
    sudo vim /etc/ssh/sshd_config
    ```
    
2. Search for the line `PermitRootLogin yes` and update it to `PermitRootLogin no`. This change prevents the root user from connecting via SSH.
    
    ![Untitled](/images/11.png)
    
    *Note: type **“i”** to change to insert mode, then press **“esc”** and type **“:wq”** to exit and save file.
    

3. After editing the file, restart the SSH service to apply the changes:
    
    ```bash
    sudo systemctl restart ssh.service
    
    ```
    
4. Now, test if you can no longer connect to your server as root:
    
    ```bash
    ssh -i .ssh/do-key root[@64.23.130.120](mailto:root@64.23.130.120)
    ```
    
    ![Untitled](/images/12.png)
    

Great! Now you are not able to log in as root user which make your server sercure!

## ****Installing and Configuring Nginx****

Nginx is a powerful web server and enable you to serve your content efficiently. The steps below will guide you through the process of of installing and configuring Nginx on a your server. Please note to connect with regular user to do the steps.

### Step 1: **Install Nginx:**

1. Update the package list to ensure you have the latest information on available packages:
    
    ```bash
    sudo apt update
    ```
    
    Install Nginx using the package manager:
    
    ```bash
    sudo apt install nginx
    ```
    

### Step 2: **Configure Nginx:**

1. To create a directory for your website content, let’s create a new `my-site` directory in `/var/www` :
    
    ```bash
    sudo mkdir /var/www/my-site
    ```
    
2. Now, generate an `index.html` file within the `my-site` directory to serve as your website, then paste your HTML code (example below), save, and exit:
    
    ```bash
    cd /var/www/my-site
    ```
    
    ```bash
    sudo vim index.html
    ```
    
    ```bash
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>2420</title>
        <style>
            body {
                display: flex;
                align-items: center;
                justify-content: center;
                height: 100vh;
                margin: 0;
            }
            h1 {
                text-align: center;
            }
        </style>
    </head>
    <body>
        <h1>Hello, World !!!!!!!</h1>
    </body>
    </html>
    ```
    
    *Note: type **“i”** to change to insert mode, then press **“esc”** and type **“:wq”** to exit and save file.
    

### Step 3: **Creating our Own Server Block and Access your Website**

We will use vim to create and edit a configuration file specific to your site. The Nginx server block configuration sets up how Nginx will serve your website, defining the root directory, index files, server name, and location handling.

1. Create a new file in `/etc/nginx/sites-available` and copy the nginx config (example below) into it:
    
    ```bash
    sudo vim /etc/nginx/sites-available/my-site.conf
    ```
    
    ```bash
    server {
    	listen 80 default_server;
    	listen [::]:80 default_server;
    	
    	root /var/www/my-site;
    	
    	index index.html index.htm index.nginx-debian.html;
    	
    	server_name _;
    	
    	location / {
    		# First attempt to serve request as file, then
    		# as directory, then fall back to displaying a 404.
    		try_files $uri $uri/ =404;
    	}
    }
    ```

2. By creating this symbolic link, you're enabling the configuration for your site. Let’s create a symbolic link to your new config file in `/etc/nginx/sites-enabled` :
    
    ```bash
    sudo ln -s /etc/nginx/sites-available/my-site.conf /etc/nginx/sites-enabled/
    ```
    

3. Check the symbolic link status:
    
    ```bash
    cd /etc/nginx/sites-enabled/
    ```
    
    ```bash
    ls
    ```
    
    If default configuration is active, run:
    
    ```bash
    sudo unlink default
    ```
    

4. Before restarting Nginx, test your configurations to ensure there are no syntax errors:
    
    ```bash
    sudo nginx -t
    ```
    
    ![Untitled](/images/13.png)
    
5. If the test is successful, restarts the Nginx service to apply the new configuration:
    
    ```bash
    sudo systemctl restart nginx
    ```
    
6. Finally, run the curl command to verify that your HTML document is being served:
    
    ```bash
    curl [64.23.130.120](mailto:root@64.23.130.120)
    
    ```
    
    Or:
    
    ```bash
    curl http://[64.23.130.120](mailto:root@64.23.130.120)
    ```
    
    ![Display in regular user.](/images/14.png)
    
    Display in regular user.
    
    ![Display in powershell.](/images/15.png)
    
    Display in powershell.
    
    ![Display in browser.](/images/16.png)
    
    Display in browser.
    

Awesome! You have successfully access your website using served by Nginx!

## conclusion

This tutorial has guided you through the essential steps of setting up a Debian 12 server on DigitalOcean, creating a new regular user with administrative privileges, securing your server by preventing root SSH access, and finally, installing and configuring Nginx to serve a sample website. 

By following this guide, you have gained insights to manage and secure your Debian server efficiently!

