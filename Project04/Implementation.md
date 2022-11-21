# Project Implementation

## 1. Install NodeJS
* NodeJS provides tha Java Runtime Environment. It can be installed via the ubuntu package manager,apt. 
* Install via the following commands:
```bash
# Upgrade package manager 
sudo apt update
sudo apt upgrade

# Add certificates
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

#  Install NodeJS
sudo apt install -y nodejs
```
![install nodejs](/Project04/images/install_nodejs.png)


## 2. Install MongoDB 
* For this project, book records will be stored in the database, MongoDB. 
* Install MongoDB
```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

sudo apt install -y mongodb
```
* Start the server and confirm the service is running
```
sudo service mongodb start
sudo systemctl status mongodb
```
![mongodb status](/Project04/images/start_mongodb.png)

* Next, install Node package manager (npm)
```
sudo apt install -y npm
```
* The 'body-parser' package will be used to process JSON files passed as requests. Install this via the following command:
```
sudo npm install body-parser
```
![install body-parser](/Project04/images/install_bodyparser.png)
* Create a directory called 'Books'. Navigate to this location and initialise the npm project. 
```
mkdir Books 
cd Books 
npm init
```
* In the same location (Books directory), create a file called server.js and paste the following code:
```js
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});

```

## 3. Install Express and set up Routes
* Express is a back-end web application framework.
* It will be used to pass book information to MongoDB. 
* Mongoose will be used to establish a database schema to store book data. 
* Install Mongoose
```
sudo npm install express mongoose
```
![install mongoose](/Project04/images/install_mongoose.png)  
* Navigate to the `Books` directory (if not already there) and created a folder called `apps`. Within the `apps` directory, create a file called `routes.js`
```bash
mkdir apps && cd apps && vi routes.js
```
* Paste the following in the `routes.js` file:
```js
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```
* Return to the `apps` directory and create a new folder called `models`. Navigate to this directory and create a file called `book.js `
```bash
mkdir models && cd models && vi book.js
```
* Paste the following in the `book.js` file:
```js
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```

## 4. Access routes via AngularJS
* AngularJS provides the front-end web application framework allowing for dynamic webpages.
* Return to the `Books` directory and create a folder called `public`. Navigate to this location and create a file, `script.js`
```bash
cd 
mkdir public && cd public && vi script.js
```
* Paste the following in `script.js`. This is the controller configuration 
```js
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```
* Also, in the `public` folder, create a file named `index.html`. Paste the following:
```html
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```
* Return to the books directory and start the server:
```bash
cd ..
node server.js
```
![node server.js](/Project04/images/node_serverjs.png)
* The server is now running and accessible via port 3300. Make sure to amend the instance Security Group to enable inbound traffic from this port.
![security group](/Project04/images/sg.png)
* Navigating to the following address `http://<public_ip>:3300` should render a blank book register to which books can be added or deleted. 
![final webpage](/Project04/images/webpage.png)