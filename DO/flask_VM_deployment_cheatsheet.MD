>>SSH:


ssh root@<server-ip-address>
adduser --gecos "" leaf
usermod -aG sudo leaf
su leaf


>>Password-less Logins:


ssh-keygen
C:\Users\PENG\.ssh
cd ~
{
    echo <paste-your-key-here> >> ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
}
or
{
    mkdir .ssh
    vi .ssh/authorized_keys
    chmod 600 ~/.ssh/authorized_keys
}
ssh leaf@<server-ip-address>


>>Securing Your Server:


sudo vi /etc/ssh/sshd_config
{
    PermitRootLogin no
}
{
    PasswordAuthentication no
}
sudo service ssh restart
Firewall
$ sudo apt-get install -y ufw
$ sudo ufw allow ssh
$ sudo ufw allow http
$ sudo ufw allow 443/tcp
$ sudo ufw --force enable
$ sudo ufw status


>>Installing Base Dependencies:


sudo apt-get -y update
sudo apt-get -y install python3 python3-venv python3-dev
sudo apt-get -y install mysql-server postfix supervisor nginx git


>>Installing the Application:


home/leaf
git clone https://github.com/miguelgrinberg/microblog
cd microblog
git checkout v0.17
python3 -m venv venv
source venv/bin/activate
(venv) $ pip install --upgrade pip
(venv) $ pip install -r requirements.txt
(venv) $ pip install gunicorn pymysql
>Environment variables

python3 -c "import uuid; print(uuid.uuid4().hex)"
vi /home/leaf/microblog/.env

SECRET_KEY=52cb883e323b48d78a0a36e8e951ba4a
MAIL_SERVER=localhost
MAIL_PORT=25
DATABASE_URL=mysql+pymysql://microblog:<db-password>@localhost:3306/microblog
MS_TRANSLATOR_KEY=<your-translator-key-here>

>FLASK_APP environment variable
(venv) ~/microblog
echo "export FLASK_APP=microblog.py" >> ~/.profile
then log out and back in
source microblog/venv/bin/activate
cd microblog
flask --help
(venv) $ flask translate compile

>>Setting Up MySQL:


sudo mysql -u root -p
mysql> create database microblog character set utf8 collate utf8_bin;
mysql> create user 'microblog'@'localhost' identified by '<db-password>';
mysql> grant all privileges on microblog.* to 'microblog'@'localhost';
mysql> flush privileges;
mysql> quit;
(venv) $ flask db upgrade


>>Setting Up Gunicorn and Supervisor:


(venv) $ gunicorn -b localhost:8000 -w 4 microblog:app
sudo vi /etc/supervisor/conf.d/microblog.conf

[program:microblog]
command=/home/leaf/microblog/venv/bin/gunicorn -b localhost:8000 -w 4 microblog:app
directory=/home/leaf/microblog
user=leaf
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true


sudo supervisorctl reload


>>Setting Up Nginx:


>Creating an SSL certificate
mkdir certs
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 \
  -keyout certs/key.pem -out certs/cert.pem
sudo rm /etc/nginx/sites-enabled/default
sudo vi /etc/nginx/sites-enabled/microblog
server {
    # listen on port 80 (http)
    listen 80;
    server_name _;
    location / {
        # redirect any requests to the same URL but on https
        return 301 https://$host$request_uri;
    }
}
server {
    # listen on port 443 (https)
    listen 443 ssl;
    server_name _;

    # location of the self-signed SSL certificate
    ssl_certificate /home/leaf/microblog/certs/cert.pem;
    ssl_certificate_key /home/leaf/microblog/certs/key.pem;

    # write access and error logs to /var/log
    access_log /var/log/microblog_access.log;
    error_log /var/log/microblog_error.log;

    location / {
        # forward application requests to the gunicorn server
        proxy_pass http://localhost:8000;
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location /static {
        # handle static files directly, without forwarding to the application
        alias /home/leaf/microblog/app/static;
        expires 30d;
    }
}
sudo service nginx reload


>>Deploying Application Updates:
(venv) $ git pull                              # download the new version
(venv) $ sudo supervisorctl stop microblog     # stop the current server
(venv) $ flask db upgrade                      # upgrade the database
(venv) $ flask translate compile               # upgrade the translations
(venv) $ sudo supervisorctl start microblog    # start a new server
