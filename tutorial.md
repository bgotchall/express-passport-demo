# Passport demo project walkthrough

Here is a description of a sample application that demonstrates a password/user login experience.  Upon first visiting, you cannot access the "member" page but only the page to login or sign up.  Upon setting up credentials you can login and visit the member page, and logout.


The application itself is a demonstration of extending express with a security/login framework.  It requires a signup and login in order to access a certain page.

The application is a node.js server side app.  It uses various other libraries pulled in via NPM including:

**mysqql2, sequelize**: interfaces to a mySQL database to store server side data.  

**express**: to run a server and handler routing.  

**passport, passport-local**: a library to handle the login/user mechanism.  Passport has many "strategies" depending on what you are trying to do, including ways to log in to and access data from many third parties like Facebook, Twitter, Spotify etc.  Here we are using "local"  

**express-session, bycryptjs**: security helpers.  



The application starts with running "server.js"

It follows a MVC (model/view/controller) structure.

There is a server.js file, and directories for config, models, public files used by the client side, and routes:

+--server.js
+--package.json
+--/config
|  +--config.json
|  +--passport.js
|  +--/middleware
|     +--isAuthenticated.js
+--/models
|  +--index.js
|  +--user.js
+--/public
|  +--login.html
|  +--members.html
|  +--signup.html
|  /js
|    +--login.js
|    +--members.js
|    +--signup.js
|  /stylesheets
|    +--style.css
+--/routes
   +--api-routes.js
   +--html-routes.js


The server.js file sets up a bunch of things, then starts an express server instance.  It requires modules the application will need: express (a common web application framework) and express-session which is used for logins. The server.js file pulls in the contents of the /models directory, which will define the database structures used.  It also pulls in the api and html route files in /routes.  It configures the express session.  

server.js requires (pulls in) the index.js file.  This in turn requires the config.json file which is just a datafile with environment variables which must be different depending on whether you are in developement or deployed (or testing, etc).  index.js also sets up sequalize, including reading in every model file (just user.js).


The last lines actually run something.  The sequelize "sync" method initializes the SQL database if needed and once that is done, the express server is started on port 8080.  The server will watch that port and take actions, recieving and sending data to the browser requests.


Now a browser needs to access the "/" route on the client side.  The api-routes.js and html-routes.js files contain the route functions that the client can access, mostly by the client side javascript calls. 

 The "/" default route checks to see if a user is logged in, and if so goes the member page.  Else it is redirected to the signup.html page.

 That signup.html page contains a simple form for email and password (and a link to bypass and login instead).  signup.html links to js/signup.js which handles the form actions.  The submitted info is checked and cleaned up and finally a post route is invoked to the /api/signup route.  

 The api-routes.js file has that route.  It takes the data (email, password) and creates a record in the database.  If that is sucess, it redirects to the /api/login route.  If it is not successful, for instance if the email is already in the database, it returns an error.

 The /login route (in html-routes.js) checks the middleware to see if the user already has a session, and if so sends them to the /members route.  If not, they go to the login.html file.

 login.html  uses login.js.  It contains a simple form asking for email and password.  login.js does some cleaning and checking, and invokes a POST to the /api/login route.

 The api-routes.js file has that route.  It uses the middleware callback passport.authenticate("local) function to check to see if they are authenticated with the supplied info.  If so, the user info is returned and the client side makes a new call to the /members route.  If not, an error is returned to the client.

 if the /members route is called, the members.html file is displayed.  The /members route checks the "isAuthenticated" callback middleware to ensure the user is logged in (otherwise just typing hostname/members would bypass your "security").  

 The members.html file uses the members.js file.  This page starts by posting a GET to /api/user_data route.  if logged in, the user's email is returned.  The members.html page is updated to reflect this information from the database.

 The html forms are *lightly* styled by /stylesheets/style.css.

 To extend/modify this project, one could now hang new content on this framework.  The "members.html" page shows how one can do routes that require authentication, and the "login.html" show how one can route public, not logged-in requests.  Other "strategies" can be used to authenticate with your Facebook credentials instead of a local strategy.  

 Any ordinary application could be added in this way.  New routes added, new tables added to the database to do useful things (which can now make use of the user's email/id) and new html pages or views added.
 

