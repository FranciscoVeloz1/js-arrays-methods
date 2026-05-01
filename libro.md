# Dominando los Métodos de Arrays en JavaScript --resume d8d8926b-8fcb-4489-9ea5-367f2710dce0

Los arrays de JavaScript son una de las estructuras de datos más utilizadas del lenguaje, y los métodos de array incorporados son las herramientas que hacen que trabajar con ellos sea poderoso y expresivo. Sin embargo, muchos desarrolladores se apoyan en solo un puñado de ellos, dejando el resto sin explorar.

Este libro es una guía práctica de todos los métodos de array de JavaScript — desde los que usas a diario hasta los que no sabías que necesitabas. Cada método se explica con definiciones claras, ejemplos del mundo real y notas sobre cuándo y por qué utilizarlo. Ya seas un principiante que se está familiarizando con `map` y `filter`, o un desarrollador experimentado que busca perfeccionar su comprensión de `reduceRight` o `flatMap`, este libro está diseñado para ser una referencia completa y duradera.

Al final, tendrás un dominio confiante y exhaustivo de toda la API de arrays y el criterio para usar el método correcto en cada situación.

---

## Datos de Ejemplo

Todos los ejemplos a lo largo de este libro usan el siguiente conjunto de datos — una empresa con departamentos, empleados y proyectos:

```js
const company = {
  name: "Horizon Labs",
  departments: [
    {
      name: "Engineering",
      budget: 120000,
      employees: [
        { id: 1, name: "Alice", role: "Senior Dev", salary: 95000, active: true, skills: ["JavaScript", "Node.js", "React"] },
        { id: 2, name: "Bob", role: "Junior Dev", salary: 55000, active: true, skills: ["JavaScript", "CSS"] },
        { id: 3, name: "Carol", role: "Tech Lead", salary: 110000, active: false, skills: ["JavaScript", "Python", "AWS"] },
      ],
    },
    {
      name: "Design",
      budget: 80000,
      employees: [
        { id: 4, name: "David", role: "UI Designer", salary: 70000, active: true, skills: ["Figma", "CSS", "Illustrator"] },
        { id: 5, name: "Eva", role: "UX Researcher", salary: 75000, active: true, skills: ["User Testing", "Figma"] },
      ],
    },
    {
      name: "Marketing",
      budget: 60000,
      employees: [
        { id: 6, name: "Frank", role: "SEO Specialist", salary: 60000, active: false, skills: ["SEO", "Google Ads"] },
        { id: 7, name: "Grace", role: "Content Writer", salary: 52000, active: true, skills: ["Copywriting", "SEO"] },
      ],
    },
  ],
};

// Lista plana de todos los empleados, usada en la mayoría de los ejemplos
const employees = company.departments.flatMap(d => d.employees);
```

---

## Métodos

---

### `flatMap`

**Definición:** Ejecuta una función de mapeo sobre cada elemento y luego aplana el resultado un nivel. Es equivalente a llamar `.map()` seguido de `.flat(1)`, pero más eficiente. Es especialmente útil cuando cada elemento puede producir cero, uno o varios elementos de salida — algo que `map` solo no puede expresar limpiamente.

```js
// Obtener una lista plana de todas las habilidades de todos los empleados
const allSkills = employees.flatMap(emp => emp.skills);

console.log(allSkills);
// [
//   "JavaScript", "Node.js", "React",
//   "JavaScript", "CSS",
//   "JavaScript", "Python", "AWS",
//   "Figma", "CSS", "Illustrator",
//   "User Testing", "Figma",
//   "SEO", "Google Ads",
//   "Copywriting", "SEO"
// ]
```

Sin `flatMap`, obtendrías un array de arrays:

```js
employees.map(emp => emp.skills);
// [["JavaScript", "Node.js", "React"], ["JavaScript", "CSS"], ...]
```

`flatMap` también es útil para salida condicional — devuelve un array vacío `[]` para omitir un elemento, o varios elementos para expandir uno en muchos:

```js
// Solo para empleados activos, devolver una etiqueta "{nombre} ({rol})"
const badges = employees.flatMap(emp =>
  emp.active ? [`${emp.name} (${emp.role})`] : []
);

console.log(badges);
// ["Alice (Senior Dev)", "Bob (Junior Dev)", "David (UI Designer)", "Eva (UX Researcher)", "Grace (Content Writer)"]
```

