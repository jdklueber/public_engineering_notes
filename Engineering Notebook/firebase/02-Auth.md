# Firebase - Authentication

## Setup

From the Firebase console...

1. Click Build -> Authentication
2. Click "Get Started"
3. Select the providers you want and enable them

## Sign Up with Email and Password

Inside of `firebase.js`:

```javascript
import {getAuth} from "firebase/auth";
//...
//Config and other imports and such
//...
// Initialize Firebase
 initializeApp(firebaseConfig); //Already there from above setup
 export const auth = getAuth();
```

Inside of the code which needs to perform the signup:

```javascript
import {auth, db} from "../firebase"; //path may vary depending on your directory structure
import {createUserWithEmailAndPassword, updateProfile} from "firebase/auth";

//The actual signup code:
const signUp = async () => {
        try {
            await createUserWithEmailAndPassword(auth, email, password);
            updateProfile(auth.currentUser, {displayName: full_name}); //Optional
        } catch (error) {
            console.log(error); //Better ways to do this but good enough for now
        }
    }
```

## Sign Up with OAuth (Google etc)

Documentation:  https://firebase.google.com/docs/auth/web/google-signin

For this, you'll be letting Google take the wheel and deliver their standard "choose which account to use" pop up.  Right now, this sample code is in a separate file from the email/password version above, but refactoring to keep the firestore user profile collection consistent shouldn't be terribly hard.

This button will also be used for signing in, since the google auth provider doesn't care whether or not the user has a profile in our application server- it just verifies that they have a google account they can sign in to (or create) and builds an auth object based on that account.  Once the auth system has verified the user they're signed in.

```jsx
import Button from "./ui/Button";
import {
    getAuth,
    signInWithPopup,
    GoogleAuthProvider,
    updateProfile
} from "firebase/auth";
import {db} from "../firebase";
import {doc, serverTimestamp, setDoc, getDoc} from "firebase/firestore";
import {toast} from "react-toastify";
import {useNavigate} from "react-router-dom";

function OAuth() {
    const navigate = useNavigate();

    const signUp = async () => {
        try {
            //Set up our firebase interface
            const provider = new GoogleAuthProvider();
            const auth = getAuth();
            
            //Perform the sign up/sign in step
            const result = await signInWithPopup(auth, provider);
            const user = result.user;
            
           //Build the user profile record (optional)
            const docRef = doc(db, "users", user.uid)
            const docSnap = await getDoc(docRef);
            
            if (!docSnap.exists()) { //We don't have a user profile for this person
                //Build the user profile data
                const dbProfileData = {
                    email: user.email,
                    displayName: auth.currentUser.displayName
                };
                dbProfileData.timestamp = serverTimestamp();
                await setDoc(docRef, dbProfileData);
                
                //Let them know the account was created successfully
                toast.success("Account created!");
            }
			//Whether or not we created a profile, they're signed in
            //Navigate back to the home page
            navigate("/");
        } catch (error) {
            //Report the error
            //TODO:  Maybe look up the error codes and/or put in a 
            //more generic message.
            toast.error(error.message);
        }
    }
    
    //The UI
    return (
        <div>
            <Button text={"Register with google"} color={"red"} text_color={"white"}
                    onClick={signUp}
            />
        </div>
    );
}

export default OAuth;
```

## Sign In

At this point, sign in is practically done for us.  For signing in with email/password, the process looks like this:

```jsx
// Call this via an onClick event (or whatever makes sense for your application)
const signIn = async () => {
        try {
            // Get a reference to the auth component
            const auth = getAuth();
            
            //Call the signin functionality
            const credentials = await signInWithEmailAndPassword(auth, formData.email, formData.password);
            if (credentials.user) {
                //Signin was successful, do whatever navigation and 
                //notification makes sense here
                navigate("/");
                toast.success("User: " + credentials.user.email);
            }
        } catch (error){
            //Signin failed, notify the user
            toast.error(error.message)
        }
    }
```

For OAuth, the OAuth component above can already handle login, so you can reuse it for that.

## Sign Out

Signing out a logged in user is very simple.  Here is the code:

```javascript
import {getAuth, signOut} from "firebase/auth";

//inside your component
const auth = getAuth();
const signOutHandler = () => signOut(auth) //call this as appropriate
```

## Forgot Password

Firebase handles the password reset logic as well.  All you need to do is provide a way to obtain the user's email address and pass it along.  Here's an example:

```jsx
import {getAuth, sendPasswordResetEmail} from "firebase/auth"; //required
import {toast} from "react-toastify"; //optional - for notifications
import {useNavigate} from "react-router-dom"; //optional - for sending
                                              //the user back to the
                                              //sign in page

//This method is called by the onClick event of a button
const submit =async () => {
        try {
            //Grab an auth handle from firebase
            const auth = getAuth();
            
            //email is tracked in state to be passed along here
            await sendPasswordResetEmail(auth, email); 
            
            //react-toastify notifies the user that 
            //their request has been processed
            toast.success("Reset email sent");
            
            //react-router-dom sends them back to the sign in page
            navigate("/sign-in")
        } catch (error) {
            //Generic react-toastify error notification
            toast.error(error.message);
        }
    }
```

## General notes on user authentication

Documentation:  https://firebase.google.com/docs/auth/web/start#set_an_authentication_state_observer_and_get_user_data

User object docs: https://firebase.google.com/docs/reference/js/firebase.User

If you want to use the logged in state / user information across your application, it's probably a good idea to compartmentalize all of the auth logic into a single React Context that can track the current logged in user as state and let your components access that Context so that they update when the auth state changes.  In order to subscribe to that state change, use `auth.onAuthStateChanged()` like this:

```javascript
import { getAuth, onAuthStateChanged } from "firebase/auth";

useEffect(() => {
	const auth = getAuth();
    onAuthStateChanged(auth, (user) => {
      if (user) {
        // User is signed in
        const uid = user.uid;
        // ...
      } else {
        // User is signed out
        // ...
      }
    }); 
}, []);

```

You must call `auth.onAuthStateChanged()` inside of a `useEffect` hook (or some other way to make sure it only gets called once), otherwise it'll subscribe every time the component is rendered and throw you into a loop of doom.

