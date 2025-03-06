# Crud y consultas en MongoDB
Solo se crea si contiene por lo menos una coleccion
*use bd1*

## Como crear una coleccion
use db1
db.createCollection("Empleado")

## Mostrar las colecciones
show collections

## Insertar un documento


json
 db.Alumnos.insertOne(
  {
    _id: ObjectId('679d2ab6078c2dd4adcb0ce2'),
    nombre: 'Soyla',
    apellido1: 'Vaca',
    ciudad: 'San Miguel de las Piedras'
  }
)


## Insercion
json
db.Alumnos.insertOne
(
 { 
    nombre: "Joaquin",
    apellido1: "Dorian",
    apellido2: "Guerrero",
    edad: 15,
    aficiones: ["Cerveza","Hueva","Canavis"]
 }
)

## Insercion de documentos mas complejos con documentos anidados 
json
db.Alumnos.insertOne
(
 {
    nombre: "oscar",
    apellido1: "salinas",
    apellido2: "escobar",
    edad: 20,
    estudios: [
            "Kinder","Primaria","Secundaria","Preparatoria"
              ],
    experiencia: {
    lenguaje: "SQL",
    sbd: "SQL Server",
    aniosExp: 3
        }
 }
)


# Practica 1

## Cargar datos
[Libros.json](./data/libros.json)


## busquedas. condiciones simples de igualdad. metodo find()
```json 
db.libros.find({})
```
2. mostrar todos los documentos que sean de la editorial Biblio
```json
bd1> db.libros.find({editorial:'Biblio'})
```
3. mostrar que todos los librois con presio sea 25
``` json 
bd1> db.libros.find({precio : 25})

```
4. seleccionar todos los libros donde el titulo sea json para todos 
``` json 
bd1> db.libros.find({titulo:'JSON para todos'})
```
##operadores de comparacion 
[operadores de comparacion](https://www.mongodb.com/docs/manual/reference/operator/query/)

![operadores de conparacion ](./img/operadores%20relacionales.png)

1. mostrar todos lo documentos ddonde el precio sea mayor a 25
```json 
bd1> db.libros.find(
... {
... precio : {$gt: 25 }
... }
... )
```
2. mostrar los documentos donde precio sea 25 
``` json
bd1> db.libros.find( { precio: { $eq: 25 } } )
```
3. mostrar los documentos cuya cantidad sea meno a 25
```json 
bd1> db.libros.find( { cantidad: { $lt: 5 } } )
```
4.mostrar los documentos que pertenescan a la editoria biblio o planeta 
```json 
bd1> db.libros.find( { editorial: { $in: ['Biblio' , 'Planeta' ] }} )
```

5. mostrar todos los documentos que cuesten 20 o 25 
```json 
db.libros.find( { precio: { $in: [20 , 25 ] }} )
```
6. mostrar todos los documentos que cuesten  no cuesten 20 o 25
```json 
db.libros.find( { precio: { $nin: [20 , 25 ] }} )

``` 
7. mostrar el primer libro que cueste 20 o 25 
``` json
db.libros.findOne( { precio:{ $in:[20 ,25]} } )
```

## operadores logicos  

![operdores logicos](./img/operadores-logicos.png)

### operador AND

Dos posibles oipciones de AND 
1. la simple mediante condiciones separadas por comas

***sintaxis*** 

db.coleccion.find({condicion1, condicion2}) -> con asume que es una ***AND***

2. usando el operador $and
***sintaxis***

db.coleccion.find($and:[{condicion1},{condicion2}]})

### ejercicios 

1. mostrar todos los aquellos libros que cuesten mas de 25 y cuya cantidad sea inferior a 15
***fORMA SIMPLE***
```json
db.libros.find( {precio:{$gt : 25}, cantidad: {$lt:15}})
```
***operador  AND***
db.libros.find( { $and:[{precio:{$gt:25}},{cantidad:{$lt:15}}]})

## mostrar todos aquellos libros  que cuesten mas de 25 o cuya cantidad  sea inferior a 15 
``` json 
 db.libros.find( { $or: [ { precio: { $gt: 25 } }, { cantidad:{$lt: 10 }} ] } )
```

### AND Y OR Combinadas 
1. mostrar los libros de la editorial Biblio con precio mayor a 40  o libros de  la editorial Planeta con precio mayor a 30  
``` json 

db.libros.find (
{
  $or:[
      {$and:[{editorial:'Blibio'},{precio:{$gt:40}}]},
      {$and: [{editorial:{$eq:'Planeta'}},{precio:{$gt:30}}]}
  ]
}
)
```
*** sintaxis *** 
db.coleccion.find(filtro, columnas)
db.libros.find({}, {titulo:1})


