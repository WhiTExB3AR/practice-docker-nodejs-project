# Project code for tutorial on how to develop a Node.js/MongoDB application using Docker Compose: https://www.digitalocean.com/community/tutorials/containerizing-a-node-js-application-for-development-with-docker-compose

# 2. Thực hành


## Deploy NodeJS by Docker

#### Cài đặt thư viện và các gói servervice cho NodeJS.

``` sh
sudo apt install npm
```

#### Tạo thư mục project Python

``` sh
mkdir node_project
cd node_project
```

#### Tạo file package.json khai báo cấu hình của npm, giúp cho npm hiểu nó cần phải cài đặt cái gì, thông tin về ứng dụng, phiên bản,...

``` sh
touch package.json
nano package.json
```

``` bash
{
	"name": "nodejs-image-demo",
	"version": "1.0.0",
	"description": "nodejs image demo",
	"author": "Thuy DevOps SVTT<ldxthuy@gmail.com>",
	"license": "MIT",
	"main": "app.js",
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1",
		"start": "node app.js",
		"dev": "nodemon app.js"
	},
	"keywords": [
		"nodejs",
		"boostrap",
		"express"
	],
	"dependencies": {
		"express": "^4.16.4",
		"nodemon": "^2.0.15"
	}
}
```

#### Khởi chạy npm để tiến hành cài đặt project

``` sh
npm start
```

#### Tạo file app.js để chạy project
``` sh
touch app.js
nano app.js     
```

```bash
const express = require('express');
const app = express();
const router = express.Router();

const path = __dirname + '/views/';
const port = 8080;

router.use(function (req,res,next) {
    console.log('/' + req.method);
    next();
  });
  
  router.get('/', function(req,res){
    res.sendFile(path + 'index.html');
  });
  
  router.get('/sharks', function(req,res){
    res.sendFile(path + 'sharks.html');
  });


app.use(express.static(path));
app.use('/', router);

app.listen(port, function () {
  console.log('Example app listening on port 8800!')
})
```

#### Viết file html cho trang web

``` sh
mkdir views
cd views
touch index.html
```

``` bash
<!DOCTYPE html>
<html lang="en">

<head>
    <title>About Sharks</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <link href="css/styles.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Merriweather:400,700" rel="stylesheet" type="text/css">
</head>

<body>
    <nav class="navbar navbar-dark bg-dark navbar-static-top navbar-expand-md">
        <div class="container">
            <button type="button" class="navbar-toggler collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false"> <span class="sr-only">Toggle navigation</span>
            </button> <a class="navbar-brand" href="#">Everything Sharks</a>
            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <ul class="nav navbar-nav mr-auto">
                    <li class="active nav-item"><a href="/" class="nav-link">Home</a>
                    </li>
                    <li class="nav-item"><a href="/sharks" class="nav-link">Sharks</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    <div class="jumbotron">
        <div class="container">
            <h1>Want to Learn About Sharks?</h1>
            <p>Are you ready to learn about sharks?</p>
            <br>
            <p><a class="btn btn-primary btn-lg" href="/sharks" role="button">Get Shark Info</a>
            </p>
        </div>
    </div>
    <div class="container">
        <div class="row">
            <div class="col-lg-6">
                <h3>Not all sharks are alike</h3>
                <p>Though some are dangerous, sharks generally do not attack humans. Out of the 500 species known to researchers, only 30 have been known to attack humans.
                </p>
            </div>
            <div class="col-lg-6">
                <h3>Sharks are ancient</h3>
                <p>There is evidence to suggest that sharks lived up to 400 million years ago.
                </p>
            </div>
        </div>
    </div>
</body>

</html>
```

#### Viết file csss cho trang web

``` sh
touch styles.css
```

``` bash
.navbar {
	margin-bottom: 0;
}

body {
	background: #020A1B;
	color: #ffffff;
	font-family: 'Merriweather', sans-serif;
}

h1,
h2 {
	font-weight: bold;
}

p {
	font-size: 16px;
	color: #ffffff;
}

.jumbotron {
	background: #0048CD;
	color: white;
	text-align: center;
}

.jumbotron p {
	color: white;
	font-size: 26px;
}

.btn-primary {
	color: #fff;
	text-color: #000000;
	border-color: white;
	margin-bottom: 5px;
}

img,
video,
audio {
	margin-top: 20px;
	max-width: 80%;
}

div.caption: {
	float: left;
	clear: both;
}
```

#### Start project

``` sh
cd ~/node_project
node app.js
```

#### Truy cập vào đường dẫn http://điền_ip_address:8080 hoặc http://localhost:8080/ để xem trang web

#### Tạo file Dockerfile để khai báo image mà chúng ta muốn Docker tạo cho mình.

``` bash
FROM node:10-alpine

RUN mkdir -p /home/node/app/node_modules && chown -R node:node /home/node/app

WORKDIR /home/node/app

COPY package*.json ./

USER node

RUN npm install

COPY --chown=node:node . .

EXPOSE 8080

CMD [ "node", "app.js" ]
```


