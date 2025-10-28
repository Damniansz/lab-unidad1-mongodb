# Analisis comparativo SQL vs NoSql

| Criterio | SQL (Relacional) | NoSQL (MongoDB) | Justificación para TechStore |
|-----------|------------------|-----------------|-------------------------------|
| Flexibilidad de esquema | Rígido, requiere ALTER TABLE | Flexible, documentos JSON | Se maneja con productos de diferentes atributos |
| Modelo de datos | Tablas normalizadas (productos, detalles) | Colección de documentos |Simplifica la gestion de catalogos, un documento único por producto permite agrupar todos los datos relacionados de un artículo en un solo lugar, eliminando la necesidad de múltiples tablas y JOINs|
| Consulta de datos | JOINs complejos | Consultas directas | Mediante las funciones es mas facil realizar las consultas y se evita el uso de los JOINs |

#  Diagrama Entidad-Relacion (DER)
Se compone de 5 entidades:
![Diagrama Entidad-Relacion TechStore](/images/DER.drawio.png)

**Categorías**  
   Tipos generales de productos disponibles en el inventario, como las laptops, smartphones y monitores, agrupa los productos bajo una misma clasificación. Una categoría puede contener muchos productos, pero cada producto pertenece solo a una categoría (1:N).

**Productos**  
   Contiene la información principal de cada artículo del inventario y se relaciona directamente con una categoría. Cada producto puede tener características específicas que dependen de su tipo (1:1).

**Laptop**  
   Propiedades específicas asociadas a los productos del tipo laptop.  
   Relacion **uno a uno** con la entidad Productos, de manera que cada laptop tiene un solo registro.

**Smartphone**  
   Propiedades específicas asociadas a los productos del tipo smartphont, relacion **uno a uno** con la entidad Productos

**Monitor**  
    Propiedades específicas asociadas a los productos del tipo monitor, relacion **uno a uno** con la entidad Productos

# Estructura de documento JSON
```json
{
  "_id": "ObjectId(...)",
  "nombre": "String",
  "sku": "String (Indexado, Único)",
  "precio": "Number",
  "stock": "Number",
  "tipo_producto": "String (Enum: 'Laptop', 'Smartphone', 'Monitor')",
  "fecha_creacion": "Date",
  "especificaciones": {
    {
  "_id": { "$oid": "ObjectId()" },
  "nombre": "Laptop",
  "sku": "LAP-001",
  "precio": 500,
  "stock": 10,
  "tipo_producto": "Laptop",
  "fecha_creacion": { "$date": "2025-10-28T00:00:00Z" },
  "especificaciones": {
    "cpu": "Intel Core i7",
    "ram_gb": 16,
    "almacenamiento_gb": 512,
    "dimensiones": {
      "alto_cm": 1.8,
      "ancho_cm": 32.0,
      "peso_kg": 1.2
    },
    "puertos": ["USB-C", "HDMI", "Audio"]
  }
}
    {
  "_id": { "$oid": "ObjectId()" },
  "nombre": "Samsung A50",
  "sku": "SM-001",
  "precio": 250,
  "stock": 5,
  "tipo_producto": "Smartphone",
  "fecha_creacion": { "$date": "2025-10-28T00:00:00Z" },
  "especificaciones": {
    "pantalla": "6.4\"SUPER AMOLED",
    "ram_gb": 12,
    "almacenamiento_gb": 128,
    "camara_mp": {
      "principal": 25,
      "enfoque dinamico": 5,
      "angular": 8 
    },
    "bateria_mah": 5000,
    "hz": 60
  }
    }
  }
}