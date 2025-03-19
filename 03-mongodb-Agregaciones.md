# Agregaciones en MongoDB (Framework)

## metodos para realizar agregaciones simples 
- distinct(): Devuelve valores no duplicados 
- countDocuments(): Cuenta los documentos en una colletion 
- estimatedDocumentCount(): Cuenta de manera aproximada durante un periodo de tiempo 

##una agregacion pipeline consta de una o mas (stage) que prosesan documentos

1. Cada etapa realizada una operacion en los documentos de entrada. por ejemplo, una fase que puede filtral documentos, agrupar documentos y calcular valores.



2. los documentos que se generen en una fase pasan a la siguiente fase. 

3. puede devolver resultados para grupos de documentos como totales, maximo, minimo, etc


### Se utiliza la clausula "aggregate"

- Existen una serie de operadores que se pueden utilizar para realizar operaciones. Se tienen distintos tipos: etapa, de comparación, booleanos, aritméticos, de cadena,etc.

## Métodos Simples: countDocuments() y distinct()

1 contar los documentos de la colección libros
```json
db.libros.countDocuments()
```

2. Contar los documentos de la editorial Terra

```json
db.libros.countDocuments({editorial: {$eq:'Terra'}})
db.libros.countDocuments({editorial:'Terra'})
```

3. Mostrar todos los libros mostrando solamente la editorial

4. Mostrar todos las distintas editoriales
```json

db.libros.distinct('editorial')
```

[Documentación de Agregaciones](https://www.mongodb.com/docs/manual/aggregation/)

## $match. Una pipeline básica
## tienen funciones de etapa
``` json
db.libros.aggregate(
    [
        {
            $match:{editorial:"Terra"}
        }
    ]
)
```

## $project. Incluir y renombrar campos
```json
db.libros.aggregate(
   [
    {
        $match:{editorial:'Terra'}
    },
    {
        $project:{
            _id:0,
            titulo:1, 
            precio:1, 
            NombreEditorial:"$editorial",
            editorial:1
        }
    }
   ]  
)
```
- Generado por MongoCompass
```json
[
  {
    $match:
    
        editorial: "Terra"
      }
  },
  {
    $project:
     
      {
        _id: 0,
        precio: 1,
        cantidad: 1,
        NombreEditorial: "$editorial",
        editorial: 1
      }
  }
]
```
# Crear un campo calculado con #project
```json
[
  {
    $match:
     
      {
        editorial: "Terra"
      }
  },
  {
    $project:
    
      {
        _id: 0,
        titulo: 1,
        precio: 1,
        cantidad: 1,
        "Nombre Editorial": "$editorial",
        "Total de Ganancias": {
          $multiply: ["$precio", "$cantidad"]
        }
      }
  }
]
```

## sort. Ordenaciones
```json
[
  {
    $match:
     
      {
        editorial: "Terra"
      }
  },
  {
    $project:
     
      {
        _id: 0,
        titulo: 1,
        precio: 1,
        cantidad: 1,
        "Nombre Editorial": "$editorial",
        "Total de Ganancias": {
          $multiply: ["$precio", "$cantidad"]
        }
      }
  },
  {
    $sort:
     
      {
        precio: 1
      }
  }
]
``` 
## $group.Agrupaciones

[Agrupaciones](https://www.mongodb.com/docs/manual/reference/operator/aggregation/group/)

- Cuantos documentos hay por cada una de las editoriales
``` json
[
  {
    $group:
      {
        _id: "$editorial",
        "Numero Documentos": {
          $count: {}
        }
      }
  }
]
```


- Cuantos documentos hay por cada una de las editoriales por 
-- numero de documentos de manera de descendentes





- Utilizando Mongo Atlas Base de Datos sample_airbnb
-- Agrupar por tipo de propiedad, mostrando el numero de propiedades
- y el promedio de sus precios

``` json
{
  _id: "$property_type",
  Numero: {
    $count: {}
  },
  Media:
  {
    $avg:"$price"
  }
}
``` 
- Operadores $set 
``` json
[
  {
    $group:
      {
        _id: "$property_type",
        Numero: {
          $count: {}
        },
        Media: {
          $avg: "$price"
        }
      }
  },
  {
    $set:
      {
        Media_Total: {
          $trunc: "$Media"
        }
      }
  }
]

``` 
-  Operador $unset

``` json
[
  {
    $group:
     
      {
        _id: "$property_type",
        Numero: {
          $count: {}
        },
        Media: {
          $avg: "$price"
        }
      }
  },
  {
    $set:
     
      {
        Media_Total: {
          $trunc: "$Media"
        }
      }
  },
  {
    $unset:
   
      ["Media", "Media_Total"]
  }
]

-- Quitando solo un campo

[
  {
    $group:
    
      {
        _id: "$property_type",
        Numero: {
          $count: {}
        },
        Media: {
          $avg: "$price"
        }
      }
  },
  {
    $set:
     
      {
        Media_Total: {
          $trunc: "$Media"
        }
      }
  },
  {
    $unset:
     
      "Media"
  }
]
```
- Creando nuevas colecciones utilizando el operador $out
-- Nota: debe ser el último en la agregación
```json
[
  {
    $group:
     
      {
        _id: "$property_type",
        Numero: {
          $count: {}
        },
        Media: {
          $avg: "$price"
        }
      }
  },
  {
    $set:
     
      {
        Media_Total: {
          $trunc: "$Media"
        }
      }
  },
  {
    $unset:
     
      "Media"
  },
  {
    $out:
     
      {
        db: "sample_airbnb",
        coll: "media_propiedades"
  
      }
  }
]
```
- Ejemplos con operadores de comparación y lógicos

```json
[
  {
    $project:
     
      {
        _id: 0,
        price: 1,
        name: 1,
        room_type: 1,
        caro: {
          $gt: ["$price", 300]
        },
        medio: {
          $and: [
            {
              $gte: ["$price", 100]
            },
            {
              $lte: ["$price", 300]
            }
          ]
        },
        baratito: {
          $lt: ["$price", 100]
        }
      }
  }
]

```

- $cond - Devuelve valores según una condición (es parecido a un operador ternario de un lenguaje de programación)

``` Json
[
  {
    $project:
   
      {
        _id: 0,
        price: 1,
        name: 1,
        room_type: 1,
        caro: {
          $cond: [
            {
              $gt: ["$price", 300]
            },
            "Si",
            "No"
          ]
        },
        medio: {
          $cond: [
            {
              $and: [
                {
                  $gte: ["$price", 100]
                },
                {
                  $lte: ["$price", 300]
                }
              ]
            },
            "Si",
            "No"
          ]
        },
        baratito: {
          $cond: [
            {
              $lt: ["$price", 100]
            },
            "Si",
            "No"
          ]
        }
      }
  }
]


- Views


db.createView("ganancias_libros", 
"libros",
[
  {
    $match:
      
      {
        editorial: "Biblio"
      }
  },
  {
    $project:
     
      {
        _id: 0,
        titulo: 1,
        precio: 1,
        cantidad: 1,
        "Nombre de Editorial": "$editorial",
        "Total de Ganancias": {
          $multiply: ["$precio", "$cantidad"]
        }
      }
  },
  {
    $sort:
     
      {
        "Total de Ganancias": -1
      }
  }
])

db["ganancias_libros"].find(
  {"Total de Ganancias":{$lte:240}}
  ,{titulo:1, 'Total de Ganancias':1}).sort({titulo:-1})

  ```