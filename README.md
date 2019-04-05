# CloudServiceManagement
Javascript for connecting to Google Drive & Dropbox through OAuth2.0 authentication using javascript sdks & XMLHttps Requests.
Uses::
```
<script type="text/javascript" src="https://www.dropbox.com/static/api/2/dropins.js" id="dropboxjs" data-app-key="*****"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/dropbox.js/4.0.16/Dropbox-sdk.js"></script>
<script type="text/javascript" src="https://unpkg.com/pcloud-sdk-js@latest/dist/pcloudsdk.js"></script>
<script async defer src="https://apis.google.com/js/api.js"
        onload="this.onload=function(){};handleClientLoad()"
        onreadystatechange="if (this.readyState === 'complete') this.onload()">
</script>
```
### Layout
![alt text](https://github.com/JaraKucera/CloudServiceManagement/raw/master/Page.png "Page Layout")
>Css Credit to (https://github.com/Soph1ia)


### Authorization

#### Google

```javascript
function handleClientLoad() {
          // Load the API client and auth2 library
          gapi.load('client:auth2', initClient);
}

var discoveryUrl = ['https://www.googleapis.com/discovery/v1/apis/drive/v3/rest'];
function initClient() {
          gapi.client.init({
              apiKey: '***',
              discoveryDocs: discoveryUrl,
              clientId: '*****',
              scope: SCOPE
          }).then(function () {
            // Listen for sign-in state changes.
            gapi.auth2.getAuthInstance().isSignedIn.listen(updateSigninStatus);
            // Handle the initial sign-in state.
            updateSigninStatus(gapi.auth2.getAuthInstance().isSignedIn.get());
            authorizeButton.onclick = handleAuthClick;
            signoutButton.onclick = handleSignoutClick;
          });
}

function updateSigninStatus(isSignedIn) {
          if (isSignedIn) {
                    //authorizeButton.style.display = 'block';
                    //signoutButton.style.display = 'none';
            if(currentClient == clientEnum.GOOGLEDRIVE){
                    //authorizeButton.style.display = 'none';
                    //signoutButton.style.display = 'block';
                    makeApiCall();
            }
            
          } else {
            //authorizeButton.style.display = 'block';
            //signoutButton.style.display = 'none';
          }
}

function handleAuthClick(event) {
          currentClient = clientEnum.GOOGLEDRIVE;
          gapi.auth2.getAuthInstance().signIn();
          setTimeout(function(){ 
                    makeApiCall();
                    console.log("Got to wait"); },9000);
          
          
 }
function handleSignoutClick(event) {
          gapi.auth2.getAuthInstance().signOut();
          currentClient = null;
          var location = $('#files');
          location.empty();
 }
```
#### DropBox

```javascript
 if(isAuthenticated()){
          dbx = new Dropbox.Dropbox({ accessToken: getAccessTokenFromUrl() });
          currentClient = clientEnum.DROPBOX;
          dbx.filesListFolder({path: ''}).then(function(response){
                    renderItems(response.entries);
          }).catch(function(error){
                    console.error(error);
          });
          }else{
                    dbx = new Dropbox.Dropbox({ clientId: CLIENT_ID });
                    var authUrl = dbx.getAuthenticationUrl('https://cloudjs-projs.firebaseapp.com/users/interfacePage');
                    document.getElementById('authlink').href = authUrl;
}

//Function to call parser for access token
 function getAccessTokenFromUrl(){
          return utils.parseQueryString(window.location.hash).access_token;
}

 //Function to check Auth
 function isAuthenticated(){
          return !!getAccessTokenFromUrl();
}
```
