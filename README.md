# TipFit - MVP React Native

Una aplicación móvil que brinda consejos personalizados de bienestar según los hábitos del usuario, con autenticación OTP y generación de consejos usando IA.

## 🚀 Características

- **Autenticación OTP**: Registro e inicio de sesión con verificación por email
- **Perfil personalizado**: Configuración de edad, horas de pantalla, nivel de actividad y sueño
- **Consejos con IA**: Generación de consejos personalizados usando Google Gemini
- **Historial**: Visualización de consejos previos
- **Interfaz intuitiva**: Diseño con colores pasteles masculinos
- **Backend serverless**: MongoDB Atlas App Services

## 📱 Pantallas

- **WelcomeScreen**: Pantalla de bienvenida con opciones de login/registro
- **LoginScreen**: Inicio de sesión con email
- **RegisterScreen**: Registro de nuevos usuarios
- **VerifyOTPScreen**: Verificación de código de 6 dígitos
- **ProfileSetupScreen**: Configuración inicial del perfil
- **HomeScreen**: Pantalla principal con consejo del día
- **ProfileScreen**: Edición del perfil y configuración
- **HistoryScreen**: Historial de consejos generados

## 🛠️ Tecnologías

### Frontend
- **React Native** con Expo
- **React Navigation** para navegación
- **AsyncStorage** para persistencia local
- **Axios** para llamadas HTTP

### Backend
- **MongoDB Atlas** como base de datos
- **MongoDB Atlas App Services** para funciones serverless
- **Nodemailer** para envío de emails
- **Google Gemini API** para generación de consejos

## 📦 Instalación

### Prerrequisitos
- Node.js (versión 16 o superior)
- npm o yarn
- Expo CLI (`npm install -g @expo/cli`)
- Cuenta de MongoDB Atlas
- Cuenta de Gmail con verificación en 2 pasos
- API key de Google Gemini

### Pasos de Instalación

1. **Clonar el repositorio**
```bash
git clone <tu-repositorio>
cd TipFit
```

2. **Instalar dependencias**
```bash
npm install
```

3. **Configurar MongoDB Atlas**
   - Sigue la guía detallada en [MONGODB_SETUP.md](./MONGODB_SETUP.md)
   - Configura las funciones serverless
   - Configura los secrets necesarios

4. **Actualizar URL de API**
   - Edita `src/services/api.js`
   - Actualiza `API_BASE_URL` con tu URL de MongoDB Atlas

5. **Ejecutar la aplicación**
```bash
npm start
```

## 🔧 Configuración

### Variables de Entorno
Crea un archivo `.env` en la raíz del proyecto:

```env
MONGODB_API_URL=https://data.mongodb-api.com/app/tu-app-id/endpoint
```

### Secrets de MongoDB Atlas
Configura los siguientes secrets en tu App Service:

- `GMAIL_USER`: Tu email de Gmail
- `GMAIL_APP_PASSWORD`: Contraseña de aplicación de Gmail
- `GEMINI_API_KEY`: Tu API key de Google Gemini

## 📁 Estructura del Proyecto

```
TipFit/
├── src/
│   ├── components/          # Componentes reutilizables
│   │   ├── CustomButton.js
│   │   ├── CustomInput.js
│   │   └── TipCard.js
│   ├── constants/           # Constantes y configuración
│   │   └── colors.js
│   ├── navigation/          # Configuración de navegación
│   │   └── AppNavigator.js
│   ├── screens/            # Pantallas de la aplicación
│   │   ├── WelcomeScreen.js
│   │   ├── LoginScreen.js
│   │   ├── RegisterScreen.js
│   │   ├── VerifyOTPScreen.js
│   │   ├── ProfileSetupScreen.js
│   │   ├── HomeScreen.js
│   │   ├── ProfileScreen.js
│   │   └── HistoryScreen.js
│   └── services/           # Servicios y API
│       ├── api.js
│       └── storage.js
├── functions/             # Funciones serverless de MongoDB
│   ├── sendOTP.js
│   ├── verifyOTP.js
│   ├── generateTip.js
│   ├── getUserProfile.js
│   ├── updateUserProfile.js
│   ├── getTipsHistory.js
│   ├── getDailyTip.js
│   └── checkUser.js
├── App.js                # Componente principal
├── MONGODB_SETUP.md      # Guía de configuración
└── README.md            # Este archivo
```

