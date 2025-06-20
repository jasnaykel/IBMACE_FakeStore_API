IBM ACE - Servicio REST-SOAP para Información de Países (Ejercicio 4)
📌 Descripción

Esta aplicación funciona como un adaptador REST que se conecta al servicio SOAP gratuito CountryInfoService. Permite consultar información de países (como monedas, idiomas y capitales etc) usando peticiones REST simples y recibir las respuestas en formato JSON, sin tener que lidiar con la complejidad del protocolo SOAP. El objetivo es facilitar a empresas y desarrolladores el acceso a datos internacionales, transformando automáticamente las solicitudes REST en llamadas SOAP y procesando las respuestas XML para entregarlas de manera sencilla y estructurada

## Problema que resuelve
Trabajar con servicios SOAP suele ser complicado, especialmente cuando solo necesitas datos básicos de países para aplicaciones modernas. Esta aplicación elimina esa complejidad actuando como intermediario: envías una petición REST sencilla y recibes la información en JSON, sin preocuparte por el manejo de XML, namespaces o la lógica SOAP.

Esto resulta especialmente útil para empresas y desarrolladores que requieren información confiable sobre países, monedas, idiomas y capitales de forma rápida y sencilla.

**Casos de uso principales:**
- **Validación de datos**: Verificar códigos de países en formularios de registro
- **Sistemas de facturación**: Obtener monedas locales para transacciones internacionales  
- **Reportes y análisis**: Generar análisis por región con datos consistentes
- **Configuración automática**: Establecer parámetros regionales según la ubicación 

La principal ventaja es que puedes integrar información internacional en tus sistemas sin lidiar con la complejidad técnica de los servicios SOAP tradicionales.


🚀 Características principales

✅ Transformación bidireccional REST-SOAP-REST
- Convierte peticiones  REST/JSON a llamadas SOAP/XML
- Transforma respuestas SOAP/XML a JSON estructurado
- Maneja múltiples operaciones SOAP desde una sola API REST
✅ Registro detallado de logs
- Logs detallados en cada etapa del proceso
✅ Manejo Inteligente de Datos
- Procesa arrays XML complejos
- Extrae información específica según el tipo de consulta
- Genera respuestas JSON consistentes y estructuradas
✅ Validación Robusta
- Verifica estructura de requests de entrada
- Maneja errores del servicio externo automáticamente

### Componentes del Flujo 
- **hub_message.msgflow**: Flujo principal con todos los nodos
- **CountryInfoService.wsdl**: Definición del servicio SOAP
- **CountryInfoService.xsd**: # Esquemas XML
- **HTTP Input**: Recibe requests REST en `/path/http_service_hub`
- **Http_Message_Compute**: Valida estructura JSON de entrada
- **CategoryToURLMapper**: Mapea requestType a operación SOAP
- **ConfigureHTTPRequest**: Construye envelope SOAP con namespaces
- **hub_message_Filter**: Filtro de operaciones
- **CountryInfoService**: Ejecuta llamada SOAP al servicio externo
- **TransformSOAPtoJSON**: Convierte respuesta XML a JSON estructurado


🔧 Instalación y Uso

1️⃣ Clonar el repositorio
git clone https://github.com/jasnaykel/IBMACE_FakeStore_API.git
cd IBMACE_FakeStore_API

2️⃣ Configurar en IBM ACE

Abre IBM App Connect Enterprise Toolkit
Importa el proyecto en tu espacio de trabajo
Verifica que todos los archivos se hayan importado correctamente
Asegúrate de que la carpeta para logs exista

3️⃣ Desplegar el servicio

Crea un archivo BAR (clic derecho en el proyecto → Export → BAR file)
Despliega el archivo BAR en tu servidor de integración

4️⃣ Probar el servicio

Usando Postman:

Crea una nueva solicitud POST
URL: http://localhost:7800/path/http_service_hub
En la pestaña Body, selecciona "raw" y "JSON"
Ingresa uno de los ejemplos JSON anteriores
Haz clic en "Send"
📊 Respuestas esperadas
Ejemplo 1: Consulta por categoría
Crea una nueva solicitud POST
URL: http://localhost:7800/path/http_service_hub
En la pestaña Body, selecciona "raw" y "JSON"
Ingresa uno de los ejemplos JSON anteriores
Haz clic en "Send"

## Funcionalidades del Sistema soportadas

📊 Respuesta esperada
### ✅ **Caso 1: información de monedas**
**Envías:**
Para información de país (countryInfo):
```json
{
  "requestType": "currencyInfo",
  "parameters": {
    "currencyCode": "CUP"
  }
}
```

