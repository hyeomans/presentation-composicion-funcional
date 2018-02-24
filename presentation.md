title: Composición funcional
author:
  name: Héctor Yeomans
  twitter: h_yeomans
  url: https://hyeomans.com
output: basic.html
controls: true

--

# Composición funcional
## Describe tu lógica de negocio de manera funcional

<h6 style="text-align:center">Por: Héctor Yeomans - @h_yeomans</h6>
--

### Composición funcional

Composición funcional es el mecanismo donde el argumento de entrada de una función es
otra función.


--

### Funciones anidadas

```javascript
const input = [1,2,3,4,5];

const doubleIt = input => { return input.map(i => i * 2); } 
// doubleIt(input) = [2,4,6,8,10]

const addOne = input => { return input.map(i => i + 1); } 
// addOne(input) = [2,3,4,5,6];

console.log(addOne(doubleIt(addOne(doubleIt(input)))));
```

--

### Una tubería

```javascript
const input = [1,2,3,4,5];

const doubleIt = input => { return input.map(i => i * 2); } 
const addOne = input => { return input.map(i => i + 1); } 

//Version anterior
addOne(doubleIt(addOne(doubleIt(input))));

funcionMisteriosa(
  doubleIt,
  addOne,
  doubleIt,
  addOne
)(input);
```

--

### Conceptos

* Javascript
* Map
* Reduce

--

### Javascript

```javascript
const doubleIt = unArreglo => unArreglo.map(i => i * 2);

const doubleIt = function(unArreglo) {
  return unArreglo.map(function(i) {
    return i * 2;
  });
}.bind(this);
```

--

### Map y Reduce

```javascript
[1,2,3].map(i => i * 2);
//[2,4,6]

[1,2,3].reduce((acumulador, i) => accumulador + i, 10);
//16

const doubleIt = (i) => i * 2;
const sumaIt = (acumulador, i) => acumulador + i;

[1,2,3].map(doubleIt); 
// [2,4,6]

[1,2,3].reduce(sumaIt, 10); 
//16
```

--

### Pipe - Primer paso

```javascript
const input = [1,2,3,4,5];
const funcionAnidada = addOne(doubleIt(addOne(doubleIt(input))));

const reducer = function(f1, f2) { return f2(f1); }

// Si tomaramos dos operaciones de la funcion anidada, la ejecución sería:
// (doubleIt, addOne) { return addOne(doubleIt) }
```

```javascript
const pipe = function(funciones, valorInicial) {
  return funciones.reduce(reducer, valorInicial);
}
//funcionMisteriosa se llama pipe

const laQueEraFuncionAnidada = pipe([
  doubleIt,
  addOne,
  doubleIt,
  addOne
], input);
//[ 7, 11, 15, 19, 23 ]
```

--

### Pipe: paso a paso

```javascript
const reducer = function(f1, f2) { return f2(f1); }
const pipe = (funciones, valorInicial) => funciones.reduce(reducer, valorInicial);

const laQueEraFuncionAnidada = pipe([doubleIt, addOne, doubleIt, addOne], input);

//1era iteracion:
f1 = input
f2 = doubleIt
reducer = doubleIt(input) //Se vuelve el acumulador de la siguiente iteracion

//2da iteracion
f1 = doubleIt(input)
f2 = addOne
reducer = addOne(doubleIt(input))

//3era iteracion
f1 = addOne(doubleIt(input))
f2 = doubleIt
reducer = doubleIt(addOne(doubleIt(input)))

//4ta iteracion
f1 = doubleIt(addOne(doubleIt(input)))
f2 = addOne
reducer = addOne(doubleIt(addOne(doubleIt(input))))
```

--

### Pipe - Curry

```javascript
const doubleItAddOnex2 =  pipe([
  doubleIt,
  addOne,
  doubleIt,
  addOne
]);

const result = doubleItAddOnex2(input);
const otroResult = doubleItAddOnex2(otroInput);
```

```javascript
const pipe = function(funciones, valorInicial) {
  return funciones.reduce(reducer, valorInicial);
};
//Se convierte en:
const pipe = function(funciones) {
  return function(valorInicial) {
    return funciones.reduce(reducer, valorInicial);
  }
}

//One liner
const pipe = (funciones) => (valorInicial) => funciones.reduce(reducer, valorInicial);
```

---

### Pipe - ES6 spread

```javascript
const doubleItAddOnex2 =  pipe(
  doubleIt,
  addOne,
  doubleIt,
  addOne
);

const result = doubleItAddOnex2(input);

//El operador spread
const pipe = (...funciones) => (valorInicial) => funciones.reduce(reducer, valorInicial);
```
--

### Conclusión

- La lógica fluye de arriba hacia abajo o de izquierda a derecha.
- Si queremos implementar la lógica de derecha a izquierda, podemos usar reduceRight
- No utilizar en producción.
- Ramda.js, lodash/fp para JavaScript
- Otros lenguajes?
  - C# -> Func<T>, Action + generics
  - Java -> Lambdas + generics
  - Ruby -> Lambdas y Procs
