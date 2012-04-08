Shiro OAuth Grails Plugin
=========================

[Shiro](http://shiro.apache.org/) is a flexible authentication and authorisation framework for Java that has a corresponding [Grails plugin](http://grails.org/plugin/shiro) for simplifying access control in Grails applications. This plugin extends the base Shiro plugin with support for authentication via OAuth using the [Scribe OAuth plugin](http://grails.org/plugin/oauth-scribe).

How it works
------------

Most of the work is done by the other two plugins. The Scribe OAuth plugin provides the actual OAuth authentication, while the Shiro plugin does the access control and account management. The two are linked through four main classes provided by this plugin:

* OAuthToken
* OAuthRealm
* ShiroOAuthIdentity
* ShiroOAuthController

Shiro works on the basis of authentication tokens that contain the information that identifies a user. For example, there is a `UsernamePasswordToken` that contains a username and password. This is probably the most common one. The `OAuthToken` class is a Shiro authentication token, although one without any credentials. It wraps a Scribe access token from a successful OAuth authentication attempt. That's it.

The `OAuthToken` isn't much use by itself because the standard OAuth response doesn't contain and form of identity for the user that just logged in. That's why the class is abstract. So the plugin also bundles provider-specific authentication tokens that extract the relevant information from the access token, such as a Twitter screen name in the case of `TwitterOAuthToken`.

The realm, `OAuthRealm`, accepts instances of `OAuthToken` and checks whether the user has been linked to an internal Shiro user account (typically a `User` domain instance). The linking is done via the `ShiroOAuthIdentity` domain class, which contains the OAuth username and provider and which has a reference to the user account domain class.

Finally, we have the `ShiroOAuthController` which can be used as a URL for successful OAuth authentication. It wraps the Scribe access token in the appropriate `OAuthToken` and attempts to log the user in. If that's successful, i.e. the `OAuthRealm` finds a corresponding user account, the user is redirected back to whatever `targetUri` is set to. Otherwise, the user is redirected to an application-provided URL that will allow the user to link the OAuth account to an existing Shiro one or to a new Shiro account.

Left to do
----------

* Allow multiple OAuth providers in a single application (requires changes in Scribe OAuth plugin).
* Hide the `shiroOAuthToken` session attribute from applications and automatically remove it after successful logins.
* Add more OAuth tokens (Facebook, Google, GitHub, LinkedIn, etc.).
* Provide a default `security.shiro.oauth.providers` map of provider names -> auth token classes.