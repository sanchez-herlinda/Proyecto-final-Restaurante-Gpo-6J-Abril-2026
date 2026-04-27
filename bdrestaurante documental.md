Aquí tienes un ejemplo claro y práctico para estructurar una base de datos tipo **NoSQL (por ejemplo, MongoDB)** para un restaurante, incluyendo **base de datos, colecciones, documentos, atributos y tipos de datos**:

---

## 📊 Base de Datos

**Nombre:** `restauranteDB`

---

## 📁 Colección 1: `clientes`

### 📄 Documento de ejemplo:

```json
{
  "_id": "C001",
  "nombre": "Juan Pérez",
  "telefono": "6561234567",
  "email": "juanperez@gmail.com",
  "direccion": {
    "calle": "Av. Juárez",
    "numero": 123,
    "ciudad": "Ciudad Juárez"
  },
  "fecha_registro": "2026-04-20",
  "activo": true
}
```

### 🧾 Atributos y tipos:

* `_id`: String
* `nombre`: String
* `telefono`: String
* `email`: String
* `direccion`: Objeto

  * `calle`: String
  * `numero`: Number
  * `ciudad`: String
* `fecha_registro`: Date (o String)
* `activo`: Boolean

---

## 📁 Colección 2: `menu`

### 📄 Documento de ejemplo:

```json
{
  "_id": "M001",
  "nombre_platillo": "Tacos al pastor",
  "descripcion": "Tacos con carne de cerdo marinada",
  "precio": 85.50,
  "categoria": "Comida mexicana",
  "disponible": true
}
```

### 🧾 Atributos y tipos:

* `_id`: String
* `nombre_platillo`: String
* `descripcion`: String
* `precio`: Number (decimal)
* `categoria`: String
* `disponible`: Boolean

---

## 📁 Colección 3: `ordenes`

### 📄 Documento de ejemplo:

```json
{
  "_id": "O001",
  "cliente_id": "C001",
  "fecha": "2026-04-27",
  "total": 171.00,
  "estado": "completada",
  "detalles": [
    {
      "menu_id": "M001",
      "cantidad": 2,
      "precio_unitario": 85.50
    }
  ]
}
```

### 🧾 Atributos y tipos:

* `_id`: String
* `cliente_id`: String (referencia a clientes)
* `fecha`: Date (o String)
* `total`: Number
* `estado`: String (ej. "pendiente", "completada")
* `detalles`: Array de objetos

  * `menu_id`: String
  * `cantidad`: Number
  * `precio_unitario`: Number

---

## 📁 Colección 4: `empleados`

### 📄 Documento de ejemplo:

```json
{
  "_id": "E001",
  "nombre": "María López",
  "puesto": "Mesera",
  "salario": 8000,
  "fecha_contratacion": "2025-10-10",
  "activo": true
}
```

### 🧾 Atributos y tipos:

* `_id`: String
* `nombre`: String
* `puesto`: String
* `salario`: Number
* `fecha_contratacion`: Date
* `activo`: Boolean

---

## 💡 Notas importantes

* Este modelo es **flexible** (propio de NoSQL).
* Se usan **referencias simples** (`cliente_id`, `menu_id`) en lugar de joins.
* Puedes agregar más colecciones como `reservaciones`, `proveedores`, etc.

---

Si quieres, puedo darte el mismo ejemplo pero en **SQL (MySQL/PostgreSQL)** con tablas y relaciones para que compares ambos enfoques.