#### Build image của project python từ Dockerfile. Lưu ý dấu chấm cuối được gọi là "build context", được xem là toàn bộ nội dung bên trong thư mục chứa Dockerfile bao gồm cả Dockerfile sẽ được gửi đến Docker Daemon để chạy và build image.

``` sh
docker build -t nodejs-image-demo:latest .
```

#### Kiểm tra image đã tạo

``` sh
docker images
```

#### Từ image run để tạo ra một container và chạy project

``` sh
docker run -it --name nodejs-image-demo -p 8800:8080 nodejs-image-demo:latest
```

#### Truy cập vào đường dẫn http://điền_ip_address:8800 hoặc http://localhost:8800/ để xem trang web


### Sử dụng docker-compose để tạo ra container (thay vì dùng lệnh dokcer run thì ta dùng docker-compsoe up để tạo 1 container)

#### Tạo file docker-compsoe.yml để config các services của project cho container

``` sh
touch docker-compose.yml
```

``` bash
version: "4.7"
services:
  app_test:
    image: app_test:latest       
    ports:
      #cổng ngoài:cổng trong container
      - 8888:8000
```

```sh
docker-compose up
```

#### Truy cập vào đường dẫn http://điền_ip_address:8800 hoặc http://localhost:8800/ để xem trang web

#### Đăng nhập vào Docker Hub

```sh
docker login
Username: username_dockerhub
Pasword:
```

#### Tạo tag cho image flaskpy_project (tạo ra một bản sao của image)

``` sh
docker tag flaskpy_project username_dockerhub/flaskpy_project:latest
```

#### Push image đã tạo lên Docker Hub để lưu trữ

``` sh
docker push username_dockerhub/flaskpy_project:latest
```

#### Pull image vừa push lên Docker Hub về để chạy (docker stop id_containeđang chạy)

``` sh
docker pull username_dockerhub/flaskpy_project:latest
```

#### Run image thành container mới

``` sh
docker run --name flaskpy_project_1 -p 8888:8000 -d username_dockerhub/flaskpy_project
```

### Tiến hành cài đặt database tool để quản lý dữ liệu (vd: MongoDB, PostgresDB,...)
#### Cài đặt MongoDB

``` sh
sudo apt install mongodb
```

#### Kiểm tra version MongoDB

``` sh
sudo systemctl status mongodb
```

#### Hiển thị tất cả databases

``` sh
show dbs
```

#### Hiển thị tất cả users

``` sh
db.getUsers()
```

#### Kết nối vào database admin

``` sh
use admin
```

#### Tạo user mới

``` sh
db.createUser(
  {
    user: "username",
    pwd: "user_pwd",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }]
  }
)
```

#### Tải module mongoose trong project

``` sh
npm install mongoose
```

#### Tạo file db.js để cấu hình database

``` sh
nano db.js
```

``` bash
const mongoose = require('mongoose');

const MONGO_USERNAME = 'username';
const MONGO_PASSWORD = 'user_pwd';
const MONGO_HOSTNAME = '127.0.0.1';
const MONGO_PORT = '27017';
const MONGO_DB = 'sharkinfo';

const url = `mongodb://${MONGO_USERNAME}:${MONGO_PASSWORD}@${MONGO_HOSTNAME}:${MONGO_PORT}/${MONGO_DB}?authSource=admin`;

mongoose.connect(url, {useNewUrlParser: true});
```

#### Thêm vào đường dẫn trong file app.js tại dòng số 4

``` bash
const db = require('./db');
```

#### Tạo thư mục models và file shaks.js bên trong để khai báo cấu hình dữ liệu của table sharks (scheme, model)

``` sh
mkdir /node_project/models
cd models
nano sharks.js
```

``` bash
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const Shark = new Schema ({
        name: { type: String, required: true },
        character: { type: String, required: true },
});

module.exports = mongoose.model('Shark', Shark)
```

#### Tạo thư mục controllers và file shaks.js bên trong để quản lý luồng dữ liệu ra vào database

``` sh
mkdir /node_project/controllers
cd controllers
nano sharks.js
```

``` bash
const path = require('path');
const Shark = require('../models/sharks');

exports.index = function (req, res) {
    res.sendFile(path.resolve('views/sharks.html'));
};

exports.create = function (req, res) {
    var newShark = new Shark(req.body);
    console.log(req.body);
    newShark.save(function (err) {
            if(err) {
            res.status(400).send('Unable to save shark to database');
        } else {
            res.redirect('/sharks/getshark');
        }
  });
               };

