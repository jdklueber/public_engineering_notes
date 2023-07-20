## Setting up Firebase

### Setting up the project in Firebase

1. Set up a new project using the firebase console (new project, then add app/web)
2. Install the firebase library

```console
npm install firebase
```

3. Create `firebase.js` in the `src/` directory.
4. Copy and paste the firebase config given during app setup into `firebase.js`
5. Install libraries

```javascript
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import {getFirestore} from "firebase/firestore";
import {getAuth} from "firebase/auth";
import {getStorage} from "firebase/storage";

// Your web app's Firebase configuration
const firebaseConfig = {
//app specific details here, find in firebase console
};

// Initialize Firebase
initializeApp(firebaseConfig);

//Get handles for database and auth
const db = getFirestore();
const auth = getAuth();
const storage = getStorage();

export {db, auth, storage}; //import these when you need to use them
```