1. seleccionar todos los documentos mostrando el titulo y la editorial 
```json 
bd1> db.libros.find({}, {titulo:1})
bd1> db.libros.find({}, {titulo:1 , editorial: 1, _id:0})
```
2. seleccionar todos los documentos de la editorial Planeta, mostrando solamente el titulo y la editorial 
```json 
bd1> db.libros.find({Titulo:Planeta}, {titulo:1 , editorial: 1, _id:0})
```
## Operador exists (Peromite SABER SI UN CAMPO SE ENCUENTRA O NO EN UN)
```json
db.libros.InsertOne({_id:10, titulo: 'Mongo entornos graficos', editorial: 'Tierra', precio: 125})
```

1.Mostrar los docs que no contengan el campo cantidad

```json
db.libros.find({ cantidad:{$exists:false}})
```


#### Operador Type (Permite preguntar si un determinado campo corresponde con un tipo) 

[Operador Type](https://www.mongodb.com/docs/manual/reference/operator/query/type/#mongodb-query-op.-type)

```jason
db.libros.find({precio:{$type:1}})

db.libros.find({precio:{$type:16}})

db.insert.insertOne({
  _id:11,
  titulo:"IA",
  editorial:"Terra",
  precio:125.4,
  cantidad:20
})
```

#### Operador Type (Permite preguntar si un determinado campo corresponde con un tipo) 

[Operador Type](https://www.mongodb.com/docs/manual/reference/operator/query/type/#mongodb-query-op.-type)

1. Mostrar todos los docs donde el precio sean Dosposibles

json
db.libros.find({precio:{$type:1}})
db.libros.find({precio:{$type:16}})


db.libros.insertOne({
  _id:11,
  titulo:'IA',
  editorial:'Terra',
  precio:125.4,
  cantidad:20
})

2. Seleccionar los docs donde el precio sea de tipo entero
db.libros.find({precio:{$type:1}}, {_id:0})

db.libros.insertMany([
 {
    _id: 12,
    titulo: 'IA',
    editorial: 'Terra',
  precio: 125,
  cantidad: 20
  },
  {
    _id: 13,
    titulo: 'Python para todos',
    editorial: 2001,
    precio: 200,
  cantidad: 30
  }]
  )

db.libros.find({precio:{$type:1}}, {_id:0, cantidad:0})


3. Seleccionar todos los docs donde la editorial sea String

db.libros.find({editorial:{$type:2}})

db.libros.find({editorial:{$type:'string'}})

1. Seleccionar todos los documentos don


## Practica de consultas
1. Instalar las tools de monofdb


2. Cargar json
--En local:
  mongoimport --db curso --collection empleados --file empleados

# Modificando Documentos
## Comandos importabtes
1. updateOne -> Modififcar un solo documento
2. updateMany -> Modificar multiples documentos 
3. replaceOne -> Sustituir el contenido completo de un documento

Tiene el segundo formato:

```json
db.collection.updateOne(
  {filtro},{operador: }
)
```

### Operador set
1. Modificar un documento
```json
db.libros.updateOne({titulo:'Python para torpes'},{$set:{titulo:'Java para todos'}})


db.libros.updateOne({_id:2},{$set:{precio:56}})
```
2. Actualizar el precio a 100 y la cantidad a 50 para el _id: 10
```json
db.libros.updateOne({_id:10},{$set:{precio:100, cantidad: 50}})

```
### Modificar Multiples Documentos

--Modificar todos los documentos donde el precio sea mayor a 100 a un precio de 150
```json
db.libros.updateMany({precio:{$gt:100}},{$set:{precio:150}})
```

2. Operador $inc y $mul

--Actualizar con un incremento de 5 todos los documentos
```json
db.libros.updateMany(
  {},
  {$inc:{precio:5}}
)
```
--Actualizar con multiplicacion de 2 todos los documentos donde la cantidad que sean mayores a 20
```json
db.libros.updateMany({cantidad:{$gt:20}},{$mul:{cantidad:20}})
```
--Actualizar todos los documentos donde el precio sea mayor a 20 y 
--se multiplique por 2 la cantidad y el precio

3.Reemplazar Documentos
```json
db1> db.libros.replaceOne({_id:2},{titulo:"De la tierra a la Luna",})

```
# Borrar Documentos 

deleteOne -> Elimnar un solo documento
deleteMany -> Elimina Multiples documentos

Eliminar el documento con id 2

db.libros.deleteOne({_id:2})

Eliminar los documentos donde la cantidad sea mayor o igual a 150

db.libros.deleteMany({cantidad:{$gte:150}})

# Expresiones Regulares

1. Buscar los libros que contengan la letra t

db.libros.find({titulo:/t/})

2. Buscar los libros que en el titulo contengan la palabra JSON

db.libros.find({titulo:/JSON/})

3. Buscar todos los documentos que en el titulo termine en tos

db.libros.find({titulo:/$tos/})

4. Todos los documentos que en el titulo comiencen con J

db.libros.find({titulo:/^J/})

# Operador $regex 

[Operador Regex](https://www.mongodb.com/docs/manual/reference/operator/query/regex/)

--Seleccionar los libros que contengan la palabra para en titulo
db.libro.find({titulo:{$regex:"para"}})

db.libros.find({titulo:{$regex:"JSON"}})

db.libros.find({titulo:{$regex:/JSON/}})

- Distinguir entre mayusculas y minusculas


db.libros.find({titulo:{$regex:/json/}}) -> No distingue entre mayusculas y minusculas

db.libros.find({titulo:{$regex:/json/, $options:"i"}})

db.libros.find({titulo:{$regex:/j/i}})

-- Seleccionar todos los libros que comiencen con j o J

db.libros.find({titulo:{$regex:/^j/i}}) 

-- Seleccionar todos los libros que terminen es

db.libros.find({titulo:{$regex:/es$/i}})

db.libros.find({titulo:{$regex:"es", $options:"i"}})

# Metodo sort (Ordernar Documentos)

1. Ordenar los libros de manera ascendente por el precio

db.libros.find({},{titulo:1,precio:1,_id:0}).sort({precio:1})

2. Ordenar los libros de manera descendete por el precio

db.libros.find({},{titulo:1,precio:1,_id:0}).sort({precio:-1})

3. Ordenar los libros de manera ascendente por la editorial y de manera 
descendente por el precio mostrando el titulo, el precio y la editorial

db.libros.find({},{titulo:1,precio:1,_id:0,editorial:1}).sort({editorial:1,precio:-1})

# Otros metodos skip, limit, size

db.libros.find({},{titulo:1, precio:1, _id:0, editorial:1}).size()

db.libro.find({titulo:{$regex:/Java/i}}).size()

-- Buscar todos los libros pero mostrando los dos primeros

db.libros.find({},{titulo:1,editorial:1,_id:0,precio:1}).limit(2)

-- Mostrar los 3 ultimos libros 

 db.libros.find({},{titulo:1,editorial:1,_id:0,precio:1}).sort({precio:-1}).limit(3)

-- Seleccionar todos los libros ordenados los titulos de forma descendete, 
saltando los dos primeros y el tamano

db.libros.find({}).sort({titulo:-1}).skip(2).size()

# Como borrar colecciones y base de Datos 

use db5

db.createCollection('ejemplo')

 db.ejemplo.insertOne({ nombre: "Chapuin"})

 db.ejemplo.drop()

 db.dropDatabase()








2. Operador $inc y $mul 
- acutalizar preocion con un incremetento de 5 
```
db.ibros.updateMany(
  {},{$inc:{precio:5}}
)
  ```


  - acturalizar todos los ddocumentos que la cantidad se sean mayores a 20 
  ```json 
db.libros.updateMany({cantidad{$gt:20},{$mul:{cantidad:2}}})
```
# borrar documentos 

1. deleteOne -> elimina un solo documento 
2. deleteMany -> elimina multiples documentos 

1. eliminar el documento con id 2
```json 
db.libros.deleteOne({_id:2})

2. eliminar los documentos donde la cantidad sea mayor o igual a 150



# expreciones reguares 
1. buscar los libros quw contengan el titulo tenga la letra t

```json 
bd.libros.find({titulo:/t/})
```

buscar todos los documentos que 

4. todos los documentos que en el titulo comiensen con j



# Operador $regex
(Operador regex)[https://www.mongodb.com/docs/manual/reference/operator/query/regex/]

-- Seleccionar los libros que contengan la palabra para en titulo
```json 
db.libros.find({titulo:{$regex:'para'}})

db.libros.find({titulo:{$regex:'JSON'}})

db.libros.find({titulo:{$regex:/JSON/}})
```

- Distinguir entre mayusculas y minusculas
```json
db.libros.find({titulo:{$regex:/json/}}) ->No distingue entre mayusculas y minusculas

db.libros.find({titulo:{$regex:/json/, $options:"i"}})

```

- seleccionar todos los documentos o libros que comienzen con j o con J

db.getCollection('libros').find({
  titulo
})

## motodo sort (ordenar los libros de manera acendente por el precio )
db.libros.find({},{titulo:1,precio:1,_id:0}).sort({precio:1})


2. ordenar los libros de manera desendente por el precio 
db.libros.find({},{titulo:1,precio:1,_id:0}).sort({precio:-1})

3. ordenr los ibros de manera acendente por la editorial y de manera desendente por el precio, mostrando el titulo el precio y la editorial 


# tors metodos skip,limit,size

db.libros.find({},{titulo:1,precio:1,_id:0}).size()

bd1> db.libros.find({titulo:{$regex:/java/i}}).size()

- buscar todos los libros pero solo mostrando los dos libros

bd1> db.libros.find({},{titulo:1,editorial:1,_id:0}}).size()

mostrar los tres ultimos libros 

db.libros.find({},{titulo:1,precio:1,_id:0}).sort({precio:1})


# Borrar Colecciones y bases de datos 
use db5
db.createCollection('ejemplo')
db.ejemplo.insertOne({
... nombre:'chapuin'})
 db.ejmplo.drop()
 db.dropDatabase()
 