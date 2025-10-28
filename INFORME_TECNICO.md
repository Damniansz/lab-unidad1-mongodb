# INFORME TECNICO – LABORATORIO UNIDAD 1

## Analisis comparativo SQL vs NoSql

| Criterio | SQL (Relacional) | NoSQL (MongoDB) | Justificación para TechStore |
|-----------|------------------|-----------------|-------------------------------|
| Flexibilidad de esquema | Rígido, requiere `ALTER TABLE` | Flexible, documentos JSON | Se maneja con productos de diferentes atributos |
| Modelo de datos | Tablas normalizadas (productos, detalles) | Colección de documentos |Simplifica la gestion de catalogos, un documento único por producto permite agrupar todos los datos relacionados de un artículo en un solo lugar, eliminando la necesidad de múltiples tablas y JOINs|
| Consulta de datos | JOINs complejos | Consultas directas | Mediante las funciones es mas facil realizar las consultas y se evita el uso de los JOINs |

#  Diagrama Entidad-Relacion (DER)
Se compone de 5 entidades:
![Diagrama Entidad-Relacion TechStore](/images/DER.drawio.png)

**Categorías**  
   Tipos generales de productos disponibles en el inventario, como las laptops, smartphones y monitores, agrupa los productos bajo una misma clasificación. Una categoría puede contener muchos productos, pero cada producto pertenece solo a una categoría `(1:N)`.

**Productos**  
   Contiene la información principal de cada artículo del inventario y se relaciona directamente con una categoría. Cada producto puede tener características específicas que dependen de su tipo `(1:1)`.

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
    "cpu": "String",
    "ram_gb": "Number",
    "almacenamiento_gb": "Number",
    "dimensiones": {
      "alto_cm": "Number",
      "ancho_cm": "Number",
      "peso_kg": "Number"
    }
  }
}
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

