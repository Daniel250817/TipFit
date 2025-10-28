# Guía de Configuración MongoDB Atlas para TipFit

Esta guía te ayudará a configurar MongoDB Atlas y App Services para tu aplicación TipFit.

## 🔐 **Flujo de Autenticación Actualizado**

### **1. Registro (Primera vez)**
1. Usuario ingresa email, nombre y contraseña
2. Se envía OTP por email
3. Usuario verifica OTP
4. Se crea la cuenta con contraseña hasheada
5. Se configura el perfil

### **2. Inicio de Sesión (Usuarios existentes)**
1. Usuario ingresa email y contraseña
2. Se verifica la contraseña
3. Se envía OTP por email
4. Usuario verifica OTP
5. Se genera token de sesión

### **3. Seguridad**
- Las contraseñas se hashean con bcrypt antes de guardarse
- Los códigos OTP expiran en 10 minutos
- Los tokens JWT expiran en 7 días
- Validación de formato de email y contraseña mínima de 6 caracteres

## 1. Configuración de MongoDB Atlas

### 1.1 Crear Cluster
1. Ve a [MongoDB Atlas](https://www.mongodb.com/atlas)
2. Inicia sesión o crea una cuenta
3. Crea un nuevo proyecto llamado "TipFit"
4. Crea un cluster gratuito (M0)
5. Elige la región más cercana a tu ubicación

### 1.2 Configurar Acceso a la Base de Datos
1. Ve a "Database Access" en el menú lateral
2. Crea un nuevo usuario de base de datos:
   - Username: `tipfit-user`
   - Password: Genera una contraseña segura
   - Database User Privileges: "Read and write to any database"
3. Guarda las credenciales de forma segura

### 1.3 Configurar Acceso de Red
1. Ve a "Network Access" en el menú lateral
2. Agrega tu IP actual o usa `0.0.0.0/0` para permitir acceso desde cualquier lugar (solo para desarrollo)

### 1.4 Obtener String de Conexión
1. Ve a "Database" en el menú lateral
2. Haz clic en "Connect" en tu cluster
3. Selecciona "Connect your application"
4. Copia el string de conexión (reemplaza `<password>` con tu contraseña)

## 2. Configuración de App Services

### 2.1 Crear App Service
1. En MongoDB Atlas, ve a "App Services" en el menú lateral
2. Haz clic en "Create New App"
3. Selecciona "Build a new App from scratch"
4. Nombre: `TipFit-Backend`
5. Selecciona tu cluster
6. Haz clic en "Create App"

### 2.2 Configurar Base de Datos
1. En tu App Service, ve a "Data Sources"
2. Selecciona tu cluster
3. Nombre de la base de datos: `tipfit`
4. Haz clic en "Add Data Source"

### 2.3 Crear Colecciones
Ejecuta estos comandos en MongoDB Compass o Atlas Data Explorer:

```javascript
// Crear colección users
db.users.createIndex({ "email": 1 }, { unique: true })

// Crear colección otpCodes con TTL
db.otpCodes.createIndex({ "expiresAt": 1 }, { expireAfterSeconds: 0 })

// Crear colección tips
db.tips.createIndex({ "userId": 1, "createdAt": -1 })
```

### 2.4 Configurar Reglas de Acceso
1. Ve a "Rules" en tu App Service
2. Configura las siguientes reglas:

**Para colección `users`:**
```javascript
{
  "%%user.id": "%%user.id"
}
```

**Para colección `otpCodes`:**
```javascript
{
  "email": "%%user.data.email"
}
```

**Para colección `tips`:**
```javascript
{
  "userId": "%%user.id"
}
```

## 3. Configurar Funciones Serverless

### 3.1 Crear Triggers/Functions
1. Ve a "Triggers" en tu MongoDB Atlas
2. Crea las siguientes funciones con el código proporcionado:
   - `sendOTP`
   - `verifyOTP`
   - `generateTip`
   - `getUserProfile`
   - `updateUserProfile`
   - `getTipsHistory`
   - `getDailyTip`
   - `checkUser`

### 3.2 Configurar Triggers como HTTP Endpoints
**IMPORTANTE**: En MongoDB Atlas moderno, las funciones se crean como HTTP Endpoints, no como Database Triggers.

#### Paso 1: Crear HTTP Endpoints
1. Ve a "HTTP Endpoints" en MongoDB Atlas
2. Haz clic en "Create Endpoint"
3. Configura cada endpoint según la tabla siguiente:

| Endpoint | Method | Function | Authentication |
|----------|--------|----------|----------------|
| `/auth/sendOTP` | POST | `sendOTP` | None |
| `/auth/verifyOTP` | POST | `verifyOTP` | None |
| `/auth/checkUser` | POST | `checkUser` | None |
| `/user/profile` | GET | `getUserProfile` | Custom JWT |
| `/user/profile` | PUT | `updateUserProfile` | Custom JWT |
| `/tips/generate` | POST | `generateTip` | Custom JWT |
| `/tips/history` | GET | `getTipsHistory` | Custom JWT |
| `/tips/daily` | GET | `getDailyTip` | Custom JWT |

#### Paso 2: Crear las Funciones
Para cada endpoint, necesitas crear la función correspondiente:

1. **Crear función `sendOTP`**:
   - Ve a "Functions" en MongoDB Atlas
   - Haz clic en "Create Function"
   - Nombre: `sendOTP`
   - Copia el código desde `functions/sendOTP.js` en tu proyecto

2. **Crear función `verifyOTP`**:
   - Nombre: `verifyOTP`
   - Copia el código desde `functions/verifyOTP.js`

3. **Crear función `checkUser`**:
   - Nombre: `checkUser`
   - Copia el código desde `functions/checkUser.js`

4. **Crear función `getUserProfile`**:
   - Nombre: `getUserProfile`
   - Copia el código desde `functions/getUserProfile.js`

5. **Crear función `updateUserProfile`**:
   - Nombre: `updateUserProfile`
   - Copia el código desde `functions/updateUserProfile.js`

6. **Crear función `generateTip`**:
   - Nombre: `generateTip`
   - Copia el código desde `functions/generateTip.js`

7. **Crear función `getTipsHistory`**:
   - Nombre: `getTipsHistory`
   - Copia el código desde `functions/getTipsHistory.js`

8. **Crear función `getDailyTip`**:
   - Nombre: `getDailyTip`
   - Copia el código desde `functions/getDailyTip.js`

### 3.3 Configurar Secrets
1. Ve a "Secrets" en MongoDB Atlas
2. Crea los siguientes secrets:

**GMAIL_USER**: Tu email de Gmail
**GMAIL_APP_PASSWORD**: Contraseña de aplicación de Gmail
**GEMINI_API_KEY**: Tu API key de Google Gemini

### 3.4 Obtener URL Base de Endpoints
1. En "HTTP Endpoints", copia la **URL base**
2. Actualiza el archivo `src/services/api.js` con esta URL:

```javascript
const API_BASE_URL = 'https://data.mongodb-api.com/app/tu-app-id/endpoint';
```

### 3.5 Configurar Contraseña de Aplicación de Gmail
1. Ve a tu cuenta de Google
2. Seguridad → Verificación en 2 pasos (debe estar activada)
3. Contraseñas de aplicaciones
4. Genera una nueva contraseña para "TipFit"
5. Copia la contraseña generada

### 3.6 Obtener API Key de Gemini
1. Ve a [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Crea una nueva API key
3. Copia la key generada

## 4. Guía Paso a Paso para Crear Triggers

### 4.1 Acceder a MongoDB Atlas
1. Ve a [MongoDB Atlas](https://www.mongodb.com/atlas)
2. Inicia sesión en tu cuenta
3. Selecciona tu proyecto "TipFit"
4. Ve a tu cluster "Tip-Fit"

### 4.2 Encontrar la Sección de Triggers/Functions
En MongoDB Atlas moderno, las funciones se encuentran en:
- **"Triggers"** en el menú lateral izquierdo
- O **"Functions"** en el menú lateral izquierdo
- O **"App Services"** → **"Functions"**

### 4.3 Crear tu Primera Función (sendOTP)

#### Paso 1: Crear la Función
1. Haz clic en **"Create Function"** o **"New Function"**
2. **Nombre**: `sendOTP`
3. **Tipo**: Selecciona **"HTTP Endpoint"** (no Database Trigger)
4. **Método**: `POST`
5. **Ruta**: `/auth/sendOTP`
6. **Autenticación**: `None`

#### Paso 2: Copiar el Código
1. Abre el archivo `functions/sendOTP.js` de tu proyecto
2. Copia todo el código (desde `// MongoDB Atlas App Services Function: sendOTP` hasta el final)
3. Pégalo en el editor de MongoDB Atlas
4. Haz clic en **"Save"** o **"Deploy"**

#### Paso 3: Probar la Función
1. Ve a la pestaña **"Test"** de tu función
2. Envía datos de prueba:
```json
{
  "email": "test@example.com",
  "password": "123456",
  "name": "Usuario Test",
  "type": "registration"
}
```
3. Haz clic en **"Run"**
4. Verifica que retorne `{"success": true, ...}`

### 4.4 Crear las Funciones Restantes
Repite el proceso para cada función:

1. **verifyOTP**:
   - Ruta: `/auth/verifyOTP`
   - Método: `POST`
   - Autenticación: `None`

2. **checkUser**:
   - Ruta: `/auth/checkUser`
   - Método: `POST`
   - Autenticación: `None`

3. **getUserProfile**:
   - Ruta: `/user/profile`
   - Método: `GET`
   - Autenticación: `Custom JWT`

4. **updateUserProfile**:
   - Ruta: `/user/profile`
   - Método: `PUT`
   - Autenticación: `Custom JWT`

5. **generateTip**:
   - Ruta: `/tips/generate`
   - Método: `POST`
   - Autenticación: `Custom JWT`

6. **getTipsHistory**:
   - Ruta: `/tips/history`
   - Método: `GET`
   - Autenticación: `Custom JWT`

7. **getDailyTip**:
   - Ruta: `/tips/daily`
   - Método: `GET`
   - Autenticación: `Custom JWT`

### 4.5 Configurar Secrets
1. Ve a **"Secrets"** en MongoDB Atlas
2. Haz clic en **"Create Secret"**
3. Crea estos secrets:

**GMAIL_USER**:
- Nombre: `GMAIL_USER`
- Valor: `tu-email@gmail.com`

**GMAIL_APP_PASSWORD**:
- Nombre: `GMAIL_APP_PASSWORD`
- Valor: `tu-contraseña-de-aplicación`

**GEMINI_API_KEY**:
- Nombre: `GEMINI_API_KEY`
- Valor: `tu-api-key-de-gemini`

### 4.6 Obtener URL Base
1. Ve a **"HTTP Endpoints"**
2. Copia la **URL base** (algo como: `https://data.mongodb-api.com/app/tipfit-xxxxx/endpoint`)
3. Actualiza tu archivo `src/services/api.js`:

```javascript
const API_BASE_URL = 'https://data.mongodb-api.com/app/tu-app-id/endpoint';
```

## 5. Configuración de la Aplicación React Native

### 5.1 Actualizar URL de API
En `src/services/api.js`, actualiza la constante `API_BASE_URL`:

```javascript
const API_BASE_URL = 'https://data.mongodb-api.com/app/tu-app-id/endpoint';
```

### 5.2 Variables de Entorno (Opcional)
Crea un archivo `.env` en la raíz del proyecto:

```env
MONGODB_API_URL=https://data.mongodb-api.com/app/tu-app-id/endpoint
```

## 6. Pruebas

### 6.1 Probar Funciones
1. Usa MongoDB Atlas Data Explorer para verificar que las colecciones se crean correctamente
2. Prueba las funciones desde la interfaz de App Services
3. Verifica que los emails se envían correctamente

### 6.2 Probar Aplicación
1. Ejecuta `npm start` en tu proyecto React Native
2. Prueba el flujo completo: registro → verificación → configuración de perfil
3. Verifica que los consejos se generen correctamente

## 7. Solución de Problemas

### 7.1 Errores Comunes
- **Error de conexión**: Verifica que la IP esté permitida en Network Access
- **Error de autenticación**: Verifica las credenciales del usuario de base de datos
- **Error de email**: Verifica que la contraseña de aplicación de Gmail sea correcta
- **Error de Gemini**: Verifica que la API key sea válida

### 7.2 Logs
- Revisa los logs en "Logs" de tu App Service
- Usa `console.log()` en las funciones para debugging

## 8. Seguridad en Producción

### 8.1 Configuraciones Importantes
- Cambia las reglas de acceso para ser más restrictivas
- Usa HTTPS para todos los endpoints
- Implementa rate limiting
- Configura CORS apropiadamente
- Usa variables de entorno para secrets

### 8.2 Monitoreo
- Configura alertas en MongoDB Atlas
- Monitorea el uso de las funciones
- Revisa logs regularmente

## 9. Costos

### 9.1 MongoDB Atlas
- Cluster M0: Gratis (512MB)
- App Services: Gratis hasta 100,000 requests/mes

### 9.2 Servicios Externos
- Gmail: Gratis (límite de 100 emails/día)
- Gemini API: Gratis hasta cierto límite de requests

## 10. Próximos Pasos

1. Implementar autenticación JWT más robusta
2. Agregar validación de entrada más estricta
3. Implementar rate limiting
4. Agregar tests automatizados
5. Configurar CI/CD
6. Implementar analytics y métricas
