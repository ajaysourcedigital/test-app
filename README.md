# How to deploy nodejs app on GCP Compute:

## Steps:

1. Create a VM
    a. Allow SSH access via key. Add your public key while you create a VM. 
    
2. Install packages:
    ```
    sudo apt-get update
    sudo apt install -y nginx
    sudo apt install -y git
    curl -sL https://deb.nodesource.com/setup_16.x | sudo bash -
    sudo apt -y install nodejs
    sudo apt install -y certbot python3-certbot-nginx
    ```
3. Clone the repo under home directory:
    `git clone https://github.com/ajaysourcedigital/test-app.git`

4. Create `script.sh` file under `/home/` with below content:
    ```
    #!/bin/bash
    cd /home/ajaykumar/hello-world
    git checkout main
    git pull
    sudo systemctl restart hello.service
    ```
    - `hello.service` is described at step 6.

5. Change the content of `/etc/nginx/sites-enabled/default` to :
    ```
    location / {
		proxy_pass http://127.0.0.1:3000;
	}
    ```
    - Any changes you do to `default` nginx file, you have to run `sudo systemctl restart nginx`

6. create systemctl service under `/etc/systemd/system/hello.service` :
    ```
    [Unit]
    Description=API service
    [Service]
    User=ajaykumar
    Restart=always
    KillSignal=SIGQUIT
    WorkingDirectory=/home/ajaykumar/hello-world
    ExecStart=/usr/bin/node app.js
    [Install]
    WantedBy=multi-user.target
    ```
    once created you have enable the systemctl daemon process:
        `systemctl enable hello.service`
    
    If you do any changes to hello.service file, you have to restart daemon:
    `systemctl daemon reload`

7. Generate SSL certificate:
    ```
    sudo certbot --nginx -d ajay.domain.com
    ```

8 CICD:
    look at `.github/workflows/main.yaml`