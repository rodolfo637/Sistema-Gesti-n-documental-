# 📋 Sistema de Trámites Docentes — Gestión Interzonas

> Sistema ministerial para carga, seguimiento y control de trámites docentes entre zonas educativas de la Provincia de Buenos Aires.

---

## 🎯 Descripción

Sistema web **single-page** (un solo archivo HTML) listo para deploy en **GitHub Pages** y **Firebase Hosting**. Permite gestionar trámites documentales docentes entre zonas educativas, con autenticación Google, roles, historial automático, búsqueda en tiempo real y exportación PDF.

---

## 🏗️ Arquitectura

```
sistema-tramites/
├── index.html              ← App completa (HTML + CSS + JS)
├── firebase.json           ← Configuración Firebase Hosting
├── firestore.rules         ← Reglas de seguridad Firestore
├── firestore.indexes.json  ← Índices compuestos Firestore
└── README.md               ← Esta documentación
```

---

## ✨ Funcionalidades

| Funcionalidad | Descripción |
|---|---|
| 🔐 **Autenticación Google** | Login/registro con cuenta Google + roles |
| 👥 **3 Roles** | Secretario (zona), Inspector (global), Admin (global) |
| 📝 **Nueva Carga** | Formulario completo con validación y selects dinámicos |
| 📤 **Enviados** | Tabla con filtros por estado, paginación, acciones |
| 📥 **Recibidos** | Tabla con acción "marcar recibido" |
| 🔍 **Buscador Global** | Tiempo real, debounce 500ms, busca por DNI/nombre/descripción |
| 📄 **Detalle** | Vista completa + historial descendente |
| ✏️ **Editar** | Formulario pre-cargado con datos actuales |
| 🗑️ **Soft Delete** | Eliminar sin borrar, restaurar cuando sea necesario |
| 📋 **Historial** | Registro automático de creación, edición, eliminación, restauración |
| 📑 **Exportar PDF** | Genera PDF con toda la info + historial |
| 📁 **Repositorio Drive** | Acceso a carpetas compartidas por zona |
| 📱 **Responsive** | Funciona en desktop, tablet y celular |

---

## 🚀 Guía de Instalación Paso a Paso

### Paso 1: Crear Proyecto en Firebase

