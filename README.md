IBM ACE - Servicio REST-SOAP para Información de Países (Ejercicio 4)
📌 Descripción

Este proyecto implementa un servicio de integración avanzado utilizando IBM App Connect Enterprise (ACE) que actúa como puente entre clientes REST y un servicio SOAP de información de países. El servicio recibe solicitudes REST, las transforma en llamadas SOAP al servicio CountryInfoService, procesa las respuestas SOAP y devuelve datos estructurados en formato JSON.

🚀 Características principales

✅ Transformación bidireccional REST-SOAP-REST
✅ Consumo de servicio SOAP público (CountryInfoService)
✅ Múltiples operaciones de consulta de países
✅ Manejo robusto de errores y excepciones
✅ Validación de mensajes de entrada y salida
✅ Transformación estructurada de datos XML a JSON


🔄 Transformaciones Implementadas
1. Transformación de REST a SOAP
El servicio recibe solicitudes REST en formato JSON y las transforma en mensajes SOAP estructurados:

Validación de Estructura: Verifica que el mensaje JSON contenga los campos requestType y parameters

Mapeo de Operaciones: Convierte el requestType JSON en la operación SOAP correspondiente:

countryInfo → FullCountryInfo o CountryISOCode (según searchType)
listCountries → ListOfCountryNamesByCode o ListOfCountryNamesByName
currencyInfo → CurrencyName
countryLanguage → ListOfLanguagesByName
capitalCity → CapitalCity
Construcción de Sobre SOAP: Genera dinámicamente el sobre SOAP con los espacios de nombres correctos:

<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns="http://www.oorsprong.org/websamples.countryinfo">
  <soapenv:Body>
    <!-- Operación específica con parámetros -->
  </soapenv:Body>
</soapenv:Envelope>
2. Transformación de SOAP a JSON
El servicio procesa las respuestas SOAP y las transforma en estructuras JSON amigables:

Para información de país (countryInfo):
Extrae datos básicos (código, nombre, capital)
Extrae detalles (código telefónico, continente, moneda, bandera)
Extrae información de idioma si está disponible
Estructura todo en un JSON jerárquico y descriptivo
Para lista de países (listCountries):
Procesa arrays de países desde la respuesta SOAP
Extrae código y nombre de cada país
Genera un contador de países
Estructura los datos en un formato JSON con metadatos
Para información de moneda (currencyInfo):
Extrae el código y nombre de la moneda
Estructura los datos en un formato JSON simple y descriptivo
Para información de idiomas (countryLanguage):
Procesa el array de idiomas desde la respuesta SOAP
Extrae código y nombre de cada idioma
Genera un contador de idiomas
Estructura los datos en un formato JSON con metadatos
Para capital de país (capitalCity):
Extrae el nombre de la capital
Incluye el código del país consultado
Estructura los datos en un formato JSON simple
3. Manejo de Estructuras Alternativas
El código implementa un manejo robusto de diferentes estructuras SOAP:

Verifica la existencia de datos en el dominio SOAP
Si no los encuentra, busca en el dominio XMLNSC
Proporciona información de depuración cuando no encuentra la estructura esperada
4. Transformación de Errores
El servicio transforma errores en respuestas JSON estructuradas:

Errores de validación (400)
Errores de recursos no encontrados (404)
Errores internos del servidor (500)
Errores de timeout (504)
Cada respuesta de error incluye:

Código de estado HTTP
Mensaje descriptivo
Detalles específicos del error
Timestamp
📊 Ejemplos de Transformaciones
Entrada REST para información de país:
{
  "Data": {
    "requestType": "countryInfo",
    "parameters": {
      "searchType": "byCode",
      "countryCode": "ES"
    }
  }
}

Transformación a SOAP:
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns="http://www.oorsprong.org/websamples.countryinfo">
  <soapenv:Body>
    <ns:FullCountryInfo>
      <ns:sCountryISOCode>ES</ns:sCountryISOCode>
    </ns:FullCountryInfo>
  </soapenv:Body>
</soapenv:Envelope>

