---
title: web_knowledge
date: 2017-09-19 23:59:07
tags:
---

Some basic knowledge of web application.
<!--more-->
### git

#### Version control system
the basic idea of version control system is that as you work on a large project and you often want to commit change to your project while still being able to access and possibly to go back to older version of the project. It's also useful for teams of programmers to coordinate their work by each of the programmer work on their static copy of their own and only commit her changes of the project possibly resolve conflict with other programmer's work with her changes complete.

#### GIT
GIT is a distributed version control system because when you clone, it make a copy of a repository. You get a full complete copy of that repository. Essentially a collections of all collaborate repository.

#### Branch
Branch is a full copy of a version of the repository so you have multiple version of your repository.

### HTTP: Hypertext Transfer Protocol

HTTP is a protocol that is used for tranfering data on the web.

If the brouser doesn't have the address of the www....com, the browser might have to start when it initiate the connection by connecting the domain name system(DNS) server and convert the www.com to IP address.

After the browser gets the IP address of the remote server, it may initiate a TCP/IP connection with the remote server. TCP is the transfer control protocol. It how most traffic internet work. To initiate a TCP/IP connection, the browser first send a SYN message to the web server and the server reply with a SYN-ACK package message to acknowledge the web server has receive the SYN. The browser send a ACK package to the server to acknowledge it has receive the SYN-ACK package. At this point, the browser can begin sending HTTP traffic by sending a HTTP request to the web server. The web server receive the HTTP request and send back the HTTP response.

#### Port
Ports for common web services:

- Telnet(23), SMTP(25), mysql(3306)
- For web servers:
	- Deployment: HTTP(80), HTTPS(443)
	- Development: HTTP(8000 or 8080), HTTPS(8443)

#### Request and Response
HTTP Request(first line is the request line):

```
<METHOD> <URI> <VERSION>
<HEADER LINE>
<HEADER LINE>
...
<HEADER LINE>
<BLANK LINE>
<OPTIONAL MESSAGE BODY>
```

HTTP Response(first line is the resopnse line):

```
<VERSION> <STATUS CODE> <STATUS MESSAGE>
<HEADER LINE>
<HEADER LINE>
...
<HEADER LINE>
<BLANK LINE>
<OPTIONAL MESSAGE BODY>
```
#### HTTP Request Method

- Safe methods are not supposed to have side effect:
 	- GET, HEAD(much like GET, but the response without message body), TRACE(the response will contain the whole request in the message body) and OPTIONS（请求http的方法）
- Idempotent（幂等性：调用一次和调用n次结果相同） methods can have side effect
	- PUT（更新会指明某个资源，做多次更新和1次更新结果一样） and DELETE（删除会指明某个资源，做一次删除和多次删除结果一样）,GET, HEAD
- Update method
	- POST（创建不会致命某个资源，一次调用创建一个，没有幂等性）

method | safe | idempotent
:--:|:--:|:--:|
GET | YES | YES
HEAD | YES | YES
OPTIONS | YES | YES
DELETE | NO | YES
PUT | NO | YES
POST | NO | NO

#### POST vs. GET:
- What data is contained in a typical request line? a header line? a message body?

	Get: the query string in the url, like
	
	```
	/test/demo_form.php?name1=value1&name2=value2
	```
	
	Post: the query string is sent in the HTTP request body
	
	```
	POST /test/demo_form.php HTTP/1.1
	Host: w3schools.com
	name1=value1&name2=value2
	```
