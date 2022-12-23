# Firebase Firestore

## Set Up

From the Firebase console...

1. Click Build->Firestore Database  **NOT** Realtime Database, that's old.
2. Click "Create Database"
3. Select "Start In Production Mode"  
4. Select appropriate region for your primary userbase
5. Click Rules and apply security as per the Security Rules section

## Security Rules

This is a reasonable template for making new Firestore security rules:

```console
// Firestore rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Listings
    match /listings/{listing} {
    	allow read;
      allow create: if request.auth != null && request.resource.data.imgUrls.size() < 7;
    	allow delete: if resource.data.userRef == request.auth.uid;
      allow update: if resource.data.userRef == request.auth.uid;
    }
   
    // Users
    match /users/{user} {
    	allow read;
    	allow create;
    	allow update: if request.auth.uid == user
    }
  }
}
```

## Implementing Firestore In Your Web App

## Creating a Document/Record

Documentation: https://firebase.google.com/docs/firestore/manage-data/add-data

Earlier we looked at a signup process that involved a custom profile record inside of the app.  In that example, we first tried to look up the profile to see if it existed, and then stored it if it did not.  Here, we're going to break this down to the simplest form.

In order to create a document, we need simply

```javascript
import {getFirestore, doc, setDoc} from "firebase/firestore";
const db = getFirestore();
await setDoc(doc(db, "collectionName", key), {values: "in document"});
```

Note that this will also overwrite a document that already exists.  This might be what you want, but it's important to keep in mind that there might be a value to checking for document existence first.  If you want to do that:

```javascript
const docRef = doc(db, "collectionName", key);
const docSnap = await getDoc(docRef);

if (docSnap.exists()) {
	//Record already exists, make your decisions here
}
```

Sometimes you don't have a key for the new document.  In this case, use the `addDoc` method:

```javascript
import { collection, addDoc } from "firebase/firestore"; 

const docRef = await addDoc(collection(db, "collectionName"), {
	contents: "Of the document"
});

console.log("Document written with ID: ", docRef.id);
```



## Updates

Documentation: https://firebase.google.com/docs/firestore/manage-data/add-data

There are a couple of ways to handle updating documents in Firebase.  The first simply follows the create logic under **Creating a Document**.  The second will merge new information in with existing.  This can be done in two steps.

1. Identify the document that needs to be updated
2. Send the new state of that document

```javascript
import {getFirestore} from "firebase/firestore";
const db = getFirestore();

//Identifty the document to update
const docRef = doc(db, "collectionName", key);

//Update it
setDoc(docRef, { newState: "For old value", newValue: "that didn't exist before" }, { merge: true }); //merge: true is the magic here
                             //It tells Firebase to merge the new data in
                             //with the old so that you don't have to
                             //specify the whole document
```

There are four ways to manipulate data in your collections:

1. **setDoc - merge: false**: Create if doesn't exist, overwrite if does
2. **setDoc - merge: true:** Create if doesn't exist, update if does
3. **create**:  Create if doesn't exist, fail if does
4. **update**: Fail if doesn't exist, update if does

Their syntaxes are all very similar, review the documentation (linked above) if needed.

## Queries - Single Document

### Retrieve Once

Documentation: https://firebase.google.com/docs/firestore/query-data/get-data

```javascript
import { doc, getDoc } from "firebase/firestore";

const docRef = doc(db, "collectionName", key);
const docSnap = await getDoc(docRef);

if (docSnap.exists()) {
  console.log("Document data:", docSnap.data());
} else {
  // doc.data() will be undefined in this case
  console.log("No such document!");
}
```

This does a single retrieve.

### Subscribe To Changes

Documentation:  https://firebase.google.com/docs/firestore/query-data/listen

```javascript
import { doc, onSnapshot } from "firebase/firestore";

const unsub = onSnapshot(doc(db, "collectionName", key), (doc) => {
    console.log("Current data: ", doc.data());
});
```

This allows you to provide a callback function to react to changes to the given document.

## Query - Multiple Documents and Indexes

Up to now, we've been looking at single documents.  Firebase makes it easy to do key/value searches.  Let's look at queries that will return multiple documents.

### `where()`

Documentation: https://firebase.google.com/docs/firestore/query-data/get-data#get_multiple_documents_from_a_collection

The process here is three steps:

1. Define the query
2. Call `getDocs` to perform the retrieval
3. Loop through the snapshot returned by `getDocs` and process each document

```javascript
import { collection, query, where, getDocs } from "firebase/firestore";

const q = query(collection(db, "cities"), where("capital", "==", true));

const querySnapshot = await getDocs(q);
querySnapshot.forEach((doc) => {
  // doc.data() is never undefined for query doc snapshots
  console.log(doc.id, " => ", doc.data());
});
```

### Retrieve all documents in a collection

Documentation: https://firebase.google.com/docs/firestore/query-data/get-data#get_all_documents_in_a_collection

