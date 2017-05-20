# Photo share
The Photo Share app allows users to view images together.

## How does it work
The app allows users to drag and drop images on to the platform and displays it live on the other clients screen.

### Routes
#### Endpoint:** /**
Will redirect the user to login page of Instagram to get a authorize the user.

#### Endpoint: ** /main**
This is the main content page of the app. An API call is made to this link `https://api.instagram.com/v1/users/self/media/recent/?access_token={access_token}`

- access_token: generated by the given code from `/auth` path

#### Endpoint: ** /auth**
This path is used as a callback URI by Instagram. The link looks as follows `http://localhost:3000/auth?code={code}`   

- code: code that is used to generate a `access_token`

### Websockets
The app allows communication via Socket.io

#### Client
**Update users**

This event listener listens to the `new user` emit function.
It receives three parameters `name`, `allUsers` and a `message`.
- name of the added/removed user for specific message
- allUsers is a list of all users still active
- Sends a message to the users

```js
// get a list of all users along with an update message
socket.on('update users', function(user, allUsers, message){
    alert(message);
});

```
***
**Update image**

This event listener listens to the `update image` emit function.
It receives one parameters `data`.
- data is a object `{src:'link-to-image', name:'name-of-image'}`

```js
// get files that are uploaded by others will be build
socket.on('update image', function(data){
    updateImage(data);
});
```
#### Server
**Connection**

This event listener listens to whenever `http://localhost:3000/` is opened.
- It receives one parameters `client` to handle further socket events by the current client.

```js
io.on('connection', function(client){})
```
***
**New user**

This event listener listens to the `new user` emit function.
It receives two parameters `name` and a `callback`.
- It checks if a user is unique
- Adds user to the "temp-database"


```js
client.on('new user', function(name, callback){
    if(isUniqueUser(name)){
        allUsers.push({name, id: client.id});
        updateUsers(client,{name,id:client.id},'has joined the party');
        callback(true); // callback to disable form
    }else{
        updateUsers(client,null,'Name already taken');
        callback(false);
    }
});
```
***
**New dropped file**

This event listener listens to the `new dropped file` emit function.
It receives one parameters `data`.
- It emits a function to the client named `update image` refesh the image on other connected clients

```js
client.on("new dropped file",function(data){
    client.broadcast.emit('update image', data);
});
```
***
**Disconnect**

This event listener listens to the `disconnect` emit function.
- It checks which user has left and removes him/her from database
- It alerts the other users (temporary annoying alert)

```js
// Whenever a user shuts down the browser
client.on('disconnect', function(){
    // Get the data of the current leaving user and update the list of users
    var leavingUser = checkCurrentUser(client.id);
    if (leavingUser) {
        updateUsers(io,leavingUser[0],'has left the party');
        // Remove leavingUser from list
        allUsers.splice(allUsers.indexOf(leavingUser),1);
    }
});

```
### Drag and drop

The `DragEvent` Javascript feature allows user to drag and drop content from one place to another.

```Javascript
element.addEventListener("dragstart",function(e){
    // Set the format of the data
    e.dataTransfer.setData("text/html", e.target.id);
    e.dataTransfer.setData("text/plain", e.target.id);
});

// Triggers whenever an element is above the dropzone
dropZoneElement.addEventListener("dragover", function(e){
    e.preventDefault();
    // Set the dropEffect to move. I am not sure what it does. I think it changes the cursor
    e.dataTransfer.dropEffect = "move";
});
// Triggers whenever an element is dropped in the dropzone
dropZoneElement.addEventListener("drop", function(e){
    e.preventDefault();
    // Get the id of the target and add the moved element to the target's DOM
    var data = e.dataTransfer.getData("text");
    this.appendChild(document.getElementById(data));
});
```
### Fallback
There is currently no default browser fallback. As a fallback the developer could make the elements an ```html <a href="#title>``` and use Javascript to handle the input.  To use the drag and drop feature on a mobile use one of these js-libraries:

- [Hammer.js](http://hammerjs.github.io/)
- [Interact.js](http://interactjs.io/)


### Browser that can use it
This is a features is supported by most browsers. It is not supported on mobile browser because of the way mobile touch events work.

| IE & Edge             | Firefox, Chrome and Safari| Mobile      |
|-----------------------|---------------------------|-------------|
|Partially supported    |Fully supported            |Not supported|






### Built With
- Node.js with an Express.js framework - Server.
- Socket.io - Realtime client - sever communication.
- Handlebars - Serverside templating.
- JavaScript (mixed ES5 and ES6) - Client.
- CSS for the styling.


## How to contribute
You can help improve this project by sharing your expertise and tips. Contributions of all kinds are welcome: reporting issues, updating documentation, fixing bugs, building examples, sharing projects, and any other tips that may improve my code.

### Install
- First clone this repo with:
```txt
$ git clone https://github.com/eltongonc/real-time_web.git
```

- then change directory to week2 and run:
```
$ npm install && npm start
```
this should start the app in your localhost.

**Live**

- To view it live on other devices run:
```
$ npm install && npm start
```
- keep this terminal open an open another one, and run:
```
$ npm run ngrok
```
this should give you a http link.

## To-do
- [x] Express server
- [x] Implement socket.io
- [ ] App is live
- [ ] Identify an user
- [ ] Make the site responsive to work on smaller devices
- [ ] Production ready code on github
- [ ] Join or create a Socket.io room on entering the page
- [ ] Upload images that can be edited
- [x] Use an API to get image
- [ ] Save the canvas as an images
- [ ] Use Instagram name as profile name

## Wishlist
- [ ] Username and password stored in database
- [ ] Edit images (I tried using Fabric.js, D3.js and P5.js, but with no success)

## Features
- Login via Instagram
- Get images from Instagram
- Drag and drop local images to the platform

## Example
Not finished yet but here is a link to the temporary link

## Known bugs
- App may crash when dropping a large image onto the canvas
- App has bugs when multiple users are connected

## Sources
- [Instagram Api](https://www.instagram.com/developer/)
- [developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API)

### Author
`Elton Gonçalves Gomes` - checkout more of my work on [github](github.com/eltongonc)

## Licensing
MIT Elton Goncalves Gomes
