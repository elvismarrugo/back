# 🧠 Backend EFS

Backend del proyecto educativo `Emprende Full Stack`, donde encontrarás todo lo necesario para aprender a darle instrucciones a un servidor utilizando JavaScript, para que éste pueda recibir peticiones de un navegador y gestionarlas según tus necesidades.

A continuación te explico uno a uno los archivos y carpetas que componen este proyecto:

## 📝 .env

Este archivo contiene las variables de entorno que necesitamos para que la aplicación funcione en nuestra computadora (entorno LOCAL), y es leído por la librería `dotenv` que importamos al inicio del entry point de nuestra aplicación:

```js
import "dotenv/config"
```

Este archivo no debe ser enviado a Github ya que podría contener información sensible, y por eso está definido dentro del archivo `.gitignore` que veremos más adelante.

Además, este archivo `NO SE NECESITA EN PRODUCCIÓN`, ya que cuando nuestro código se ejecuta en la Web App que creamos en Azure, las variables de entorno las definimos en la pestaña `Configuración` de esa aplicación:

![Imagen donde vemos desde dónde se definen las variables de entorno de nuestro backend en Azure](./images/Azure%20ENV%20VARIABLES.jpg)

## 📝 .env.example

Este archivo contiene la denominación de las `variables de entorno` que necesita nuestra aplicación para funcionar correctamente cuando la iniciemos. Lo subimos a Github para que otras personas sepan que las necesitan para levantar la app.

## 📝 .gitignore

Acá indicamos qué archivos y carpetas queremos evitar que se suban a Github, principalmente por cuestiones de seguridad (para no subir información sensible) y optimización.

Por ejemplo, por seguridad ignoramos el `.env` y por optimización ignoramos la carpeta `/node_modules` porque la misma se vuelve a agregar a nuestro proyecto en el proceso de deploy cuando el proveedor de servicios en la nube que elijamos ejecute un `yarn install --production` para instalar todas las dependencias de producción de nuestro proyecto.

## 📝 Dockerfile

En este archivo definimos una IMAGEN de Docker para garantizar la portabilidad de nuestra aplicación, es decir, que funcione en CUALQUIER LADO (en local o en la nube).

En síntesis, una IMAGEN de Docker es una plantilla para instanciar/levantar un CONTENEDOR de Docker que cumplan con las características definidas en la IMAGEN. El CONTENEDOR es el entorno en el cual se ejecutará nuestra aplicación, y contiene TODO lo que ésta necesita para funcionar.

Los contenedores de Docker se ejecutan de manera aislada en el servidor, con lo cual, al instanciar ese contenedor, nos garantizamos que, sin importar DÓNDE lo despleguemos, nuestra app siempre va a tener TODO lo que necesita para funcionar, por ejemplo:

- Va a tener instalado Node JS
- Va a tener las variables de entorno
- Va a tener las dependencias instaladas
- Cualquier otra cosa que necesite

El servidor donde querramos levantar nuestra app podría tranquilamente NO TENER instalado NODE JS, y nuestra aplicación funcionar perfectamente, porque nuestro contenedor SI LO TIENE INSTALADO.

La denominación de este archivo debe ser exactamente `Dockerfile`, ya que es el archivo que va a buscar el servicio en la nube que intente hacer el `build` de nuestra imagen, la que luego nos permitirá `instanciar contenedores` que cumplan con las características de dicha imagen.

## 📝 index.ts

Este es el punto de inicio (entry point) de nuestra aplicación.

Como en este proyecto estamos utilizando TypeScript, nuestro código primero debería pasar por un proceso de building, en el cual se generarían los archivos de JavaScript que cumplan con todas las restricciones que fuimos definiendo con TypeScript.

Lo que finalmente terminamos pasándole a Node JS para que interprete es una archivo de JavaScript, no de TypeScript. Este último lo utilizamos al momento de desarrollar, principalmente para que nos impida cometer errores y nos brinde autocompletado.

Si seguimos los `import` de este archivo, es como ir tirando de un hilo que reconstruye toda la lógica de nuestra app.

Ejemplo, cuando ejecutamos...

```js
import routes from "./routes/index"
```

... nos estamos trayendo el objeto `router` que exportamos de dicho archivo, el cual ya estaba configurado con los `routers` que a su vez importamos de los demás archivos de la carpeta `/routes`. Estos archivos a su vez importan código de las carpetas `/controllers`, `/schemas` y `/middlewares`, todo termina estando relacionado con todo.

