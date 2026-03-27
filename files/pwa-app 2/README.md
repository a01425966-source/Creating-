# 🔥 Guía de Configuración — Marketplace PWA + Firebase

## Archivos del proyecto
```
pwa-app/
├── index.html        ← App principal (PWA + Firebase)
├── manifest.json     ← Configuración PWA (íconos, nombre, colores)
├── sw.js             ← Service Worker (funciona offline)
├── icons/
│   ├── icon-192.png  ← Ícono para Android / instalación
│   └── icon-512.png  ← Ícono grande
└── README.md         ← Este archivo
```

---

## Paso 1 — Crear proyecto en Firebase

1. Ve a **https://console.firebase.google.com**
2. Clic en **"Agregar proyecto"**
3. Ponle un nombre (ej: `marketplace-local`)
4. Activa Google Analytics si quieres (opcional)
5. Clic en **"Crear proyecto"**

---

## Paso 2 — Registrar tu app web

1. En el panel del proyecto, clic en el ícono **`</>`** (Web)
2. Ponle un nombre a tu app (ej: `Marketplace Web`)
3. Marca **"También configura Firebase Hosting"** si vas a usar Firebase Hosting
4. Clic en **"Registrar app"**
5. Copia el objeto `firebaseConfig` que aparece — se verá así:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "tu-proyecto.firebaseapp.com",
  projectId: "tu-proyecto",
  storageBucket: "tu-proyecto.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

---

## Paso 3 — Pegar config en index.html

Abre `index.html` y busca este bloque cerca de la línea 200:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  ...
};
```

**Reemplázalo** con tu `firebaseConfig` real del paso anterior.

---

## Paso 4 — Activar Firebase Authentication

1. En Firebase Console → menú lateral → **Authentication**
2. Clic en **"Comenzar"**
3. En la pestaña **"Sign-in method"**, activa:
   - ✅ **Correo electrónico/contraseña**
   - ✅ **Google** (para el botón "Continuar con Google")
4. En Google, agrega tu dominio en "Dominios autorizados"

---

## Paso 5 — Activar Firestore Database

1. Firebase Console → **Firestore Database**
2. Clic en **"Crear base de datos"**
3. Elige **"Comenzar en modo de prueba"** (para desarrollo)
4. Elige la región más cercana a tus usuarios (ej: `us-central1`)
5. Clic en **"Listo"**

### Reglas de seguridad sugeridas (Firestore):
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    match /orders/{orderId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

## Paso 6 — Desplegar la app (Firebase Hosting)

```bash
# Instalar Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Inicializar en la carpeta del proyecto
cd pwa-app/
firebase init hosting

# Cuando pregunte el directorio público, escribe: .
# Para SPA: No
# Para sobrescribir index.html: No

# Desplegar
firebase deploy
```

Tu app quedará en: `https://TU-PROYECTO.web.app`

---

## Paso 7 — Verificar que es PWA

1. Abre tu app en Chrome
2. F12 → pestaña **Lighthouse**
3. Marca **"Progressive Web App"** y clic en **"Analizar"**
4. En móvil Android: aparecerá el banner **"Agregar a pantalla de inicio"**
5. En iOS Safari: botón compartir → **"Agregar a inicio"**

---

## Lo que hace Firebase en la app

| Función | Firebase |
|---------|----------|
| Login con email/pass | Authentication |
| Login con Google | Authentication |
| Guardar favoritos | Firestore `/users/{uid}` |
| Historial de compras | Firestore `/orders/` |
| Contador de cursos/proyectos | Firestore `/users/{uid}` |
| Persistencia offline | Firestore cache |

---

## Estructura de datos en Firestore

```
users/
  {uid}/
    name: "María García"
    email: "maria@email.com"
    purchases: 3
    courses: 2
    projects: 1
    favorites: { "1": true, "3": true }
    createdAt: timestamp

orders/
  {docId}/
    userId: "uid..."
    product: "Macetas de Cerámica"
    price: "$180"
    createdAt: timestamp
```
