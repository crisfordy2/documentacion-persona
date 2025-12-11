# Guía de Integración: Mobile SDKs con Persona en Laravel

## Introducción

Los **Mobile SDKs** de Persona permiten integrar el flujo de verificación de identidad directamente en aplicaciones móviles nativas para iOS y Android. Esta guía te ayudará a integrar los SDKs móviles de Persona con tu backend Laravel, permitiendo que tu aplicación móvil se comunique con Persona y tu servidor Laravel para completar el proceso de verificación.

### Ventajas de los Mobile SDKs

- ✅ **Experiencia nativa**: Integración completa con la experiencia móvil
- ✅ **Cámara nativa**: Acceso directo a la cámara del dispositivo
- ✅ **Mejor rendimiento**: Optimizado para dispositivos móviles
- ✅ **Offline parcial**: Algunas funcionalidades funcionan sin conexión
- ✅ **UI personalizable**: Puedes personalizar la interfaz de usuario

### Cuándo usar Mobile SDKs

- Cuando tienes una aplicación móvil nativa (iOS/Android)
- Cuando quieres una experiencia completamente móvil
- Cuando necesitas acceso directo a la cámara del dispositivo
- Para aplicaciones que requieren verificación de identidad móvil

---

## Arquitectura General

```
┌─────────────────────────────────────────────────────────────┐
│                    APLICACIÓN MÓVIL                          │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │  iOS/Android App │  │  Persona SDK     │                │
│  │  (Swift/Kotlin)  │  │  (Native)        │                │
│  └──────────────────┘  └──────────────────┘                │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    BACKEND LARAVEL                           │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │  API Endpoints   │  │  PersonaService  │                │
│  │  (REST API)      │  │  (API Client)    │                │
│  └──────────────────┘  └──────────────────┘                │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    PERSONA API                               │
│  - Crear Inquiries                                          │
│  - Obtener Estados                                          │
│  - Webhooks                                                 │
└─────────────────────────────────────────────────────────────┘
```

---

## Requisitos Previos

Antes de comenzar, asegúrate de tener:

1. ✅ Una cuenta activa en Persona ([app.withpersona.com](https://app.withpersona.com))
2. ✅ Un **Inquiry Template** configurado en tu dashboard de Persona
3. ✅ Tu aplicación Laravel configurada con las variables de entorno de Persona
4. ✅ Una aplicación móvil iOS (Swift) o Android (Kotlin/Java)
5. ✅ Acceso a tu dashboard de Persona para obtener el template ID

---

## Parte 1: Configuración del Backend Laravel

### Paso 1.1: Variables de Entorno

Agrega las siguientes variables a tu archivo `.env`:

```env
# Persona API Configuration
PERSONA_API_KEY=key_live_xxxxxxxxxxxxx
PERSONA_BASE_URL=https://api.withpersona.com
PERSONA_VERSION_ID=2021-05-14
PERSONA_INQUIRY_TEMPLATE_ID=itmpl_xxxxxxxxxxxxx
PERSONA_WEBHOOK_SECRET=whsec_xxxxxxxxxxxxx
```

### Paso 1.2: Verifica que tu configuración de Persona esté correcta


### Paso 1.3: Crear Controlador para API Móvil

Crea un controlador para manejar las peticiones de la aplicación móvil:

```bash
php artisan make:controller Api/MobilePersonaController
```

### Paso 1.4: Configurar Rutas API

---

## Parte 2: Integración iOS (Swift)

### Paso 2.1: Instalar el SDK de Persona

#### Usando CocoaPods

Agrega a tu `Podfile`:

```ruby
platform :ios, '13.0'
use_frameworks!

target 'TuApp' do
  pod 'PersonaInquirySDK2', '~> 2.0'
end
```

Ejecuta:

```bash
pod install
```

### Paso 2.2: Configurar Info.plist

### Paso 2.3: Implementar el Flujo de Verificación

Crea un ViewController o View para manejar la verificación

---

## Parte 3: Integración Android (Kotlin)

### Paso 3.1: Instalar el SDK de Persona

Agrega a tu `build.gradle` (Module: app):

```gradle
dependencies {
    implementation 'com.withpersona.sdk2:inquiry:2.0.0'
}
```

Sincroniza el proyecto.

### Paso 3.2: Configurar AndroidManifest.xml

### Paso 3.3: Solicitar Permisos en Tiempo de Ejecución

### Paso 3.4: Implementar el Flujo de Verificación

---

## Parte 4: Configurar Webhooks

Asegúrate de que los webhooks estén configurados correctamente en tu backend Laravel. El controller existente debería manejar estos eventos automáticamente y actualizar el estado de las verificaciones.

---

## Referencias

- [Documentación Oficial de Persona - Mobile SDKs](https://docs.withpersona.com/mobile-sdks)
- [iOS SDK Integration Guide](https://docs.withpersona.com/ios-sdk-v2-integration-guide)
- [Android SDK Integration Guide](https://docs.withpersona.com/android-sdk-v2-integration-guide)
- [Dashboard de Persona - Mobile SDKs Setup](https://app.withpersona.com/dashboard/getting-started/mobile-sdks)

---

## Conclusión

Los Mobile SDKs de Persona ofrecen una experiencia nativa completa para aplicaciones móviles. Con esta guía, deberías poder:

1. ✅ Configurar el backend Laravel para manejar peticiones móviles
2. ✅ Integrar el SDK de Persona en iOS (Swift)
3. ✅ Integrar el SDK de Persona en Android (Kotlin)
4. ✅ Manejar el flujo completo de verificación
5. ✅ Probar y desplegar en producción


