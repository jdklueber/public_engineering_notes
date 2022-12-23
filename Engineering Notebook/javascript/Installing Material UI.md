# Installing Material UI

1) Install materialUI

```
npm install @mui/material @emotion/react @emotion/styled
```



2. Install Roboto font

```
npm install @fontsource/roboto
```

3. Changes to index.js

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import "@fontsource/roboto/300.css";
import "@fontsource/roboto/400.css";
import "@fontsource/roboto/500.css";
import "@fontsource/roboto/700.css";
import CssBaseline from '@mui/material/CssBaseline';
import { StyledEngineProvider } from '@mui/material';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
<StyledEngineProvider>
  <CssBaseline />
  <App />
</StyledEngineProvider>
);
```

## Helpful Notes

MaterialUI Components Library: https://mui.com/material-ui/