Respuesta SOAP:
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <m:FullCountryInfoResponse xmlns:m="http://www.oorsprong.org/websamples.countryinfo">
      <m:FullCountryInfoResult>
        <m:sISOCode>ES</m:sISOCode>
        <m:sName>Spain</m:sName>
        <m:sCapitalCity>Madrid</m:sCapitalCity>
        <m:sPhoneCode>34</m:sPhoneCode>
        <m:sContinentCode>EU</m:sContinentCode>
        <m:sCurrencyISOCode>EUR</m:sCurrencyISOCode>
        <m:sCountryFlag>http://www.oorsprong.org/WebSamples.CountryInfo/Flags/Spain.jpg</m:sCountryFlag>
        <m:Languages>
          <m:tLanguage>
            <m:sISOCode>es</m:sISOCode>
            <m:sName>Spanish</m:sName>
          </m:tLanguage>
        </m:Languages>
      </m:FullCountryInfoResult>
    </m:FullCountryInfoResponse>
  </soap:Body>
</soap:Envelope>

Transformación a JSON:
{
  "responseInfo": {
    "status": "success",
    "timestamp": "2023-11-15T10:30:45.123Z"
  },
  "countryData": {
    "basicInfo": {
      "code": "ES",
      "name": "Spain",
      "capital": "Madrid"
    },
    "details": {
      "phoneCode": "34",
      "continent": {
        "code": "EU"
      },
      "currency": {
        "code": "EUR"
      },
      "flagUrl": "http://www.oorsprong.org/WebSamples.CountryInfo/Flags/Spain.jpg",
      "language": {
        "code": "es",
        "name": "Spanish"
      }
    }
  }
}

🏗️ Arquitectura del flujo

El servicio se compone de los siguientes componentes clave:

1️⃣ HTTP Input

Recibe solicitudes REST en el endpoint /path/http_service_hub
Configura un timeout de 15 segundos para el cliente
Procesa mensajes en formato JSON
2️⃣ Http_Message_Compute

Extrae y valida los datos de entrada
Almacena los parámetros en Environment para uso posterior
Verifica que el formato de los datos sea correcto
3️⃣ CategoryToURLMapper

Determina qué operación SOAP invocar basado en el requestType
Configura los parámetros específicos para cada tipo de solicitud
Prepara el entorno para la solicitud SOAP
4️⃣ ConfigureHTTPRequest

Construye el mensaje SOAP con los espacios de nombres correctos
Configura la operación SOAP específica (FullCountryInfo, ListOfCountryNamesByCode, etc.)
Establece los parámetros de la solicitud SOAP
5️⃣ CountryInfoService (SOAPRequest)

Realiza la llamada al servicio SOAP externo
Utiliza el WSDL para estructurar correctamente la solicitud
Maneja timeouts y errores de conexión
6️⃣ TransformSOAPtoJSON

Transforma la respuesta SOAP en formato JSON estructurado
Extrae datos relevantes según el tipo de solicitud
Maneja diferentes estructuras de respuesta SOAP
7️⃣ Error Handlers

Captura y procesa errores en diferentes puntos del flujo
Genera respuestas de error estructuradas en formato JSON
Asigna códigos HTTP apropiados según el tipo de error
📄 Estructura del código

El proyecto consta de varios archivos clave:

hub_message.msgflow: Define el flujo de mensajes completo con todos los nodos y conexiones.

Http_Message_Compute.esql: Contiene módulos para validar y procesar la solicitud REST inicial.

FilterProductsByPrice.esql: Contiene el módulo TransformSOAPtoJSON que transforma las respuestas SOAP en JSON.

ErrorHelper.esql: Proporciona funciones para el manejo estructurado de errores.

ServerErrorHandler.esql: Implementa el módulo ImprovedErrorHandler para procesar excepciones.

hub_message_Filter.esql: Contiene el módulo de filtro que determina el flujo de procesamiento.

CountryInfoService.wsdl: Define la interfaz del servicio SOAP externo.

CountryInfoService_InlineSchema1.xsd y CountryInfoService.xsd: Definen los esquemas XML para el servicio SOAP.

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
Usando Postman:

Crea una nueva solicitud GET
URL: http://localhost:7800/path/http_service_hub
En la pestaña Body, selecciona "raw" y "JSON"
Ingresa uno de los ejemplos JSON anteriores
Haz clic en "Send"
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