---

### `map`

**Definición:** Crea un nuevo array con los resultados de llamar a una función sobre cada elemento. No muta el array original y siempre devuelve un array de la misma longitud.

```js
// Extraer solo el nombre y el salario de cada empleado
const salaryCards = employees.map(emp => ({
  name: emp.name,
  salary: emp.salary,
}));

console.log(salaryCards);
// [
//   { name: "Alice", salary: 95000 },
//   { name: "Bob", salary: 55000 },
//   { name: "Carol", salary: 110000 },
//   { name: "David", salary: 70000 },
//   { name: "Eva", salary: 75000 },
//   { name: "Frank", salary: 60000 },
//   { name: "Grace", salary: 52000 },
// ]
```

---

### `filter`

**Definición:** Crea un nuevo array que contiene solo los elementos para los cuales la función proporcionada devuelve `true`. El array original no se muta y el resultado puede ser más corto que la entrada.

```js
// Obtener solo los empleados actualmente activos
const activeEmployees = employees.filter(emp => emp.active);

console.log(activeEmployees.map(e => e.name));
// ["Alice", "Bob", "David", "Eva", "Grace"]
```

---

### `forEach`

**Definición:** Ejecuta una función una vez por cada elemento del array. A diferencia de `map`, devuelve `undefined` — se usa únicamente para efectos secundarios como registrar en consola, actualizar estado externo o disparar operaciones.

```js
// Mostrar un resumen por consola para cada empleado
employees.forEach(emp => {
  const status = emp.active ? "Activo" : "Inactivo";
  console.log(`[${status}] ${emp.name} — ${emp.role} ($${emp.salary.toLocaleString()})`);
});

// [Activo]   Alice — Senior Dev ($95,000)
// [Activo]   Bob — Junior Dev ($55,000)
// [Inactivo] Carol — Tech Lead ($110,000)
// [Activo]   David — UI Designer ($70,000)
// [Activo]   Eva — UX Researcher ($75,000)
// [Inactivo] Frank — SEO Specialist ($60,000)
// [Activo]   Grace — Content Writer ($52,000)
```

---

### `reduce`

**Definición:** Itera sobre el array y construye un único valor de salida, paso a paso. En cada iteración, el callback recibe el resultado acumulado hasta el momento (`accumulator`) y el elemento actual. Lo que el callback devuelva se convierte en el nuevo acumulador para el siguiente paso. Tras el último elemento, se devuelve el valor final del acumulador.

El segundo argumento de `reduce` es el **valor inicial** — en qué estado comienza el acumulador antes de procesar el primer elemento.

```js
// Calcular el salario total de todos los empleados
const totalSalary = employees.reduce((sum, emp) => sum + emp.salary, 0);
//                                    ^^^  ^^^                        ^
//                                    |    empleado actual            |
//                                    total acumulado                 empieza en 0

console.log(totalSalary);
// 517000
```

Para ver qué ocurre en cada paso:

```js
// Paso a paso:
// Inicio:        sum = 0
// Tras Alice:    sum = 0 + 95000 = 95000
// Tras Bob:      sum = 95000 + 55000 = 150000
// Tras Carol:    sum = 150000 + 110000 = 260000
// ... y así hasta que los 7 empleados sean procesados
```

El valor inicial `0` importa — le dice a `reduce` qué tipo de cosa estás construyendo. Aquí construyes un número, así que empiezas desde `0`. Si construyeras un array, empezarías desde `[]`. Si construyeras un objeto, empezarías desde `{}`:

```js
// Construir un mapa de id de empleado → objeto empleado para búsquedas rápidas
const employeeById = employees.reduce((map, emp) => {
  map[emp.id] = emp;
  return map;
}, {});
//  ^ empieza como objeto vacío, se le agrega una clave por empleado

console.log(employeeById[3].name); // "Carol"
console.log(employeeById[5].role); // "UX Researcher"
```

---

### `find`

**Definición:** Devuelve el primer elemento del array para el cual la función proporcionada devuelve `true`. Si ningún elemento coincide, devuelve `undefined`. Deja de iterar en cuanto encuentra una coincidencia, lo que lo hace más eficiente que `filter` cuando solo necesitas un resultado.

```js
// Encontrar al primer empleado que gana más de $90,000
const highEarner = employees.find(emp => emp.salary > 90000);

console.log(highEarner.name);   // "Alice"
console.log(highEarner.salary); // 95000
```

