IBMACE_FakeStore_API 🛒
IBM ACE - Servicio HTTP para Consulta de Productos (Ejercicio 2)
📌 Descripción

Este proyecto implementa un servicio de integración utilizando IBM App Connect Enterprise (ACE) que actúa como intermediario para consultar productos de la API pública FakeStore. El servicio recibe solicitudes HTTP, las procesa mediante módulos ESQL y devuelve los datos de productos obtenidos.

🚀 Características principales

✅ Procesamiento de mensajes JSON
✅ Validación de estructura de datos de entrada
✅ Consulta a la API externa (https://fakestoreapi.com/products)
✅ Registro detallado para monitoreo y depuración
✅ Manejo de errores y excepciones

🏗️ Arquitectura del flujo

El servicio se compone de los siguientes componentes clave:

1️⃣ HTTP Input

Recibe solicitudes HTTP en el endpoint /path/http_service_hub
Configura un timeout de 15 segundos para el cliente
Procesa mensajes en formato JSON
2️⃣ Http_Message_Compute

Valida la estructura del mensaje JSON de entrada
Verifica la existencia del nodo JSON.Data
Lanza excepciones si la estructura no es válida
3️⃣ ConfigureHTTPRequest

Configura los parámetros para la solicitud HTTP
Establece la URL de destino (https://fakestoreapi.com/products)
Define el método HTTP (GET) y el timeout
4️⃣ ProxyResponseHandler

Procesa la respuesta de la API externa
Copia los datos JSON al mensaje de salida
5️⃣ Nodos de Trace

Registran información detallada en diferentes puntos del flujo
Capturan entradas, salidas, errores y timeouts
📄 Estructura del código

El proyecto consta de dos archivos principales:

hub_message.msgflow: Define el flujo de mensajes con los nodos de entrada/salida HTTP, nodos de computación y nodos de registro.

Http_Message_Compute.esql: Contiene tres módulos ESQL:

Http_Message_Compute: Valida la estructura JSON de entrada
ConfigureHTTPRequest: Configura los parámetros de la solicitud HTTP
ProxyResponseHandler: Procesa la respuesta de la API externa
🔧 Instalación y Uso

1️⃣ Clonar el repositorio
git clone https://github.com/jasnaykel/IBMACE_FakeStore_API.git
2️⃣ Configurar en IBM ACE

Abre IBM App Connect Enterprise Toolkit
Importa el proyecto en tu espacio de trabajo (File → Import → General → Existing Projects into Workspace)
Verifica que los archivos hub_message.msgflow y Http_Message_Compute.esql se hayan importado correctamente
Asegúrate de que la carpeta D:\LOG\ exista para los archivos de registro
3️⃣ Desplegar el servicio

Crea un archivo BAR (clic derecho en el proyecto → Export → BAR file)
Despliega el archivo BAR en tu servidor de integración
4️⃣ Probar el servicio

Usando cURL: http://localhost:7800/path/http_service_hub 

Usando Postman:
Crea una nueva solicitud GET
URL: http://localhost:7080/path/http_service_hub
En la pestaña Body, selecciona "raw" y "JSON"
Ingresa el siguiente JSON:
## 📊 Ejemplos de Funcionamiento

**Envías:**
```json
{
  "Data": {
    "param1": "value1"
  }
}
```

📊 Respuesta esperada

**Recibirás un array JSON con los productos de FakeStoreAPI:**
```json
[
  {
    "id": 1,
    "title": "Fjallraven - Foldsack No. 1 Backpack, Fits 15 Laptops",
    "price": 109.95,
    "description": "Your perfect pack for everyday use...",
    "category": "men's clothing",
    "image": "https://fakestoreapi.com/img/81fPKd-2AYL._AC_SL1500_.jpg",
    "rating": {
      "rate": 3.9,
      "count": 120
    }
  },
  // Más productos...
]

```
### ❌ **Caso: Sin JSON**
**Envías:** (cuerpo vacío)  
**Recibes:** `HTTP 500 - Entrada JSON no válida, nodo Data no encontrado`


🔍 Monitoreo y Solución de Problemas

Los logs detallados se almacenan en D:\LOG\log_http_prueba.log
Revisa los logs para identificar:
Mensajes de entrada y salida
Errores de conexión o validación
Timeouts
Problemas de formato JSON
Errores comunes:

Error 1001: "Entrada JSON no válida, nodo Data no encontrado" - Asegúrate de incluir el nodo "Data" en tu JSON de entrada
Timeout del cliente: La solicitud tarda más de 15 segundos
Timeout del servidor: La API externa no responde en 5 segundos
🔄 Flujo de Datos

Cliente envía solicitud JSON con nodo "Data"
HTTP Input recibe la solicitud
Http_Message_Compute valida la estructura
ConfigureHTTPRequest prepara la solicitud a la API externa
WSRequest envía la solicitud a FakeStoreAPI
ProxyResponseHandler procesa la respuesta
HTTP Reply devuelve los datos al cliente
📝 Notas Adicionales

El servicio requiere que el mensaje de entrada tenga un nodo JSON.Data
Se utilizan nodos de Trace estratégicamente ubicados para facilitar la depuración
La conexión con la API externa utiliza TLS para mayor seguridad
El flujo está diseñado para ser fácilmente extensible con funcionalidades adicionales
