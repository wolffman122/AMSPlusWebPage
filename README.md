# AMS+ Filament Generator

[中文](./README_CN.md) | English

This project is a sample webpage built with **Vue** (front end) + **Node.js (Express)** (back end) that generates a specific **Filament configuration** (output as JSON). 

## Features

1. **Color Input**
   - A color picker at the top of the page, bound to `colorHex`. Default is `#FFFF00`.
   - When generating JSON, the `#` is removed, and stored as `color_hex`.

2. **Material Type**
   - A dropdown box lists various materials (e.g., `"PLA (GFL99)"`, `"PETG (GFG99)"`, `"TPU (GFU99)"`, etc.).  
   - Selecting a material updates `type` in the data, which is used in the final JSON payload.

3. **Temperature (Min / Max)**
   - Two numeric inputs for `min_temp` and `max_temp`, allowing the user to specify the recommended temperature range for the chosen material.

4. **Surface (A/B)**
   - Two mutually exclusive buttons, each with an image.  
   - On smaller or narrower screens, the images and buttons shrink or wrap to avoid overflow.

5. **JSON Output**
   - A `<textarea>` to display the generated JSON.  
   - The **Generate** button updates this text; the **Copy** button copies the text to the clipboard.


## Tech Stack

- **Vue 3** (or Vue 2, depending on your setup)  
- **Node.js (Express)**  
- **HTML/CSS** (using Flexbox / media queries for responsive design)


## Project Structure Example

```bash
my-filament-generator
├── client
│   ├── package.json
│   ├── vite.config.js (or vue.config.js)
│   ├── src
│   │   ├── App.vue
│   │   ├── main.js
│   │   └── components
│   │       └── FilamentGenerator.vue
│   └── public
├── server
│   └── server.js
└── ...
```

- `client`: the frontend (Vue) project directory.  
- `server`: a simple backend server (Express) for serving the built static files.

> You can adjust the structure as needed (e.g., put `server.js` in the root).

---

## How to Run (Development)

1. **Install Dependencies**  
   ```bash
   cd client
   npm install
   # or yarn
   ```
2. **Start the Dev Server**  
   ```bash
   npm run dev
   # or npm run serve, depending on your setup
   ```
3. **Local Access**  
   - By default, you can visit `http://localhost:3000` (Vite) or `http://localhost:8080` (Vue CLI).  
   - To allow other devices on the same LAN to access it, configure the dev server to bind to `0.0.0.0` instead of `localhost` (e.g., `devServer.host = '0.0.0.0'`).



## How to Build & Deploy

### 1. Build

In the `client` directory:

```bash
npm run build
```

This creates a static build in the `dist` folder (you can change this in your build config).

### 2. Host Using Express

In `server/server.js`, you might have something like:

```js
const express = require('express');
const path = require('path');
const app = express();
const port = 8080;

// Serve the dist folder as static
app.use(express.static(path.join(__dirname, '../client/dist')));

// For any other route, return the index.html
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '../client/dist', 'index.html'));
});

app.listen(port, '0.0.0.0', () => {
  console.log(`Server running at http://0.0.0.0:${port}`);
});
```

Then:

```bash
cd server
node server.js
```

### 3. LAN Access

- Find your internal IP (like `192.168.x.x`).  
- In a web browser on another device in the same LAN, open `http://192.168.x.x:8080`.

For HTTPS, or a more complex setup (NGINX / PM2, etc.), see their documentation.