**Recibes:**
```json
{
    "responseInfo": {
        "status": "success",
        "timestamp": "2025-05-28 16:39:07.368476"
    },
    "currencyData": {
        "code": "CUP",
        "name": "Pesos"
    }
}
```

### ✅ **Caso 2: Lista de idiomas**
**Envías:**
```json
{
  "requestType": "countryLanguage",
  "parameters": {}
}
```

**Recibes:**
```json
{
    "responseInfo": {
        "status": "success",
        "timestamp": "2025-05-28 08:00:56.713934"
    },
    "languages": {
        "items": {
            "code": "abk",
            "name": "Abkhazian"
        },
        "items": {
            "code": "ace",
            "name": "Achinese"
        },
        "items": {
            "code": "ach",
            "name": "Acoli"
        },
        "items": {
            "code": "ada",
            "name": "Adangme"
        },
        "items": {
            "code": "aar",
            "name": "Afar"
        },
]
```

### ✅ **Caso 3: Capital de un país en especifico
**
**Envías:**
```json
{
    "requestType": "capitalCity",
    "parameters": {
      "countryCode": "US"
  }
}
```

**Recibes:**
```json
{
    "responseInfo": {
        "status": "success",
        "timestamp": "2025-05-28 16:29:09.029450"
    },
    "capitalInfo": {
        "countryCode": "US",
        "capitalName": "Washington"
    }
}
```

### ✅ **Caso 4: Listado de paises por código**
**Envías:**

```json
{
    "requestType": "listCountries",
    "parameters": {
      "searchType": "byCode"
  }
}
```

**Recibes:**
```json
{
    "responseInfo": {
        "status": "success",
        "timestamp": "2025-05-28 08:04:36.887031"
    },
    "countries": {
        "items": {
            "code": "AD",
            "name": "Andorra"
        },
        "items": {
            "code": "AE",
            "name": "United Arab Emirates"
        },
        "items": {
            "code": "AF",
            "name": "Afghanistan"
        },
        "items": {
            "code": "AG",
            "name": "Antigua & Barbuda"
        }}
```
### ✅ **Caso 5: Listado de paises por nombre**
**Envías:**

```json
{
    "requestType": "listCountries",
    "parameters": {
      "searchType": "byName"    
  }
}
```

**Recibes:**
```json
{
    "responseInfo": {
        "status": "success",
        "timestamp": "2025-05-28 08:05:16.263784"
    },
    "countries": {
        "items": {
            "code": "AX",
            "name": "Åland Islands"
        },
        "items": {
            "code": "AF",
            "name": "Afghanistan"
        },
        "items": {
            "code": "AL",
            "name": "Albania"
        },
        "items": {
            "code": "DZ",
            "name": "Algeria"
        },
        "items": {
            "code": "AS",
            "name": "American Samoa"
        },
        "items": {
            "code": "AD",
            "name": "Andorra"
        }}
```
📊 Estructura de Respuesta de Error:
```json Vacio
{
    "error": {
        "code": 500,
        "status": "Internal Server Error",
        "detail": "BIP2230E: Se ha detectado un error mientras se procesaba un mensaje en el nodo 'hub_message.Trace'. \nBIP5705E: Se han producido errores de análisis de JSON. \nBIP5702E: Se ha producido un error de análisis de JSON. Se ha llegado al final de la corriente de bits antes de que el mensaje estuviera completo.  El analizador JSON esperaba encontrar uno de los siguientes caracteres o tipos: "
    ]", "
}", ",
", ",
"  El código de error interno es 0x0000018C. "
}
}
```
```
📊 Estructura de Respuesta de Error:
```**Envías:**

```json
{
  "Data": {
    "requestType": "listCountries",
    "parameters": {
      "searchType": "byName"
    }
  }
}
```

**Recibes:**
{
    "error": {
        "code": 504,
        "status": "Internal Server Error",
        "detail": "BIP2230E: Se ha detectado un error mientras se procesaba un mensaje en el nodo 'hub_message.Trace'. \nBIP5705E: Se han producido errores de análisis de JSON. \nBIP5702E: Se ha producido un error de análisis de JSON. Se ha llegado al final de la corriente de bits antes de que el mensaje estuviera completo.  El analizador JSON esperaba encontrar uno de los siguientes caracteres o tipos: "
    ]", "
}", ",
", ",
"  El código de error interno es 0x0000018C. "
}
}