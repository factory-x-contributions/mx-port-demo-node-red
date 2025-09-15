This flow provides "basic HTTP Authorization" for HTTP endpoints which are intended for certain users only.

> This flow is part of a set of [node-red-authorization-examples](https://github.com/rozek/node-red-authorization-examples) but also published here for easier lookup

**Prerequisites**

This example requires the following Node-RED extension:

* [node-red-contrib-reusable-flows](https://flows.nodered.org/node/node-red-contrib-reusable-flows)<br>"Reusable Flows" allow multiply needed flows to be defined once and then invoked from multiple places

Additionally, it expects the global flow context to contain an object called `UserRegistry` which has the same format as described in ["node-red-within-express"](https://github.com/rozek/node-red-within-express):

* the object's property names are the ids of registered users<br>user ids are strings with no specific format, they may be user names, email addresses or any other data you are free to choose - with **two important exceptions**: user ids must neither contain any slashes ("/") nor any colons (":") or the authentication mechanisms described below (and the user management described in [node-red-user-management-example](https://github.com/rozek/node-red-user-management-example)) will fail. Additionally, upper and lower case in user ids is not distinguished
* the object's property values are JavaScript objects with the following properties, at least (additional properties may be added at will):
  * **Roles**<br>is either missing or contains a list of strings with the user's roles. There is no specific format for role names except that a role name must not contain any white space
  * **Salt**<br>is a string containing a random "salt" value which is used during PBKDF2 password hash calculation
  * **Hash**<br>is a string containing the actual PBKDF2 hash of the user's password

When used outside "node-red-within-express", the following flows allow such a registry to be loaded from an external JSON file called `registeredUsers.json` (or to be created if no such file exists or an existing file can not be loaded) and written back after changes:

![outside-node-red-within-express](outside-node-red-within-express.png)

These flows are already part of this example but may be removed (or customized) if not needed.

For testing and debugging purposes, the following flow may also be imported, which dumps the current contents of the user registry onto Node-RED's debug console when clicked:

![show-user-registry](show-user-registry.png)

Which is also already part of this example.

**Postman Collection**

For testing purposes, the [GitHub repository](https://github.com/rozek/node-red-authorization-examples) for this example additionally contains a [Postman](https://www.postman.com/) collection with a few predefined requests.

## Basic HTTP Authorization

The simplest approach to user authentication and authorization is to utilize the "basic HTTP authentication" built into every browser: if a requested resource requires a client to authenticate, the browser itself opens a small dialog window where users may enter their name and password (or cancel the request). These credentials are then sent back to the server for validation - if the server still denies access, the dialog is presented again, allowing users to enter different credentials - otherwise the browser stores the successful entries internally and, from now on, automatically sends them along with every request.

> Nota bene: user credentials (especially passwords) are sent in plain text form - for that reason, it is important to use secured connections (i.e., HTTPS) only

![basic-auth](basic-auth.png)

The upper output is used for successful authentications, the lower one for failures.

If you require the authenticating user to have a specific role, you may set `msg.requiredRole` to that role before invoking the `basic auth` flow - otherwise, user roles will not be checked.

Upon successful authentication, `msg.authenticatedUser` contains the id of the authenticated user and `msg.authorizedRoles` contains a (possibly empty) list with the roles of that user.

**Try yourself**

The following example illustrates how to integrate basic authentication into Node-RED flows:

![try-basic-auth](try-basic-auth.png)

The "basic HTTP authentication" procedure frees developers from having to design and implement their own login forms as the user interface is already built into the browser.

However, basic authentication lacks (implicit) expiration and explicit logout, making it very difficult to terminate an authenticated "session" or to change users: once correct credentials have been given, the browser always automatically attaches them to every request - unless a "private" window (or tab) is opened: in that case, the browser withdraws any given credentials as soon as the window (or tab) is closed.

**Automated Tests**

The [GitHub repository](https://github.com/rozek/node-red-authorization-examples) for this example also contains some flows for automated tests.
