# Web de Talleres del Norte (Eibar)

Web estática (HTML, CSS y JavaScript sin dependencias). Se puede publicar tal cual en GitHub Pages, Netlify, Vercel o cualquier hosting.

```
index.html              Web pública con el asistente "Solicitar revisión"
admin.html              Panel del taller (modo demostración)
legal.html              Aviso legal, privacidad y cookies (con campos pendientes)
assets/css/styles.css   Estilos
assets/js/config.js     ← TODO lo editable: datos, horario, servicios, precios, integraciones
assets/js/integrations.js  Envío de solicitudes, matrícula y almacenamiento (separado de la interfaz)
assets/js/app.js        Interfaz de la web pública
assets/js/admin.js      Interfaz del panel
```

## Cómo funciona ahora

1. El cliente rellena la solicitud (coche, zonas, síntomas, móvil con WhatsApp).
2. El taller la recibe por correo (con Formspree configurado). Al final del correo hay un enlace **"Hacer presupuesto"**.
3. Ese enlace abre `presupuesto.html` con los datos del cliente ya cargados. El mecánico añade piezas y mano de obra, y pulsa **Enviar por WhatsApp**: se abre WhatsApp con el mensaje escrito para ese cliente.

`presupuesto.html` también sirve sin solicitud: se rellenan los datos a mano.

**Trabajos habituales:** en `config.js` → `trabajos.lista`. Ahora van sin precio (`p: null`). Si el taller da sus precios "desde" (IVA incluido), se ponen en `p` y la web los muestra y suma.

**Cita:** el cliente elige día y mañana/tarde según el horario. Es una preferencia: el mecánico la confirma (o propone otra) en el mismo WhatsApp del presupuesto.

## Datos del taller

Verificados en la ficha pública de Google Maps (octubre 2026): nombre, dirección (Armagin Kalea, 8, bajo, 20600 Eibar), teléfono 843 64 16 31, horario L–V 9:00–13:00 y 15:00–19:00, valoración 4,8 con 26 reseñas.

**Falta que el taller confirme o aporte:**
- Correo electrónico y número de WhatsApp (si lo usan).
- Lista definitiva de servicios.
- Tarifa de mano de obra y precios orientativos por zona (los actuales son de demostración).
- Datos legales: razón social, NIF, plazo de conservación de datos.
- Logo oficial y fotos reales del taller (el logo actual es una propuesta).

## Antes de publicar con datos reales

1. Rellena `assets/js/config.js`.
2. Pon `modoDemo: false` y, si los precios ya son reales, `sonDeDemostracion: false`.
3. Completa los campos `[PENDIENTE]` de `legal.html`.

## Integraciones que hay que activar

### Envío de solicitudes (recomendado)
Sin configurar, la solicitud no llega sola al taller: el cliente la manda por WhatsApp o correo (si están configurados), la copia o llama.

Opción sencilla: un servicio de formularios (Formspree, Getform, Basin…).
1. Crear el formulario en el servicio y copiar su URL.
2. En `config.js`: `envio.endpoint = "https://formspree.io/f/XXXX"`.
3. Si el plan admite adjuntos: `envio.admiteArchivos = true`.
El taller recibe cada solicitud por correo con el resumen ordenado.

### Panel del taller con datos reales
El `admin.html` actual es una demostración: guarda en el navegador y el PIN no es seguridad real. Para usarlo de verdad hace falta un backend con acceso autenticado, por ejemplo:
- Supabase (base de datos + login) o Firebase.
- Una tabla `solicitudes` con los campos que genera `construirSolicitud()` en `app.js`.
- Cambiar `enviarSolicitud()` en `integrations.js` para insertar ahí, y leer desde `admin.js` con usuario y contraseña.
Nunca publiques solicitudes de clientes en una página pública.

### Identificación por matrícula
No hay ninguna fuente pública y gratuita. Hay APIs de pago (proveedores de datos de vehículos para talleres o recambistas). No pongas su clave en `config.js`: se vería en el navegador. Monta un pequeño servidor intermedio (función de Netlify/Vercel/Cloudflare) que llame a la API con la clave guardada como variable de entorno y devuelva:

```json
{ "marca": "", "modelo": "", "version": "", "anio": "", "motor": "", "combustible": "" }
```

y pon su URL en `matricula.apiUrl`. Hasta entonces el cliente rellena los datos a mano.

### Mapa
Se carga desde Google Maps solo cuando el visitante pulsa “Mostrar mapa” (no necesita clave).

## Privacidad
- Casilla de consentimiento obligatoria antes de enviar.
- La matrícula y los datos de contacto se borran del formulario al enviar.
- En modo demostración las solicitudes se guardan solo en el navegador de quien las hace.