Solo separamos el código en distintos archivos y carpetas para organizarnos mejor, entender más fácilmente nuestro código y reutilizarlo.

## 📝 package.json

En este archivo definimos `metadata`, `configuraciones` y `dependencias` de nuestra aplicación, así como también `scripts` que pueden ser ejecutados con un gestor de paquetes como `yarn` o `npm` para realizar distintas tareas.

Los scripts más comunes son `dev` y `start` que nos deberían permitir levantar nuestra aplicación en modo de desarrollo y producción respectivamente.

```json
"scripts": {
    "dev": "nodemon index",
    "build": "tsc",
    "start": "node ./dist/index.js"
  }
```

En nuestro caso también tenemos el script `build` que nos permite transpilar nuestro código de TypeScript a JavaScript. Podríamos definir tantos scripts como quisiéramos/necesitáramos.

Las `dependencias` refieren al código de terceros del cual depende nuestra aplicación para funcionar, y se dividen en 2 categorías, las de desarrollo (propiedad "devDependencies") y las de producción (propiedad "dependencies"):

```json
 // Solo las necesitamos para DESARROLLAR
 "devDependencies": {
    "@types/cookie-parser": "^1.4.3",
    "@types/cors": "^2.8.13",
    "@types/express": "^4.17.17",
    "@types/jsonwebtoken": "^9.0.2",
    "@types/multer": "^1.4.7",
    "@types/nodemailer": "^6.4.8",
    "dotenv": "^16.3.1",
    "nodemon": "^2.0.22",
    "ts-node": "^10.9.1",
    "typescript": "^5.1.3"
  },

// Las necesita nuestra aplicación para funcionar en PRODUCCIÓN
  "dependencies": {
    "@azure/storage-blob": "^12.15.0",
    "cookie-parser": "^1.4.6",
    "cors": "^2.8.5",
    "express": "^4.18.2",
    "express-async-errors": "^3.1.1",
    "jsonwebtoken": "^9.0.0",
    "mongoose": "^7.2.2",
    "multer": "^1.4.5-lts.1",
    "nodemailer": "^6.9.3",
    "zod": "^3.21.4"
  }
```

Un ejemplo de `configuración` es la propiedad "engines", donde indicamos que nuestra aplicación tiene que ejecutarse en un entorno que tenga instalada una versión de Node JS mayor o igual a la `18.15.0`.

```json
"engines": {
    "node": ">=18.15.0"
  }
```

## 📝 README.md

Es el archivo que estás leyendo ahora 😅.

Principalmente se utiliza para dar indicaciones a las personas que tengan acceso al repositorio.

Por lo general, un repositorio puede contener código de una aplicación (como por ejemplo ésta), y el creador podría dar indicaciones de cómo ponerla a funcionar, o bien, podría contener código de una librería (como por ejemplo "express") y el creador podría contarte cómo utilizarla en tu proyecto.

## 📝 tsconfig.json

En este archivo definimos cómo queremos que funcione TypeScript:

- Cómo queremos que nos ayude, cuán estricto queremos que sea (la propiedad `"strict": true` activa todas las reglas de tipado)
- A qué versión de JS queremos que transpile nuestro código (propiedad `"target"`)
- En qué carpeta se guardaran los archivos de JS que se generen al hacer el `build` y transpilar nuestra app (propiedad `"outDir"`)
- Qué archivos o carpetas no debería monitorear (propiedad `"exclude"`)
- Otras configuraciones

## 📝 yarn.lock

Este archivo contiene la "fuente de la verdad" respecto de las versiones de las dependencias de nuestro proyecto y su presencia modifica el comportamiento de las instalaciones de dependencias de nuestro proyecto, ahora te lo explico.

Cuando en el package.json definimos una dependencia con el símbolo `^` al inicio, estamos diciendo que cualquier versión de esa librería posterior a la que definamos, nos sirve, siempre y cuándo no cambie la MAJOR VERSION, que es el primer número.

Por ejemplo, cuando definimos la dependencia de `mongoose` así...

```json
"mongoose": "^7.2.2"
```

...estamos diciendo que al instalarla siempre busque la última versión que empiece con `7`, con lo cual, si clonamos el repositorio y hacemos `yarn install`, y la última versión es la `7.5.2`, esa será la instalada.

Esto es así siempre y cuando `NO HAYA` un archivo `yarn.lock` que indique específicamente qué versión de la librería utiliza nuestra aplicación. En el ejemplo de mongoose, si al ejecutar `yarn install` se detecta que el `yarn.lock` define una versión específica de esa librería...

