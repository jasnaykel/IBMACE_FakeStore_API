IBMACE_FakeStore_API 🛒
IBM ACE - Servicio HTTP para Consulta de Productos (Ejercicio 2)
📌 Descripción

Este proyecto implementa un servicio de integración utilizando IBM App Connect Enterprise (ACE) que actúa como intermediario para consultar productos de la API pública FakeStore. El servicio recibe solicitudes HTTP, las procesa mediante módulos ESQL y devuelve los datos de productos obtenidos.

🚀 Características principales

✅ Envío de mensajes JSON
✅ Validación de estructura de datos de entrada
✅ Consulta a la API externa (https://fakestoreapi.com/products)
✅ Registro detallado de logs 

🏗️ Arquitectura del flujo

El servicio se compone de los siguientes componentes clave:
hub_message.msgflow: Define el flujo de mensajes con los nodos de entrada/salida HTTP, nodos de computación y nodos de registro.
1️⃣ HTTP Input
Recibe solicitudes HTTP en el endpoint /path/http_service_hub
Configura un timeout de 15 segundos para el cliente
Procesa mensajes en formato JSON
2️⃣ Input_Validation_HTTP_Setup (Nodo Compute)
- Valida que existe el nodo `JSON.Data` en la entrada
- Configura parámetros para la solicitud HTTP externa:
  - URL: `https://fakestoreapi.com/products`
  - Método: GET
  - Timeout: 15 segundos
- Guarda datos de entrada en Environment para referencia

5️⃣ Nodos de Trace (Nodo registro)
Registran información detallada en diferentes puntos del flujo
Capturan entradas, salidas, errores y timeouts

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

Usando cURL: http://localhost:7800/path/http_service_hub/product

Usando Postman:
Crea una nueva solicitud 
URL: http://localhost:7800/path/http_service_hub/product
- Método: POST
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
**Recibes:** `HTTP 500 - JSON no encontrado`


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

📝 Notas Adicionales

El servicio requiere que el mensaje de entrada tenga un nodo JSON.Data
Se utilizan nodos de Trace estratégicamente ubicados para facilitar la depuración