🔍 Tipos de Solicitudes Soportadas
El servicio soporta cinco tipos principales de solicitudes REST que se transforman en operaciones SOAP específicas:

🌍 1. Información de País (countryInfo)
Descripción: Obtiene información detallada de un país específico
Parámetros requeridos:
searchType: "byCode" o "byName"
countryCode: Código ISO del país (ej: "ES", "US") - solo para byCode
countryName: Nombre del país (ej: "Spain") - solo para byName
Operaciones SOAP: FullCountryInfo | CountryISOCode
Respuesta: Datos completos del país (código, nombre, capital, moneda, idioma, bandera)
📋 2. Lista de Países (listCountries)
Descripción: Lista todos los países disponibles
Parámetros requeridos:
searchType: "byCode" (ordenado por código) o "byName" (ordenado por nombre)
Operaciones SOAP: ListOfCountryNamesByCode | ListOfCountryNamesByName
Respuesta: Array de países con código y nombre
💰 3. Información de Moneda (currencyInfo)
Descripción: Obtiene información sobre una moneda específica
Parámetros requeridos:
currencyCode: Código ISO de moneda (ej: "EUR", "USD", "GBP")
Operación SOAP: CurrencyName
Respuesta: Código y nombre de la moneda
🗣️ 4. Lista de Idiomas (countryLanguage)
Descripción: Lista todos los idiomas disponibles en el sistema
Parámetros: No requiere parámetros adicionales
Operación SOAP: ListOfLanguagesByName
Respuesta: Array de idiomas con código ISO y nombre
🏛️ 5. Capital de País (capitalCity)
Descripción: Obtiene la capital de un país específico
Parámetros requeridos:
countryCode: Código ISO del país (ej: "ES", "US", "FR")
Operación SOAP: CapitalCity
Respuesta: Código del país y nombre de la capital
🔄 Flujo de Datos Detallado
📊 Arquitectura de Procesamiento:
REST Client → Validación → Mapeo → SOAP → Transformación → JSON Response

⚠️ Manejo de Errores Robusto
🛡️ Sistema de Manejo de Errores:
El servicio implementa un sistema multicapa de manejo de errores que garantiza respuestas consistentes:

📋 Tipos de Errores Capturados:
Código HTTP	Tipo de Error	Descripción	Ejemplo
400	Bad Request	Estructura JSON inválida o parámetros faltantes	requestType no especificado
404	Not Found	Recurso no encontrado	País con código inexistente
500	Internal Server Error	Error interno del servicio	Fallo en transformación SOAP
502	Bad Gateway	Error en servicio SOAP externo	Servicio CountryInfo no disponible
504	Gateway Timeout	Timeout en solicitud SOAP	Servicio externo no responde
🔧 Componentes de Manejo:
Validación de Entrada

Verifica estructura JSON requerida
Valida presencia de campos obligatorios
Genera errores 400 para datos inválidos
Captura de Errores SOAP

Maneja fallas de conexión al servicio externo
Procesa timeouts de red
Transforma faults SOAP en errores HTTP
Módulos Especializados

ErrorHelper: Funciones utilitarias para manejo de errores
ImprovedErrorHandler: Procesamiento avanzado de excepciones
Nodos Trace: Logging detallado para depuración
📊 Estructura de Respuesta de Error:
{
  "responseInfo": {
    "status": "error",
    "timestamp": "2023-11-15T10:30:45.123Z"
  },
  "error": {
    "code": 400,
    "message": "Invalid request format",
    "details": "Missing required field: requestType",
    "type": "ValidationError"
  }
}


📝 Notas Adicionales

El servicio utiliza el servicio SOAP público CountryInfoService (http://webservices.oorsprong.org/websamples.countryinfo/CountryInfoService.wso)
La transformación bidireccional permite que los clientes REST accedan fácilmente a datos de un servicio SOAP
El sistema está diseñado para ser extensible con nuevos tipos de solicitudes
Se implementa un manejo detallado de espacios de nombres XML para garantizar la compatibilidad con el servicio SOAP
