# React Toastify

Documentation: https://fkhadra.github.io/react-toastify/introduction/

1. Install

```console
npm install react-toastify
```

2. Configure: https://fkhadra.github.io/react-toastify/introduction/#the-playground

3. Import the css into `index.js`

   ```jsx
   import 'react-toastify/dist/ReactToastify.min.css';
   ```

4. Add to the page(s) that need to display toasts (at the bottom of the `App.js` component is a good call if you're using `react-router` and want toasts potentially everywhere.)

   ```jsx
   import {ToastContainer} from "react-toastify";
   	<ToastContainer //copied and pasted from the configure page
               position="bottom-center"
               autoClose={5000}
               hideProgressBar={false}
               newestOnTop={false}
               closeOnClick
               rtl={false}
               pauseOnFocusLoss
               draggable
               pauseOnHover
               theme="dark"
           />
   ```

5. Make toasts

```jsx
toast.success("Account created!"); //Green check
toast.error(error.message);        //Red !
```

