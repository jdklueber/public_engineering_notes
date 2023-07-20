# Firebase Storage 

## Setup

From the FIrebase Console...

1. Click Build->Storage
2. Click "Get Started"
3. Start in Production Mode
4. Click Rules and apply security rules as per the Security Rules section

## Security Rules

This is a reasonable default for storage security rules

```console
// Storage rules
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read;
      allow write: if
      request.auth != null &&
      request.resource.size < 2 * 1024 * 1024 && //2MB
      request.resource.contentType.matches('image/.*')
    }
  }
}
```

## Implementing Firebase Storage in your web app

Documentation: https://firebase.google.com/docs/storage/web/start

### File References

File references are a core concept in Firebase Storage.  At its most basic level, Firebase Storage is exposing a remote hierarchal file system.  You can think of it like you think of your own hard drive.  Here's how you create a reference to a file:

*From the official documentation*:

```javascript
import { getStorage, ref } from "firebase/storage";

// Create a root reference
const storage = getStorage();

// Create a reference to 'mountains.jpg'
const mountainsRef = ref(storage, 'mountains.jpg');

// Create a reference to 'images/mountains.jpg'
const mountainImagesRef = ref(storage, 'images/mountains.jpg');

// While the file names are the same, the references point to different files
mountainsRef.name === mountainImagesRef.name;           // true
mountainsRef.fullPath === mountainImagesRef.fullPath;   // false 
```

Note that this file does not necessarily exist- it's just a reference to a file location. 

### Uploading a File

*Again, from the official documentation:*

```javascript
import { getStorage, ref, uploadBytes } from "firebase/storage";

const storage = getStorage();
const storageRef = ref(storage, 'some-child');

// 'file' comes from the Blob or File API
uploadBytes(storageRef, file).then((snapshot) => {
  console.log('Uploaded a blob or file!');
});
```

But how do we get a reference to the file, I hear you cry?

Doing this in a React-ish way, but this is doubtless transferable to other frameworks or just plain Javascript.

Here is a file chooser:

```jsx
import {useState} from "react";

function FileChooser({label, fileHandlerCallback}) {
    const [value, setValue] = useState();
    const changeHandler = (evt) => {
        if (evt.currentTarget.files.length > 0) { //We have selected a file
            fileHandlerCallback(evt.currentTarget.files[0]) //Tell the parent about the file
        } else {
            fileHandlerCallback(null); //We have unselected a file, tell the parent to clear the file data
        }
        setValue(evt.currentTarget.value) //Keep the file input managed appropriately
    }

    return (
        <>  <div>{label}:</div>
            <input type={"file"} value={value} onChange={changeHandler}
                   className={"file:border-green-900 file:p-2 file:rounded-2xl file:bg-slate-100" +
                       " file:drop-shadow-2xl file:mr-5 mb-10"}
            />
        </>
    );
}

export default FileChooser;
```

Here is the component calling the file chooser and uploading the picture:

```jsx
import H2 from "../components/ui/H2";
import Frame from "../components/ui/Frame";
import Button from "../components/ui/Button";
import constants from "../constants";
import FileChooser from "../components/ui/FileChooser";
import {useState} from "react";
import {storage} from "../firebase/firebase";
import {ref, uploadBytes} from "firebase/storage"
import {toast} from "react-toastify";

function UploadPicture() {
    const [file, setFile] = useState();

    const changeHandler = (fileData) => {
        setFile(fileData);
    }

    const handleUpload = () => {
        if (file != null) {
            const storageRef = ref(storage, 'images/testfile.png');
            uploadBytes(storageRef, file).then((snapshot) => {
                toast.success("File uploaded!!")
            });
        }
    }

    return (
        <Frame>
            <H2>Upload A Picture</H2>
            <FileChooser label={"Select File"} fileHandlerCallback={changeHandler}/>
            <Button label={"Save"} style={constants.buttons.GREEN} onClick={handleUpload}/>
        </Frame>
    );
}

export default UploadPicture;
```

If you want to store a lot of metadata or otherwise associate a file with application data stored in Firebase, consider creating a collection for the picture and storing its file path there.
