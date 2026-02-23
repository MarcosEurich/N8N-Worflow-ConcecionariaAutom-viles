\# 🚗 Bot de Cotización y Financiación de Autos con IA

Este workflow de n8n automatiza la atención al cliente para una concesionaria. Utiliza Inteligencia Artificial para clasificar consultas (Ventas vs. Reclamos), busca vehículos en una base de datos (Google Sheets), calcula opciones de financiación con diferentes bancos y envía un presupuesto detallado por correo electrónico.

\#\# 📋 Prerrequisitos

Para ejecutar este proyecto necesitas:  
1\.  \*\*n8n\*\* (Versión Desktop o Cloud).  
2\.  \*\*Cuenta de Google\*\* (Para Sheets y Gmail).  
3\.  \*\*API Key Ollama\*\*.  
4\.  \*\*Credenciales configuradas en n8n:\*\*  
    \* Google Sheets OAuth2 API.  
    \* Gmail OAuth2 API.  
    \* Ollama 3.2

\#\# ⚙️ Configuración del Entorno

\#\#\# 1\. Base de Datos (Google Sheets)  
Crea una hoja de cálculo nueva y asegúrate de que tenga \*\*exactamente\*\* estas columnas en la primera fila:

| ID\_Auto | Marca      | Modelo                 | Valor      | Tasa | Banco  |  
|---------|------------|------------------------|------------|------|--------|  
| 1       | Volkswagen | Volkswagen Vento tsi   | 58000000   | 55%  | Nacion |  
| 2       | Volkswagen | Volkswagen Vento tsi   | 58000000   | 59%  | BBVA   |  
| 3       | Toyota     | Toyota Corolla Xei Cvt | 14700000   | 63%  | Nacion |

\> \*\*Nota:\*\* Es importante respetar los nombres de las columnas para que el nodo "Calculadora" las lea correctamente.

\#\#\# 2\. Importación del Workflow  
1\.  Descarga el archivo \`.json\` de este proyecto.  
2\.  En n8n, ve a \*\*Workflows\*\* \> \*\*Import from File\*\*.  
3\.  Selecciona el archivo JSON.

\#\#\# 3\. Conexión de Credenciales  
Una vez importado, verás nodos con advertencias rojas. Haz doble clic en ellos y selecciona tus credenciales:  
\* \*\*Nodo "Buscar Autos"\*\*: Selecciona tu credencial de Google Sheets y el archivo que creaste en el paso 1\.  
\* \*\*Nodo "Gmail Trigger" / "Enviar Email"\*\*: Selecciona tu credencial de Gmail.  
\* \*\*Nodo "AI Agent"\*\*: Selecciona tu credencial de Ollama.

\---

\#\# 🚀 Pasos para Ejecutar (Testing)

El flujo está diseñado para activarse automáticamente, pero para pruebas manuales reproducibles, sigue estos pasos:

\#\#\# Prueba A: Consulta de Venta (Happy Path)  
1\.  Abre el nodo inicial (Trigger o el nodo de "Formulario de Consulta").  
2\.  Modifica el input de prueba (JSON) con este mensaje:  
    \`\`\`json  
    {  
      "Consulta": "Hola, estoy buscando precio y financiación para un Toyota Corolla",  
      "email": "tu\_email\_para\_pruebas@gmail.com",  
      "nombre": "Tester"  
    }  
    \`\`\`  
3\.  Haz clic en \*\*Execute Workflow\*\*.  
4\.  \*\*Resultado esperado:\*\*  
    \* La IA clasifica como "Consulta" y tipo "Sedan".  
    \* El buscador encuentra los datos del Corolla en el Sheet.  
    \* Recibes un \*\*correo electrónico único\*\* con una tabla comparativa de financiación.

\#\#\# Prueba B: Consulta de Reclamo  
1\.  Modifica el input de prueba con este mensaje:  
    \`\`\`json  
    {  
      "Consulta": "Hace dos meses pagué la licitación y no me entregan la unidad. Es una vergüenza.",  
      "email": "cliente@test.com"  
    }  
    \`\`\`  
2\.  Haz clic en \*\*Execute Workflow\*\*.  
3\.  \*\*Resultado esperado:\*\*  
    \* La IA clasifica como "Reclamo".  
    \* El flujo toma la rama inferior ("Reclamos").  
    \* Se genera un ticket o respuesta automática de contención (según tu configuración).

\---

\#\# 🧠 Lógica de los Nodos Clave

\* \*\*AI Agent:\*\* Clasifica la intención. Usa un prompt avanzado para distinguir clientes nuevos de clientes con problemas (post-venta).  
\* \*\*Filtro Inteligente (Code Node):\*\* No usa coincidencia exacta. Busca palabras clave (ej: "Toyota" \+ "Corolla") para encontrar el auto en la base de datos incluso si el cliente no escribe el nombre completo.  
\* \*\*Calculadora de Presupuestos:\*\* Recupera el precio del auto y cruza datos con las tasas de los bancos para calcular la cuota mensual (Sistema Francés simplificado).  
\* \*\*Resumen Calculadora:\*\* Agrupa todas las opciones financieras en un solo bloque HTML para enviar un único correo al cliente.

\#\# ⚠️ Solución de Problemas Comunes

\* \*\*Error "No se encontraron autos":\*\* Verifica que en tu Google Sheet los nombres no tengan espacios extra al final. El "Filtro Inteligente" ayuda, pero la base de datos debe estar limpia.  
\* \*\*Llega un mail por cada auto:\*\* Asegúrate de que el nodo "Resumen Calculadora" tenga activada la opción \*\*"Execute Once"\*\* en sus Settings.  
\* \*\*La fecha sale como código:\*\* En el nodo de Google Sheets, cambia el campo fecha de "Fixed" a "Expression" y usa \`{{ $now }}\`.

\---  
