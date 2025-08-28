---

## ¿Qué es Node.js?

Node.js es un entorno de ejecución de JavaScript fuera del navegador. Permite crear programas de consola, scripts y servidores web usando JavaScript.

- Usa el motor V8 (el mismo de Google Chrome) para interpretar y ejecutar JS.

---

## Cómo descargar e instalar Node

1. Ir a [nodejs.org](http://nodejs.org/).
2. Descargar la versión LTS (Long Term Support) → es la estable y recomendada para proyectos reales.
    - Las versiones Current son más nuevas pero no estables.
3. Verificar instalación en la consola:

```bash
node -v
```

> Al instalar Node se instala también npm (Node Package Manager), el “compañero inseparable” de Node.
> 

---

## ¿Para qué sirve npm?

npm es el gestor de paquetes de Node. Sirve para:

- Instalar librerías creadas por la comunidad.
- Administrar dependencias del proyecto.
- Definir scripts que automatizan tareas (iniciar el server, correr tests, etc.).

Las librerías se publican en [npmjs.com](http://npmjs.com/).

> ¿Qué es una dependencia? Cualquier librería externa de la que depende tu proyecto para funcionar (por ejemplo, Express).
> 

---

## package.json

Es el archivo central del proyecto: describe el proyecto, lista dependencias y define scripts.

### Crear un proyecto desde cero

1. Pararse en el directorio donde irá el proyecto.
2. Ejecutar en consola:

```bash
npm init
```

Agregá -y para aceptar valores por defecto:

```bash
npm init -y
```

Esto crea el package.json.

> package.json está en formato JSON: pares clave: valor. Puede tener objetos anidados (por ejemplo, en scripts).
> 

### Scripts de ejemplo

```json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js",
    "test": "echo \"(acá irían tus tests)\""
  }
}

```

Ejecutar scripts:

```bash
npm start       # corre node src/index.js
npm run dev     # corre nodemon src/index.js
npm test        # scripts especiales pueden correr sin 'run'

```

> Sin run solo podés ejecutar: start, test, stop y restart. Para el resto (ej: dev, build) usá npm run <script>.
> 

---

## Dependencias: de producción vs. de desarrollo

- **dependencies** → necesarias **en producción** (ej.: express).
- **devDependencies** → usadas **solo en desarrollo** (ej.: nodemon).

Instalar una dependencia **de desarrollo**:

```bash
npm i -D nodemon

```

Esto agrega algo como:

```json
"devDependencies": {
  "nodemon": "^3.1.10"
}

```

Al instalar paquetes se crea la carpeta node_modules.

- **No** se sube al repo → agregala a .gitignore.
- Cuando alguien clona el proyecto, debe correr npm i o npm install para bajar las dependencias.

---

## Ambientes: desarrollo vs. producción

- **Desarrollo**: usamos herramientas para trabajar cómodos (recarga automática, logs, etc.).
- **Producción**: versión final para usuarios, sin herramientas extra.

### Script de desarrollo

```json
"scripts": {
  "dev": "nodemon src/index.js"
}
```

```bash
npm run dev
```

> ¿Qué hace nodemon? Mantiene el proceso corriendo y recarga la app al detectar cambios.
> 

 Para frenar la ejecución en consola: CTRL + C.

---

## Repaso de funciones de orden superior

Son funciones que **reciben** otras funciones o **devuelven** funciones. Ejemplo:

```jsx
const f = (x) => {
  return (y) => y + x
}

const sumar4 = f(4)      // devuelve una función
console.log(sumar4(6))   // 10

```

### Arrays: filter, map, reduce

**Ejemplo base:**

```jsx
const arr = [
  { nombre: "a", valor: 3 },
  { nombre: "b", valor: 8 },
  { nombre: "c", valor: 12 }
]

```

**1) Quedarse con los objetos cuyo valor ≥ 10:**

```jsx
const mayores = arr.filter(e => e.valor >= 10)
// [ { nombre: "c", valor: 12 } ]

```

**2) Obtener solo los nombres de esos objetos:**

```jsx
const nombresMayores = arr
  .filter(e => e.valor >= 10)
  .map(e => e.nombre)
// [ "c" ]

```