exports.list = function (req, res) {
        Shark.find({}).exec(function (err, sharks) {
                if (err) {
                        return res.send(500, err);
                }
                res.render('getshark', {
                        sharks: sharks
             });
        });
};
```

#### Tải và sử dụng EJS và Express Middleware để Collect and Render Data

```sh
npm install ejs
```

#### Thêm form cho người dùng nhập dữ liệu vào trang web tại file sharks.html tại dòng 44 và chỉnh sửa dòng 31 và 38 từ col-lg-6 thành col-lg-4

``` bash
        <div class="col-lg-4">
            <p>
                <form action="/sharks/addshark" method="post">
                    <div class="caption">Enter Your Shark</div>
                    <input type="text" placeholder="Shark Name" name="name" <%=sharks[i].name; %>
                    <input type="text" placeholder="Shark Character" name="character" <%=sharks[i].character; %>
                    <button type="submit">Submit</button>
                </form>
            </p>
        </div>
```

#### Tạo file getsharks.html để hiển thị dữ liệu sau khi người dùng đã nhập vào form

``` sh
nano node_project/views/getshark.html
```

``` bash
<!DOCTYPE html>
<html lang="en">

<head>
    <title>About Sharks</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <link href="css/styles.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Merriweather:400,700" rel="stylesheet" type="text/css">
</head>
<nav class="navbar navbar-dark bg-dark navbar-static-top navbar-expand-md">
    <div class="container">
        <button type="button" class="navbar-toggler collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false"> <span class="sr-only">Toggle navigation</span>
        </button> <a class="navbar-brand" href="/">Everything Sharks</a>
        <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
            <ul class="nav navbar-nav mr-auto">
                <li class="nav-item"><a href="/" class="nav-link">Home</a>
                </li>
                <li class="active nav-item"><a href="/sharks" class="nav-link">Sharks</a>
                </li>
            </ul>
        </div>
    </div>
</nav>
<div class="jumbotron text-center">
    <h1>Shark Info</h1>
</div>
<div class="container">
    <div class="row">
        <div class="col-lg-4">
            <p>
                <div class="caption">Some sharks are known to be dangerous to humans, though many more are not. The sawshark, for example, is not considered a threat to humans.
                </div>
                <img src="https://assets.digitalocean.com/articles/docker_node_image/sawshark.jpg" alt="Sawshark">
            </p>
        </div>
        <div class="col-lg-4">
            <p>
                <div class="caption">Other sharks are known to be friendly and welcoming!</div>
                <img src="https://assets.digitalocean.com/articles/docker_node_image/sammy.png" alt="Sammy the Shark">
            </p>
        </div>
        <div class="col-lg-4">
            <p>
              <div class="caption">Your Sharks</div>
                  <ul>
                     <% sharks.forEach(function(shark) { %>
                        <p>Name: <%= shark.name %></p>
                        <p>Character: <%= shark.character %></p>
                     <% }); %>
                  </ul>
            </p>
        </div>
    </div>
</div>

</html>
```

#### Add thêm engine vào file app.js tại dòng 21

``` bash
app.engine('html', require('ejs').renderFile);
app.set('view engine', 'html');  
```

#### Tạo thư mục routes và file index.js bên trong để cấu hình route cho trang index

``` sh
nano /node_project/routes/index.js
```

``` bash
const express = require('express');
const router = express.Router();
const path = require('path');

router.use (function (req,res,next) {
  console.log('/' + req.method);
  next();
});

router.get('/',function(req,res){
  res.sendFile(path.resolve('views/index.html'));
});

module.exports = router;
```

#### Tạo thư mục routes và file sharks.js bên trong để cấu hình route cho trang shark

``` sh
nano /node_project/routes/sharks.js
```

``` bash
const express = require('express');
const router = express.Router();
const shark = require('../controllers/sharks');

router.get('/', function(req, res){
    shark.index(req,res);
});

router.post('/addshark', function(req, res) {
    shark.create(req,res);
});

router.get('/getshark', function(req, res) {
    shark.list(req,res);
});

module.exports = router;
```

#### Tại file app.js chỉnh sửa như sau

``` bash
const db = require('./db'); # line 4
Command từ line 9 đến line 20
Command line 26, và thêm
app.use('/sharks', sharks);
```

#### Khởi chạy lại container

``` sh
docker start <container_id>
```

#### Truy cập vào đường dẫn http://điền_ip_address:8800/sharks hoặc http://localhost:8800/sharks để xem trang web

#### Tiến hành nhập thông tin vào form
``` sh
Shark Name: Megalodon
Shark Character: Ancient
Nhấn Submit
```

#### Tạo docker-compose.yml

``` sh
version: '3'

services:
  nodejs:
    build:
      context: .
      dockerfile: Dockerfile
    image: nodejs
    container_name: nodejs
    restart: unless-stopped
    env_file: .env
    environment:
      - MONGO_USERNAME=$MONGO_USERNAME
      - MONGO_PASSWORD=$MONGO_PASSWORD
      - MONGO_HOSTNAME=db
      - MONGO_PORT=$MONGO_PORT
      - MONGO_DB=$MONGO_DB 
    ports:
      - "8088:8080"
    volumes:
      - .:/home/node/app
      - node_modules:/home/node/app/node_modules
    networks:
      - app-network
    command: ./wait-for.sh db:27017 -- /home/node/app/node_modules/.bin/nodemon app.js 
```

#### Tạo môi trường .env mới
``` sh

```