The process here is similar to above, only without the query creation step.  We simply ask `getDocs` to retrieve the collection and process the resulting snapshot.

```javascript
import { collection, getDocs } from "firebase/firestore";

const querySnapshot = await getDocs(collection(db, "cities"));
querySnapshot.forEach((doc) => {
  // doc.data() is never undefined for query doc snapshots
  console.log(doc.id, " => ", doc.data());
});
```

### Query Operators

Documentation: https://firebase.google.com/docs/firestore/query-data/queries#query_operators

Here are the operators that can be used in a query:

- `<` less than
- `<=` less than or equal to
- `==` equal to
- `>` greater than
- `>=` greater than or equal to
- [`!=` not equal to](https://firebase.google.com/docs/firestore/query-data/queries#not_equal)
- [`array-contains`](https://firebase.google.com/docs/firestore/query-data/queries#array_membership)
- [`array-contains-any`](https://firebase.google.com/docs/firestore/query-data/queries#in_and_array-contains-any)
- [`in`](https://firebase.google.com/docs/firestore/query-data/queries#in_and_array-contains-any)
- [`not-in`](https://firebase.google.com/docs/firestore/query-data/queries#in_and_array-contains-any)

### Compound Queries

Documentation: https://firebase.google.com/docs/firestore/query-data/queries#compound_queries

You can chain where clauses in a query by separating them with a comma in the `query` call.  This implies that they are handled with a vararg in the library, but that's just conjecture.  Here are some examples:

```javascript
import { query, where } from "firebase/firestore";  

//Multiple == comparisons can be unlimited
const q1 = query(citiesRef, where("state", "==", "CO"), where("name", "==", "Denver"));

//Only one field can be tested with inequality operators (<=, >=, <, >, !=)
//This is valid
const q2 = query(citiesRef, where("state", "==", "CA"), where("population", "<", 1000000));

//This is not
const q = query(citiesRef, where("state", ">=", "CA"), where("population", ">", 100000));
```

Per the documentation, here are the rules:

> You can perform range (`<`, `<=`, `>`, `>=`) or not equals (`!=`) comparisons only on a single field, and you can include at most one `array-contains` or `array-contains-any` clause in a compound query.

When setting up a compound query, it will fail the first time out because compound queries need an index.  It is easiest to do, however, if you run the query and let it fail first because then Firebase will capture that attempt and give you a quick way to build the necessary index.  Here's how:

1. Go to the **Cloud Firestore** section of the [Firebase console](https://console.firebase.google.com/project/_/firestore/data).
2. Go to the **Indexes** tab and click **Add Index**.
3. Enter the collection name and set the fields you want to order the index by.
4. Click **Create**.

For more details, see the documentation on indexes:  https://firebase.google.com/docs/firestore/query-data/indexing#use_the_firebase_console

### `orderBy` and `limit`

To enforce an ordering on your results, use the `orderBy` function.

Example:

```javascript
import { query, orderBy, limit } from "firebase/firestore";  

const q = query(citiesRef, orderBy("name"), limit(3));
```

This will order cities by the "name" field and limit you to three results.  Ascending order is assumed by default.  To mark as descending, add "desc" to the `orderBy` call:

```javascript
import { query, orderBy, limit } from "firebase/firestore";  

const q = query(citiesRef, orderBy("name", "desc"), limit(3));
```

This will return the last three cities alphabetized by name.

Calls to `orderBy` can be chained:

```javascript
import { query, orderBy } from "firebase/firestore";  

const q = query(citiesRef, orderBy("state"), orderBy("population", "desc"));
```

And, of course, you can combine these with `where` statements:

```javascript
import { query, where, orderBy, limit } from "firebase/firestore";  

const q = query(citiesRef, where("population", ">", 100000), orderBy("population"), limit(2));
```

There are some limitations on how `orderBy` can be used.  

* When you query with `orderBy` you will only get documents that contain the requested field.  If your data schema has changed over time, you might not get all the documents you're expecting due to this.
* If you include a range comparison in your `where` statements (>, >=, <, <=), your first ordering **must** be on the same field.
* You cannot order your query by any field included in an equality (`=`) or `in` clause. (Why would you?)

For the full discussion, see the documentation: https://firebase.google.com/docs/firestore/query-data/order-limit-data#limitations

###  Server Side Document Count

Documentation: https://firebase.google.com/docs/firestore/query-data/aggregation-queries

```javascript
import { query, where, orderBy, limit, getCountFromServer } from "firebase/firestore";  
const coll = collection(db, "cities");
const snapshot = await getCountFromServer(coll);
console.log('count: ', snapshot.data().count);
```

This will give you a count of documents without actually downloading them.

### Pagination Queries

Documentation: https://firebase.google.com/docs/firestore/query-data/query-cursors

WIP

### Deleting Data

Documentation: https://firebase.google.com/docs/firestore/manage-data/delete-data

WIP

### Transactions

Documentation: https://firebase.google.com/docs/firestore/manage-data/transactions

WIP