> [ATTENTION!]
> The file belongs as a note to my studies. I'm not here to teach

# ¿Cómo funciona la herencia en javascript?

Generalmente la herencia pensada es el copia pega (de POO), donde tenemos un objeto y este hereda de otro (clases) sus propiedades y métodos.
Javascript no tiene clases, posee un sistema de herencia más simple, versátil y poderoso.
Por eso la herencia para Kyle Simpson (autor de "You don't know js") y para mi son objetos linkeados a otros objetos. Siglas **OLOO**.

Debido a la especificación del lenguaje creado por ECMAScript tenemos propiedades internas dentro del lenguaje y aquí entra **[[Prototype]]**: propiedad que crea una referencia a otro objeto.
Aplica un mecanismo que cuando no encontramos una propiedad de un objeto (no la encontramos en **este**) utiliza otra propidad interna llamada **[[get]]**, continua buscando en el objeto vinculado via [[Prototype]]. Esta serie de vínculos entre objetos se conoce como **cadena de prototipos**.

Podemos controlar este vínculo usando object.create()

```javascript
const literalObj = { a: 21 };
const controlledObject = Object.create(literalObj);

console.log(controlledObject.a); // 21
```

> [TIP!]
> Para no heredar ningún vínculo colocamos el valor null

## This

Es el **contexto**: objeto que está ejecutando una función en un momento específico. Hay veces que tenemos comportamientos raros y por ello debemos de conocer los distintos tipos de enlazamientos (binding) que existen.

Todos ellos van en jerarquía y observan ciertos parámetros:

- Cómo fue escrita la función
- Modificaciones desde la creación
- Lugar de invocación (call site)

1. Lexical Binding (arrow functions)

La llegada de las arrow functions en ES6 viene con una gran cantidad de cambios y aquí hablaremos como afectan a this.
No tienen contexto, acceden al contexto exterior. O sea, utilizan aquel valor en this en donde fue creado (cuando recién es cargado en memoria la función, ¿dónde fue creado? Si, adoptan ese valor).

```javascript
//This doesn't work
const obj = {
  name: "ciruela",
  greet: function () {
    function goodMorning() {
      console.log(`Que tengas lindo día ${this.name}`);
    }
    console.log(`Hola cómo estás? ${this.name}`);
    goodMorning();
  },
};

// this yes!

const obj = {
  name: "ciruela",
  greet: function () {
    const goodMorning = () => {
      console.log(`Que tengas lindo día ${this.name}`);
    };
    console.log(`Hola cómo estás? ${this.name}`);
    //Ignora el default binding ya que "no tiene contexto" lo hereda por así decir.
    goodMorning();
  },
};
```

> [TIP!]
> Si no sabemos el valor de this podemos imprimir su valor!. Solamente es preguntarle el valor en dónde se cree la referencia de nuestra función.

> [IMPORTANT!]
> Al estar primero en la jerarquia de especificaciones, este no puede ser reemplazado por nada.

New binding (object instantiate)

La instancia de objetos ya sea a través de una función constructora o clases (es lo mismo), crea un nuevo objeto (con el operador [**new**](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Operators/new)) con el valor en this del mismo.

```javascript
function Human(name) {
  this.name = name;
} // it returns "this" implicitly

const pipon = new Human("Pipon"); // javascript recognizes the instance when we use "new" operator.
```

3. Explicit binding (indirect invocation)

   Enlazamiento explicito con métodos nativos llamados call, apply y bind. Indicamos el contexto de this escribiendo el nombre del objeto. Call y apply invocan directamente a la función.

   Bind retorna una nueva función con el contexto enlazado al objecto indicado y espera a ejecutarse en el flujo del programa (eventos, timers, callback, etc).

   ```javascript
   const obj1 = {
     name: "pipon",
     greet: function (param) {
       console.log(`hello ${this.name}. Param: ${param}`);
     },
   };
   const obj2 = {
     name: "killita",
   };
   obj.greet.call(obj2, "example of parameter"); // hello killita. Param: example of parameter

   const bindFunction = obj1.greet.bind(obj2);

   bindFunction("i'm a function binded"); // hello killita. Param: i'm a function binded
   ```

   > [WARNING!]
   > Una función creada con bind no puede volver a ser enlazada a otro objeto.