**3) reduce: dejar un solo resultado (ej.: el objeto con mayor valor):**

```jsx
const maximo = arr.reduce((a, b) => (a.valor > b.valor ? a : b))
// { nombre: "c", valor: 12 }

```

> Si no pasás valor inicial a reduce, a arranca siendo el primer elemento y b el segundo.
> 

---

## Express: crear un servidor web

Express no viene con Node: hay que instalarlo desde npm.

```bash
npm i express
```

> Es una dependencia de producción, así que va en dependencies.
> 

### Paso a paso: servidor mínimo

```jsx
const express = require("express")
// Si el string no comienza con './' o '../', require busca en node_modules

const app = express() // ejecutar la función crea la app

// Middleware para poder recibir JSON en req.body
app.use(express.json())

// Puerto en el que el servidor "escucha" pedidos
const PORT = 3001
app.listen(PORT, () => {
  console.log(`App iniciada en el puerto ${PORT}`)
})

```

### Endpoints básicos

**GET /api/series** – devolver datos

```jsx
const series = [ { id: 1, nombre: "Lost", cant_temporadas: 7 } ]

app.get('/api/series', (req, res) => {
  res.status(200).json(series)
})

```

**POST /api/series** – recibir datos

```jsx
app.post('/api/series', (req, res) => {
  const data = req.body           // viene en el body (JSON)
  const save = { id: Date.now(), ...data } // "spread": copia las props de data
  series.push(save)
  res.status(201).json(save)      // devolvemos lo creado
})

```

> ...data (operador spread) copia todas las propiedades del objeto data dentro de uno nuevo con el id agregado.
> 

### req.body, req.params, req.query

- req.body → datos enviados en el cuerpo (POST/PUT/PATCH) en formato JSON (requiere express.json).

(Esto no lo vimos todavía pero no está de más ponerlo)

- req.params → parámetros en la ruta (ej.: /api/series/:id).
- req.query→ parámetros en la query string (ej.: /api/series?desde=10).

### Probar otros verbos HTTP

Para probar endpoints se puede usar **Postman** o **Thunder Client** (extensión de VS Code).

---

## Navegación de directorios (rutas relativas)

- . → el directorio actual.
- ..→ El directorio anterior.
- / → separador de carpetas.

---

## ¿Qué es un *endpoint*? ¿Qué es “pegarle” a un endpoint?

- Un endpoint es una ruta (URL) de tu servidor que responde a un verbo HTTP (GET/POST/PUT/DELETE).
- “Pegarle a un endpoint” = hacer una petición a esa ruta (desde el navegador, Postman, otra app, fetch, etc.).

Ej.: “Pegarle al endpoint GET /api/series” ⇒ abrir http://localhost:3001/api/series o usar Postman para hacer un GET.

---

## Algunos comandos útiles:

### Inicializar un repo y primer commit

```bash
git init
git status

git add archivo.js   # agregar al área de 'staging'
# o
git add .

git commit -m "Mensaje claro y descriptivo"

```

**Commits**: Es muy importante que sean **claros, breves y descriptivos** de lo que hiciste. 

---

## Comando echo

echo imprime en pantalla el texto que le pasás. Útil para probar scripts o dejar mensajes.

```bash
echo "Hola mundo"
```

Ejemplo como script de prueba en package.json:

```json
"scripts": { "test": "echo \"Corriendo tests\"" }

```

---

### Abrir VS Code rápido

En el directorio del proyecto:

```bash
code .
```

---

## Checklist rápido para levantar el server

1. npm init -y
2. npm i express
3. npm i -D nodemon
4. Agregar scripts en package.json:

```json
"scripts": {
  "start": "node src/index.js",
  "dev": "nodemon src/index.js"
}

```

1. Crear src/index.js con Express, endpoints y express.json().
2. Correr npm run dev y abrir http://localhost:3001/api/series.
3. Probar POST /api/series con Postman/Thunder Client.

---

En esta clase también se realizó el ejercicio js_practica_usuarios

---

Autor: Maximiliano Alonso

Materia: Estrategias de Persistencia

Universidad Nacional de Hurlingham