```json
mongoose@^7.2.2:
  version "7.2.2"
  resolved "https://registry.yarnpkg.com/mongoose/-/mongoose-7.2.2.tgz#5e4c9b87f20b0ef74382706da810bad292fa0f03"
  integrity sha512-JPBMTF+oYfLGVFWbHSZ/H+f1GajNanGLYH6c/P0nE3bNJfwGhX573vieGR0kNlNhj3cZk8WCPrnVsTNeUmFUag==
```

...por más que la hayamos indicado el `^` en el package.json y que la última versión sea la `7.5.2`, yarn instalará la versión especificada en el `yarn.lock`.

Si al momento de la instalación no hubiera existido el archivo `yarn.lock`, se hubiera instalado la versión `7.5.2` de mongoose y creado un `yarn.lock` con la siguiente información:

```json
mongoose@^7.2.2:
  version "7.5.2"
  resolved "https://registry.yarnpkg.com/mongoose/-/mongoose-7.5.2.tgz#1561cd1fe93c8453e65cea73203ce7eadc5deb7b"
  integrity sha512-yEkmI1jfiog7QUvMWz3eB/XoA3/5DrVvSz+z3V5hnq8VtZIHC7ujEV0RKzRXwr8QNMOs+OTB7+aK7R/N/V3yXA==
```

## 📁 controllers

Carpeta donde guardamos el código de las funciones que se van a ejecutar cuando recibamos peticiones en algunas de las rutas que configuramos para nuestro servidor.

## 📁 db

