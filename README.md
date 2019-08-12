# flask-blog
基于flask搭建的blog系统


## nginx 配置信息
path>>> 
```
sudo rm /etc/nginx/sites-enabled/default
sudo touch /etc/nginx/sites-available/flask
sudo ln -s /etc/nginx/sites-available/flask /etc/nginx/sites-enabled/flask 
```

`vim /etc/nginx/sites-available/flask`
```
upstream flask {
    server 127.0.0.1:5000;
}
server {
     listen 80;
     server_name flask.wanbowen.com;
     # Add Headers for odoo proxy mode
     proxy_set_header X-Forwarded-Host $host;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_set_header X-Forwarded-Proto $scheme;
     proxy_set_header X-Real-IP $remote_addr;

     # log
     access_log /var/log/nginx/flask.access.log;
     error_log /var/log/nginx/flask.error.log;
     # Redirect requests to flask backend server
     location / {
         proxy_redirect off;
         proxy_pass http://flask;
     }
     # common gzip
    gzip_types text/css text/scss text/plain text/xml application/xml application/json application/javascript;
    gzip on;
}
```
`sudo systemctl reload nginx # 重载nginx服务`

## flask 进程守护（systemd服务）
path>>>
`vim /lib/systemd/system/flask.service`
```
[Unit]
Description=flask
After=odoo.service

[Service]
Type=simple
User=root
Group=root
ExecStart=/usr/bin/python3 /root/flask-dev/app.py
Restart=always

```
注册服务：
`sudo systemctl enable flask.service`
启动服务：
`sudo systemctl start flask`
检查服务状态：
`sudo systemctl status flask`
