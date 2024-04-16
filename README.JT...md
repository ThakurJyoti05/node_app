

# Problem Statement:
1. Create a HelloWorld app in NodeJs and push the code on github.
2. Setup the secure infrastructure for the App on AWS and host that app on Infrastructure.
3. Setup Ngnix to serve the application on port 80.
4. Install the SSL certificate from Lets Encrypt on the app and use the domain name "yourname".signiance.com. You can share the endpoint of your server or loadbalancer with me during review and I will map it with the domain.
5. Create a CI/CD pipleine for the app using tools of your choice.
6. It shouild build and deploy the code on push.
7. Changes should be visible on the above URL.
8. Document the assignment and share your learning.


# Solution:
## Base OS: Ubuntu 

## Step 1. Create a HelloWorld app in NodeJS.
### A: Update Package Index and install Nodejs and npm

First, update the package and install the latest versions of packages.

sudo apt update
sudo apt install nodejs npm

### B. Verify Installation
After the installation is complete, verify that Node.js and npm were installed successfully.

node -v
npm -v

### C. Create a Project dir and write simpe code for "Hello World"

mkdir myapp
cd myapp

### D. Steps to create a Nodejs app and source code
 Create a new file named index.html in the same directory as the JavaScript file.
2. Copy and paste the HTML content above into the index.html file.
3. Save the index.html file.
4. Create a new file with a .js extension (e.g., app.js).
5. Copy and paste the JavaScript code above into the server.js file.
6. Save the app.js file.

npm install express
vim app.js

source code for "index.html"

<!DOCTYPE html>
<html>
<head>
  <title>HelloWorld</title>
</head>
<body>
  <h1>HelloWorld Web Page</h1>
  <form method="post">
    <label for="username">Enter your name:</label>
    <input type="text" id="username" name="username">
    <button type="submit">Submit</button>
  </form>
</body>
</html>

source code for "app.js"

// app.js
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello, World');
});

app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});

Open a terminal and navigate to the directory where the files are saved.
8. Run and test the following command to start the server:

node app.js
npm init => to create a pakage.json file
npm install => to create a package.json.lock file

### E. Push the code on github

create a new repo on github with name: "node_app"
2. On your local machine in project folder give the following commands
  
git init
git branch -M main
git remote add origin 
https://github.com/ThakurJyoti05/node_app.git
git push -u origin main 

### f. Git comamnds on local machine for branch sync

git checkout main
git pull --rebase
git rebase main
git push -f origin main

## Step 2: Setup the secure infrastructure for the App on AWS and host that app on Infrastructure.
1. For secure infrastructure we will create one intance named node_app with security groups.
2. Open security groups & edit inbound rules.
3. Enabled port 80, 443 & 3000(for our local host) and then save rules.

Run following commands on  ec2 instance:

sudo apt update

# Install curl
sudo apt install -y curl

# Install Nginx
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx

# Install Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs

#check the installation on ec2 by using following commands
sudo -i
node -v
npm -v
systemctl status nginx

## Step 3: Create a CI/CD pipleine for the app using tools of your choice. 
## Here, we are using jenkins to setup CI/CD pipleine

For installing the jenkins we need to install java & SSH key to connect with server.

sudo apt update
sudo apt install openjdk-11-jre
java -version

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \   /usr/share/keyrings/jenkins-keyring.asc > /dev/null 
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \   https://pkg.jenkins.io/debian binary/ | sudo tee \   /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update 
sudo apt-get install jenkins
cat jenkins_fresh_install.sh
ssh -i "Thakur.pem" ubuntu@ec2-44-220-138-89.compute-1.amazonaws.com
su jenkins

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
sudo cat /var/lib/jenkins/secrets/initialJyotiPassword

install pm2 for prodcution server

sudo npm install pm2@latest -g
OR
sudo npm i -g pm2

Check whether pm2 is installed or not:

pm2

Now lets configure NGINX server:

sudo nano /etc/nginx/sites-available/default

#### Test Nginx Configuration:

sudo nginx -t

## Restart Nginx:
If the configuration test is successful, restart Nginx to apply the changes:

sudo systemctl restart nginx

Before connecting the Jenkins with github we need public key & private key. So for that we need to run the command on EC2:

cd .sh
cd .rsa
cd .rsa.public

## Now Integrate Jenkins with Github:

1. Go to GitHub(Your repository settings)---> Access(SSH & GPG key)---> Create new SSH key.
2. Inside the SSH key paste the public key in key area along with filling required information.

## Now add credentials to jenkins:
1. My_app---> Configure---> General ---> Github repository(https://github.com/ThakurJyoti05/node_app.git/)---> GitHub hook trigger for GITScm polling
2. Apply---> Save

Now we created the pipleine between github repository & jenkins.

Now, if you made any changes in the code and push to GitHub repo they will be automatically reflected..

## ADD SSL with lets-encrypt:

$ sudo apt install certbot python3-certbot-nginx

$ sudo ufw allow 'Nginx Full'
$ sudo ufw delete allow 'Nginx HTTP'

$ sudo ufw status

$ sudo certbot --nginx -d example.com -d www.example.com

If that’s successful, certbot will ask how you’d like to configure your HTTPS settings.

Output:
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
 - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
 1: No redirect - Make no further changes to the webserver configuration.
 2: Redirect - Make all requests redirect to secure HTTPS access.
 Choose this for new sites, or if you're confident your site works on HTTPS.
 You can undo this change by editing your web server's configuration.
 - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
 Select the appropriate number [1-2] then [enter] (press 'c' to cancel):

 Select your choice then hit ENTER. Of course, you are here because of HTTPS redirect. It will be better to select option 2. That way, if people visit your site with HTTP, they will be automatically redirected to a more secured connection. The configuration will be updated, and Nginx will reload to pick up the new settings. certbot will wrap up with a message telling you the process was successful and where your certificates are stored:

## Ouput 
 IMPORTANT NOTES: - Congratulations! Your certificate and chain have been saved at:   /etc/letsencrypt/live/example.com/fullchain.pem Your key file has been saved at:   /etc/letsencrypt/live/example.com/privkey.pem Your cert will expire on 2020-08-18. To obtain a   new or tweaked version of this certificate in the future, simply run certbot again with the "certonly"   option. To non-interactively renew *all* of your certificates, run "certbot renew" - If you like   Certbot, please consider supporting our work by: Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate Donating to EFF: https://eff.org/donate-le

To test the renewal process, you can do a dry run with certbot:

$ sudo certbot renew --dry-run
