Si no existe ninguna coincidencia, `find` devuelve `undefined` — protégete siempre ante eso cuando el resultado sea incierto:

```js
// Buscar un empleado por id
const employee = employees.find(emp => emp.id === 4);

console.log(employee?.name); // "David"
```

---

### `findIndex`

**Definición:** Devuelve el índice del primer elemento para el cual la función proporcionada devuelve `true`. Si ningún elemento coincide, devuelve `-1`. Es el equivalente de `find` pero devuelve el índice en lugar del elemento.

```js
// Encontrar la posición del primer empleado inactivo
const firstInactiveIndex = employees.findIndex(emp => !emp.active);

console.log(firstInactiveIndex);                    // 2
console.log(employees[firstInactiveIndex].name);    // "Carol"
```

Un valor de retorno de `-1` significa que no se encontró ninguna coincidencia — compruébalo antes de usar el índice:

```js
// Encontrar el índice del empleado llamado "Eva"
const evaIndex = employees.findIndex(emp => emp.name === "Eva");

if (evaIndex !== -1) {
  console.log(`Eva está en el índice ${evaIndex}`); // "Eva está en el índice 4"
}
```

---

### `some`

**Definición:** Devuelve `true` si al menos un elemento del array supera la prueba proporcionada. Devuelve `false` si ninguno lo hace. Deja de iterar en el momento en que encuentra una coincidencia, por lo que nunca procesa más elementos de los necesarios.

Piensa en ello como preguntar: *"¿Algún elemento satisface esta condición?"*

```js
// Comprobar si algún empleado está inactivo
const hasInactive = employees.some(emp => !emp.active);

console.log(hasInactive); // true
```

```js
// Comprobar si algún empleado gana más de $100,000
const hasSixFigure = employees.some(emp => emp.salary > 100000);

console.log(hasSixFigure); // true  (Carol gana $110,000)
```

---

### `every`

**Definición:** Devuelve `true` solo si todos los elementos del array superan la prueba proporcionada. En cuanto un elemento falla, se detiene y devuelve `false`.

Piensa en ello como preguntar: *"¿Todos los elementos satisfacen esta condición?"*

```js
// Comprobar si todos los empleados tienen JavaScript como habilidad
const allKnowJS = employees.every(emp => emp.skills.includes("JavaScript"));

console.log(allKnowJS); // false  (David, Eva, Frank y Grace no lo tienen)
```

```js
// Comprobar si todos los empleados ganan al menos $50,000
const allAboveMinimum = employees.every(emp => emp.salary >= 50000);

console.log(allAboveMinimum); // true
```

`some` y `every` son opuestos de forma útil: si `some` devuelve `false`, ningún elemento pasó. Si `every` devuelve `false`, al menos un elemento falló — que es exactamente lo que `some` encontraría.

---

### `includes`

**Definición:** Devuelve `true` si el array contiene el valor dado, `false` en caso contrario. Usa igualdad estricta (`===`) para comparar, por lo que funciona bien con primitivos como strings y números.

```js
// Comprobar si una habilidad específica existe en algún lugar de la lista de habilidades
const allSkills = employees.flatMap(emp => emp.skills);

console.log(allSkills.includes("Python"));    // true
console.log(allSkills.includes("TypeScript")); // false
```

Ten en cuenta que `includes` busca un valor directamente — no puede evaluar una condición. Para objetos o comprobaciones complejas, usa `some`:

```js
// Esto no funcionará como se espera — los objetos se comparan por referencia, no por contenido
employees.includes({ id: 1 }); // false

// Usa some cuando necesites comparar por una propiedad
employees.some(emp => emp.id === 1); // true
```

---

### `indexOf`

**Definición:** Devuelve el índice de la primera aparición de un valor dado en el array. Si el valor no se encuentra, devuelve `-1`. Al igual que `includes`, usa igualdad estricta (`===`), por lo que es más adecuado para primitivos como strings y números.

```js
const allSkills = employees.flatMap(emp => emp.skills);

console.log(allSkills.indexOf("Python"));     // 7
console.log(allSkills.indexOf("TypeScript")); // -1
```

Cuando un valor aparece más de una vez, `indexOf` solo devuelve la primera aparición. Puedes encontrar las siguientes pasando un segundo argumento — el índice desde el que empezar a buscar:

