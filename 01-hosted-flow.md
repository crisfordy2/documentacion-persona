# Guía de Integración: Hosted Flow con Persona en Laravel

## Introducción

El **Hosted Flow** es el método más rápido y sencillo para integrar Persona en tu aplicación Laravel. Con este método, Persona aloja y gestiona completamente el flujo de verificación de identidad, lo que significa que no necesitas desarrollar ninguna interfaz personalizada. Los usuarios son redirigidos a una página segura de Persona donde completan el proceso de verificación.

### Ventajas del Hosted Flow

- ✅ **Implementación rápida**: Sin necesidad de código frontend complejo
- ✅ **Mantenimiento mínimo**: Persona se encarga de todas las actualizaciones de UI
- ✅ **Seguridad**: La verificación se realiza en servidores seguros de Persona
- ✅ **Personalizable**: Puedes personalizar colores, logos y textos desde el dashboard
- ✅ **Multiidioma**: Soporte para múltiples idiomas

### Cuándo usar Hosted Flow

- Cuando necesitas una solución rápida sin desarrollo frontend
- Cuando prefieres que Persona maneje toda la experiencia de usuario
- Cuando quieres minimizar el mantenimiento de código
- Para aplicaciones web simples o MVP

---

## Requisitos Previos

Antes de comenzar, asegúrate de tener:

1. ✅ Una cuenta activa en Persona ([app.withpersona.com](https://app.withpersona.com))
2. ✅ Un **Inquiry Template** configurado en tu dashboard de Persona
3. ✅ Tu aplicación Laravel configurada con las variables de entorno de Persona
4. ✅ Acceso a tu dashboard de Persona para obtener el template ID

---

## Paso 1: Configuración en el Dashboard de Persona

### 1.1 Obtener el Inquiry Template ID

1. Inicia sesión en tu [Dashboard de Persona](https://app.withpersona.com)
2. Navega a **Settings** → **Inquiry Templates** (o **Transaction Types**)
3. Selecciona el template que deseas usar o crea uno nuevo
4. Copia el **Template ID** (formato: `itmpl_xxxxxxxxxxxxx`)
5. Este ID lo necesitarás para generar los enlaces de verificación

### 1.2 Configurar el Template

En el template, asegúrate de configurar:

- **Verificaciones requeridas**: ID gubernamental, selfie, AML, etc.
- **Personalización**: Logo, colores, textos
- **Idioma**: Selecciona los idiomas soportados
- **Campos requeridos**: Define qué información necesitas recopilar

### 1.3 Configurar Webhooks (Opcional pero Recomendado)

1. Ve a **Settings** → **Webhooks**
2. Agrega una nueva URL de webhook: `https://tu-dominio.com/api/persona/webhook`
3. Selecciona los eventos que deseas recibir:
   - `inquiry.created`
   - `inquiry.completed`
   - `inquiry.failed`
   - `report.completed`
4. Copia el **Webhook Secret** y guárdalo en tu `.env`

---

## Paso 2: Configuración en Laravel

### 2.1 Variables de Entorno

Agrega las siguientes variables a tu archivo `.env`:

```env
# Persona API Configuration
PERSONA_API_KEY=key_live_xxxxxxxxxxxxx
PERSONA_BASE_URL=https://api.withpersona.com
PERSONA_VERSION_ID=2021-05-14
PERSONA_INQUIRY_TEMPLATE_ID=itmpl_xxxxxxxxxxxxx
PERSONA_WEBHOOK_SECRET=whsec_xxxxxxxxxxxxx
```

### 2.2 Verifica que tu configuración de Persona esté correcta:

---

## Paso 3: Crear el Controlador para Hosted Flow

Crea un controlador que maneje la generación de enlaces y el procesamiento de respuestas:

```bash
php artisan make:controller PersonaHostedFlowController
```

---

## Paso 4: Configurar las Rutas

Agrega las rutas en `routes/web.php`:

- Rutas para Hosted Flow
- Rutas de resultado
---

## Paso 5: Configurar Webhooks (Recomendado)

Aunque el callback maneja la respuesta inmediata, los webhooks son más confiables para actualizaciones de estado. Ya deberías tener configurado el `PersonaWebhookController` (como se muestra en la documentación técnica existente).

### 5.1 Verificar que el Webhook esté Configurado

El webhook ya debería estar configurado en `routes/web.php`:

```php
Route::post('/api/persona/webhook', [PersonaWebhookController::class, 'handle'])
    ->name('persona.webhook')
    ->middleware('throttle:60,1');
```

### 5.2 Procesar Webhooks

El `PersonaService` ya debería tener el método `processWebhook()` que actualiza el estado de las verificaciones. Asegúrate de que esté procesando correctamente los eventos de Hosted Flow.

---

## Paso 6: Crear Vistas (Opcional)

### 6.1 Vista para Iniciar Verificación

### 6.2 Vista de Éxito

### 6.3 Vista de Fallo

---

## Paso 7: Manejo de Estados

### 7.1 Estados de Inquiry

Los estados posibles de una verificación son:

- `created`: La verificación ha sido creada
- `processing`: Persona está procesando la verificación
- `approved`: La verificación fue aprobada
- `declined`: La verificación fue rechazada
- `needs_review`: Requiere revisión manual
- `errored`: Ocurrió un error

### 7.2 Actualizar Estado en Base de Datos

En el callback o webhook, actualiza el estado del usuario

---

## Referencias

- [Documentación Oficial de Persona - Hosted Flow](https://docs.withpersona.com/hosted-flow)
- [Dashboard de Persona - Hosted Flow Setup](https://app.withpersona.com/dashboard/getting-started/hosted-flow)
- [Documentación de Persona - Getting Started](https://docs.withpersona.com/2025-10-27/getting-started)

---

## Conclusión

El Hosted Flow es la forma más rápida de integrar Persona en tu aplicación Laravel. Con esta guía, deberías poder:

1. ✅ Configurar el Hosted Flow en tu dashboard de Persona
2. ✅ Generar enlaces de verificación en Laravel
3. ✅ Manejar callbacks y webhooks
4. ✅ Actualizar el estado de los usuarios
5. ✅ Probar y desplegar en producción


