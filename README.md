### IntelliJIDEALicenseServer

#### 使用帮助
1. 复制JB文件夹到/data目录下
2. 打开`/etc/rc.local` (添加开机自启动)

        vi /etc/rc.local

3. 添加

        cd /data/JB
        ./IntelliJIDEALicenseServer_linux_amd64 -p 2703

4. 配置Nginx反向代理

        server {
            listen       80;
            listen      443;
            server_name  jb.bafflingbug.cn;
            ssl on;
            ssl_certificate 1_jb.bafflingbug.cn_bundle.crt;
            ssl_certificate_key 2_jb.bafflingbug.cn.key;
            ssl_session_timeout 5m;
            ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
            ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
            ssl_prefer_server_ciphers on;
            location / {
                proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass http://127.0.0.1:2703;
                proxy_redirect http://127.0.0.1:2703 https://jb.bafflingbug.cn;
            }
            location /static/  { 
                root  /data/JB/;
                expires      7d; 
            } 
            error_page 497  https://$host$uri$args;
        }

5. 重启Nginx

        service nginx restart