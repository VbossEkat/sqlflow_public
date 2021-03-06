## Instructions on how to install SQLFlow on your own server.

SQLFow was comprised of two parts: frontend and backend. 
The frontend and backend can be installed on the same server, or they can be installed on two different servers seperately.

### Prerequisites
- A linux server with at least 8GB memory (ubuntu 20.04 is recommended).
- Java 8
- Nginx web server. 
- Port needs to be opened. (80, 8761,8081,8083)

### Setup Environment (Ubuntu for example)
	sudo apt-get update
	sudo apt-get install nginx -y
	sudo apt-get install default-jre -y	

CentOS
- [How To Install Nginx on CentOS](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7)
- [How To Install Java on CentOS ](https://www.digitalocean.com/community/tutorials/how-to-install-java-on-centos-and-fedora)

Windows
- [Install SQLFlow on Windows](install_sqlflow_on_windows.md)

### Upload Files

create a directory :

```bash
# it must be created start with root path
sudo mkdir -p /wings/sqlflow
```

upload your backend and frontend file to `sqlflow` folder, like this :

```
/wings/
└── sqlflow
    ├── backend
    │   ├── bin
    │   │   ├── backend.sh
    │   │   ├── stop.sh
    │   │   ├── monitor.sh 
    │   │   ├── sqlservice.sh 
    │   │   ├── gspLive.sh  
    │   │   ├── eureka.sh
    │   │   ├── backend.bat
    │   │   ├── stop.bat
    │   │   ├── monitor.bat
    │   │   ├── sqlservice.bat
    │   │   ├── gspLive.bat
    │   │   ├── eureka.bat
    │   │   ├── sqlservice.vbs
    │   │   ├── gspLive.vbs  
    │   │   ├── eureka.vbs
    │   ├── lib
    │   │   ├── sqlservice.jar  
    │   │   ├── gspLive.jar  
    │   │   ├── eureka.jar
    │   ├── conf
    │   │   ├── gudu_sqlflow_license.txt     
    │   ├── data
    │   │   ├── job  
    │   │   │   ├── task     
    │   │   │   ├── {userid}   
    │   │   ├── session     
    │   ├── log
    │   │   ├── sqlservice.log 
    │   │   ├── gspLive.log  
    │   │   ├── eureka.log 
    │   │   ├── slow                   (slow query records)
    │   │   ├── sqlflow                (sqlflow access records)
    │   ├── tmp
    │   │   └── cache  
    └── frontend
        ├── 1.app.b95fd285b4e8a1af563a.js
        ├── 1.index.b95fd285b4e8a1af563a.css
        ├── app.b95fd285b4e8a1af563a.js
        ├── config.private.json
        ├── font
        │   ├── Roboto-Regular.ttf
        │   ├── segoeui-light.woff2
        │   └── segoeui-semilight.woff2
        ├── images
        │   ├── check.svg
        │   ├── Join.svg
        │   ├── pic_Not logged in.png
        │   └── visualize.svg
        ├── index.b95fd285b4e8a1af563a.css
        └── index.html
```

set folder permissions :

```bash
sudo chmod -R 755 /wings/sqlflow
```

### Start Backend Services

start service in background: 

```bash
  /wings/sqlflow/backend/bin/backend.sh
```

please allow 1-2 minutes to start the service.

use `ps -ef|grep java` to check those 3 processing are running.

```
ubuntu   11047     1  0 Nov02 ?        00:04:44 java -server -jar eureka.jar
ubuntu   11076     1  0 Nov02 ?        00:04:11 java -server -Xmn512m -Xms2g -Xmx2g -Djavax.accessibility.assistive_technologies=  -jar sqlservice.jar
ubuntu   11114     1  0 Nov02 ?        00:05:17 java -server -jar gspLive.jar
```

#### default port

| File           | Port |
| -------------- | ---- |
| eureka.jar     | 8761 |
| gspLive.jar    | 8081 |
| sqlservice.jar | 8083 |

### Start Frontend Services

open your nginx configuration file ( at `/etc/nginx/sites-available/default` under ubuntu ), add a server :

```nginx
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /wings/sqlflow/frontend/;
	index index.html;

	location ~* ^/index.html {
		add_header X-Frame-Options deny;
		add_header Cache-Control no-store;
	}

	location / {
		try_files $uri $uri/ =404;
	}
}
```

start your nginx : 

```bash
sudo service nginx start
```

or reload : 

```bash
sudo nginx -s reload
```

open http://yourdomain.com/ to see the SQLFlow.

open http://yourdomain.com:8081/gspLive_backend/doc.html?lang=en to see the Restful API documention.