```
# Resultados

## Insercion de 3 productos usando insertMany()
```javascript
use('techstore');
// Limpieeza inicial para pruebas
db.productos.drop();
// Insertar datos a 3 prodcutos 
db.productos.insertMany([
    {
    "nombre": "Samsung A50",
    "sku": "SM-001",
    "precio": 250,
    "stock": 5,
    "fecha_creacion": new Date("2025-10-28"),
    "id_categoria": 1,
    "tipo": "Smartphone",
    "especificaciones": {
      "pantalla": "6.8 pulgadas AMOLED",
      "ram_gb": 4,
      "almacenamiento_gb": 128,
      "hz": 60
    }
  },
  {
    "nombre": "Dell XPS 15 9530",
    "sku": "LPTP-DXPS15-512-SLV",
    "precio": 1899.99,
    "stock": 8,
    "fecha_creacion": new Date("2024-02-20"),
    "id_categoria": 2,
    "tipo": "Laptop",
    "especificaciones": {
      "cpu": "Intel Core i7-13700H",
      "ram_gb": 16,
      "peso_kg": 1.86,
      "ancho_cm": 34.4,
      "alto_cm": 23.0

  }
},
{
    "nombre": "LG UltraGear 27GN950",
    "sku": "MNTR-LG27-4K-144",
    "precio": 799.99,
    "stock": 12,
    "fecha_creacion": new Date("2024-03-10"),
    "id_categoria": 3,
    "tipo": "Monitor",
    "especificaciones": {

      "resolucion": "3840x2160 (4K)",
      "tamano_pulgadas": 27,
      "hz": 144  
    }
}
]);
```
#### Verificación de insercion
![Inserción de productos](/images/Inserccion%20Datos/insertMany.png)
![Verificación de inserción de productos en compass](/images/Inserccion%20Datos/insertManyCompass.png)

## Lectura de datos (READ)

### Consulta 1: Mostrar todos los productos en la colección. (find())
Retorna todos los documentos de la colección `productos` sin filtros.

![Consulta 1: Mostrar todos los productos en la colección](/images/Consultas/Consulta1.png)
### Consulta 2: Mostrar solo los productos que sean de tipo "Laptop".
Filtra documentos donde el campo `tipo` es exactamente "Laptop".

![Consulta 2: Mostrar solo los productos que sean de tipo "Laptop"](/images/Consultas/Consulta2.png)
### Consulta 3: Mostrar los productos que tengan más de 10 unidades en stock Y un precio menor a 1000.
Consulta compuesta con operadores lógicos:
- `$gt`: Greater than (mayor que)
- `$lt`: Less than (menor que)

![Consulta 3: Mostrar los productos que tengan más de 10 unidades en stock Y un precio menor a 1000](/images/Consultas/Consulta3.png)
### Consulta 4: Mostrar solo el nombre, precio y stock de los "Smartphone" (Proyección).
Proyección que limita los campos retornados:
- `0`: Excluir campo
- `1`: Incluir campo

![Consulta 4: Mostrar solo el nombre, precio y stock de los "Smartphone"](/images/Consultas/Consulta4.png)

## Actualizacion de datos (UPDATE)

### Operacion 1: Se vendió un Smartphone (busque uno por su sku). Reduzca su stock en 1 unidad. (Use $inc).
**Operador `$inc`:**
- Incrementa/decrementa valores numéricos **atómicamente**
- Valores negativos decrementan
- Operación thread-safe

![Operacion 1: Se vendió un Smartphone](/images/Operaciones/Operacion1.png)
![Verificacion Compass](/images/Operaciones/Operacion1.2.png)
### Operacion 2: El precio de la Laptop ha subido. Actualice su precio a un nuevo valor y añada un nuevo campo ultima_revision: new Date(). (Use $set).
**Operador `$set`:**
- Actualiza campos existentes
- **Crea nuevos campos** si no existen
- Permite agregar campos dinámicamente

![Operacion 2: El precio de la Laptop ha subido](/images/Operaciones/Operacion2.png)
![Verificacion Compass](/images/Operaciones/Operacion2.2.png)

## Analisis Reflexivo

### 1. ¿Cuál fue la ventaja más significativa de usar un modelo de documento (MongoDB) para el caso "TechStore" en comparación con el modelo relacional que diseñó?
La ventaja mas significativa es la flexibilidad que ofrece para manejar productos con atributos diferentes. En un modelo relacional, cada tipo de producto requeriria tablas separadas y relaciones complejas, lo que complicaria un poc la gestion y consulta de datos, no seria algo que no se pueda hacer pero sin duda, con MongoDB, se puede almacenar toda la informacion relevante de un producto en un solo documento, permitiendo que cada producto tenga sus propias especificaciones sin necesidad de una estructura rigida. Esto simplifica la insercion, actualizacion y consulta de datos, ya que no es necesario hacer JOINs entre muchas tablas para obtener toda la informacion de un producto.
### 2. ¿Cómo facilita el anidamiento de documentos (el campo especificaciones) la gestión de datos heterogéneos (diferentes atributos por producto)?
El anidamiento de documentos permite agrupar todas las especificaciones relacionadas con un producto dentro de un solo campo, lo que facilita la gestion de datos diferentes. Cada producto puede tener un conjunto unico de atributos sin afectar la estructura general de la base de datos. Al anidar estas especificaciones dentro del documento del producto, se evita la necesidad de crear tablas separadas para cada tipo de producto y sus atributos.
### 3. ¿Qué problemas potenciales podría enfrentar esta base de datos a futuro si no se controla la flexibilidad del esquema (es decir, si se permite insertar cualquier dato)?
La falta de estandarizacion podria complicar el mantenimiento y la escalabilidad de la base de datos a medida que crece, si no se controla la flexibilidad del esquema, podrían surgir varios problemas potenciales, como la inconsistencia de datos, donde diferentes documentos para el mismo tipo de producto tienen atributos diferentes. Esto dificultaria las consultas y el analisis de datos, ya que no habria una estructura uniforme para trabajar. Ademas, podria haber problemas de integridad de datos, donde se insertan valores incorrectos o inapropiados para ciertos campos, lo que afectaria la calidad de la informacion almacenada. 
### 4. ¿Qué paso técnico recomendaría a continuación para "profesionalizar" esta base de datos? (Piense en rendimiento e integridad de datos que no cubrimos en este laboratorio).
Implementar validaciones de esquema, permitiria definir reglas claras sobre los tipos de datos y estructuras que se pueden insertar en la base de datos, asegurando la integridad de los datos y evitando inconsistencias. Ademas, recomendaria crear indices en campos frecuentemente consultados, como el SKU y el tipo de producto, implementaria mecanismos de respaldo y recuperacion de datos para proteger la informacion contra posibles fallos o perdidas.