angular-spa-security
====================

A handy security provider for Angular JS. Works with MVC 5's SPA template without any modifications to the backend code.

##Installation

####Nuget
`install-package AngularJs.SPA.Security`

##Usage

1. Include the `angular-spa-security.js` script provided by this component into your app
2. add `security` as a module dependency to your app

####Javascript
```javascript
angular.module('app',['SignalR'])
.config(['securityProvider', function(securityProvider){
	//Modify provider settings
	securityProvider.events.login = function(security, user){
		alert('Hello '+user.userName);
	}
}])
.run(['$rootScope','security', function($rootScope, security){
	$rootScope.security = security; //So you can access security variables and methods anywhere
}])
.controller('SecretPageCtrl', ['security', function(security){
	security.authenticate(); //If user isn't authenticated, will send them to the login page
}])
.controller('LoginCtrl', ['security','$scope', function(security, $scope){
	security.redirectAuthenticated('/'); //If the user is already authenticated, send them to the homepage

	$scope.login = function(user){
		security.login(user).then(function(user){
			//Success
			//Automatically sends them back to the page they were trying to access or the home page
		}, function(errorData){
			//Error
		});
	}
}])
```

####HTML <sub><sup>List External Login Buttons</sup></sub>
```html
<h4>Or Login With</h4>
<button class="btn btn-default" ng-repeat="login in security.externalLogins" ng-bind="login.name" ng-click="security.loginWithExternal(login)"></button>
```

##Provider Options

* `registerThenLogin` when registering automatically log in right after *true*
* `urls.login` url of login page */login*
* `urls.postLogout` url of where to send the user after logout */login*
* `urls.registerExternal` url of where to send the user after returning from OAuth on first login with that account */registerExternal*
* `urls.home` url of where to send the user after login */*
* `apiUrls.join` where to send registration request */api/account/register*
* `apiUrls.login` where to send login request */token*
* `apiUrls.logout` where to send the logout request */api/account/logout*
* `apiUrls.userInfo` where to send the userInfo request */api/account/userInfo*
* `apiUrls.changePassword` where to send the changePassword request */api/account/changePassword*
* `events` hooks for functions that will be called during these corresponding events: login, logout, register, reloadUser, closeOAuthWindow

##Security Variables

* `user` holds the response from login request - using the login event hook you can override this
* `externalUser` holds the response from external login request when the user doesn't already have an account
* `externalLogins` array of the available 3rd party login providers - providers are objects like `{name: 'Twitter', url:'/api/account/...'}`

##Security Methods

* `login(data)` login with the data from the data object (username, password)
* `loginWithExternal(login)` attempt to login using the specified login provider from the externalLogins array - redirect to external register if the don't already have an account <sub><sup>see example above</sup></sub>
* `logout()` logout the current user
* `register(data)` register with the data from the data object (username, password, confirmPassword)
* `registerExternal()` send the updated externalUser data for updating and get the new 3rd party token and login
* `changePassword(data)` change the password with the data object (oldPassword, newPassword, confirmPassword)

##Demo

[Modified SPA demo](https://github.com/JustMaier/MVC5-SPA-Angular)

##Notes

* Rather than modifying the security provider, I reccomend using the event hooks to add custom handling and additional parameters. If you want to add functionality that you think will be helpful to others, just submit a pull request