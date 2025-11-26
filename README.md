
mi-tienda/
│  index.html
│  vercel.json
│  package.json
│  vite.config.js
│
├─ src/
│   ├─ App.jsx
│   ├─ main.jsx
│   ├─ data/products.js
│   └─ styles.css
│
└─ api/
    └─ checkout.js
```

---
#  **1. package.json**
```json
{
  "name": "mi-tienda",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.0.0",
    "react-dom": "^18.0.0"
  },
  "devDependencies": {
    "vite": "^5.0.0"
  }
}
```

---
#  **2. vite.config.js**
```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
});
```

---
#  **3. vercel.json**
```json
{
  "version": 2,
  "builds": [
    { "src": "vite.config.js", "use": "@vercel/static-build" },
    { "src": "api/**/*.js", "use": "@vercel/node" }
  ],
  "routes": [
    { "src": "/api/(.*)", "dest": "/api/$1" },
    { "src": "(.*)", "dest": "/index.html" }
  ]
}
```

---
#  **4. index.html**
```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Mi Tienda</title>
    <link rel="stylesheet" href="/src/styles.css" />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

---
#  **5. src/main.jsx**
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import "./styles.css";

ReactDOM.createRoot(document.getElementById("root")).render(<App />);
```

---
#  **6. src/data/products.js**
```js
export const products = [
  { id: "p1", title: "Camiseta oficial", category: "Ropa", price: 45000, description: "Camiseta 100% algodón.", image: null },
  { id: "p2", title: "Taza personalizada", category: "Accesorios", price: 18000, description: "Taza de cerámica.", image: null },
  { id: "p3", title: "Sudadera premium", category: "Ropa", price: 75000, description: "Sudadera con capucha.", image: null }
];
```

---
#  **7. src/styles.css** (Tailwind básico simulado)
```css
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f3f3f3;
}
.card {
  background: white;
  padding: 16px;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.btn {
  padding: 8px 12px;
  background: #4f46e5;
  color: white;
  border: none;
  border-radius: 6px;
}
```

---
#  **8. src/App.jsx** (versión compacta del sitio)
```jsx
import React, { useState } from "react";
import { products } from "./data/products";

export default function App() {
  const PHONE = "+573001112233";
  const LAT = 4.710988;
  const LNG = -74.07209;

  const [cart, setCart] = useState([]);

  function add(p) {
    const exists = cart.find((i) => i.id === p.id);
    if (exists) setCart(cart.map(i => i.id === p.id ? { ...i, qty: i.qty + 1 } : i));
    else setCart([...cart, { ...p, qty: 1 }]);
  }

  function checkout(method) {
    fetch("/api/checkout", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ cart, method })
    })
      .then(r => r.json())
      .then(data => alert(data.message));
  }

  return (
    <div style={{ padding: 20 }}>
      <h1>Mi Tienda</h1>
      <h2>Productos</h2>

      <div style={{ display: "grid", gap: 20, gridTemplateColumns: "repeat(auto-fill, minmax(200px, 1fr))" }}>
        {products.map(p => (
          <div key={p.id} className="card">
            <h3>{p.title}</h3>
            <p>{p.description}</p>
            <b>${p.price.toLocaleString()}</b>
            <br /><br />
            <button className="btn" onClick={() => add(p)}>Agregar</button>
          </div>
        ))}
      </div>

      <hr style={{ margin: "40px 0" }} />

      <h2>Carrito</h2>
      {cart.length === 0 ? "Vacío" : cart.map(i => <div key={i.id}>{i.title} x {i.qty}</div>)}

      <br />
      <button className="btn" onClick={() => checkout("sistecredito")}>Pagar con Sistecrédito</button>
      <br /><br />
      <button className="btn" onClick={() => checkout("addi")}>Pagar con Addi</button>

      <br /><br />
      <a className="btn" href={`https://wa.me/${PHONE.replace('+','')}`} target="_blank">WhatsApp</a>

      <h2>Ubicación</h2>
      <iframe
        width="100%"
        height="300"
        src={`https://www.google.com/maps?q=${LAT},${LNG}&z=15&output=embed`}
      />
    </div>
  );
}
```

---
#  **9. api/checkout.js** (Backend en Vercel)
```js
export default async function handler(req, res) {
  if (req.method !== "POST") return res.status(405).json({ message: "Método no permitido" });

  const { method, cart } = req.body;

  // Aquí va la lógica real para Sistecrédito / Addi
  // Por ahora es una simulación

  return res.status(200).json({
    success: true,
    message: `Checkout iniciado con ${method}. Aún falta conectar API real.`,
  });
}
```

---
