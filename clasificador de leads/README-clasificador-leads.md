# Sistema Clasificador de Leads - Escuela Canina

## Descripción

Sistema completo de automatización para la gestión de leads y clientes de una escuela canina. El sistema utiliza agentes de IA conversacionales (Marcela) para clasificar leads, agendar citas, gestionar cursos grupales y personalizados, y automatizar todo el proceso de admisión y seguimiento.

## Arquitectura del Sistema

El sistema está compuesto por múltiples flujos de n8n que trabajan en conjunto:

### Componentes Principales

1. **Agentes de IA Conversacionales**:
   - **System_MARcela Agents**: Agente principal de admisión que clasifica leads
   - **marcela_grupales Agents**: Agente para gestión de cursos grupales
   - **marcela_personalizadas Agents**: Agente para casos personalizados

2. **Flujos de Procesamiento**:
   - **fase 00 entrada de datos**: Entrada inicial de datos de clientes
   - **registro cliente marcela 1**: Registro de información del cliente
   - **solicitud a flowise**: Comunicación con Flowise para agentes de IA
   - **envio_repuesta**: Sistema de evaluación de calidad de respuestas

3. **Flujos de Documentación**:
   - **IA ibague creacion documento grupales**: Generación de documentos para cursos grupales
   - **Envio documento**: Envío automatizado de documentos

4. **Flujos de Gestión**:
   - **SOLICITUD AGENDAR y BUSCAR CITA**: Sistema de agendamiento de citas
   - **cursos_grupales_disponibles**: Gestión de disponibilidad de cursos
   - **transformacion de datos**: Transformación y limpieza de datos

## Funcionalidades Principales

### 1. Clasificación Automática de Leads

El agente **Marcela** (System_MARcela Agents) realiza un cuestionario estructurado para determinar si un cliente es apto para:
- **Proceso Grupales**: Para perros que socializan bien
- **Proceso Personalizadas**: Para perros que requieren atención individualizada

### 2. Gestión de Cursos Grupales

El agente **marcela_grupales**:
- Consulta disponibilidad de cursos
- Presenta opciones al cliente
- Gestiona reservas y pagos
- Maneja negociación de precios y promociones

### 3. Gestión de Casos Personalizados

El agente **marcela_personalizadas**:
- Explica el proceso de diagnóstico personalizado
- Consulta disponibilidad de citas
- Gestiona pagos de diagnósticos
- Registra direcciones para visitas domiciliarias

### 4. Generación Automática de Documentos

- Crea documentos personalizados con información del cliente
- Incluye precios, horarios, fechas y resultados esperados
- Envía documentos automáticamente por WhatsApp

## Configuración en n8n

### Requisitos Previos

1. **Credenciales Necesarias**:
   - **OpenAI API**: Para los agentes de IA conversacionales
   - **Google Sheets OAuth2**: Para almacenamiento de datos
   - **Flowise API**: Para comunicación con agentes de IA (si se usa)
   - **WhatsApp Business API**: Para envío de mensajes (si se usa)

### Pasos de Configuración

1. **Importar Flujos**:
   - Importa todos los archivos JSON en n8n
   - Organiza los flujos por funcionalidad

2. **Configurar Agentes de IA**:
   - Abre cada archivo de agente (System_MARcela, marcela_grupales, marcela_personalizadas)
   - Configura las credenciales de OpenAI
   - Personaliza los prompts según tus necesidades
   - Configura las herramientas (tablas de Google Sheets, funciones de búsqueda, etc.)

3. **Configurar Google Sheets**:
   - Crea las hojas de cálculo necesarias:
     - Tabla de clientes/leads
     - Tabla de cursos disponibles
     - Tabla de preinscripciones
     - Tabla de citas agendadas
   - Configura las credenciales de Google Sheets OAuth2 en cada nodo
   - Actualiza los `documentId` y `sheetName` en cada flujo

4. **Configurar Flowise** (si aplica):
   - Configura la URL de tu instancia de Flowise
   - Configura las credenciales de API
   - Verifica que los agentes estén desplegados en Flowise

5. **Configurar WhatsApp** (si aplica):
   - Configura las credenciales de WhatsApp Business API
   - O configura webhooks según tu proveedor