```js
console.log(allSkills.indexOf("JavaScript"));     // 0  (primera aparición)
console.log(allSkills.indexOf("JavaScript", 1));  // 3  (la siguiente, empezando después del índice 0)
console.log(allSkills.indexOf("JavaScript", 4));  // 6  (la siguiente, empezando después del índice 3)
```

---

### `push`

**Definición:** Agrega uno o más elementos al **final** de un array y devuelve la nueva longitud del array. Muta el array original.

```js
const activeNames = employees
  .filter(emp => emp.active)
  .map(emp => emp.name);

console.log(activeNames); // ["Alice", "Bob", "David", "Eva", "Grace"]

activeNames.push("Henry");

console.log(activeNames); // ["Alice", "Bob", "David", "Eva", "Grace", "Henry"]
console.log(activeNames.length); // 6
```

Puedes hacer push de varios valores a la vez:

```js
activeNames.push("Iris", "James");

console.log(activeNames); // ["Alice", "Bob", "David", "Eva", "Grace", "Henry", "Iris", "James"]
```

---

### `pop`

**Definición:** Elimina el **último** elemento de un array y lo devuelve. Muta el array original.

```js
const activeNames = employees
  .filter(emp => emp.active)
  .map(emp => emp.name);

console.log(activeNames); // ["Alice", "Bob", "David", "Eva", "Grace"]

const last = activeNames.pop();

console.log(last);        // "Grace"
console.log(activeNames); // ["Alice", "Bob", "David", "Eva"]
```

`push` y `pop` juntos hacen que los arrays funcionen como una **pila** — último en entrar, primero en salir.

---

### `shift`

**Definición:** Elimina el **primer** elemento de un array y lo devuelve. Muta el array original. Cada elemento restante desciende una posición de índice.

```js
const activeNames = employees
  .filter(emp => emp.active)
  .map(emp => emp.name);

console.log(activeNames); // ["Alice", "Bob", "David", "Eva", "Grace"]

const first = activeNames.shift();

console.log(first);       // "Alice"
console.log(activeNames); // ["Bob", "David", "Eva", "Grace"]
```

---

### `unshift`

**Definición:** Agrega uno o más elementos al **inicio** de un array y devuelve la nueva longitud. Muta el array original. Cada elemento existente sube una posición de índice.

```js
const activeNames = employees
  .filter(emp => emp.active)
  .map(emp => emp.name);

console.log(activeNames); // ["Alice", "Bob", "David", "Eva", "Grace"]

activeNames.unshift("Henry");

console.log(activeNames); // ["Henry", "Alice", "Bob", "David", "Eva", "Grace"]
```

`unshift` y `shift` juntos hacen que los arrays funcionen como una **cola** — primero en entrar, primero en salir. Agrega al final con `push`, saca del frente con `shift`.

---

### `splice`

**Definición:** Cambia el contenido de un array eliminando, reemplazando o insertando elementos en una posición específica. Muta el array original y devuelve un array con los elementos eliminados.

Su firma es: `array.splice(startIndex, deleteCount, ...itemsToInsert)`

- `startIndex` — desde dónde empezar a hacer cambios
- `deleteCount` — cuántos elementos eliminar (0 significa ninguno)
- `...itemsToInsert` — elementos opcionales a insertar en esa posición

```js
const names = employees.map(emp => emp.name);
// ["Alice", "Bob", "Carol", "David", "Eva", "Frank", "Grace"]

// Eliminar 1 elemento en el índice 2 (Carol)
const removed = names.splice(2, 1);

console.log(removed); // ["Carol"]
console.log(names);   // ["Alice", "Bob", "David", "Eva", "Frank", "Grace"]
```

Insertar sin eliminar pasando `0` como cantidad a eliminar:

```js
// Insertar "Henry" en el índice 2, sin eliminar nada
names.splice(2, 0, "Henry");

console.log(names); // ["Alice", "Bob", "Henry", "David", "Eva", "Frank", "Grace"]
```

Reemplazar eliminando e insertando en una sola llamada:

```js
// Reemplazar "Frank" (índice 5) con "Iris"
names.splice(5, 1, "Iris");

console.log(names); // ["Alice", "Bob", "Henry", "David", "Eva", "Iris", "Grace"]
```

---

### `slice`

**Definición:** Devuelve un nuevo array con una porción del original, desde un índice de inicio hasta (sin incluir) un índice de fin. No muta el array original.

