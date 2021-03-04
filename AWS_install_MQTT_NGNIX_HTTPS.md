# Initialization

```
sudo amazon-linux-extras install epel
sudo yum -y install epel-release
```

install python3.8

```
sudo amazon-linux-extras enable python3.8
sudo yum -y install python3.8
sudo yum -y install python38-devel
sudo yum -y install gcc

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

To allow utf-8 character set, add the following lines in `/etc/environment`

```
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
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

# Setup dynamic DNS and auto update

```
sudo yum install -y noip
sudo noip2 -C
sudo systemctl enable noip.service
sudo systemctl start noip.service
```

# Install TAK server (docker version)

```
docker volume create fts_data
docker run -d -p 8080:8080/tcp -p 8087:8087/tcp -e FTS_CONNECTION_MESSAGE="Server Connection Message" -e FTS_SAVE_COT_TO_DB="True" -v fts_data:/data --name fts --restart unless-stopped freetakteam/freetakserver:1.1.2
```

Configure AWS EC2 to allow TCP 8080/8087 ports (for CoT) and allow TCP 19023 port (for API)

Reference
- https://github.com/FreeTAKTeam/FreeTAKServer-Docker

# Install TAK server (PyPi)

Install TAK package and the depenant OpenSSL package

```
sudo python3 -m pip install FreeTAKServer[ui]
sudo python3 -m pip install pyOpenSSL
```

Make some folders that are required to run FreeTAKServer

```
sudo mkdir /usr/local/lib/python3.8/dist-packages
sudo mkdir /usr/local/lib/python3.8/dist-packages/FreeTAKServer
sudo mkdir /usr/local/lib/python3.8/dist-packages/FreeTAKServer/ExCheck
sudo mkdir /usr/local/lib/python3.8/dist-packages/FreeTAKServer/certs/
```

A Quick Test Run:

```
sudo python3 -m FreeTAKServer.controllers.services.FTS -DataPackageIP 0.0.0.0 -AutoStart True
```

Create the system service configuration file at `/etc/systemd/system/FreeTAKServer.service` with the following contents:
```
[Unit]
Description=FreeTAK Server service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
ExecStart=/usr/bin/python3.8 -m FreeTAKServer.controllers.services.FTS -DataPackageIP 0.0.0.0 -AutoStart True

[Install]
WantedBy=multi-user.target
```

Make FreeTAKServer as a system service and run it

```
sudo systemctl enable FreeTAKServer.service
sudo systemctl start FreeTAKServer.service
```

You can also enter CLI mode to interact with FreeTAKServer:
```
sudo python3.8 -m FreeTAKServer.views.CLI
```

Reference:
- https://freetakteam.github.io/FreeTAKServer-User-Docs/Installation/PyPi/Linux/Service/

# Run FreeTAKServer-UI

Change IP/Port settings in the configuration file `/usr/local/lib/python3.8/site-packages/FreeTAKServer-UI/config.py`

- change **IP** to AWS external IP
- change **APPIP** to AWS internal IP

Run UI server:
```
sudo python3.8 /usr/local/lib/python3.8/site-packages/FreeTAKServer-UI/run.py
```

Configure AWS EC2 to allow TCP 5000 port (for http access), and access the UI page
- go to AWS external IP at port 5000 using HTTP
- the default account is `admin` and the default password is `password`

# Install and Run FreeTAKServer-Simulator

Download FreeTAKServer-Simulator and its dependant library
```
git clone https://github.com/FreeTAKTeam/FreeTAKServer-Simulator
git clone https://github.com/pinztrek/takpak
mv takpak/takpak FreeTAKServer-Simulator/
```

Edit wander_example.py to give the server IP address (AWS external IP) and the location information; then, run this program to simulate nodes

```
python3.8 wander_example.py
```
