# Guía de Integración: Server APIs con Persona en Laravel

## Introducción

Las **Server APIs** de Persona permiten realizar verificaciones directamente desde el servidor, sin necesidad de interfaz de usuario. Este método es ideal cuando ya has recopilado la información del usuario (documentos, fotos, etc.) y quieres enviarla a Persona para su verificación mediante llamadas API.

### Ventajas de Server APIs

- ✅ **Control total**: Manejas todo el proceso desde el servidor
- ✅ **Sin frontend**: No necesitas código JavaScript o SDKs
- ✅ **Automatización**: Puedes automatizar completamente el proceso
- ✅ **Flexibilidad**: Puedes integrar con cualquier sistema existente
- ✅ **Seguridad**: Los datos nunca salen de tu servidor hasta llegar a Persona

### Cuándo usar Server APIs

- Cuando ya tienes los documentos del usuario almacenados
- Para procesos automatizados o batch processing
- Cuando necesitas integración con sistemas legacy
- Para verificaciones programáticas sin interacción del usuario

---

## Requisitos Previos

Antes de comenzar, asegúrate de tener:

1. ✅ Una cuenta activa en Persona ([app.withpersona.com](https://app.withpersona.com))
2. ✅ Un **Transaction Type** configurado en tu dashboard de Persona
3. ✅ Tu aplicación Laravel configurada con las variables de entorno de Persona
4. ✅ Acceso a tu dashboard de Persona para obtener el Transaction Type ID
5. ✅ Los documentos del usuario (fotos de ID, selfies, etc.) almacenados

---

## Paso 1: Configuración en el Dashboard de Persona

### 1.1 Obtener el Transaction Type ID

1. Inicia sesión en tu [Dashboard de Persona](https://app.withpersona.com)
2. Navega a **Settings** → **Transaction Types**
3. Selecciona el transaction type que deseas usar o crea uno nuevo
4. Copia el **Transaction Type ID** (formato: `itmpl_xxxxxxxxxxxxx`)

**Nota**: Server APIs usa Transaction Types, no Inquiry Templates.

### 1.2 Configurar el Transaction Type

En el transaction type, asegúrate de configurar:

- **Verificaciones requeridas**: ID gubernamental, selfie, AML, etc.
- **Campos requeridos**: Define qué información necesitas enviar
- **Checks**: Configura las verificaciones que deseas ejecutar

### 1.3 Configurar Webhooks

1. Ve a **Settings** → **Webhooks**
2. Agrega una nueva URL de webhook: `https://tu-dominio.com/api/persona/webhook`
3. Selecciona los eventos:
   - `transaction.status-updated`
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
PERSONA_TRANSACTION_TYPE_ID=itmpl_xxxxxxxxxxxxx
PERSONA_WEBHOOK_SECRET=whsec_xxxxxxxxxxxxx
```

### 2.2 Verifica que tu configuración de Persona esté correcta

---

## Paso 3: Extender PersonaService

El `PersonaService` existente ya tiene métodos básicos. Necesitamos agregar/verificar el método para crear transacciones usando Server APIs.

### 3.1 Método createTransaction (Ya existe)

El método `createTransaction()` en `PersonaService` ya está implementado y maneja la creación de transacciones. Verifica que esté usando el formato correcto para Server APIs.

### 3.2 Agregar Método para Crear Transacción con Archivos

Asegúrate de que el método maneje correctamente los archivos. El método existente `buildMultipartData()` debería manejar esto.

---

## Paso 4: Crear Controlador para Server APIs

Crea un controlador para manejar las verificaciones mediante Server APIs:

```bash
php artisan make:controller PersonaServerApiController
```

---

## Paso 5: Configurar Rutas

Agrega las rutas en `routes/api.php`:

- Crear verificación usando Server API
   
- Obtener estado de transacción
   

---

## Paso 6: Campos Requeridos y Opcionales

### 6.1 Campos Requeridos

Según la documentación de Persona, los campos mínimos requeridos son:

- `transaction_type_id`: ID del Transaction Type
- `reference_id`: ID de referencia único
- `id_class`: Tipo de documento (dl, passport, national_id, etc.)
- `address_country_code`: Código de país de 2 letras (ISO 3166-1 alpha-2)
- `id_front_photo`: Foto frontal del documento

### 6.2 Campos Opcionales

- `name_first`, `name_last`, `name_middle`: Nombres
- `birthdate`: Fecha de nacimiento (formato: YYYY-MM-DD)
- `address_street_1`, `address_street_2`: Dirección
- `address_city`: Ciudad
- `address_postal_code`: Código postal
- `address_subdivision`: Subdivisión (estado/provincia) - ISO 3166-2
- `phone_number`: Número de teléfono
- `email_address`: Correo electrónico
- `id_back_photo`: Foto trasera del documento (si es requerida)
- `selfie_photo`: Foto selfie (si es requerida)

### 6.3 Mapeo de ID Classes

Persona soporta varios tipos de documentos. Algunos ejemplos:

- `dl` o `driver_license`: Licencia de conducir
- `passport`: Pasaporte
- `national_id`: ID nacional
- `voter_id`: ID de votante
- `tax_id`: ID fiscal

Consulta la [documentación de Persona](https://docs.withpersona.com/api-reference/verifications/government-id-verifications) para la lista completa.

---

## Paso 7: Manejo de Estados

### 7.1 Estados de Transacción

Los estados posibles son:

- `created`: La transacción ha sido creada
- `processing`: Persona está procesando la verificación
- `approved`: La verificación fue aprobada
- `declined`: La verificación fue rechazada
- `needs_review`: Requiere revisión manual
- `errored`: Ocurrió un error

### 7.2 Escuchar Webhooks

El `PersonaWebhookController` existente debería manejar los webhooks de `transaction.status-updated`. Asegúrate de que esté procesando correctamente estos eventos.

---

## Paso 8: Ejemplo de Uso

### 8.1 Desde un Formulario Web

```blade
<form action="{{ route('api.persona.server-api.verify') }}" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="hidden" name="user_id" value="{{ auth()->id() }}">
    
    <div>
        <label>Tipo de Documento</label>
        <select name="id_class" required>
            <option value="dl">Licencia de Conducir</option>
            <option value="passport">Pasaporte</option>
            <option value="national_id">ID Nacional</option>
        </select>
    </div>
    
    <div>
        <label>Código de País</label>
        <input type="text" name="address_country_code" value="US" required maxlength="2">
    </div>
    
    <div>
        <label>Foto Frontal del ID</label>
        <input type="file" name="id_front_photo" accept="image/*" required>
    </div>
    
    <div>
        <label>Foto Trasera del ID (opcional)</label>
        <input type="file" name="id_back_photo" accept="image/*">
    </div>
    
    <div>
        <label>Selfie (opcional)</label>
        <input type="file" name="selfie_photo" accept="image/*">
    </div>
    
    <button type="submit">Enviar Verificación</button>
</form>
```

### 8.2 Desde una API Externa

```bash
curl -X POST https://tu-dominio.com/api/persona/server-api/verify \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "user_id=1" \
  -F "id_class=dl" \
  -F "address_country_code=US" \
  -F "id_front_photo=@/path/to/front.jpg" \
  -F "id_back_photo=@/path/to/back.jpg" \
  -F "selfie_photo=@/path/to/selfie.jpg"
```
---

## Referencias

- [Documentación Oficial de Persona - Server APIs](https://docs.withpersona.com/2025-10-27/integration-guide-gov-id-via-api)
- [Persona API Reference](https://docs.withpersona.com/api-reference)
- [Dashboard de Persona - API Setup](https://app.withpersona.com/dashboard/getting-started/api)
- [Transaction Fields Documentation](https://docs.withpersona.com/2025-10-27/integration-guide-gov-id-via-api#transaction-fields)

---

## Conclusión

Las Server APIs ofrecen el máximo control sobre el proceso de verificación, permitiendo integrar Persona completamente desde el servidor. Con esta guía, deberías poder:

1. ✅ Configurar Transaction Types en Persona
2. ✅ Crear transacciones usando Server APIs
3. ✅ Manejar archivos y campos requeridos
4. ✅ Procesar webhooks y actualizar estados
5. ✅ Probar y desplegar en producción