Su firma es: `array.slice(startIndex, endIndex)`

```js
const names = employees.map(emp => emp.name);
// ["Alice", "Bob", "Carol", "David", "Eva", "Frank", "Grace"]

// Obtener los primeros tres empleados
const firstThree = names.slice(0, 3);

console.log(firstThree); // ["Alice", "Bob", "Carol"]
console.log(names);      // ["Alice", "Bob", "Carol", "David", "Eva", "Frank", "Grace"] (sin cambios)
```

Omitir el índice final llega hasta el final del array:

```js
// Todos desde el índice 4 en adelante
const lastOnes = names.slice(4);

console.log(lastOnes); // ["Eva", "Frank", "Grace"]
```

Los índices negativos cuentan desde el final:

```js
// Obtener los últimos 2 empleados
const lastTwo = names.slice(-2);

console.log(lastTwo); // ["Frank", "Grace"]
```

---

### `concat`

**Definición:** Une dos o más arrays en un nuevo array, sin mutar ninguno de los originales. También puede aceptar valores simples (no solo arrays) y los añadirá como elementos individuales.

```js
const engineering = company.departments[0].employees.map(emp => emp.name);
// ["Alice", "Bob", "Carol"]

const design = company.departments[1].employees.map(emp => emp.name);
// ["David", "Eva"]

const marketing = company.departments[2].employees.map(emp => emp.name);
// ["Frank", "Grace"]

const allNames = engineering.concat(design, marketing);

console.log(allNames);
// ["Alice", "Bob", "Carol", "David", "Eva", "Frank", "Grace"]
```

También puedes añadir valores individuales:

```js
const updated = engineering.concat("Henry", "Iris");

console.log(updated); // ["Alice", "Bob", "Carol", "Henry", "Iris"]
```

---

### `join`

**Definición:** Une todos los elementos de un array en un único string, con un separador entre cada elemento. El separador por defecto es una coma si no se especifica. No muta el array original.

```js
const names = employees.map(emp => emp.name);

console.log(names.join(", "));  // "Alice, Bob, Carol, David, Eva, Frank, Grace"
console.log(names.join(" | ")); // "Alice | Bob | Carol | David | Eva | Frank | Grace"
console.log(names.join(""));    // "AliceBobCarolDavidEvaFrankGrace"
```

Un uso práctico: generar una línea de resumen legible por empleado.

```js
const aliceSkills = employees[0].skills.join(", ");

console.log(`Alice sabe: ${aliceSkills}`);
// "Alice sabe: JavaScript, Node.js, React"
```

---

### `sort`

**Definición:** Ordena los elementos de un array **en el lugar** y devuelve el array ordenado. Muta el original. Por defecto, ordena convirtiendo los elementos a strings y comparándolos — lo que funciona para strings pero da resultados incorrectos con números.

```js
const names = employees.map(emp => emp.name);

names.sort();

console.log(names);
// ["Alice", "Bob", "Carol", "David", "Eva", "Frank", "Grace"]
```

Para números, el orden por defecto es incorrecto — compara los dígitos como caracteres, no como valores:

```js
const salaries = employees.map(emp => emp.salary);

salaries.sort();

console.log(salaries);
// [110000, 52000, 55000, 60000, 70000, 75000, 95000]
// ¡Incorrecto! "1" viene antes que "5" alfabéticamente, así que 110000 queda primero
```

Para ordenar números correctamente, pasa una **función comparadora**. Es una función que escribes tú para indicarle a `sort` cómo ordenar dos elementos cualesquiera. `sort` la llamará repetidamente con pares de valores — `a` y `b` — y usará tu valor de retorno para decidir el orden:

- devuelve un **número negativo** → `a` va antes que `b`
- devuelve un **número positivo** → `b` va antes que `a`
- devuelve **`0`** → se deja en el mismo orden

El truco con números es que la resta ya te da exactamente esos resultados:

```js
// 52000 vs 95000: 52000 - 95000 = -43000 (negativo) → 52000 va primero ✓
// 95000 vs 52000: 95000 - 52000 = +43000 (positivo) → 52000 sigue yendo primero ✓
// 70000 vs 70000: 70000 - 70000 = 0 → se deja como está ✓

salaries.sort((a, b) => a - b);

console.log(salaries);
// [52000, 55000, 60000, 70000, 75000, 95000, 110000]
```

