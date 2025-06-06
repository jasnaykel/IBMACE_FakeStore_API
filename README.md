IBMACE_FakeStore_API 🛒
IBM ACE - Servicio de Transformación y Filtrado de Productos (Ejercicio 3)
📌 Descripción

Este proyecto implementa un servicio de integración utilizando IBM App Connect Enterprise (ACE) que actúa como intermediario inteligente para la API pública FakeStore. El servicio se caracteriza por realizar validaciones estrictas de categorías exactas, construcción dinámica de URLs con codificación manual, filtrado flexible por categoría y/o precio mediante comparación exacta y manejo robusto de excepciones. Procesa solicitudes JSON, valida parámetros de entrada y devuelve datos filtrados según criterios específicos, garantizando respuestas precisas y manejo de errores controlado.

🚀 Características principales

✅ Envio de mensajes JSON
✅ Validación de la estructura del json
✅ Mapeo inteligente de categorías de productos
✅ Filtrado de productos por precio especifico
✅ Manejo de errores con excepciones
✅ Registro detallado de logs

🏗️ Arquitectura del flujo

El servicio se compone de los siguientes componentes clave:

1️⃣ HTTP Input

Recibe solicitudes HTTP en el endpoint /path/http_service_hub/products/categ
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

6️⃣ Fakestoreapi (WSRequest)
- Realiza solicitud a FakeStore API
- Usa URL construida dinámicamente

7️⃣ ConfHTTPRequest1 (FilterProductsByPrice)
Filtra los productos recibidos según el precio especificado
Construye una nueva estructura JSON con los productos filtrados
Proporciona mensajes informativos cuando no hay coincidencias

📄 Estructura del código

**hub_message.msgflow:** Flujo principal con nodos y conexiones
**Http_Message_Compute.esql:** Contiene 4 módulos:
- `Http_Message_Compute`: Validación de entrada
- `CategoryToURLMapper`: Mapeo y validación de categorías  
- `ConfigureHTTPRequest`: Configuración HTTP
- `FilterProductsByPrice`: Filtrado por precio
**hub_message_Filter.esql:** Filtro para determinar si aplicar filtrado por precio

🔧 Instalación y Uso

1️⃣ Clonar repositorio**
```bash
git clone https://github.com/jasnaykel/IBMACE_FakeStore_API.git
```
2️⃣ Configurar en IBM ACE**
- Importar proyecto en workspace
- Verificar archivos .msgflow y .esql
- Crear carpeta para logs

3️⃣ Desplegar**
- Crear archivo BAR
- Desplegar en servidor de integración

4️⃣ Probar servicio**

**URL:** `http://localhost:7080/path/http_service_hub/products/categ`  
**Método:** POST  
**Content-Type:** application/json

### 📊 Ejemplos de uso

**Consulta por categoría:**
```json
{"category": "electronics"}
```

**Filtrado por precio:**
```json
{"price": 695}
```

**Combinado:**
```json
{
  "category": "jewelery",
  "price": 695
}
```

### ✅ Validación de categorías:** El servicio requiere categorías exactas:

- `"men's clothing"`✅
- `"women's clothing"`✅
- `"jewelery"`✅
- `"electronics"`✅
- Cualquier otra → ERROR ❌

### ❌ Manejo de errores

**Categoría vacía:**
```
HTTP 500 - Category is required and cannot be empty
```

**Categoría inválida:**
```
HTTP 500 - Invalid category. Valid categories are: men's clothing, women's clothing, jewelery, electronics
```

**Precio no encontrado:**
```json
{"message": "No products found with price 999"}
```

### ✅ **Caso 1 ejemplo: JSON Simple**
**Envías:**
Para una consulta por categoría (ejemplo: "men's clothing"):
```json
{"category": "men's clothing"}
```