4. Implicit binding (method invocation)

   Enlazamiento implícito. This pertenece al objecto que ejecuta el método (el que está a la izquierda del ".").

   ```javascript
   const obj = {
     name: "pipon",
     greet: function () {
       console.log(`hello ${this.name}`);
     },
   };
   obj.greet(); // hello pipon
   ```

5. Default binding (direct invocation)

   Invocación directa de una "function", el valor de this será el por defecto: window (o undefined si tenemos use strict).

   ```javascript
   function defaultBinding() {
     console.log(`hello ${this.name}`);
   }
   defaultBinding(); // hello window
   ```

> [TIP!]
> No usar this en las funciones globales.

## Expresión

Unidad de código que produce un valor al ser evaluada. Algunas pueden tener efectos secundarios

## expresiones primarias

Todo valor pequeño que por sí solo produce un valor por ejemplo: **valores primitivos** (producen el mismo valor que tienen).

## expresiones con objetos, arrays, funciones

Son referencias: posiciones en memoria donde se encuentran almacenados esos objetos. Cada objeto tiene un identificador único gracias a la referencia, no podemos compararlos. Ejemplo:

```javascript
const obj1 = { same: true };

const ob2 = { same: true };
obj1 === obj2; // (<DC5A1>) === (<08D4B>) false
```

## expresión de función

Cuando escribimos una función en lugar de un valor esperado ya sea en una variable o parametro de otra función. Ejemplo:

```javascript
const funcionExpresada = function () {};

numbers.filter(function filtrarPares(num)}{});
```

## Variables y sus diferencias

Las variables son un espacio en memoria que almacenan un tipo de dato soportado por el lenguaje de programación

**var:**

