# Ejercicios de Agregaciones
> Jose Luis Herrera Gallardo 
> Juan Mateo Henrnadez De Luna | 5TIDSM-G1

#### Consultas:
##### 1.Cuenta los productos de tipo “medio”, usando un método básico

```mongodb
[
  {
    $match: {
      tipo: "medio",
    },
  },
  {
    $count:
      /**
       * Proporciona el nombre del campo para el recuento.
       */
      "productos del tipo medio",
  },
]
```
##### 2. Indicar con un distinct, las empresas (fabricantes) que hay en la colección
```mongodb
[
  {
    $group:
      /**
       * _id: The id of the group.
       * fieldN: The first field name.
       */
      {
        _id: "$fabricante",
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 1,
      },
  },
]
```

##### 3. Usando aggregate, visualizar los productos que tengan más de 80 unidades
```mongodb

[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $gt: 80,
        },
      },
  },
]
```
##### 4. Con $project visualizar solo el nombre, unidades y precio de los productos que tengan menos de 10 unidades
```mongodb
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        unidades: 1,
        precio: 1,
      },
  },
]
```

##### 5. Con $project ponemos el fabricante, pero le cambiamos el nombre por “empresa”. Usamos el mismo comando anterior
```mongodb
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        empresa: "$fabricante",
        unidades: 1,
        precio: 1,
      },
  },
]
```

##### 6. Añadir a la consulta anterior un campo calculado que se llame total y que multiplique precio por unidades.
```mongodb
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
      },
  },
]
```

##### 7. Hacer que el nombre salga en mayúsculas con el operador $toUpper
```mongodb
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: {
          $toUpper: "$nombre",
        },
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
      },
  },
]
```

##### 8. Añadir un campo calculado que ponga el nombre del producto y el tipo concatenado con el operador $concat. Le llamamos al campo “completo”
```mongodb
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
        completo: {
          $concat: ["$nombre", " - ", "$tipo"],
        },
      },
  },
]
```

##### 9. Ordena el resultado por el campo “total”
```mongodb
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
        completo: {
          $concat: ["$nombre", " - ", "$tipo"],
        },
      },
  },
  {
    $sort:
      /**
       * Provide any number of field/order pairs.
       */
      {
        total: 1,
      },
  },
]
```
##### 10. Haciendo una nueva consulta, averiguar el numero de productos por tipo de producto
```mongodb
[
  {
    $group:
      /**
       * _id: The id of the group.
       * fieldN: The first field name.
       */
      {
        _id: "$tipo",
        count: {
          $sum: 1,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        tipo: "$_id",
        cantidad: "$count",
      },
  },
]
```

##### 11. Añadir el valor mayor y el menor
```mongodb
[
  {
    $group:
      /**
       * _id: The id of the group.
       * fieldN: The first field name.
       */
      {
        _id: "$tipo",
        cantidad: {
          $sum: 1,
        },
        max_precio: {
          $max: "$precio",
        },
        min_precio: {
          $min: "$precio",
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        tipo: "$_id",
        cantidad: 1,
        max_precio: 1,
        min_precio: 1,
      },
  },
]
```

##### 12. Añade el total de unidades por cada tipo
```mongodb
[
  {
    $group:
      /**
       * _id: The id of the group.
       * fieldN: The first field name.
       */
      {
        _id: "$tipo",
        cantidad: {
          $sum: 1,
        },
        total_unidades: {
          $sum: "$unidades",
        },
        max_precio: {
          $max: "$precio",
        },
        min_precio: {
          $min: "$precio",
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        tipo: "$_id",
        cantidad: 1,
        total_unidades: 1,
        max_precio: 1,
        min_precio: 1,
      },
  },
]
```

##### 13. Con el operador set y el operador “$substr” visualiza todos los datos del producto "Small Metal Tuna" y los primeros 5 caracteres del nombre
```mongodb
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        nombre: "Small Metal Tuna",
      },
  },
  {
    $set:
      /**
       * field: The field name
       * expression: The expression.
       */
      {
        nombre: {
          $substr: ["$nombre", 0, 5],
        },
      },
  },
]
```

##### 14. Creamos una salida que tenga el nombre del articulo y el total (precio por unidades) y lo guardamos en una colección denominada productos2
```mongodb
[
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        total: {
          $multiply: ["$precio", "$unidades"],
        },
      },
  },
  {
    $out:
      /**
       * Provide the name of the output collection.
       */
      "productos2",
  },
]
```

