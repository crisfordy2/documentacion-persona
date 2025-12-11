# Guía de Integración: Embedded Flow con Persona en Laravel

## Introducción

El **Embedded Flow** permite integrar el flujo de verificación de Persona directamente en tu aplicación web mediante un iframe embebido. A diferencia del Hosted Flow, el Embedded Flow mantiene al usuario dentro de tu aplicación mientras completa la verificación, ofreciendo una experiencia más integrada y personalizada.

### Ventajas del Embedded Flow

- ✅ **Experiencia integrada**: El usuario permanece en tu aplicación
- ✅ **Control total**: Puedes personalizar completamente la UI alrededor del iframe
- ✅ **Mejor UX**: Transiciones más suaves sin redirecciones
- ✅ **Branding consistente**: Mantiene tu marca visible durante todo el proceso
- ✅ **Eventos en tiempo real**: Puedes escuchar eventos del flujo de verificación

### Cuándo usar Embedded Flow

- Cuando quieres mantener al usuario en tu aplicación
- Cuando necesitas más control sobre la experiencia de usuario
- Para aplicaciones web modernas con JavaScript
- Cuando quieres personalizar completamente la UI

---

## Requisitos Previos

Antes de comenzar, asegúrate de tener:

1. ✅ Una cuenta activa en Persona ([app.withpersona.com](https://app.withpersona.com))
2. ✅ Un **Inquiry Template** configurado en tu dashboard de Persona
3. ✅ Tu aplicación Laravel configurada con las variables de entorno de Persona
4. ✅ Acceso a tu dashboard de Persona para obtener el template ID
5. ✅ Conocimientos básicos de JavaScript

---

## Paso 1: Configuración en el Dashboard de Persona

### 1.1 Obtener el Inquiry Template ID

1. Inicia sesión en tu [Dashboard de Persona](https://app.withpersona.com)
2. Navega a **Settings** → **Inquiry Templates** (o **Transaction Types**)
3. Selecciona el template que deseas usar o crea uno nuevo
4. Copia el **Template ID** (formato: `itmpl_xxxxxxxxxxxxx`)
5. Este ID lo necesitarás para inicializar el Embedded Flow

### 1.2 Configurar el Template

En el template, asegúrate de configurar:

- **Verificaciones requeridas**: ID gubernamental, selfie, AML, etc.
- **Personalización**: Logo, colores, textos
- **Idioma**: Selecciona los idiomas soportados
- **Campos requeridos**: Define qué información necesitas recopilar

### 1.3 Configurar Webhooks (Recomendado)

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

# Para Embedded Flow - Environment (sandbox o production)
PERSONA_ENVIRONMENT=sandbox
```

**Nota**: Para desarrollo, usa `PERSONA_ENVIRONMENT=sandbox`. Para producción, usa `PERSONA_ENVIRONMENT=production`.

### 2.2 Verifica que tu configuración de Persona esté correcta

---

## Paso 3: Instalar el SDK de Persona

### 3.1 Incluir el Script de Persona

El SDK de Persona se carga desde un CDN. Inclúyelo en tu layout principal o en la vista donde necesites el Embedded Flow.

En `resources/views/layouts/app.blade.php` o en tu layout principal:

```blade
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <!-- ... otros head tags ... -->
</head>
<body>
    <!-- ... contenido ... -->
    
    <!-- Persona Embedded Flow SDK -->
    <script src="https://withpersona.com/static/js/iframe-api.js"></script>
    
    <!-- Tu JavaScript personalizado -->
    @stack('scripts')
</body>
</html>
```

O si prefieres cargarlo solo en páginas específicas, usa `@push`:

```blade
@push('scripts')
<script src="https://withpersona.com/static/js/iframe-api.js"></script>
@endpush
```

---

## Paso 4: Crear el Controlador

Crea un controlador para manejar la creación de inquiries y el procesamiento de resultados:

```bash
php artisan make:controller PersonaEmbeddedFlowController
```

---

## Paso 5: Extender PersonaService

Agrega un método para crear inquiries usando la API de Persona

---

## Paso 6: Crear la Vista con Embedded Flow

Aqui sera donde visualizaras tu flujo embebido

---

## Paso 7: Configurar las Rutas

Agrega las rutas en `routes/web.php`:

- Rutas para Embedded Flow
- Mostrar página con Embedded Flow
- Manejar completación

---

## Paso 8: Implementación Alternativa con API Directa

Si prefieres crear el inquiry desde el frontend usando la API de Persona directamente, puedes hacerlo creando un Endpoint para Obtener Session Token y luego actualizar la Vista para Usar el Inquiry ID

---

## Paso 9: Manejo de Eventos Avanzados

El SDK de Persona proporciona varios eventos que puedes escuchar:

```javascript
const client = new Persona.Client({
    templateId: '{{ $templateId }}',
    environment: '{{ $environment }}',
    onReady: function() {
        // Se ejecuta cuando Persona está listo
    },
    onComplete: function({ inquiryId }) {
        // Se ejecuta cuando la verificación se completa exitosamente
    },
    onCancel: function() {
        // Se ejecuta cuando el usuario cancela
    },
    onError: function(error) {
        // Se ejecuta cuando ocurre un error
    },
    onEvent: function(event) {
        // Se ejecuta para cualquier evento del flujo
        console.log('Evento de Persona:', event);
    },
});
```

---

## Paso 11: Configurar Webhooks

Asegúrate de que los webhooks estén configurados correctamente para recibir actualizaciones de estado.

---

## Referencias

- [Documentación Oficial de Persona - Embedded Flow](https://docs.withpersona.com/embedded-flow)
- [Dashboard de Persona - Embedded Flow Setup](https://app.withpersona.com/dashboard/getting-started/embedded-flow)
- [Persona JavaScript SDK Reference](https://docs.withpersona.com/embedded-flow/javascript-sdk-reference)
- [Documentación de Persona - Getting Started](https://docs.withpersona.com/2025-10-27/getting-started)

---

## Conclusión

El Embedded Flow ofrece una experiencia de usuario más integrada que el Hosted Flow, manteniendo al usuario dentro de tu aplicación. Con esta guía, deberías poder:

1. ✅ Configurar el Embedded Flow en tu dashboard de Persona
2. ✅ Integrar el SDK de Persona en tu aplicación Laravel
3. ✅ Crear inquiries y manejar eventos
4. ✅ Procesar completaciones y actualizar estados
5. ✅ Probar y desplegar en producción