Carpeta donde guardamos el código que nos permite conectarnos al clúster que creamos en [MongoDB Atlas](https://www.mongodb.com), usando la librería [mongoose](https://mongoosejs.com).

## 📁 helpers

Carpeta que contiene lógica que decidimos encapsular en funciones principalmente por 2 motivos:

- Necesitábamos utilizarla en más de un sitio
- Queríamos simplificar la comprensión del código en algún archivo que estaba quedando demasiado extenso.

## 📁 middlewares

Carpeta donde guardamos código que necesitamos para procesar requests antes o después<sup><small>1</small></sup> de que las mismas sean "controladas" por las funciones de la carpeta "controllers".

<small style="color: #888;">1. El middleware de errores se ejecuta después de que la request fuese procesada por su controlador correspondiente.</small>

## 📁 models

Carpeta donde utilizamos [mongoose](https://mongoosejs.com) para definir los ESQUEMAS que deben respetar los documentos a almacenar en las diferentes colecciones de nuestra base de datos de MongoDB.

Adicionalmente, utilizamos [mongoose](https://mongoosejs.com) para crear MODELOS que nos permiten comunicarnos con cada colección de nuestra base de datos. Cada MODELO es configurado para respetar un determinado ESQUEMA, lo cual nos permite garantizar la calidad de la información que almacenamos en MongoDB.

## 📁 node_modules

Carpeta que se crea automáticamente al ejecutar `"yarn"` desde la consola e instalar las dependencias de nuestro proyecto que están definidas en el <span style="background-color: #333; border-radius: 5px; padding: 0 0 5px 5px;">
package.json
`.

## 📁 routes

Carpeta donde utilizamos el Router de [express](https://mongoosejs.com) para configurar distintas rutas de nuestra aplicación. Cada archivo contiene rutas que están relacionadas por algún motivo, por ejemplo:

- Por estar relacionadas a una misma entidad de nuestra base de datos, como `clients.ts`.
- Por estar relacionadas a una misma funcionalidad o pieza de lógica de nuestra aplicación, como como `auth.ts`.

Por último, en el archivo `index.ts` configuramos un nuevo Router, que contendrá tantas rutas como archivos hayan en esta carpeta, y cada ruta será gestionada por el Router importado de su archivo correspondiente.

```javascript
import authRoutes from "./auth"
import salesRoutes from "./sales"
import productsRoutes from "./products"
import clientsRoutes from "./clients"
import attachmentsRoutes from "./attachments"
import commentsRoutes from "./comments"
const router = express.Router()

router.use("/auth", authRoutes)
router.use("/sales", salesRoutes)
router.use("/products", productsRoutes)
router.use("/clients", clientsRoutes)
router.use("/attachments", attachmentsRoutes)
router.use("/comments", commentsRoutes)

export default router
```

Este último Router que exportamos contiene TODA la configuración de rutas de la lógica de nuestra aplicación, y es el que finalmente termina detrás de la ruta `"/api"` en el entry point de nuestra aplicación:

```javascript
import routes from "./routes/index"
const app = express()

app.use("/api", router)
```

Noten que en este caso no se trata de un Router, sino de la aplicación en sí misma. La inicialización ya no es con `const router = express.Router()`, sino que es `const app = express()`.

## 📁 schemas

Carpeta donde guardamos todo lo que tenga que ver con tipos de datos de `TypeScript` o validaciones de `Zod`.

Zod es una librería para hacer validaciones de una manera muy sencilla, creando `ZodObjects` que nos permiten evaluar si OTROS OBJETOS (comunes, de javascript) cumplen dichas validaciones o no, utilizando su método `parse`.

Veamos un ejemplo del archivo `/schemas/auth.ts`:

```js
import z from "zod"

const EmailParamSchema = z.object({ email: z.string().email("Email inválido") })

const LoginBodySchema = z.object({ code: z.string().length(6) })

export const LoginSchema = z.object({
  body: LoginBodySchema,
  params: EmailParamSchema,
})

export const GetCodeSchema = z.object({
  params: EmailParamSchema,
})
```

En las líneas previas creamos `ZodObjects` que nos permitirán evaluar si los objetos que llegan en los `params` y el `body` de las `requests` recibidas en las rutas `/api/auth/login/:email` y `/api/auth/login/:email/code` cumplen las validaciones que definimos o no.

Para ello, en el archivo `/routes/auth.ts` utilizamos el middleware que nos importamos de `/middlewares/validateRequest.ts`, y le pasamos como parámetro el `ZodObject` correspondiente:

```js
import { validateRequest } from "../middlewares/validateRequest"
import { GetCodeSchema, LoginSchema } from "../schemas/auth"

const router = express.Router()

router.post("/login/:email", validateRequest(LoginSchema), login)
router.post("/login/:email/code", validateRequest(GetCodeSchema), generateCode)
```

Finalmente, dentro del middleware `validateRequest`, retornamos una función que nos permite validar si las propiedades `/params`, `body` y `query` de la `request`, cumplen con las validaciones del `ZodObject` recibido como parámetro, llamando su método `parse`.

```js
import { NextFunction, Request, Response } from "express"
import { AnyZodObject } from "zod"

export const validateRequest = (schema: AnyZodObject) => {
  return (req: Request, _: Response, next: NextFunction) => {
    schema.parse({ body: req.body, params: req.params, query: req.query })
    next()
  }
}
```

Si la validación es exitosa, se ejecutará la función `next()` y la request continuará su camino. De lo contrario, Zod arrojará un error, que será capturado por la librería `express-async-errors` y controlado por el middleware `handleErrors` que configuramos en el entry point de nuestra aplicación, luego de configurar las rutas:

```js
import { handleErrors } from "./middlewares/errors"

const app = express()
...
// Gestión de requests
app.use("/api", routes)

// Gestión de errores
app.use(handleErrors)
```

Dentro del middleware `handleErrors`, utilizamos el helper `logger` (importado desde `/helpers/logger.ts`), que no es más que una función donde creamos documentos en la colección `errors` de MongoDB, para tener constancia y detalle sobre:

- El `error` que ocurrió
- La `request` que originó el error
- El `usuario` que tuvo el error

</br>

```js
import { ErrorModel } from "../models/error"

export const logger = async (err: any, origin: string, req: MyRequest) => {
  try {
    await ErrorModel.create({
      origin,
      ...err,
      path: req.originalUrl,
      body: req.body,
      params: req.params,
      query: req.query,
      user: req.user,
    })
  } catch (error) {
    console.log("Error al guardar el error")
  }
}
```

## 📁 utils

En esta carpeta solemos guardar configuraciones o constantes que pretendemos reutilizar dentro de nuestra aplicación.

A diferencia de la carpeta `helpers`, en donde almacenamos `funciones` con una cierta lógica, en esta carpeta solo pretendemos guardar valores estáticos o expresiones muy pequeñas.

A modo de ejemplo, actualmente solo tenemos una constante `CONFIG` con una propiedad `isProd` que nos permite saber si nuestro código se está ejecutando en producción o no evaluando el contenido de la variable de entorno `NODE_ENV`:

```js
export const CONFIG = {
  isProd: process.env.NODE_ENV === "production",
}
```

Posteriormente podríamos agregar más propiedades a este objeto y sabríamos que todos estos valores estáticos o pequeñas evaluaciones las tenemos centralizadas en un solo lugar.