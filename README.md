# Node.js / Express Mini Project

This document specifies a backend project that will be completed using Node.js / Express. 

The aim of this project is to assess you as a Node.js backend developer.

##### Scope of Project

* A: User registration
* B: User login, logout, access to protected resources
* C: Persist the user information using MongoDB
* D: Adminstrative functions
* E: Validate user inputs


## Project scope A: User registration

### User registration page
The user is presented with a web form to register an account.

**Note that the assets ([register.html](code/register.html), [register.js](code/register.js)) will be given to you. You do not have to implement them from scratch.**

![image](images/register-screen.png)

The user will key in his email, username, password and tap the `Verify your phone number` button. Upon doing this, a dialog ([Facebook AccountKit](https://developers.facebook.com/docs/accountkit/webjs)) pops up and the user is required to key in his mobile number. 

For development, please key in your personal mobile number.

![image](images/accountkit-1.png)

A verification code from Facebook will be sent to the mobile number via SMS. The user has to key in the verification code and tap on the `Continue` button.

![image](images/accountkit-2.png)

Upon successful verification, the dialog closes and on the registration form, the number is shown to be verified and the `Register` button is enabled.

![image](images/accountkit-3.png)

The user will be able to proceed with the registration.

### Facebook Account Kit

We use Facebook Account Kit to verify a user's mobile number. A user enters his mobile number in the account kit dialog, receives an SMS with a verification code and keys in the verification code.

Facebook verifies that the entered verification code is valid and calls back a function in the user registration page with the response.status and response.code.

A facebook app with account kit has been configured, so you don't have to deal with setting that up.

##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement
There should be an API endpoint that takes in the response.code and

* Calls Facebook API to retrieve the phone number. **Code given:** [fbacckit.js](code/fbacckit.js)
* Checks that the phone number IS NOT an existing phone number belonging to any other user (All users have unique phone number).
* Generate a signed time-limited token (we use JWT) that contains the phone number
* Returns that token to the calling web page so that it can be passed as a hidden field in the form.


### Create user account
The user registration form contains the following input fields

* username
* email
* password
* phone_token (hidden field)

##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement 
There should be an API endpoint that takes in the above fields and

* Check that the phone_token originates from server (not tampered with) and has not expired.
* Extract the phone from the phone_token
* Check that the username, email, phone are unique
* Generates a unique id. **Code given:** [utils.js](code/utils.js)
* Salt and hash password. **Code given:** [utils.js](code/utils.js)
* Stores the user (perhaps in-memory for now, or you can use MongoDB straight away)
* Returns a json response, with a profile and a id_token (again, a JWT) that will be passed in, as a means of authentication, for subsequent requests to protected resources.



The newly created user is represented as follows:

```
{
    "id": "DdfyFgdeRC2BfDLwiHQM5Q",
    "created": 1488421913396,
    "username": "someone",
    "password": "$2a$10$iByQvY.E.ICJNYuqTRSU2.f2QEyVUCcMXJEL7erEoZzAIjYuod3Wu",
    "email": "someone@example.com",
    "e_verified": false,
    "phone": "+6591234567",
    "role": "parent",
    "locked": false
}
```
The actual JSON returned (hint: use [https://jwt.io](https://jwt.io) to examine the id_token)

```
{
	"profile": {
		"id": "DdfyFgdeRC2BfDLwiHQM5Q",
		"created": 1488421913396,
		"username": "someone",
		"email": "someone@example.com",
		"e_verified": false,
        "phone": "+6591234567",
		"role": "parent"
	},
	"id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJTcVM0d2V3YlNOV0xkZzdfc0RwbHJRIiwidXNlcmlkIjoiRGRmeUZnZGVSQzJCZkRMd2lIUU01USIsInJvbGUiOiJwYXJlbnQiLCJzZXJ2aWNlIjoiQVUiLCJpYXQiOjE0ODg0MjE5MTMsImV4cCI6MTQ4OTYzMTUxM30.XGxyojdocjXvfn3TMM6dHOlM3DP4mJx1QE7oWjHIDIY"
}
```

## Project scope B: User login, logout, access to protected resources

### User login
A user should be able to login given his username and password.

##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement
There should be an API endpoint that takes in the username and password. For a valid user, the API will sign the token and return it.

```
{
    "id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJTcVM0d2V3YlNOV0xkZzdfc0RwbHJRIiwidXNlcmlkIjoiRGRmeUZnZGVSQzJCZkRMd2lIUU01USIsInJvbGUiOiJwYXJlbnQiLCJzZXJ2aWNlIjoiQVUiLCJpYXQiOjE0ODg0MjE5MTMsImV4cCI6MTQ4OTYzMTUxM30.XGxyojdocjXvfn3TMM6dHOlM3DP4mJx1QE7oWjHIDIY"
}
```
 Returns error if no user with such username and password is found.

### User access to protected resources
As a proof-of-concept, implemented a protected API endpoint (me), that returns the user profile when supplied with the id_token in the Authorization header. 

##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement
Create an endpoint to return a user profile. The id_token is present in the Authorization header. See image for details.

You can use Postman or cURL to test.
![Image](images/profile.png)


### User logout
Logout a user

##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement
Create an endpoint to logout the user. Extract the id_token and check if it is valid. If so, just console.log out the JTI of the token for now.


## Project scope C: Persist the user information using MongoDB

If you have been using an in-memory store to keep user information, it is time now to persist data in MongoDB storage.


##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement
Use mongodb to persist user accounts

Use a local MongoDB instance for development.

We have also created a hosted MongoDB database with MLab. Later on, you can connect using a driver via the standard MongoDB URI 

`mongodb://<dbuser>:<dbpassword>@ds113630.mlab.com:13630/tentenapp`

Ask me for the dbuser and dbpassword.

## Project scope D: Adminstrative functions

For proof-of-concept, an adminstrator (who is authenticated) will be able to  

* list all users
* retreive a user by id
* update a user
* delete a user

##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement
Implement the above API endpoints. Ensure that the adminstrator is authenticated. (As proof-of-concept, can be as checking for `Authorisation: ADMIN` in the HTTP header)

*UI not required.*


## Project scope E: Validate user inputs

Currently user inputs when creating user account are not validated. User inputs should be validated.

* username - minimum 5 characters, alphanumeric and underscores only
* password - minimum 6 characters
* email - valid email format

##### ![star](https://assets-cdn.github.com/images/icons/emoji/unicode/2b50.png) Requirement
Implement validation



## What we are looking out for

#### API design
Design sensible APIs with proper namespace, request parameters and responses. Return errors as neccessary.

#### Code organisation
Structure your code such that it is clean, modular and maintainable. Some examples

* separate routings / services in logical groups
* models kept separately
* database code kept separately
* configuration code kept separately 

## What we are NOT looking out

- Frontend design and style


## Development and Deployment

The Facebook account kit needs the server URLs to be whitelisted. You should use http://localhost:3000 for development.

You can upload your code to Github repo and import it as a project to  [GoMix](https://gomix.com/) (free) to run it. If you do so, let me know the server url so I can add it to the whitelist.

The APIs need to be be accessed from different domains (the web server and API server are different), so you may have to deal with CORS as well.


## How to proceed

* Estimate how much time you will take for each part (A to E). Let me know.
* There is no need to do all parts - depends on your speed, availability, appetite.
* Create a git repo and commit your code. Let me have access to the repo. A public repo is fine by me.
* Remember, code organisation is very important.