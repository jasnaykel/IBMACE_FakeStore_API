# IBMACE_FakeStore_API 
🛒 IBM ACE - Servicio HTTP para Filtrado de Productos
📌 Descripción
Este servicio en IBM App Connect Enterprise (ACE) actúa como intermediario para consultar y filtrar productos de una tienda online mediante la API pública FakeStoreAPI. Recibe parámetros de filtrado como categoría y rango de precios, consulta la API y devuelve solo los productos que cumplen los criterios.

🚀 Características principales
✅ Procesamiento de filtros con parámetros de categoría y precio ✅ Consulta dinámica a la API (https://fakestoreapi.com/products) ✅ Transformación de respuesta JSON ✅ Preparado para futuras validaciones y excepciones

🏗️ Arquitectura del flujo
El servicio se compone de cuatro módulos clave:

1️⃣ Http_Message_Compute

Almacena los parámetros de filtrado en Environment para disponibilidad en todo el flujo.

2️⃣ Http_Consulta_Productos

Extrae la categoría del mensaje JSON de entrada.

Construye dinámicamente la URL de la API (https://fakestoreapi.com/products).

Si se especifica una categoría, la incluye en la URL (/category/men's clothing).

3️⃣ HTTP_Filtrar

Nodo de paso que simplemente transmite el mensaje.

Puede ampliarse en el futuro para realizar validaciones adicionales.

4️⃣ HTTP_Filtrar_Respuesta

Recupera los parámetros de filtrado desde Environment.

Procesa la respuesta de FakeStoreAPI.

Filtra los productos según el rango de precios y construye la respuesta JSON.

🔧 Instalación y Uso
1️⃣ Clonar el repositorio
sh
git clone https://github.com/jasnaykel/IBMACE_FakeStore_API.git
cd IBMACE_FakeStore_API
2️⃣ Configurar en IBM ACE
Abre IBM App Connect Enterprise.

Importa el proyecto en tu espacio de trabajo.

Configura los nodos de conexión y despliega el flujo.

3️⃣ Ejecutar el servicio
Configura los parámetros de filtrado en el JSON de entrada.

Lanza la solicitud HTTP y recibe la respuesta con los productos filtrados.

🛠️ Tecnologías utilizadas
🔹 IBM App Connect Enterprise (ACE 12) 🔹 Git (2.37) 🔹 FakeStoreAPI (https://fakestoreapi.com/products) 🔹 ESQL para transformaciones de datos
