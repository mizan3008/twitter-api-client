Twitter API Client
==================

Super-simple, minimum abstraction Twitter API Client

Requires PHP 5.3 and a pulse.

Installation
------------

You can install the Twitter using Composer:

```
composer require mizanur/twitter-api-client
```

Examples
--------

Q. How to create twitter APP?

A. Everything is here https://apps.twitter.com/


use this on top of your class:

```
use Twitter\Twitter;
```

Now we are going to make a request for twitter authorization

```
function twitterLogin ($consumerKey, $consumerSecret)
{
	$twitterOauth = new TwitterOAuth($consumerKey, $consumerSecret);
	//this callback url should trigger your callback function, so please set this url as you platform
	//in my case my callback url is *www.example.com/twitterCallback*
	$url = 'YOUR-CALLBACK-URL';
	//requesting for access token
	$request_token = $twitterOauth->getRequestToken($url);
	//stroing token into session
	$_SESSION['oauth_token'] = $request_token ['oauth_token'];
	$_SESSION['oauth_token_secret'] = $request_token ['oauth_token_secret'];
	// If everything goes well..
	if ($twitterOauth->http_code == 200) {
		// Let's generate the URL and redirect
		$url = $twitterOauth->getAuthorizeURL($request_token ['oauth_token']);
		header('Location: '.$url);
		exit();
	} else {
		// It's a bad idea to kill the script, but we've got to know when there's an error.
		die('Something wrong happened.');
	}
}

```

Now we are going to make a callback fucntion. This function will be execute after success login from twitter

```
function twitterCallback()
{
	if (isset($_GET ['oauth_verifier']) && $_GET ['oauth_verifier'] != "") {
		$twitterOauth = new TwitterOAuth($consumerKey, $consumerSecret, $_SESSION['oauth_token'], $_SESSION['oauth_token_secret']);
		$twitterOauth->getAccessToken($_GET ['oauth_verifier']);
		//fetching user information
		$user_info = $twitterOauth->get('account/verify_credentials');
		if (!isset($user_info->error)) {
			//now you have user profile info, now you can do whatever you want
			echo "<pre>";print_r($user_info);echo "</pre>";

			//lets tweet on user timeline if you want
			$status = $twitterOauth->post('statuses/update', ['status' => 'YOUR-STATUS-MESSAGE']);
		}
	} else {
		die("There was an error.");
	}
}
```
