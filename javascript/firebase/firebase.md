
# Firebase Notes

## Setup
* Add web app
* Add firebase to package.json
* Create src/data/firebase.js
    * Copy in the setup code from Firebase
    * Add exports to the bottom of the file for app and analytics
        * *I changed “app” to “firebase” for ease of memory*
* Add:  import {getFirestore} from “firebase/firestore”;
* Add:  const firestore = getFirestore(firebase);
* Add:  export for firestore
* npm install -g firebase-tools
* firebase init – note that react default build is into build/, not public
* Powershell may require certain permissions to run the firebase script.  
    * Set-ExecutionPolicy -ExecutionPolicy Bypass from an admin powershell
    Github Integration
* Set up github repo for project, push code
* firebase init hosting:github
    * run a build script
    * *use default script (npm ci && npm run build)
* Note that linting warnings and errors can cause these builds to die a terrible death

## Basic Usage
See: https://firebase.google.com/docs/firestore/quickstart?authuser=0#web-version-9
But:  import firestore from firestore.js, import collection, addDoc, whatever else from firebase/firestore

## Encapsulation Strategy
For each collection, make a .js file which imports the appropriate bits from firestore and exports CRUD functionality to the broader application.

## Authentication
This is going to be the fun part.
* Basically, need a sign in form, a log in form, a log out form.
* At app level—possibly context?? Import onAuthStateChanged from firebase/auth and monitor the auth token, setting a state variable for the signed in user to pass around.
* Sign In and Sign Out: https://firebase.google.com/docs/auth/web/password-auth
* Sign Up:  https://firebase.google.com/docs/auth/web/start
Cloud Firestore
* Probably the correct database to use
* Modeling Hints:  https://db-engines.com/en/blog_post/51