**Recibes:**
```json
[
    {
        "id": 1,
        "title": "Fjallraven - Foldsack No. 1 Backpack, Fits 15 Laptops",
        "price": 109.95,
        "description": "Your perfect pack for everyday use and walks in the forest. Stash your laptop (up to 15 inches) in the padded sleeve, your everyday",
        "category": "men's clothing",
        "image": "https://fakestoreapi.com/img/81fPKd-2AYL._AC_SL1500_.jpg",
        "rating": {
            "rate": 3.9,
            "count": 120
        }
    },
    {
        "id": 2,
        "title": "Mens Casual Premium Slim Fit T-Shirts ",
        "price": 22.3,
        "description": "Slim-fitting style, contrast raglan long sleeve, three-button henley placket, light weight & soft fabric for breathable and comfortable wearing. And Solid stitched shirts with round neck made for durability and a great fit for casual fashion wear and diehard baseball fans. The Henley style round neckline includes a three-button placket.",
        "category": "men's clothing",
        "image": "https://fakestoreapi.com/img/71-3HjGNDUL._AC_SY879._SX._UX._SY._UY_.jpg",
        "rating": {
            "rate": 4.1,
            "count": 259
        }
    },
    {
        "id": 3,
        "title": "Mens Cotton Jacket",
        "price": 55.99,
        "description": "great outerwear jackets for Spring/Autumn/Winter, suitable for many occasions, such as working, hiking, camping, mountain/rock climbing, cycling, traveling or other outdoors. Good gift choice for you or your family member. A warm hearted love to Father, husband or son in this thanksgiving or Christmas Day.",
        "category": "men's clothing",
        "image": "https://fakestoreapi.com/img/71li-ujtlUL._AC_UX679_.jpg",
        "rating": {
            "rate": 4.7,
            "count": 500
        }
    },
    {
        "id": 4,
        "title": "Mens Casual Slim Fit",
        "price": 15.99,
        "description": "The color could be slightly different between on the screen and in practice. / Please note that body builds vary by person, therefore, detailed size information should be reviewed below on the product description.",
        "category": "men's clothing",
        "image": "https://fakestoreapi.com/img/71YXzeOuslL._AC_UY879_.jpg",
        "rating": {
            "rate": 2.1,
            "count": 430
        }
    }
]
```

### ✅ **Caso 2: JSON**
**Envías:**
```json
{"category": "women's clothing"}
```

