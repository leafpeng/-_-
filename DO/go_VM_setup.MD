>SSH:


>ssh root@<server-ip-address>

>adduser --gecos "" leaf

>usermod -aG sudo leaf
>
>su leaf


>Password-less Logins:


>ssh-keygen

>C:\Users\PENG\.ssh

>cd ~

{
    echo <paste-your-key-here> >> ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
}
>or

{
    mkdir .ssh
    vi .ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
}
ssh leaf@<server-ip-address>


>Install GO


>sudo apt-get update

>sudo apt-get -y upgrade

>sudo apt-get -y install mysql-server git


>https://golang.org/dl/

>curl -O latest-GO-version(url)

>tar -xvf gox.xx.x.linux-amd64.tar.gz

>sudo mv go /usr/local

>set Go’s root value

>sudo vi ~/.profile

>At the end of the file, add this line:

>export GOPATH=$HOME/work

>export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin

>refresh your profile 

>source ~/.profile

>Set global env var for email pw reset

cd /etc

sudo vi environment

EMAIL_OUTLOOK=leaf.peng@outlook.com

EMAIL_OUTLOOK_PW=

>Check env var

printenv

>Set local env var for email pw reset

sudo vi ~/.profile

>At the end of the file, add this line:
export EMAIL_OUTLOOK=leaf.peng@outlook.com

export EMAIL_OUTLOOK_PW=

>refresh your profile 

source ~/.profile



>Testing your go installation

go version


>you can point Go to the new workspace by exporting GOPATH . If you haven’t done it before.

export GOPATH=$HOME/work

mkdir $HOME/work

mkdir -p work/src/github.com/user

>Downloads code from github or scp from local file

git clone

or use

scp

>use go Modules

go build -o app


>>Mysql setup

In the new my-sql if the password is left empty while installing then it is based on the auth_socket plugin.

The correct way is to login to my-sql with sudo privilege.

$ sudo mysql -u root -p

And then updating the password using:

$ ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new-password';

Once this is done stop and start the mysql server.

$  sudo service mysql stop

$  sudo service mysql start

>Daemonize The Application

>create systemctl file /lib/systemd/system/app.service

sudo vi /lib/systemd/system/app.service
```
[Unit]
Description=web app service

[Service]
User=root
Group=root
Type=simple
ExecStart=/home/leaf/work/src/github.com/golang-web-learning/app
WorkingDirectory=/home/leaf/work/src/github.com/golang-web-learning
Restart=on-failure
RestartSec=30

[Install]
WantedBy=multi-user.target
```

sudo systemctl start app

sudo systemctl status app

sudo systemctl enable app




