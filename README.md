IBMACE_FakeStore_API 🛒
IBM ACE - Servicio de Transformación y Filtrado de Productos (Ejercicio 3)
📌 Descripción

Este proyecto implementa un servicio avanzado de integración utilizando IBM App Connect Enterprise (ACE) que actúa como intermediario inteligente para la API pública FakeStore. A diferencia de un simple proxy, este servicio realiza transformaciones bidireccionales de mensajes, validaciones de datos y filtrado de productos según criterios específicos.

🚀 Características principales

✅ Transformación bidireccional de mensajes (request/response)
✅ Validación de estructura y contenido de mensajes
✅ Mapeo inteligente de categorías de productos
✅ Filtrado de productos por precio y categoría
✅ Construcción dinámica de URLs basada en parámetros
✅ Registro detallado para monitoreo y depuración

🏗️ Arquitectura del flujo

El servicio se compone de los siguientes componentes clave:

1️⃣ HTTP Input

Recibe solicitudes HTTP en el endpoint /path/http_service_hub
Configura un timeout de 15 segundos para el cliente
Procesa mensajes en formato JSON
2️⃣ Http_Message_Compute

Extrae y valida los datos de entrada
Almacena los parámetros en Environment para uso posterior
Verifica que el formato de los datos sea correcto
3️⃣ CategoryToURLMapper

Obtiene la categoría del Environment
Normaliza y mapea la categoría a los valores exactos aceptados por la API
Construye dinámicamente la URL de destino con la categoría mapeada
4️⃣ ConfigureHTTPRequest

Configura los parámetros para la solicitud HTTP
Establece la URL de destino construida dinámicamente
Define el método HTTP (GET) y el timeout
5️⃣ FilterProductsByPrice

Filtra los productos recibidos según el precio especificado
Construye una nueva estructura JSON con los productos filtrados
Proporciona mensajes informativos cuando no hay coincidencias
6️⃣ Filter_precio (hub_message_Filter)

Determina si se debe aplicar el filtro de precio
Dirige el flujo según la existencia del parámetro de precio
📄 Estructura del código

El proyecto consta de tres archivos principales:

hub_message.msgflow: Define el flujo de mensajes completo con todos los nodos y conexiones.

Http_Message_Compute.esql: Contiene cuatro módulos ESQL:

Http_Message_Compute: Valida y extrae datos de entrada
CategoryToURLMapper: Mapea categorías y construye URLs
ConfigureHTTPRequest: Configura la solicitud HTTP
FilterProductsByPrice: Filtra productos por precio
hub_message_Filter.esql: Contiene el módulo de filtro que determina si se debe aplicar el filtrado por precio.

🔧 Instalación y Uso

1️⃣ Clonar el repositorio
git clone https://github.com/jasnaykel/IBMACE_FakeStore_API.git
2️⃣ Configurar en IBM ACE

Abre IBM App Connect Enterprise Toolkit
Importa el proyecto en tu espacio de trabajo
Verifica que todos los archivos se hayan importado correctamente
Asegúrate de que la carpeta D:\LOG\ exista para los archivos de registro
3️⃣ Desplegar el servicio

Crea un archivo BAR (clic derecho en el proyecto → Export → BAR file)
Despliega el archivo BAR en tu servidor de integración
4️⃣ Probar el servicio

Ejemplo 1: Consulta por categoría
Usando Postman:

Crea una nueva solicitud GET
URL: http://localhost:7800/path/http_service_hub
En la pestaña Body, selecciona "raw" y "JSON"
Ingresa uno de los ejemplos JSON anteriores
Haz clic en "Send"
📊 Respuesta esperada

Para una consulta por categoría (ejemplo: "men clothing"):

[
  {
    "id": 1,
    "title": "Fjallraven - Foldsack No. 1 Backpack, Fits 15 Laptops",
    "price": 109.95,
    "category": "men's clothing",
    "description": "Your perfect pack for everyday use...",
    "image": "https://fakestoreapi.com/img/81fPKd-2AYL._AC_SL1500_.jpg"
  },
  // Más productos de la categoría...
]
Para un filtrado por precio (ejemplo: 109.95):

{
  "Item": [
    {
      "id": 1,
      "title": "Fjallraven - Foldsack No. 1 Backpack, Fits 15 Laptops",
      "price": 109.95,
      "category": "men's clothing",
      "description": "Your perfect pack for everyday use...",
      "image": "https://fakestoreapi.com/img/81fPKd-2AYL._AC_SL1500_.jpg"
    }
  ]
}

Si no hay productos con el precio especificado:

{
  "message": "No products found with price 999.99"
}
🔍 Características de transformación

Mapeo de categorías: El servicio normaliza y mapea las categorías ingresadas por el usuario a los valores exactos aceptados por la API:

"mens_clothing", "men_clothing", "men clothing" → "men's clothing"
"womens_clothing", "women_clothing", "women clothing" → "women's clothing"
"jewelery", "jewelry" → "jewelery"
"electronics", "electronic" → "electronics"
Construcción dinámica de URL: Basado en la categoría mapeada, el servicio construye la URL apropiada:

Con categoría: https://fakestoreapi.com/products/category/[categoría]
Sin categoría: https://fakestoreapi.com/products
Filtrado de productos: Si se especifica un precio, el servicio filtra los productos que coinciden exactamente con ese precio.

🔍 Monitoreo y Solución de Problemas

Los logs detallados se almacenan en D:\LOG\log_http_prueba.log
Los nodos de Trace registran información en diferentes puntos del flujo:
Entrada HTTP
Errores y timeouts
Salida antes de la solicitud externa
Respuesta de la API externa
Errores comunes:

Formato JSON inválido
Categoría no reconocida (se usará el valor original)
Precio no encontrado en ningún producto
Timeout del cliente (15s) o del servidor externo (5s)
🔄 Flujo de Datos

Cliente envía solicitud JSON con parámetros (categoría y/o precio)
Http_Message_Compute extrae y valida los parámetros
CategoryToURLMapper normaliza la categoría y construye la URL
ConfigureHTTPRequest prepara la solicitud HTTP
WSRequest envía la solicitud a FakeStoreAPI
Si hay un precio especificado:
FilterProductsByPrice filtra los productos por precio
HTTP Reply devuelve los productos (filtrados o no) al cliente
📝 Notas Adicionales

El servicio maneja variaciones en los nombres de categorías para mejorar la experiencia del usuario
La transformación bidireccional permite que el cliente use un formato simplificado mientras se comunica correctamente con la API externa
El sistema está preparado para futuras extensiones como rangos de precios
