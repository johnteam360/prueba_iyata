# Sistema de Envío de Correos en Frío (Cold Email)

## Descripción

Este sistema automatizado de n8n está diseñado para realizar prospección de clientes mediante búsquedas en Google Maps, extracción de información de sitios web y envío automatizado de correos electrónicos personalizados. El sistema consta de dos flujos principales que trabajan en conjunto:

1. **cold email fase 00**: Extrae información de negocios desde Google Maps y sus sitios web
2. **envio de email cold email**: Genera y envía correos personalizados usando IA

## Componentes del Sistema

### Flujo 1: Cold Email Fase 00

Este flujo se encarga de la recolección de datos:

1. **Búsqueda en Google Maps**: Utiliza SerpAPI para buscar negocios locales según criterios específicos
2. **Extracción de Datos**: Obtiene información de cada negocio (título, tipo, teléfono, sitio web, dirección)
3. **Scraping de Sitios Web**: Accede a cada sitio web y extrae correos electrónicos mediante regex
4. **Almacenamiento**: Guarda toda la información en Google Sheets para procesamiento posterior

### Flujo 2: Envío de Email Cold Email

Este flujo procesa los datos recolectados y envía correos:

1. **Trigger Automático**: Se activa cuando se agrega una nueva fila en Google Sheets
2. **Filtrado**: Solo procesa registros con estado "en_espera"
3. **Procesamiento de Emails**: Convierte el array de emails de string JSON a array de JavaScript
4. **Generación de Contenido con IA**: Utiliza un agente de IA (OpenAI/Gemini) para:
   - Seleccionar el mejor email de contacto
   - Generar un asunto personalizado
   - Redactar el cuerpo del correo personalizado
5. **Formateo HTML**: Convierte el texto plano en un email HTML profesional
6. **Envío**: Envía el correo a través de Gmail
7. **Actualización**: Marca el registro como "enviado" en Google Sheets

## Configuración en n8n

### Requisitos Previos

1. **Credenciales Necesarias**:
   - **SerpAPI**: Cuenta activa de SerpAPI con API key
   - **Google Sheets OAuth2**: Para leer y escribir en las hojas de cálculo
   - **Google Sheets Trigger OAuth2**: Para el trigger automático
   - **OpenAI API**: Cuenta con API key (opcional, también se puede usar Gemini)
   - **Google Gemini API**: Cuenta con API key (opcional, alternativa a OpenAI)
   - **Gmail OAuth2**: Cuenta de Gmail para envío de correos

### Configuración del Flujo 1: Cold Email Fase 00

1. **Importar el flujo**:
   - Importa `cold email fase 00.json` en n8n

2. **Configurar SerpAPI**:
   - Abre el nodo "Google_maps search"
   - Configura las credenciales de SerpAPI
   - Personaliza la búsqueda modificando el parámetro `q` (ej: "gestoría Madrid")
   - Ajusta la ubicación con `ll` (latitud, longitud, zoom)

3. **Configurar Google Sheets**:
   - Abre el nodo "Append row in sheet"
   - Configura las credenciales de Google Sheets OAuth2
   - Actualiza el `documentId` con el ID de tu hoja de cálculo de destino
   - Verifica que la hoja tenga las columnas: `email`, `ciudad_direccion`, `tipo`, `titulo`, `numero_tlf`, `sitioweb`, `estado`

4. **Ajustar Tiempo de Espera**:
   - El nodo "Wait" tiene un delay de 2 segundos entre requests para evitar sobrecargar los servidores
   - Puedes ajustar este tiempo según tus necesidades

### Configuración del Flujo 2: Envío de Email Cold Email

1. **Importar el flujo**:
   - Importa `envio de email cold email.json` en n8n

2. **Configurar Google Sheets Trigger**:
   - Abre el nodo "Google Sheets Trigger"
   - Configura las credenciales de Google Sheets Trigger OAuth2
   - Actualiza el `documentId` con el mismo ID de la hoja usada en el flujo 1
   - Configura la frecuencia de polling (por defecto: cada hora)

3. **Configurar Modelo de IA**:
   - Abre el nodo "OpenAI Chat Model" o "Google Gemini Chat Model"
   - Configura las credenciales correspondientes
   - Ajusta el modelo según tus preferencias (por defecto: "gpt-5.1")
   - Configura la temperatura (por defecto: 0.5)

4. **Configurar Gmail**:
   - Abre el nodo "Send a message"
   - Configura las credenciales de Gmail OAuth2
   - Verifica que la cuenta tenga permisos para enviar correos

5. **Personalizar Prompt del Agente de IA**:
   - Abre el nodo "AI Agent"
   - Revisa y personaliza el `systemMessage` según tus necesidades
   - El prompt actual está configurado para Johnteam.IA - ajústalo para tu empresa

6. **Configurar Template HTML**:
   - Abre el nodo "DocumentGenerator"
   - Personaliza el template HTML según tu marca
   - Ajusta colores, estilos y contenido

## Estructura de Datos

### Hoja de Cálculo de Datos

La hoja debe tener las siguientes columnas:

- `email`: Array de emails extraídos (almacenado como string JSON)
- `ciudad_direccion`: Dirección del negocio
- `tipo`: Tipo de negocio
- `titulo`: Nombre del negocio
- `numero_tlf`: Número de teléfono
- `sitioweb`: URL del sitio web
- `estado`: Estado del registro ("en_espera" o "enviado")
- `asunto`: Asunto del correo (se llena automáticamente)
- `row_number`: Número de fila (se llena automáticamente)

## Flujo de Trabajo Completo

1. **Ejecutar Flujo 1** (manual o programado):
   - Busca negocios en Google Maps
   - Extrae información y correos de sus sitios web
   - Guarda en Google Sheets con estado "en_espera"

2. **Flujo 2 se activa automáticamente**:
   - Detecta nuevos registros con estado "en_espera"
   - Procesa cada registro
   - Genera correo personalizado con IA
   - Envía el correo
   - Actualiza el estado a "enviado"

## Personalización

### Modificar la Búsqueda

En el nodo "Google_maps search", puedes cambiar:
- `q`: Término de búsqueda (ej: "restaurantes Bogotá")
- `ll`: Coordenadas y zoom (ej: "@4.7110,-74.0721,12z")

### Ajustar el Prompt de IA

El `systemMessage` en el nodo "AI Agent" controla:
- El tono del correo
- La información sobre tu empresa
- Los servicios que ofreces
- La estructura del correo

### Personalizar Template HTML

El template en "DocumentGenerator" controla:
- El diseño visual del correo
- Los colores y estilos
- El contenido estático (footer, botones, etc.)

## Notas Importantes

- **Rate Limiting**: El flujo incluye delays para evitar sobrecargar APIs y servidores
- **Validación de Emails**: El sistema valida que existan emails antes de procesar
- **Manejo de Errores**: Los nodos HTTP Request tienen `onError: continueRegularOutput` para no detener el flujo completo
- **Memoria del Agente**: El agente de IA usa memoria basada en el sitio web para mantener contexto
- **Formato de Salida**: El agente usa un parser estructurado para garantizar formato consistente

## Troubleshooting

- **No se envían correos**: Verifica que las credenciales de Gmail estén correctas y que la cuenta tenga permisos
- **No se extraen emails**: Verifica que los sitios web sean accesibles y que el regex de extracción funcione
- **Errores de IA**: Verifica que las credenciales de OpenAI/Gemini estén configuradas y que tengas créditos disponibles
- **Problemas con Google Sheets**: Asegúrate de que las credenciales tengan permisos de lectura y escritura

