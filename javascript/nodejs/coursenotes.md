# Udemy NodeJS Course Notes
# Sources
* https://www.udemy.com/course/nodejs-the-complete-guide/

# Assumptions
Notes assume intermediate knowledge of modern Javascript

# Important Core Modules
* http - launch a server, send req
* https - launch an SSL server
* fs - filesystem
* path - file paths
* os - OS stuff

# Requiring modules
Generally:
```javascript
const http = require('http');
```
# Module Specific Notes
## http
### createServer
* Needs a function to act as the server, eg
```javascript
const server = http.createServer((req, res) => {});
server.listen(portnum);
```
```javascript
process.exit() // end server process, generally don't do this.
```


# Conventions
* Main file is generally app.js or server.js 
* When requiring a module, generally use the name of the module for the name of the variable catching the require.  eg:  
```javascript
const http = require('http')
```
* 