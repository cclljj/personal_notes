# Initialization

```
sudo amazon-linux-extras install epel
sudo yum -y install epel-release
```

install python3.8

```
sudo amazon-linux-extras enable python3.8
sudo yum install python3.8
sudo ln -s /usr/bin/python3.8 /usr/bin/python3
sudo pip3.8 install pandas
sudo pip3.8 install requests
```

install git

```
sudo yum -y install git
```

install docker and run docker service

```
sudo amazon-linux-extras install docker
sudo service docker start
sudo systemctl enable docker.service
sudo usermod -a -G docker ec2-user
```

# Install MQTT Broker

```
sudo yum -y install mosquitto
```

Edit /etc/mosquitto/mosquitto.conf

```
sudo systemctl status mosquitto.service
sudo systemctl start mosquitto
sudo systemctl enable mosquitto.service
```


# Install NGINX and PHP

```
sudo yum -y install nginx
sudo yum -y install php-fpm
```

Edit /etc/nginx/nginx.conf

```
sudo systemctl start php-fpm
sudo systemctl start nginx
sudo systemctl enable php-fpm.service
sudo systemctl enable nginx.service
```

Reference
- https://blog.toright.com/posts/3890/無堅不摧，唯快不破！快改用-nginx-php-fpm-取代-apache-吧！.html

# Enable HTTPS on NGINX

Install ssl and certbot

```
sudo yum install mod_ssl
sudo yum install certbot -y
sudo certbot certonly --webroot -w /var/www/html -d cclljj.ddns.net --email cclljj@gmail.com --agree-tos
```

Reference
- https://blog.hellojcc.tw/setup-https-with-letsencrypt-on-nginx/
- https://serversforhackers.com/c/redirect-http-to-https-nginx
- https://www.team-bob.org/update_letsencrypt_cert_using_systemd/

# Install TAK server

```
docker volume create fts_data
docker run -d -p 8080:8080/tcp -p 8087:8087/tcp -e FTS_CONNECTION_MESSAGE="Server Connection Message" -e FTS_SAVE_COT_TO_DB="True" -v fts_data:/data --name fts --restart unless-stopped freetakteam/freetakserver:1.1.2
```

Configure AWS EC2 to allow TCP 8080/8087 ports

Reference
- https://github.com/FreeTAKTeam/FreeTAKServer-Docker

# Setup dynamic DNS and auto update

```
sudo yum install -y noip
sudo noip2 -C
sudo systemctl enable noip.service
sudo systemctl start noip.service
```