- What are GET and POST typically used for?

	Use GET for safe and idempotent requests
	
	Use POST for neither safe nor idempotent requests
	
	Use POST for destructive actions such as creation (I'm aware of the irony), editing, and deletion, because you can't hit a POST action in the address bar of your browser. Use GET when it's safe to allow a person to call an action.
- How do browsers treats GET and POST request differently?

	Web browsers typically treat GET and POST differently since GET is safe and POST is unsafe. It's OK to refresh a web page retrieved by a GET request – the web browser will just re-issue the last GET request and render whatever the server sends back. However, if the page we are looking at in a browser is the response to a POST request, the browser will warn us if we try to refresh the page.

### HTML(Hypertext Markup Language)

### CSS(Cascading Style Sheets)

It a mean to seperate the style from the content and the structure.

### Bootstrap

Bootstrap is a popular CSS library that allows you to easily create sophisticated web page designs. You don't need to know any less or Java script or any of the other libraries like jquery that bootstrap uses you can just download precompiled CSS and Java script bootstrap files and include them in the header of your HTML file and then use the bootstrap CSS rules as you would use any CSS.

LESS is a CSS scripting language implemented in Java script.

you don't need to hand write any of the CSS or the Java script that makes that interactive, you can just use the appropriate bootstrap specific classes here and then your page will be interactive.

#### How bootstrap work
It 's a lot less style sheets better compiled to CSS and javascript and it use jquery library.

### Hidden Field
#### How to use hidden field
```
<input type="hidden"...>
```
#### The limitation of hidden field

- They aren't secret, they just aren't displayed.
- The user can control the value sent back to the server

### CSRF(Cross Site Request Forgery)
#### CSRF basic problem
One site could potentially send authenticated requests to another site if you are simultaneously logged in in a single browser session
#### The solution to CSRF
Require each request to contain some secret information that is not automatically added by the browser. One way to do that is to include a parameter in every request and have the value of that parameter be some big randomly generated secret value that only the site and possibly the user knows if they want.

### MVC(Model View Controller)
#### The basic components of a typical MVC framework

- Web browser
- Web servser
	- Controller application: a single point of control for the common features such as authentication, and mapping urls to the model and the views managing user session data and so on.
	- Model
		- Data model: store data
		- Actions: the core logic of the application
	- Views: justifies how information is presented back to the user after authenticating a request and performing some other basic processing

#### The flow of information in MVC to process a request

The controller typically determines which action in the application is responsible for processing this particular request and it forward the request to the model to be executed by the appropriate action. The action typically interacts with the data model getting and changing data as needed. When the action is done processing the request typically it forwards the request to some particular view it chooses which view is the right view for displaying the data for this request. The information from the model will help the view generate the actual response. The view itself is just responsible for formatting the data that was presented to it and generating some sort of response to the web browser
 
### Django
#### Django components correspond to MVC

MVC | Django
:--:|:--:|
controller | Django framework itself with Django URL configuration
view | template
data model | model
actions | view

#### Standard details of a basic Django application

- Project configuration in setting.py
- Request route configuration in urls.py
- Action in views.py
- View templates

### Relational Model
#### Relational model itself

- primary key: the column that can identify the record in the relation
- foreign key: when one table refers to another table using that table's primary key. This is called the foreign key.

#### The goals of the relational model
Abstract away the physical data layout. User doesn't need to understand the physical data layout.

### ORM(Object Relational Mapping Tools)
#### ORM
ORM is to provide an additional layer of abstraction between the web application and the storage system. User the ORM to retrieve or save data in the database, the web app never needs to write an y direct database or cloud service specific code itself. Web app just plays objects and the ORM generate an execute whatever code is needed to interact with the storage system.

#### Advantage vs. Disadvantage
AD. This makes the web application much easier to write and it also allows you to easily change on back end store system without needing to rewrite your web app.

Dis. 1. lose the full power when interact directly with the storage system. 2. The web app need features provided by the ORM but some high performance apps might need special feature that just not provided by the ORM.

### Django's URL dispatcher
#### Basic regular expressions
regular-expression groups is (?P<name>pattern)

### Django data model
#### How to use Django's ORM

- Edit configuration in settings.py
- Create data models in your application's models.py
- Run python manage.py makemigrations and python manage.py migrate
- Use your data models in your actions

#### Basic Django ORM features

- The id primary key
- .objects.all()
- .objects.get()
- .save()
- .delete()
- .filter()

### Cookies
Cookies is just a small piece of data provided by the web server in http response. The cookies are actually sent back to the server in the header and stored in the header. Cookies transmitted between web client and web server.

when the web client sends a subsequent request it will store the cookie and then send the cookie along with every subsequent http request to the same server.

#### Limitations

- 4KB limit enforsed by most browsers
- the user can see and forge cookie data
- the user can disable cookies

### Sessions

#### Sessions
- HOW: When the user first access the site and the server is not yet storing data for the user, the server creates a server side session and a sign that session some id and then stores some data as session data for the session and sends the session id back to the user and response usually as a cookie. 

- WHAT(for web application): The session data is typically stored on the server as a big dictionary of name value pairs for each session so that the server of the web application can maintain arbitrary data for each user.

#### Limitations

- Session ID must be transmitted with each subsequent request
- Server must maintain session data for each active user

**The session id transmitted in cookie**

### Django's built-in authentication module

#### HOW:

- configuration in settings.py
- URLs that route to Django's authentication actions
- How to use the Django's authentication form
- Automatic authentication using `@login_required`
- Manual authentication using `authenticate` and `login`

### Race condition

- Thread: a sequence of instruction that is scheduled and executed by the operating system and shared resources such as memory and programs

- Thread safe: Thread-safe code only manipulates shared data structures in a manner that ensures that all threads behave properly and fulfil their design specifications without unintended interaction.

- Race condition: it was check in one thread and then check in the second thread and then act in the first thread and the action the second threat so that the action in the second thread didn't get to see the effect of the first threats execution.

### Transaction

####  ACID properties

- Atomicity: All or nothing
- Consistency: Won't violate programmer-defined integrity constraints
- Isolation: Each transaction runs as if it is alone
- Durability: After successful commit, database won't undo transaction

#### Typical transaction operations

- `begin` or `start`: Starts a transaction
- `commit`: Attempts to successfully complete the transaction
- `rollback` or `abort`: Undoes the work of an uncommitted transaction
- `checkpoint` or `savepoint`: Marks partial work of an uncommitted transaction as a version that can be rolled-back to

### Reverse URL Resolution in Django
#### Django url template tag

- Four routes:
	- hard coding with static url
	- hard coding with django url
	- url template tag with url action
	- url template tag with url name

### Django Form
#### What problems Forms are solving

- validates the user input and checks to make sure that all the expected parametsers are present in the request and the parameters are in the right format.
- execuet some core logic. This varies from application to appliction depending on the purpose of the app and once the core logic is executed the application finally renders the view.

#### How to define a Django Form

- Define and use basic field types
- How to write custom overall validation code
- How to write custom field-specific validation code

#### Using a Django Form in your action

- How to create and use an unbound Form
- How t create a bound Form from request parameters
- How to validate a bound form
- How to use a Form's cleaned_data

#### Using a Django Form in your view

- How to use a Form's non-field-specific errors
- How to use the default Form view
- How to use the Form's fields individually
	- How to access each field
	- How to print labels, input widget, errors

#### MIME: Multipurpose Internet Mail Extensions
MIME: describe the data format on purpose

Common MIME-related header fields

- MIME-Version
- Content-Type: the type of content being sent
- Content-transfer-encoding: Encoding format for how the binary data s representing non-binary information
- Accept: specifies content-types a client is willing to accept 

#### Multipart form
Normal <form> the content-type is "application/x-www-form-urlencoded". It only accept text type request

Multipart form accept both text type and binary data type

```
<form enctype="multipart/form-data" action="..." method="post">

<input type="file">
</form>
```

#### Sending email
SMTP: Simple Mail Transfer Protocol

- The standard, text-based protocol for sending mail on the internet
	- You can run your own SMTP server
	- or you can use smtp.andrew.cmu.edu but you will need to authenticate yourself

```
from django.core.mail import send_mail

send_mail(subject="Verify your email address",
			meddage = email_body,
			from_email="XXX@cs.cmu.edu",
			recipient_list=[new_user,email])
```

#### DOM: The Document Object Model
A tree of nodes that represent the HTML documents

#### Client-side Web Programming

JavaScript: A client-side scripting language that runs in the web browser

- Interpreted, dynamically typed, garbage-collected
- Object-oriented, C-like syntax
- Functions are objects

##### two ways:

- Write it drectly in HTML page:

```
<script type="text/javascript">
</script>
```
- Include it from an external source file

```
<script src="foo.js" type="text/javascript"></script>
```
##### Using javascript

- Write javascript functions
- Respond to in-browser events:
	- onload, onclick, onfocus, onblur,onmouseover...
	
	```
	<img onclick="foo()">
	<bodu onload="document.forms[0].elements[0].focus()">
	```
- Generate asynchronous HTTP requests
- Navigate and update the DOM