1. Ir a [console.firebase.google.com](https://console.firebase.google.com)
2. Click en **"Agregar proyecto"**
3. Nombre: `tramites-docentes` (o el que prefieras)
4. Desactivar Google Analytics (opcional)
5. Click en **"Crear proyecto"**

### Paso 2: Activar Authentication

1. En la consola de Firebase, ir a **Build → Authentication**
2. Click en **"Comenzar"**
3. En la pestaña **"Sign-in method"**, habilitar **Google**
4. Configurar email de soporte
5. **Guardar**

### Paso 3: Crear Base de Datos Firestore

1. Ir a **Build → Firestore Database**
2. Click en **"Crear base de datos"**
3. Elegir ubicación: `southamerica-east1` (São Paulo, más cercano a Argentina)
4. Iniciar en **modo producción**
5. Click en **"Crear"**

### Paso 4: Copiar Configuración de Firebase

1. En la consola, ir a **⚙️ Configuración del proyecto**
2. En la sección **"Tus apps"**, click en el ícono `</>`  (Web)
3. Registrar app con nombre: `tramites-web`
4. **NO marcar** Firebase Hosting por ahora
5. Copiar el objeto `firebaseConfig`

### Paso 5: Configurar el Código

Abrir `index.html` y buscar esta sección (aproximadamente línea 850):

```javascript
const firebaseConfig = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_PROYECTO.firebaseapp.com",
  projectId: "TU_PROJECT_ID",
  storageBucket: "TU_PROYECTO.appspot.com",
  messagingSenderId: "TU_SENDER_ID",
  appId: "TU_APP_ID"
};
```

**Reemplazar** con los datos copiados del paso anterior.

### Paso 6: Configurar Carpetas de Google Drive

1. Crear una carpeta en Google Drive por cada zona (ej: "Zona 1131", "Zona 1132", etc.)
2. Configurar cada carpeta como **"Cualquier persona con el enlace puede ver"**
3. Copiar el **ID de cada carpeta** (está en la URL: `drive.google.com/drive/folders/ID_ACA`)
4. En `index.html`, buscar `DRIVE_FOLDERS` y reemplazar los IDs:

```javascript
const DRIVE_FOLDERS = {
  "Zona 1131": "1abc123...",  // ← ID real de la carpeta
  "Zona 1132": "1def456...",
  // etc.
};
```

### Paso 7: Subir Reglas de Seguridad

**Opción A — Desde la Consola Web:**
1. Ir a **Firestore → Reglas**
2. Copiar y pegar el contenido de `firestore.rules`
3. Click en **"Publicar"**

**Opción B — Con Firebase CLI:**
```bash
npm install -g firebase-tools
firebase login
firebase init  # Seleccionar Firestore
firebase deploy --only firestore:rules
```

### Paso 8: Crear Índices Compuestos

**Opción A — Automática:**
Los índices se crean automáticamente cuando Firebase detecta una consulta que los necesita. La primera vez que uses la app, podés ver errores en la consola del navegador con links para crear cada índice.

**Opción B — Con Firebase CLI:**
```bash
firebase deploy --only firestore:indexes
```

### Paso 9: Crear el Primer Usuario Admin

**IMPORTANTE:** El primer usuario debe ser admin para poder aprobar a los demás.

1. Abrir la app
2. Click en **"Registrate acá"**
3. Seleccionar tu zona y rol **"Administrador/a"**
4. Registrarse con Google
5. Los admins se auto-aprueban, así que ya podés usar el sistema

Para aprobar otros usuarios, ir a la consola de Firestore:
1. Ir a **Firestore → datos**
2. Buscar la colección **`usuarios`**
3. Encontrar el documento del usuario pendiente
4. Cambiar el campo `aprobado` de `false` a `true`

---

## 🌐 Deploy

### Opción A: GitHub Pages

1. Crear repositorio en GitHub
2. Subir todos los archivos
3. Ir a **Settings → Pages**
4. Source: **Deploy from a branch**
5. Branch: **main**, carpeta: **/ (root)**
6. Guardar
7. Esperar ~2 minutos, tu app estará en `https://tu-usuario.github.io/tu-repo/`

**⚠️ IMPORTANTE:** Agregar el dominio de GitHub Pages a los dominios autorizados en Firebase:
1. Ir a **Authentication → Settings → Authorized domains**
2. Agregar: `tu-usuario.github.io`

### Opción B: Firebase Hosting

```bash
npm install -g firebase-tools
firebase login
firebase init hosting  # Seleccionar directorio actual como public
firebase deploy --only hosting
```

Tu app estará en `https://tu-proyecto.web.app`

---

## 👥 Roles y Permisos

| Permiso | Secretario | Inspector | Admin |
|---|:---:|:---:|:---:|
| Ver trámites de su zona | ✅ | ✅ | ✅ |
| Ver trámites de todas las zonas | ❌ | ✅ | ✅ |
| Crear trámites (solo su zona) | ✅ | ✅ | ✅ |
| Editar trámites de su zona | ✅ | ✅ | ✅ |
| Editar trámites de cualquier zona | ❌ | ✅ | ✅ |
| Eliminar (soft delete) | ✅* | ✅ | ✅ |
| Restaurar eliminados | ❌ | ✅ | ✅ |
| Aprobar usuarios | ❌ | ❌ | ✅ |

*Secretario solo elimina trámites de su zona

---

## 📊 Base de Datos (Firestore)

### Colección `usuarios`
```
usuarios/{email}
├── email: string
├── nombre: string
├── rol: "secretario" | "inspector" | "admin"
├── zona: string
├── uid: string
├── aprobado: boolean
└── fechaRegistro: timestamp
```

### Colección `tramites`
```
tramites/{id}
├── docenteNombre: string
├── docenteDni: string
├── descripcion: string
├── fechaCreacion: timestamp
├── zonaOrigen: string
├── escuelaOrigen: string
├── zonaDestino: string
├── escuelaDestino: string
├── estadoActual: string
├── recibidoPor: string (email)
├── activo: boolean
├── creadoPor: string (email)
├── fechaUltimaModificacion: timestamp
├── _nombreLower: string (helper para búsquedas)
├── _dniSearch: string (helper para búsquedas)
└── historial/ (subcolección)
    └── {historialId}
        ├── tipoEvento: string
        ├── usuario: string (email)
        ├── fecha: timestamp
        └── cambios: map (opcional)
```

### Estados Posibles
| Estado | Icono | Significado |
|---|---|---|
| Email Recibido del CE | 📧 | Se recibió comunicación del Centro Educativo |
| Email Enviado al CE | 📤 | Se envió comunicación al Centro Educativo |
| CAP recibido del CE | 📥 | Se recibió el CAP del Centro Educativo |
| CAP enviado a Region1 | 🚀 | El CAP fue enviado a Región 1 |
| CAP entregado al CE | ✅ | Trámite completado, CAP entregado |
| Email Solicitando a Zona | 📩 | Se solicitó información a otra zona |

---

## 🔧 Personalización

### Agregar/Modificar Zonas y Escuelas

En `index.html`, buscar el objeto `ZONAS_DATA` y modificar:

```javascript
const ZONAS_DATA = {
  "Zona 1131": ["EP N°1", "EP N°2", ...],
  "Mi Nueva Zona": ["Escuela A", "Escuela B", ...],
};
```

### Cambiar Colores

Las variables CSS están al inicio del `<style>`:

```css
:root {
  --gold-500: #d4a84b;      /* Color principal dorado */
  --navy-800: #0a1628;      /* Fondo oscuro */
  --blue-400: #2d6bc4;      /* Azul medio */
  /* ... etc ... */
}
```

### Agregar Nuevos Estados

1. Agregar al array `ESTADOS`
2. Agregar ícono en `ESTADO_ICONS`
3. Agregar clase CSS en `ESTADO_CLASSES`
4. Agregar estilo CSS `.status-badge.mi-nuevo-estado`
5. Agregar `<option>` en los selects de estado

---

## 🐛 Troubleshooting

| Problema | Solución |
|---|---|
| "Error al iniciar sesión" | Verificar que Google Auth esté habilitado en Firebase Console |
| Login funciona pero dice "no registrado" | Registrate primero usando el formulario de registro |
| No carga trámites | Revisar la consola del navegador (F12). Si dice "index required", hacer click en el link para crear el índice |
| Estilos no cargan | Verificar conexión a internet (Font Awesome y Google Fonts se cargan por CDN) |
| "Permission denied" | Verificar que las reglas de Firestore estén publicadas correctamente |
| Drive no abre | Reemplazar los IDs de carpeta en `DRIVE_FOLDERS` con IDs reales |

---

## 📱 Compatibilidad

- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+
- ✅ Chrome Mobile
- ✅ Safari iOS

---

## 📄 Licencia

Desarrollado por **Devimpulso** — Córdoba, Argentina

© 2026 — Todos los derechos reservados