Para ordenar en sentido descendente, simplemente intercambia `a` y `b`:

```js
salaries.sort((a, b) => b - a);

console.log(salaries);
// [110000, 95000, 75000, 70000, 60000, 55000, 52000]
```

Para entender qué hace `sort` internamente, aquí está la misma lógica escrita con bucles `for` anidados (un bubble sort):

```js
const salaries = employees.map(emp => emp.salary);

for (let i = 0; i < salaries.length; i++) {
  for (let j = 0; j < salaries.length - i - 1; j++) {
    if (salaries[j] > salaries[j + 1]) {
      // Intercambiar los dos elementos
      const temp = salaries[j];
      salaries[j] = salaries[j + 1];
      salaries[j + 1] = temp;
    }
  }
}

console.log(salaries);
// [52000, 55000, 60000, 70000, 75000, 95000, 110000]
```

`sort` hace todo esto por ti — el bucle, las comparaciones, los intercambios — y usa un algoritmo más eficiente internamente. Tú solo proporcionas la regla para comparar dos valores.

La función comparadora también funciona con objetos — ordenar empleados por salario, de menor a mayor:

```js
const sorted = [...employees].sort((a, b) => a.salary - b.salary);
//              ^^^^^^^^^^^^^ spread en una copia primero para no mutar el original

console.log(sorted.map(emp => `${emp.name}: $${emp.salary}`));
// ["Grace: $52000", "Bob: $55000", "Frank: $60000", "David: $70000", "Eva: $75000", "Alice: $95000", "Carol: $110000"]
```

---

### `reverse`

**Definición:** Invierte el orden de los elementos de un array **en el lugar** y devuelve el array. Muta el original.

```js
const names = employees.map(emp => emp.name);
// ["Alice", "Bob", "Carol", "David", "Eva", "Frank", "Grace"]

names.reverse();

console.log(names);
// ["Grace", "Frank", "Eva", "David", "Carol", "Bob", "Alice"]
```

Como `sort` también devuelve el array, `reverse` se encadena habitualmente después para obtener un orden descendente:

```js
const names = employees.map(emp => emp.name);

names.sort().reverse();

console.log(names);
// ["Grace", "Frank", "Eva", "David", "Carol", "Bob", "Alice"]
```

Si necesitas invertir sin mutar el original, haz un spread en una copia primero:

```js
const reversed = [...names].reverse();
```

---

### `flat`

**Definición:** Devuelve un nuevo array con los arrays anidados desempaquetados — sus elementos se extraen y se colocan directamente en el array exterior. No muta el array original.

La forma más fácil de visualizarlo: imagina eliminar una capa de corchetes de los arrays anidados.

```js
const skillsByEmployee = employees.map(emp => emp.skills);
// map nos da un array de arrays — un array interior por empleado:
// [
//   ["JavaScript", "Node.js", "React"],  ← Alice
//   ["JavaScript", "CSS"],               ← Bob
//   ["JavaScript", "Python", "AWS"],     ← Carol
//   ...
// ]

const allSkills = skillsByEmployee.flat();
// flat elimina una capa de corchetes, trayendo cada habilidad a un único array:
// ["JavaScript", "Node.js", "React", "JavaScript", "CSS", "JavaScript", "Python", "AWS", ...]
```

El argumento de **profundidad** controla cuántas capas de corchetes eliminar. El valor por defecto es `1`:

```js
// Profundidad 0: no ocurre nada
[["Alice", "Bob"], ["Carol"]].flat(0);
// [["Alice", "Bob"], ["Carol"]]  — sin cambios

// Profundidad 1 (por defecto): elimina una capa
[["Alice", "Bob"], ["Carol"]].flat(1);
// ["Alice", "Bob", "Carol"]

// Profundidad 2: elimina dos capas
[["Alice", ["Bob"]], ["Carol"]].flat(2);
// ["Alice", "Bob", "Carol"]
//                  ^^^^^ "Bob" estaba dos niveles de profundidad, ahora está fuera
```

Usa `Infinity` cuando no sabes qué tan anidado está el array y simplemente quieres todo plano:

```js
[["Alice", ["Bob", ["Carol"]]]].flat(Infinity);
// ["Alice", "Bob", "Carol"]
```

Cuando necesitas tanto mapear como aplanar un nivel, `flatMap` es la opción más eficiente en lugar de encadenar `.map().flat()`.

---
