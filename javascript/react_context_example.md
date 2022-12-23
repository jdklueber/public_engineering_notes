```javascript
import React, {useEffect, useState} from "react";
import {auth} from "../firebase";
import {signInWithEmailAndPassword,onAuthStateChanged,signOut} from "firebase/auth";


const AuthContext = React.createContext()

function AuthProvider({children}) {
    const [user, setUser] = useState()

    useEffect(() => {
        onAuthStateChanged(auth, (user) => {
            if (user) {
                // User is signed in, see docs for a list of available properties
                // https://firebase.google.com/docs/reference/js/firebase.User
                setUser(user)
            } else {
                setUser(null)
            }
        });
    },[])

    const login = (email, password) => {
        signInWithEmailAndPassword(auth, email, password)
    }

    const logout = () => {
        signOut(auth)
    }

    const authObj = {
        user,
        login,
        logout
    }

    return (
        <AuthContext.Provider value={authObj}>
            {children}
        </AuthContext.Provider>
    )
}

export default AuthProvider
export {AuthContext}
```