- tiene [hoisting](https://www.itdo.com/blog/javascript-conceptos-fundamentales-hoisting/)
- scope de función
- reasignación

**let:**

- no tiene hoisting
- scope local
- reasignación

**const:**

- no tiene hoisting
- scope local
- no-reasignable (no significa que sea [inmutable](https://www.todojs.com/datos-inmutables-en-javascript/#:~:text=El%20uso%20de%20estructuras%20mutables,diferente%20en%20un%20momento%20determinado.))

## DOM

Siglas del inglés Document Object Model (Modelo de objeto del documento), interfaz que nos permite crear, cambiar y eliminar elementos del documento html. También podemos añadir eventos a estas etiquetas. Se representa como un árbol de nodos (etiquetas html).

## Scopes

El scope es el contexto actual de ejecución (no el contexto, eso es this!). Un bloque donde tenemos el acceso a determinados valores en cierto momento de la ejecución del programa.

Diferenciamos 2 tipos de scopes:

### scope global

las variables globales pueden ser accedidas desde cualquier parte del programa. Son todas las variables declaradas fuera de toda función o bloque de codigo. Sin importar su declaración con var, let o const.

- var se guarda como una propiedad del objecto **"window"**.

- let y const se guardan como propiedad del objeto **"script"**.

> [WARNING!]
> Las variables en scope global quedan en memoria, esto podría ser contraproducente en el rendimiento, tener cuidado.

### scope local

las variables pueden ser accedidas desde una parte determinada del programa y este tiene 2 subtipos:

**Function scope:** aquí podemos acceder a la variable en todo el scope de una función. Por ejemplo:

```javascript
function number() {
  if (true) {
    var n = 20;
  }
  console.log(n); // 20
}
```

Podriamos acceder desde cualquier ambito de la función number a la variable creada en el if.

**Local scope:**

Aquí se hace uso del "bloque de código" basicamente es todo valor creado dentro de **_{ }_**. Sin importar su valor reservado, sea un while, for, function, object, etc.

```javascript
function str(true) {
if(true){
  let name = "benitoka";
  console.log(name); //benitoka
}
console.log(name); //ReferenceError: name is not defined
}
```

> [TIP!]
> Una buena practica es declarar variables con el scope más reducido posible.

> [NOTE!]
> Los valores se quedan en memoria solamente durante la ejecución de una función.

## Callstack

Es la manera de cómo ejecuta y administra nuestro código javascript. Especificamente, cuando se crea el contexto de ejecución de la función (function execution context), se agrega el callstack colocandole registros o "frames" con información asociada a dicha función. El momento de la salida de la "pila" es cuando se termina la fase de creación y de ejecución de un contexto.

**En términos más simples:**

¿Cuándo ocurre?

El callstack (pila de llamadas) ocurre cuando se ejecutan funciones en un determinado momento dado por su llamada en el flujo de la aplicación.

¿A qué viene su nombre?

Hace referencia a "pila" ya que colocamos el siguiente encima del anterior y así sucesivamente, no podemos colocar la última llamada como primera o viceversa. Y para "desapilar", la última en llamarse es la primera en salir. En ingles esto se conoce como LIFO (last in first out).

## Clousure

Son funciones anidadas que permiten crear una sola función que utilice distintos contextos. Ejemplo: una función "hija" (se retorna) utiliza una variable de la función "padre" y la imprime. Lo más interesante entonces es poder acceder al valor de esa función desde otro scope. Podemos compartir información en donde ese valor no existiría en el contexto de ejecución actual donde fue invocada.

```javascript
function a(str) {
  const print = str;
  function b() {
    console.log(print);
  }
  b();
}
const clousure1 = a("hello");
const clousure2 = a("bye");

clousure1(); // hello
clousure2(); // bye

clousure.print; // Error!!
```

El [**Contexto de ejecución global**](https://www.freecodecamp.org/espanol/news/contexto-de-ejecucion-de-javascript-y-hoisting-explicados-con-ejemplos-de-codigo/) crea el primer registro de la pila de ejecución (callstack), una función que engloba todo el programa. Pero debemos de separarlo del [**Contexto de ejecución de la función**](https://www.freecodecamp.org/espanol/news/contexto-de-ejecucion-de-javascript-y-hoisting-explicados-con-ejemplos-de-codigo/#:~:text=El%20contexto%20de%20ejecuci%C3%B3n%20de%20la%20funci%C3%B3n%20pasa%20por%20las,un%20valor%20especial%20llamado%20arguments%20.) que es el que utilizamos en ESTE CASO en nuestras clousures.

Todo contexto de ejecución pasa por 2 fases:

1-
**Fase de creación:** se carga todo en memoria para ejecutar esa función, como el archivo al que pertenece la función, un puntero que indica la proxima linea a ejecutar, el objeto window (en las funciones se crea un objeto arguments), el valor de **this** y crea un **entorno léxico** (lexical environment).

> [REMEMBER!]
> Si existe alguna "function" o variable declarada en el contexto global, se le asigna un espacio en memoria con el valor undefined (variables) y las funciones cargan directamente su declaración.

> [ATTENTION!]
> Cada vez que se ejecuta una función se le crea un entorno léxico.

### Entorno léxico

Objeto que tienen los contextos de ejecución donde almacenan en clave/valor (environment record) las variables, objetos, array, etc (están dentro de la función). Además, guardan un puntero al entorno léxico exterior (obviamente la función global no tiene).

Al terminar todo esta fase, la función queda en pila y comienza la fase de ejecución.

2- **Fase de ejecución:**
javascript ejecuta sentencia por sentencia las declaraciones y asigna los valores a aquellas variables inicializados en fase de creación como undefined.

```javascript
function customizeLog({ type, msg, bgColor, colour }) {
  return () => {
    console.log(
      `%c ${type}: ${msg}`,
      `background: ${bgColor}; color: ${colour};`
    );
  };
}
const errorLogDescription = {
  type: "Error",
  msg: "you have a problem etc...",
  bgColor: "red",
  color: "black",
};
const errorLog = customizeLog(errorLogDescription);
```

## Mutable e inmutable

Hace referencia al cambio de estado de algo, poder cambiar una propiedad o la referencia que tiene una variable. Aquello que produzca una variación de las caracteristicas de aquello "mutado".
Los tipos de datos "primivitos" no pueden ser mutados. Ejemplo:

```javascript
let str = "deep learning";
str[0].toUpperCase(); // D
console.log(str); //deep learning
```

Si quisieramos modificar algo de la anterior variable deberiamos de crear una nueva instancia.

Los tipos de datos de "referencia" () son mutables. Esto quiere decir que podemos alterar su estado y propiedad sin la necesidad de crear una nueva variable. Ejemplo:

```javascript
const obj = { name: "Juan" };
obj.name = "Pepito";
console.log(obj.name); // Pepito
```

## Síncronia y Asíncronia

> [ATTENTION!]
> Antes deberiamos ver algunos conceptos previos.

### Concurrencia y paralelismo

> Thread: es una caracteristica que permite a una aplicación realizar varias tareas a la vez. Se apilan las tareas a ejecutarse.

- Concurrencia: dos o más tareas progresan simultáneamente. No significa que sucedan al mismo tiempo. A veces asimilamos que para esto necesitamos más de un **"thread"** y no es así, podemos dividir en fragmentos pequeños las tareas de manera que se ejecuten durante un breve período de tiempo (esta técnica se llama **entrelazado**). Da la sensación de terminar todo al mismo tiempo.
- Paralelismo: dos o más tareas se ejecutan a la vez, al mismo tiempo.

### Operaciones CPU-Bound vs I/O-Bound

- CPU-Bound: anteriormente hemos visto tareas que consumen recursos de nuestra CPU (procesador). O sea, operaciones cuyas cargas seran ejecutadas en nuestra aplicación.
- I/O-Bound: también es frecuente encontrar otro tipo de operaciones como: leer un fichero, acceder a una base de datos externa o consultar datos a través de red, etc. Estas operaciones de entrada/salida disparan peticiones especiales que son atendidas fuera del contexto de la aplicación.

### Código bloqueante y código no-bloqueante

- Código bloqueante: no devuelve el control al hilo hasta que se haya completado. Por lo tanto el thread queda bloqueado en estado de espera.
- Código no-bloqueante: devuelve inmediatamente el control independientemente del resultado. En caso que se haya completado, devolverá los datos. EN caso contrario (si no pudo ser satisfecha la operación) podría devolver un código de error.

**Ahora si, luego de tanta explicación (sumamente importante) retomemos...**

Hacen referencia a cuando tendrá lugar la respuesta.

- Síncrono: Ejecución secuencial, retorna el control cuando la operación fue completada en su totalidad.

- Asíncrono: la finalización de la operación se le informa al programa principal y el proceso de respuesta se hará en algún momento del futuro, mediante **mecanismos de asincronía** como callback, promise o async/await.

## Callback

Son funciones que se pasan como parámetros a otra función (la que recibe estos callbacks se llama "función de orden superior").
Nos sirve para decidir el flujo de un programa y ejecutar tal acción u otra. Podemos usarlo como un mecanismo asíncrono.

```javascript
const cbResolve = (name) => console.log("hola soy " + name);
const cbReject = () =>
  console.log("Solo se permite el valor string como parámetro");

const sayName = (str, cbResolve, cbReject) => {
  if (typeof str === "string") {
    cbResolve(str);
  } else {
    cbReject();
  }
};

sayName("pip", cbResolve, cbReject); // hola soy pip
sayName(20, cbResolve, cbReject); // Solo se permite el valor string como parámetro
```

## Promises

Objeto que es el resultado de una operación asíncrona. Este resultado podría estar **ahora** o en un **futuro**. Están basadas en callbacks, pero tienen una forma más linda de usarlas gracias al azúcar sintáctica (misma funcionalidad distinta sintaxis). Además de su sintaxis añade un nuevo nivel de prioridad en el event loop. Siendo el primero, llamado microtask.

Nuestras promises pueden tener 3 **estados**:

1. Pending
2. Resolved
3. Rejected

Podemos usar su sintaxis más legible:

- gracias a la función **then()** podemos encadenar la promesa. Este método devuelve una promesa con un nuevo valor (valor que retorne cada then).
- para los errores podemos utilizar **catch()**. Este captura cualquier y primer error encontrado en la encadenación de then.

> [NOTE!]
> Si tuviesemos que pedir muchas peticiones y a su vez crear código síncrono, nuestro encadenamiento se vuelve más tedioso. NO podemos refactorizar (abstraer cada then)

```javascript
let shoppingElements = "";
function shoppingDate() {
  return new Promise((resolve) => {
    setTimeout(() => {
      shoppingElements += "🍔";
      resolve(shoppingElements);
    }, 1000);
  });
}
shoppingDate()
  .then((res) => console.log("Resolved: " + res)) // 🍔
  .catch((err) => console.log("Error"));

//Inclusive podemos encadenar más promesas o la misma. OPCIONAL:
shoppingDate()
  .then(() => shoppingDate()) // 🍔🍔
  .then(() => shoppingDate()); // 🍔🍔🍔
```

## Async/Await

El resultado de añadir más azúcar sintáctica pero a las promises en este caso (devuelve una promise). El código simula la sintaxis de lo que seria un código secuencial y síncrono.

Las únicas diferencias serian (hasta donde sepa claro):

1- forzamos la espera del resultado con await, para poder iniciar lo siguiente (bloquea la ejecución del código dentro de la función asíncrona). En las promesas no sabemos cuándo se va a resolver la petición.

2- Podemos refactorizar el código utilizando distintas funciones asíncronas.

Por último, manejamos los errores con un bloque try/catch.

> [REMEMBER!]
> Siempre colocar async a la función y await a las variables que esperen el resultado.

```javascript
const searchUsername = "pipo";

const userData = async (username) => {
  const url = `https://etc.com?user=${username}`;
  try {
    const req = await fetch(url);
    const res = await res.json();

    return res;
  } catch (err) {
    console.log(`%c Error: ${err}`, "background: red; color: white;");
  }
};

const bankAccountData = async (searchUsername) => {
  try {
    const { key } = await userData(searchUsername); // {id:2, name:"pipo", key:"#fdawcx22xqQ" }
    const url = `https://etc.com?bankAccount=${id}&key=${key}`;
    const req = await fetch(url); // {id:23132, name:"pipo michi", balance:"2000$" }
  } catch (err) {
    console.log(`%c Error: ${err}`, "background: red; color: white;");
  }
};
```

# ¿Cómo funciona javascript entonces?

## Modelo de javascript

Javascript fue creado y diseñado para ser ejecutado en navegadores. Por lo tanto, el trabajo con la red y procesar interacciones de usuario, sumado el mantener la interfaz fluida es un dicho ¿Bloqueante o no bloqueante, síncrono o asíncrono?
Javascript fue evolucionando para operaciones de tipo I/O por esta razón: **_utiliza un modelo asíncrono y no bloqueante, con un loop de eventos implementado con un único thread para las interfaces de I/O_**.
Gracias a esto, es altamente concurrente el modelo a pesar de tener un único thread.

## ¿Qué es el event loop?

Antes:

### Queue o cola

Cada vez que recibimos una notificación que esté fuera de la aplicación (la respuesta de nuestras peticiones asíncronas), este mensaje se introduce en una **cola** de "mensajes pendientes" y se le registra un callback correspondiente.

Entonces:

Cuando se vacía el callstack (finaliza la carga de las funciones de la aplicación) comienza a funcionar un mecanismo que procesa los "mensajes" (callbacks) de la queue. Consiste en llamar a las callbacks asociado a cada mensaje, lo que dará lugar a un nuevo frame en el callstack, entonces, reiteramos el funcionamiento de nuestro callstack.
Cabe mencionar que también dispone de una jerarquía de tareas (son las peticiones ya en la queue), donde una tiene mayor importancia a ser apilada que otra y distinguen de varias clases o clasificaciones de tareas.
Nuestro amigo event loop entonces se encarga de administrar qué tareas y cuándo van a ser apiladas en el callstack.
