# API Echo - IBM ACE 🔄

**IBM App Connect Enterprise - Ejercicio 1: API REST Echo**

## 📌 Descripción

Este servicio en IBM App Connect Enterprise (ACE) implementa un **API Echo** que recibe datos JSON vía REST y devuelve exactamente el mismo JSON de vuelta. Es el ejercicio fundamental para demostrar el procesamiento básico de mensajes JSON en IBM ACE.

## 🎯 Objetivo del Ejercicio

**Crear una aplicación que:**
- ✅ Reciba algo vía REST con JSON
- ✅ Responda exactamente lo mismo para atrás
- ✅ API de toda la vida (patrón Echo básico)

## 🚀 Características principales

✅ Recibe JSON vía REST  
✅ Devuelve exactamente el mismo JSON  
✅ Validación de entrada (Error 500 si no hay JSON)  
✅ Endpoint: `/path/http_service_hub`  
✅ Puerto: 7080  
✅ Manejo de errores controlado  

## 🏗️ Arquitectura del flujo

**Archivos del proyecto:**
- `hub_message.msgflow` - Flujo principal
- `Http_Message_Compute.esql` - Lógica de procesamiento

**Componentes:**

**1️⃣ HTTP Input**
- Endpoint: `/path/http_service_hub`
- Puerto: 7080
- Acepta requests con JSON

**2️⃣ Http_Message_Compute**
- Valida que exista el nodo JSON.Data usando CARDINALITY
- Copia el JSON de entrada al JSON de salida
- Lanza excepción si no hay datos válidos

**3️⃣ HTTP Reply**
- Devuelve el mismo JSON recibido
- Status: 200 OK para éxito
- Status: 500 para errores

## 🔧 Proceso de Despliegue

### **Pasos que seguí:**

1️⃣ **Desarrollo:**
   - Creé mi flujo en ACE Toolkit (`hub_message.msgflow`)
   - Programé mi Compute Node (`Http_Message_Compute.esql`)

2️⃣ **Despliegue:**
   - Creé el BAR file
   - Lo guardé
   - Lo desplegué en el nodo de integración

3️⃣ **Pruebas:**
   - Vi en el nodo de integración la URL generada
   - Copié la URL: `http://localhost:7080/path/http_service_hub`
   - La probé en Postman

## 📮 Configuración en Postman

```
Método: POST
URL: http://localhost:7080/path/http_service_hub
Headers: Content-Type: application/json
Body: JSON válido
```

## 📊 Ejemplos de Funcionamiento

### ✅ **Caso 1: JSON Simple**
**Envías:**
```json
{
  "nombre": "Juan",
  "edad": 30
}
```

**Recibes:**
```json
{
  "nombre": "Juan",
  "edad": 30
}
```

### ✅ **Caso 2: JSON Complejo**
**Envías:**
```json
{
  "usuario": {
    "id": 123,
    "datos": ["a", "b", "c"]
  }
}
```

**Recibes:**
```json
{
  "usuario": {
    "id": 123,
    "datos": ["a", "b", "c"]
  }
}
```

### ❌ **Caso 3: Sin JSON**
**Envías:** (cuerpo vacío)  
**Recibes:** `HTTP 500 - Entrada JSON no válida, nodo Data no encontrado`

## 🔍 Código Principal

```esql
CREATE COMPUTE MODULE Http_Message_Compute
    CREATE FUNCTION Main() RETURNS BOOLEAN
    BEGIN
        -- Verificar si existe el nodo JSON.Data usando CARDINALITY
        IF CARDINALITY(InputRoot.JSON.Data.*[]) > 0 THEN
            -- Copiar el JSON de entrada al JSON de salida
            SET OutputRoot.JSON.Data = InputRoot.JSON.Data;
        ELSE
            -- Manejar el caso donde el nodo no existe
            THROW USER EXCEPTION CATALOG 'UserDefined' MESSAGE 1001 
                VALUES('Entrada JSON no válida, nodo Data no encontrado');
        END IF;

        RETURN TRUE;
    END;
END MODULE;
```

## 🎬 Demostración del Funcionamiento

### **"Comportamiento del API:"**

✅ **CASO EXITOSO:**
   - Envío cualquier JSON válido
   - Recibo exactamente el mismo JSON de vuelta
   - Status: 200 OK

❌ **CASO DE ERROR:**
   - Envío body vacío (sin JSON)
   - Recibo Error 500: "nodo no encontrado"
   - Comportamiento esperado y correcto

## 🔄 Flujo de Datos

```
Cliente → POST JSON → IBM ACE → Validación → Mismo JSON → Cliente
```

## 🧪 Casos de Prueba Validados

| Entrada | Salida | Status | Resultado |
|---------|--------|--------|-----------|
| `{"nombre": "Juan", "edad": 30}` | Mismo JSON | 200 OK | ✅ Exitoso |
| `{"usuario": {"id": 123, "datos": ["a","b","c"]}}` | Mismo JSON | 200 OK | ✅ Exitoso |
| (vacío) | Error message | 500 Error | ✅ Validación correcta |

## 🎯 Cumplimiento del Ejercicio

**✅ REQUISITOS CUMPLIDOS:**
- Aplicación vía REST ✓
- Recibe JSON ✓  
- Devuelve exactamente lo mismo ✓
- API de toda la vida (patrón Echo) ✓
- Manejo de errores ✓

## 📋 Información Técnica

- **URL:** `http://localhost:7080/path/http_service_hub`
- **Puerto:** 7080
- **Método:** POST
- **Content-Type:** application/json requerido
- **Archivos:** `hub_message.msgflow`, `Http_Message_Compute.esql`
