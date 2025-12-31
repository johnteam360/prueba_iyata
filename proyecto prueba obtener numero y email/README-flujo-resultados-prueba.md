# Flujo de Resultados de Prueba de Google Sheet

## 📊 Dashboard con Resultados Finales

**Enlace al Dashboard**: [Ver Dashboard de Resultados](https://docs.google.com/spreadsheets/d/1lghGhMGTibgCa70nglYeeSWsJROk1hlgAkVCXrZSVdY/edit?gid=1750655662#gid=1750655662)

Este es el enlace directo a la hoja de cálculo Dashboard donde se registran todos los resultados finales de las validaciones realizadas por el flujo.

## Descripción

Este flujo de n8n está diseñado para analizar y validar datos de contactos almacenados en Google Sheets. El sistema extrae información de una hoja de cálculo y realiza validaciones exhaustivas sobre emails y números de teléfono, generando métricas detalladas que se registran en un dashboard.

## Funcionalidades

El flujo realiza las siguientes validaciones y conteos:

1. **Emails válidos**: Cuenta los correos electrónicos que cumplen con el formato estándar (contienen @, tienen dominio válido, sin espacios).

2. **Emails inválidos**: Identifica correos que no cumplen con los criterios de validación.

3. **Teléfonos colombianos válidos**: Cuenta números telefónicos colombianos que:
   - Tienen 10 dígitos
   - Comienzan con "3"
   - Están asociados a nacionalidad "es_CO"

4. **Teléfonos extranjeros válidos**: Identifica números telefónicos de otros países que:
   - Tienen entre 7 y 15 dígitos
   - No son números colombianos
   - Están asociados a nacionalidad diferente a "es_CO"

5. **Teléfonos inválidos totales**: Cuenta todos los números telefónicos que no cumplen con ninguno de los criterios de validación anteriores.

6. **Números colombianos en nacionalidad extranjera**: Detecta casos donde un número telefónico tiene formato colombiano válido pero está asociado a una nacionalidad extranjera (posible inconsistencia de datos).

## Estructura del Flujo

1. **Trigger Manual**: Inicia el flujo manualmente
2. **Get row(s) in sheet**: Obtiene todos los registros de la hoja de cálculo fuente
3. **Nodos de Conteo (JavaScript)**: Seis nodos Code que realizan las validaciones en paralelo
4. **Actualización de Dashboard**: Cada nodo de conteo actualiza su métrica correspondiente en la hoja "Dashboard" usando `appendOrUpdate`

## Configuración en n8n

### Requisitos Previos

1. **Credenciales de Google Sheets**:
   - Necesitas configurar una credencial OAuth2 de Google Sheets en n8n
   - La credencial debe tener acceso de lectura a la hoja fuente y de escritura a la hoja Dashboard

### Pasos de Configuración

1. **Importar el flujo**:
   - Abre n8n y ve a "Workflows"
   - Haz clic en "Import from File" o "Import from URL"
   - Selecciona el archivo `flujo de resultados de prueba de google sheet (javascript).json`

2. **Configurar Google Sheets**:
   - Abre el nodo "Get row(s) in sheet"
   - Configura las credenciales de Google Sheets OAuth2
   - Actualiza el `documentId` con el ID de tu hoja de cálculo fuente
   - Actualiza el `sheetName` con el nombre o ID de la pestaña que contiene los datos

3. **Configurar Dashboard**:
   - Para cada nodo de actualización (Emails válidos, Emails inválidos, etc.):
     - Abre el nodo
     - Configura las credenciales de Google Sheets
     - Actualiza el `documentId` con el ID de tu hoja de cálculo de Dashboard
     - Actualiza el `sheetName` con el nombre de la pestaña "Dashboard"
     - Verifica que la columna "METRICAS" exista en tu Dashboard

4. **Estructura del Dashboard**:
   - Asegúrate de que tu hoja Dashboard tenga las siguientes columnas:
     - `METRICAS`: Nombre de la métrica (ej: "Emails válidos")
     - `RESULTADO FINAL`: Valor numérico del conteo
   - El flujo usa `appendOrUpdate` con `METRICAS` como columna de coincidencia

5. **Estructura de la Hoja Fuente**:
   - La hoja fuente debe contener las siguientes columnas:
     - `email`: Dirección de correo electrónico
     - `telefono`: Número de teléfono
     - `nacionalidad`: Código de nacionalidad (ej: "es_CO" para Colombia)

## Ejecución

1. Una vez configurado, puedes ejecutar el flujo manualmente haciendo clic en "Execute workflow"
2. El flujo leerá todos los registros de la hoja fuente
3. Realizará las validaciones en paralelo
4. Actualizará automáticamente el Dashboard con los resultados

## Notas Importantes

- El flujo procesa todos los registros de la hoja en una sola ejecución
- Las validaciones se ejecutan en paralelo para mayor eficiencia
- Los resultados se actualizan en el Dashboard usando `appendOrUpdate`, por lo que si ejecutas el flujo múltiples veces, los valores se actualizarán en lugar de duplicarse
- Asegúrate de que las credenciales de Google Sheets tengan los permisos necesarios (lectura y escritura)