## 🎨 Paleta de Colores

```javascript
const colors = {
  primary: '#A8DADC',      // Azul pastel
  secondary: '#457B9D',    // Azul medio
  accent: '#E0E1DD',       // Gris claro
  background: '#F1FAEE',   // Blanco crema
  text: '#1D3557',         // Azul oscuro
  success: '#90C9B5',      // Verde pastel
  error: '#F4A6A3'         // Rojo pastel
}
```

## 🔄 Flujo de Usuario

1. **Bienvenida**: Usuario ve pantalla de bienvenida
2. **Registro/Login**: Usuario se registra o inicia sesión
3. **Verificación OTP**: Usuario ingresa código de 6 dígitos
4. **Configuración de perfil**: Usuario completa su perfil (edad, hábitos)
5. **Pantalla principal**: Usuario ve consejo del día
6. **Generación de consejos**: Usuario puede generar nuevos consejos
7. **Historial**: Usuario puede ver consejos previos
8. **Perfil**: Usuario puede editar su información

## 🚀 Despliegue

### Desarrollo
```bash
npm start
# Escanea el código QR con Expo Go
```

### Producción
1. Configura las variables de entorno de producción
2. Actualiza las URLs de API
3. Configura las reglas de seguridad en MongoDB Atlas
4. Implementa rate limiting
5. Configura monitoreo y logs

## 🧪 Pruebas

### Pruebas Manuales
1. Registro de nuevo usuario
2. Verificación de OTP
3. Configuración de perfil
4. Generación de consejos
5. Visualización de historial
6. Edición de perfil
7. Cierre de sesión

### Pruebas de API
Usa MongoDB Atlas Data Explorer para verificar:
- Creación de usuarios
- Generación de códigos OTP
- Creación de consejos
- Actualización de perfiles

## 🐛 Solución de Problemas

### Errores Comunes

**Error de conexión a MongoDB**
- Verifica que la IP esté permitida en Network Access
- Confirma las credenciales del usuario de base de datos

**Error de envío de email**
- Verifica que la contraseña de aplicación de Gmail sea correcta
- Confirma que la verificación en 2 pasos esté activada

**Error de generación de consejos**
- Verifica que la API key de Gemini sea válida
- Confirma que el usuario tenga perfil completo

**Error de navegación**
- Verifica que todas las pantallas estén registradas en AppNavigator
- Confirma que los parámetros se pasen correctamente

## 📊 Monitoreo

### Métricas Importantes
- Número de usuarios registrados
- Frecuencia de generación de consejos
- Tiempo de respuesta de las funciones
- Errores de autenticación
- Uso de la API de Gemini

### Logs
- Revisa los logs en MongoDB Atlas App Services
- Monitorea errores en la aplicación React Native
- Verifica el envío de emails

## 🔒 Seguridad

### Implementado
- Autenticación OTP por email
- Tokens JWT para sesiones
- Validación de entrada
- Reglas de acceso en MongoDB

### Recomendaciones para Producción
- Implementar rate limiting
- Usar HTTPS para todos los endpoints
- Configurar CORS apropiadamente
- Implementar logging de seguridad
- Usar variables de entorno para secrets

## 💰 Costos

### Gratuito
- MongoDB Atlas M0 cluster (512MB)
- App Services hasta 100,000 requests/mes
- Gmail hasta 100 emails/día
- Gemini API hasta límite gratuito

### Estimación de Costos
- **Desarrollo**: $0/mes
- **Producción pequeña**: $0-10/mes
- **Producción mediana**: $10-50/mes

## 🤝 Contribución

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo `LICENSE` para más detalles.

## 📞 Soporte

Si tienes problemas o preguntas:

1. Revisa la guía de configuración en `MONGODB_SETUP.md`
2. Verifica los logs en MongoDB Atlas
3. Consulta la documentación de React Navigation
4. Revisa la documentación de MongoDB Atlas App Services

## 🎯 Próximas Características

- [ ] Notificaciones push
- [ ] Modo oscuro
- [ ] Múltiples idiomas
- [ ] Integración con wearables
- [ ] Análisis de progreso
- [ ] Consejos por categorías específicas
- [ ] Compartir consejos
- [ ] Recordatorios personalizados

---

**Desarrollado con ❤️ para mejorar tu bienestar diario**
