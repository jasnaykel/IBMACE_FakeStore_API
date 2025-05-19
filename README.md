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
Errores de gateway (502)
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

Para información de país (countryInfo):

📊 Respuestas esperadas

Para información de país (countryInfo):

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
Para lista de países (listCountries):

{
  "responseInfo": {
    "status": "success",
    "timestamp": "2023-11-15T10:31:45.123Z"
  },
  "countries": {
    "count": 239,
    "items": [
      {
        "code": "AF",
        "name": "Afghanistan"
      },
      {
        "code": "AL",
        "name": "Albania"
      },
      // Más países...
    ]
  }
}
Para información de moneda (currencyInfo):

{
  "responseInfo": {
    "status": "success",
    "timestamp": "2023-11-15T10:32:45.123Z"
  },
  "currencyData": {
    "code": "EUR",
    "name": "Euro"
  }
}
Para información de idiomas (countryLanguage):

{
  "responseInfo": {
    "status": "success",
    "timestamp": "2023-11-15T10:33:45.123Z"
  },
  "languages": {
    "count": 139,
    "items": [
      {
        "code": "aa",
        "name": "Afar"
      },
      {
        "code": "ab",
        "name": "Abkhazian"
      },
      // Más idiomas...
    ]
  }
  Para capital de país (capitalCity):

{
  "responseInfo": {
    "status": "success",
    "timestamp": "2023-11-15T10:34:45.123Z"
  },
  "capitalInfo": {
    "countryCode": "US",
    "capitalName": "Washington"
  }
}
🔍 Tipos de solicitudes soportadas

El servicio soporta cinco tipos principales de solicitudes:

countryInfo: Obtiene información detallada de un país

Parámetros: searchType ("byCode"), countryCode
listCountries: Lista todos los países

Parámetros: searchType ("byCode" o "byName")
currencyInfo: Obtiene información sobre una moneda

Parámetros: currencyCode
countryLanguage: Lista todos los idiomas disponibles

No requiere parámetros adicionales
capitalCity: Obtiene la capital de un país

Parámetros: countryCode
🔄 Flujo de Datos

Cliente envía solicitud REST en formato JSON
Http_Message_Compute extrae y valida los parámetros
CategoryToURLMapper determina la operación SOAP a invocar
ConfigureHTTPRequest construye el mensaje SOAP
CountryInfoService envía la solicitud SOAP al servicio externo
TransformSOAPtoJSON transforma la respuesta SOAP en JSON estructurado
HTTP Reply devuelve la respuesta JSON al cliente
⚠️ Manejo de Errores

El servicio implementa un sistema robusto de manejo de errores que:

Captura errores en diferentes puntos del flujo
Clasifica los errores según su tipo (validación, no encontrado, timeout, etc.)
Asigna códigos HTTP apropiados (400, 404, 500, 502, 504)
Genera respuestas de error estructuradas con detalles útiles
Registra información detallada para facilitar la depuración
Los errores se manejan mediante los módulos ErrorHelper e ImprovedErrorHandler.

📝 Notas Adicionales

El servicio utiliza el servicio SOAP público CountryInfoService (http://webservices.oorsprong.org/websamples.countryinfo/CountryInfoService.wso)
La transformación bidireccional permite que los clientes REST accedan fácilmente a datos de un servicio SOAP
El sistema está diseñado para ser extensible con nuevos tipos de solicitudes
Se implementa un manejo detallado de espacios de nombres XML para garantizar la compatibilidad con el servicio SOAP