**Recibes:**
```json
[
    {
        "id": 15,
        "title": "BIYLACLESEN Women's 3-in-1 Snowboard Jacket Winter Coats",
        "price": 56.99,
        "description": "Note:The Jackets is US standard size, Please choose size as your usual wear Material: 100% Polyester; Detachable Liner Fabric: Warm Fleece. Detachable Functional Liner: Skin Friendly, Lightweigt and Warm.Stand Collar Liner jacket, keep you warm in cold weather. Zippered Pockets: 2 Zippered Hand Pockets, 2 Zippered Pockets on Chest (enough to keep cards or keys)and 1 Hidden Pocket Inside.Zippered Hand Pockets and Hidden Pocket keep your things secure. Humanized Design: Adjustable and Detachable Hood and Adjustable cuff to prevent the wind and water,for a comfortable fit. 3 in 1 Detachable Design provide more convenience, you can separate the coat and inner as needed, or wear it together. It is suitable for different season and help you adapt to different climates",
        "category": "women's clothing",
        "image": "https://fakestoreapi.com/img/51Y5NI-I5jL._AC_UX679_.jpg",
        "rating": {
            "rate": 2.6,
            "count": 235
        }
    },
    {
        "id": 16,
        "title": "Lock and Love Women's Removable Hooded Faux Leather Moto Biker Jacket",
        "price": 29.95,
        "description": "100% POLYURETHANE(shell) 100% POLYESTER(lining) 75% POLYESTER 25% COTTON (SWEATER), Faux leather material for style and comfort / 2 pockets of front, 2-For-One Hooded denim style faux leather jacket, Button detail on waist / Detail stitching at sides, HAND WASH ONLY / DO NOT BLEACH / LINE DRY / DO NOT IRON",
        "category": "women's clothing",
        "image": "https://fakestoreapi.com/img/81XH0e8fefL._AC_UY879_.jpg",
        "rating": {
            "rate": 2.9,
            "count": 340
        }
    },
    {
        "id": 17,
        "title": "Rain Jacket Women Windbreaker Striped Climbing Raincoats",
        "price": 39.99,
        "description": "Lightweight perfet for trip or casual wear---Long sleeve with hooded, adjustable drawstring waist design. Button and zipper front closure raincoat, fully stripes Lined and The Raincoat has 2 side pockets are a good size to hold all kinds of things, it covers the hips, and the hood is generous but doesn't overdo it.Attached Cotton Lined Hood with Adjustable Drawstrings give it a real styled look.",
        "category": "women's clothing",
        "image": "https://fakestoreapi.com/img/71HblAHs5xL._AC_UY879_-2.jpg",
        "rating": {
            "rate": 3.8,
            "count": 679
        }
    },
    {
        "id": 18,
        "title": "MBJ Women's Solid Short Sleeve Boat Neck V ",
        "price": 9.85,
        "description": "95% RAYON 5% SPANDEX, Made in USA or Imported, Do Not Bleach, Lightweight fabric with great stretch for comfort, Ribbed on sleeves and neckline / Double stitching on bottom hem",
        "category": "women's clothing",
        "image": "https://fakestoreapi.com/img/71z3kpMAYsL._AC_UY879_.jpg",
        "rating": {
            "rate": 4.7,
            "count": 130
        }
    },
    {
        "id": 19,
        "title": "Opna Women's Short Sleeve Moisture",
        "price": 7.95,
        "description": "100% Polyester, Machine wash, 100% cationic polyester interlock, Machine Wash & Pre Shrunk for a Great Fit, Lightweight, roomy and highly breathable with moisture wicking fabric which helps to keep moisture away, Soft Lightweight Fabric with comfortable V-neck collar and a slimmer fit, delivers a sleek, more feminine silhouette and Added Comfort",
        "category": "women's clothing",
        "image": "https://fakestoreapi.com/img/51eg55uWmdL._AC_UX679_.jpg",
        "rating": {
            "rate": 4.5,
            "count": 146
        }
    },
    {
        "id": 20,
        "title": "DANVOUY Womens T Shirt Casual Cotton Short",
        "price": 12.99,
        "description": "95%Cotton,5%Spandex, Features: Casual, Short Sleeve, Letter Print,V-Neck,Fashion Tees, The fabric is soft and has some stretch., Occasion: Casual/Office/Beach/School/Home/Street. Season: Spring,Summer,Autumn,Winter.",
        "category": "women's clothing",
        "image": "https://fakestoreapi.com/img/61pHAEJ4NML._AC_UX679_.jpg",
        "rating": {
            "rate": 3.6,
            "count": 145
        }
    }
]
```

### ✅ **Caso 3: JSON**
**Envías:**
Para un filtrado por precio (ejemplo: 695):
```json
{
  "price": 695

}
```

**Recibes:**
```json
{
    "Item": {
        "id": 5,
        "title": "John Hardy Women's Legends Naga Gold & Silver Dragon Station Chain Bracelet",
        "price": 695,
        "description": "From our Legends Collection, the Naga was inspired by the mythical water dragon that protects the ocean's pearl. Wear facing inward to be bestowed with love and abundance, or outward for protection.",
        "category": "jewelery",
        "image": "https://fakestoreapi.com/img/71pWzhdJNwL._AC_UL640_QL65_ML3_.jpg",
        "rating": {
            "rate": 4.6,
            "count": 400
        }
    }
}
```

### ✅ **Caso 4: Si no hay productos con el precio especificado:**
**Envías:**

```json
{
  "price": 6952
}
```

**Recibes:**
```json
{
    "message": "No products found with price 6952"
}
```


### 🔍 Configuración de logs

Los logs se almacenan según configuración de nodos Trace en el msgflow. Registran información en diferentes puntos del flujo para facilitar depuración.

### 📝 Notas técnicas

- El servicio valida categorías exactas de FakeStore API
- URLs se construyen dinámicamente con codificación manual
- Filtrado por precio usa comparación exacta
- Manejo robusto de excepciones en validaciones