## Estructura de Datos

### Tabla de Clientes/Leads

Columnas principales:
- `nombre_tutor`: Nombre del dueño del perro
- `nombre_perro`: Nombre del perro
- `edad_perro`: Edad del perro
- `raza`: Raza del perro
- `comportamiento_urgente`: Urgencia del caso
- `comportamiento_mejorar`: Comportamiento a mejorar
- `disponibilidad_para_curso`: Disponibilidad del cliente
- `socializa_con_otros_perros`: Si/No
- `numero_de_telefono`: Teléfono de contacto
- `fecha_registro`: Fecha y hora de registro
- `tipo_proceso`: "grupales" o "personalizadas"

### Tabla de Cursos Disponibles

Columnas principales:
- `nombre_lugar`: Nombre del lugar del curso
- `fecha_inicio`: Fecha de inicio
- `hora_inicio`: Hora de inicio
- `cupos_disponibles`: Número de cupos
- `estado`: "Activo" o "Inactivo"
- `cliente1`, `cliente2`, etc.: Números de teléfono de preinscritos

### Tabla de Citas

Columnas principales:
- `fecha`: Fecha de la cita
- `hora`: Hora de la cita
- `numero_telefono`: Teléfono del cliente
- `nombre_cliente`: Nombre del cliente
- `nombre_perro`: Nombre del perro
- `direccion`: Dirección para visita
- `estado_pago`: Estado del pago

## Flujo de Trabajo Completo

### Proceso de Admisión

1. Cliente inicia conversación → **System_MARcela Agents**
2. Agente realiza cuestionario estructurado
3. Cliente es clasificado como "grupales" o "personalizadas"
4. Datos se guardan en Google Sheets
5. Si es "grupales" → Se activa **marcela_grupales**
6. Si es "personalizadas" → Se activa **marcela_personalizadas**

### Proceso de Cursos Grupales

1. **marcela_grupales** consulta cursos disponibles
2. Presenta opciones al cliente
3. Cliente selecciona opción
4. Se registra preinscripción
5. Se gestiona pago (completo o separación)
6. Se confirma reserva

### Proceso de Casos Personalizados

1. **marcela_personalizadas** explica diagnóstico
2. Cliente acepta agendar
3. Se consulta disponibilidad
4. Se gestiona pago de diagnóstico (80.000)
5. Se crea cita
6. Se registra dirección
7. Se confirma cita

## Personalización

### Modificar Prompts de Agentes

Cada agente tiene un `systemMessage` extenso que controla:
- El tono y personalidad
- El flujo de conversación
- Las reglas de negocio
- El manejo de objeciones

### Ajustar Precios y Promociones

Los precios y promociones están hardcodeados en los prompts:
- Precio normal curso grupal: 740.000
- Precio promocional: 549.000
- Separación: 70.000
- Diagnóstico personalizado: 80.000

### Configurar Medios de Pago

Los medios de pago están en los prompts:
- Nequi: 3175291962
- Daviplata: 3213377157
- Llave Bancolombia: 1030571252

## Notas Importantes

- **Memoria de Conversación**: Los agentes mantienen contexto durante la conversación
- **Validación de Calidad**: El flujo `envio_repuesta` valida la calidad de las respuestas del agente
- **Manejo de Errores**: Los flujos incluyen manejo de errores para no interrumpir el proceso
- **Rate Limiting**: Se incluyen delays para evitar sobrecargar APIs
- **Privacidad**: Los números de teléfono se usan como identificadores únicos

## Troubleshooting

- **Agente no responde**: Verifica que Flowise esté activo y que las credenciales sean correctas
- **No se guardan datos**: Verifica permisos de Google Sheets y que las columnas existan
- **Errores en pagos**: Verifica que los números de pago estén actualizados en los prompts
- **Problemas con disponibilidad**: Verifica que la tabla de cursos tenga datos actualizados

## Mejores Prácticas

1. **Monitoreo Regular**: Revisa las conversaciones y ajusta los prompts según sea necesario
2. **Actualización de Datos**: Mantén las tablas de Google Sheets actualizadas
3. **Testing**: Prueba los flujos antes de activarlos en producción
4. **Backup**: Realiza backups regulares de los flujos y datos

