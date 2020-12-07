
> Ceci est une traduction française de ce lien: https://github.com/ryanmcdermott/clean-code-javascript

## Table Des Matières

1.  [Introduction](#introduction)
2.  [Variables](#variables)
3.  [Fonctions](#fonctions)
4.  [Objets Et Structures De Données](#objets-et-structures-de-données)
5.  [Classes](#classes)
6.  [SOLID](#solid)
7.  [Testage](#testage)
8.  [Simultanéité](#simultanéité)
9.  [La Gestion Des Erreurs](#la-gestion-des-erreurs)
10. [Mise En Forme](#mise-en-forme)
11. [Commentaires](#commentaires)
12. [Traduction](#traduction)

## Introduction

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Principes d'ingénierie logicielle, de l'ouvrage de Robert C. Martin [_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), adapté pour JavaScript. Ce n'est pas un guide de style. C'est un guide pour la production de logiciels [lisibles, réutilisables et refactorisables](https://github.com/ryanmcdermott/3rs-of-software-architecture) en JavaScript.

Tous les principes énoncés ici ne doivent pas être strictement suivis, et sont encore moins universellement acceptés. Ce sont des lignes directrices et rien de plus, mais ce sont des lignes codifiées au fil de nombreuses années d'expérience collective par les auteurs de _Clean Code_.

Notre métier de génie logiciel a un peu plus de 50 ans et nous apprenons encore beaucoup. Lorsque l'architecture logicielle sera aussi ancienne que l'architecture elle-même, nous aurons peut-être des règles plus strictes à suivre. Pour l’instant, laissez ces instructions servir de point de repère pour évaluer la qualité du code JavaScript que vous et votre équipe produisez.

Encore une chose à savoir : cela ne fera pas de vous un meilleur développeur de logiciels et utiliser ces principes pendant de nombreuses années ne signifie pas que vous ne ferez pas d'erreurs. Chaque morceau de code commence par un premier brouillon, comme l'argile humide qui prend sa forme finale. Enfin, nous corrigeons les imperfections lorsque nous les examinons avec nos pairs. Ne vous battez pas pour les premières ébauches à améliorer. Battez le code à la place!

## **Variables**

### Utilisez des noms de variables significatifs et prononçables

**Mal:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Bien:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ retour au sommet](#table-des-matières)**

### Utilisez le même vocabulaire pour le même type de variable

**Mal:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Bien:**

```javascript
getUser();
```

**[⬆ retour au sommet](#table-des-matières)**

### Utilisez des noms interrogeables

Nous lirons plus de code que nous n'en écrirons jamais. Il est important que le code écrit soit lisible et consultable. En ne nommant __pas__ des variables qui finissent par être significatives pour comprendre notre programme, nous blessons nos lecteurs.
Rendez vos noms consultables. Des outils comme
[buddy.js](https://github.com/danielstjules/buddy.js) et
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
peuvent aider à identifier des constantes non nommées.

**Mal:**

```javascript
// À quoi sert le 86400000?
setTimeout(blastOff, 86400000);
```

**Bien:**

```javascript
// Déclarez_les comme constantes nommées en majuscules.
const MILLISECONDS_IN_A_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```

**[⬆ retour au sommet](#table-des-matières)**

### Utiliser des variables explicatives

**Mal:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Bien:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter la cartographie mentale

Explicite est meilleur qu'implicite.

**Mal:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Attendez, c'est quoi `l` déjà ?
  dispatch(l);
});
```

**Bien:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas ajouter de contexte inutile

Si votre nom de classe / d’objet est explicite, ne répétez pas cela dans les membres de cet objet/classe.

**Mal:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car) {
  car.carColor = "Red";
}
```

**Bien:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car) {
  car.color = "Red";
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Utiliser des arguments par défaut au lieu de court-circuiter ou conditionnels

Les arguments par défaut sont souvent plus propres que les courts-circuits. Sachez que si vous les utilisez, votre fonction ne fournira des valeurs par défaut que pour les arguments non définis (`undefined`). Les autres valeurs "fausses" telles que `''`, `" ",` `false`, `null`, `0` et `NaN`, ne seront pas remplacées par une valeur par défaut.

**Mal:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Good:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

## **Fonctions**

### Arguments de fonction (idéalement 2 ou moins)

Limiter le nombre de paramètres de fonction est extrêmement important car cela facilite le test de votre fonction. Avoir plus de trois arguments conduit à une explosion combinatoire et vous devez tester des tonnes de cas différents avec chaque argument séparé.

Un ou deux arguments est le cas idéal, et trois devraient être évités si possible.
Habituellement, si vous avez plus de deux arguments, votre fonction essaie de faire trop. Dans les cas où il n'est pas possible de faire autrement, un objet de niveau supérieur suffira en tant qu'argument.

Puisque JavaScript vous permet de créer des objets à la volée, sans trop de classe passe-partout, vous pouvez utiliser un objet si vous vous aveze besoin de beaucoup d'arguments.

Pour rendre évidentes les propriétés attendues par la fonction, vous pouvez utiliser la syntaxe de déstructuration d'ES2015 / ES6.
Cela présente quelques avantages:

1.  Quand quelqu'un regarde la signature de la fonction, on voit immédiatement les propriétés qui sont utilisées.
2.  La destruction clone également les valeurs primitives spécifiées de objet passé en argument à la fonction. Cela peut aider à prévenir les effets secondaires. Remarque: les objets et les tableaux qui sont déstructurés à partir de l'objet argument ne sont PAS cloné.
3.  Les linters peuvent vous avertir des propriétés inutilisées, ce qui serait impossible sans déstructuration.

**Mal:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);
```

**Bien:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ retour au sommet](#table-des-matières)**

### Les fonctions doivent faire une chose

C’est de loin la règle la plus importante en génie logiciel. Quand une fonction fait plus d’une chose, il est plus difficile de composer, de tester et de raisonner.
Lorsque vous pouvez isoler une fonction à une seule action, elle peut être refactorisée facilement et votre code sera beaucoup plus propre. Si vous ne retenez rien d'autre de ce guide que ceci, vous serez en avance sur de nombreux développeurs.

**Mal:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Les noms de fonction doivent dire ce qu'ils font

**Mal:**

```javascript
ffunction addToDate(date, month) {
  // ...
}

const date = new Date();

// Il est difficile de dire à partir du nom de la fonction ce qui est ajouté
addToDate(date, 1);
```

**Bien:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ retour au sommet](#table-des-matières)**

### Les fonctions ne devraient être qu'un seul niveau d'abstraction

Lorsque vous avez plus d'un niveau d'abstraction, votre fonction en fait généralement trop. La scission des fonctions conduit à la réutilisation et à faciliter les tests.

**Mal:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Bien:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Supprimez le code en double

Faites de votre mieux pour éviter le code en double. Dupliquer le code est mauvais parce qu'il signifie qu'il y a plus d'un endroit que vous devrez modifier si vous avez besoin de changer un peu de logique.

Imaginez que vous dirigiez un restaurant et que vous gardiez une trace de votre inventaire: tout vos oignons, tomates, ail, épices, etc. Si vous avez plusieurs listes, alors toutes doivent être mises à jour lorsque vous servez un plat avec des tomates. Si vous n'avez qu'une seule liste, il n'y a qu'un seul endroit à mettre à jour !

Vous avez souvent un code en double parce que vous en avez deux fonctions ou plus qui diffèrent légèrement, tout en partageant partagent beaucoup. Leurs différences vous forcent à avoir deux ou plusieurs fonctions distinctes qui font beaucoup de choses identiques. Enlever
le code dupliquer signifie créer une abstraction capable de gérer cet ensemble de choses différentes avec une seule fonction / module / classe.

Il est essentiel de bien comprendre l’abstraction, c’est pourquoi vous devriez suivre le principes SOLID énoncés dans la section _Classes_. Les mauvaises abstractions peuvent être pire que le code en double, alors soyez prudent! Cela dit, si vous pouvez faire
une bonne abstraction, faites-le! Ne vous répétez pas, sinon vous devrez mettre à jour plusieurs endroits chaque fois que vous voudrez changer une chose.

**Mal:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Bien:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Définir des objets par défaut avec Object.assign

**Mal:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**

```javascript
const menuConfig = {
  title: "Order",
  // L'utilisateur n'a pas inclus la clé 'body'
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // 'config' est maintenant égale à : {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas utiliser les drapeaux (flags) comme paramètres de fonction

Les drapeaux indiquent à l'utilisateur que cette fonction fait plus d'une chose. Les fonctions devraient faire une seule chose. Séparez vos fonctions si elles suivent différents chemins de code basés sur un booléen.

**Mal:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Bien:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les effets secondaires (partie 1)

Une fonction produit un effet secondaire si elle fait autre chose que prendre une valeur et renvoyer une ou plusieurs valeurs. Un effet secondaire pourrait être l'écriture dans un fichier, la modification d'une variable globale ou le transfert accidentel de tout votre argent à un étranger.

Cependant, à l'occasion, vous pouvez avoir des effets secondaires dans un programme. Comme dans l'exemple précédent, vous devrez peut-être écrire dans un fichier. Ce que tu veux faire c'est centraliser où vous faites cela. Ne pas avoir plusieurs fonctions et classes
qui écrivent dans un fichier particulier. Avoir un service qui le fait. Seul et l'unique.

Le principal est d'éviter les pièges courants tels que le partage d'état entre objets sans aucune structure, en utilisant des types de données mutables qui peuvent être écrits par n'importe qui, et ne pas centraliser où vos effets secondaires se produisent. Si vous pouvez faire cela, vous pourrez être plus heureux que la grande majorité des autres programmeurs.

**Mal:**

```javascript
// Variable globale référencée par la fonction suivante.
// Si nous avions une autre fonction qui utilise cette cariable, comme elle devient un tableau, cela pourrait créer un effet de bord.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Bien:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les effets secondaires (partie 2)

En JavaScript, certaines valeurs sont inchangeables (immuables) et certaines sont modifiables (mutables). Les objets et les tableaux sont deux types de valeurs mutables, il est donc important de les gérer avec précaution lorsqu'ils sont passés en tant que paramètres à une fonction. Une fonction JavaScript peut changer les propriétés d'un objet ou modifier le contenu d'un tableau ce qui pourrait facilement causer des bogues en dehors de la fonction.

Supposons qu'il existe une fonction qui accepte un paramètre de tableau représentant un panier (`cart`). Si la fonction modifie ce tableau `cart` - en ajoutant un article à acheter, par exemple - alors toute autre fonction qui utilise ce même tableau de panier sera affectée par cet ajout.Cela peut être formidable, mais cela peut aussi être mauvais. Imaginons un mauvais
situation:

L'utilisateur clique sur le bouton "Acheter", qui appelle une fonction "achat" qui génère une requête réseau et envoie le tableau `cart` au serveur. Parce que la connexion réseau est demauvaise qualité, la fonction `purchase` doit continuer à réessayer la demande. Maintenant, qu'en est-il si, dans l'intervalle, l'utilisateur clique accidentellement sur le bouton "Ajouter au panier" un article qu'il ne voulait pas avant le début de la demande réseau ?
Si cela se produit et que la demande de réseau commence, alors cette fonction d'achat enverra l'article ajouté accidentellement car il a une référence au tableau `cart` que la fonction `addItemToCart` a modifié en ajoutant un article indésirable.

Une bonne solution serait pour la fonction `addItemToCart` de toujours cloner le tableau `cart`, l'éditer et retournez le clone. Cela garantit qu'aucune autre fonction ne puisse conserver une référence du panier d'achat utilisé par `addItemToCart`.

Deux mises en garde à mentionner à cette approche:

1.  Il peut arriver que vous souhaitiez réellement modifier l’objet d’entrée, mais lorsque vous adoptez cette pratique de programmation, vous constaterez que ces cas sont assez rares. La plupart des choses peuvent être refactorisées sans effets secondaires !
2.  Le clonage de gros objets peut être très coûteux en termes de performances. Heureusement, ce n'est pas un gros problème en pratique car il existe [d'excellentes bibliothèques](https://facebook.github.io/immutable-js/) qui permettent à ce type d'approche de programmation d'être rapide et pas aussi gourmand en mémoire qu'il le serait pour vous de cloner manuellement des objets et des tableaux.

**Mal:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Bien:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas écrire dans les fonctions globales

Les globaux polluants sont une mauvaise pratique en JavaScript car vous pourriez entrer en conflit avec une autre bibliothèque et l'utilisateur de votre API ne serait pas averti jusqu'à ce qu'il obtienne une exception en production. Prenons à un exemple : si vous vouliez étendre la méthode Array native de JavaScript pour avoir une méthode `diff` qui pourrait montrer la différence entre deux tableaux ? Vous pouvez écrire votre nouvelle fonction `Array.prototype`, mais elle pourrait entrer en conflit avec une autre bibliothèque qui a essayé de faire la même chose. Et si cette autre bibliothèque utilisait simplement `diff` pour trouver la différence entre le premier et le dernier élément d'un tableau? C'est pourquoi il serait bien mieux d’utiliser simplement les classes ES2015 / ES6 et d’étendre simplement le `Array` global.

**Mal:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Bien:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Privilégier la programmation fonctionnelle à la programmation impérative

JavaScript n'est pas un langage fonctionnel comme Haskell, mais il est propice à la programmation fonctionnelle. Les langages fonctionnels peuvent être plus propres et plus faciles à tester.
Privilégiez ce style de programmation quand vous le pouvez.

**Mal:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Bien:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ retour au sommet](#table-des-matières)**

### Encapsuler des conditions

**Mal:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Bien:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les conditionnels négatifs

**Mal:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Bien:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les conditionnels

Cela semble être une tâche impossible. En entendant, la plupart des gens disent : "comment suis-je censé faire quoi que ce soit sans une déclaration`if`? " La réponse est que vous pouvez utiliser le polymorphisme pour accomplir la même tâche dans de nombreux cas. 
La deuxième question est généralement, "bien c'est génial, mais pourquoi voudrais-je faire cela ? " la réponse est un précédent concept de code propre que nous avons appris : une fonction ne devrait faire qu'une seule chose. Quand vous avez des classes et des fonctions qui ont des instructions `if`, vous dites à votre utilisateur que votre fonction fait plus d’une chose. 
Rappellez-vous, faites juste une chose.

**Mal:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter la vérification de type (partie 1)

JavaScript n'est pas typé, ce qui signifie que vos fonctions peuvent accepter n'importe quel type d'argument.
Parfois, vous êtes ennuyé par cette liberté et cela devient tentant de faire la vérification de type dans vos fonctions. Il y a plusieurs façons d'éviter d'avoir à le faire.
La première chose à considérer est la cohérence des API.

**Mal:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Bien:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter la vérification de type (partie 2)

Si vous travaillez avec des valeurs primitives de base comme des chaînes et des entiers, et que vous ne pouvez pas utiliser le polymorphisme mais que vous ressentez toujours le besoin de vérifier le type, vous devriez envisager d'utiliser TypeScript. C'est une excellente alternative à JavaScript, car il fournit du typage statique en plus de la syntaxe du JavaScript standard. Le problème avec la vérification manuelle du code JavaScript est que bien le faire nécessite tellement de verbiage supplémentaire que le faux "type-safety" que vous obtenez ne compense pas la lisibilité perdue. 
Gardez votre JavaScript propre, écrivez de bons tests et de bonnes critiques de code. Sinon, faites tout cela mais avec TypeScript (qui, comme je l'ai dit, est une excellente alternative!).

**Mal:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Bien:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas trop optimiser

Les navigateurs modernes effectuent beaucoup d’optimisation au moment de l’exécution. Parfois, si vous optimisez, vous perdez simplement votre temps. [Il y a de bonnes ressources](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
pour voir où l'optimisation fait défaut. Ciblez-les entre-temps, jusqu'à ce que ils sont corrigés s'ils peuvent l'être.

**Mal:**

```javascript
// Sur les anciens navigateurs, chaque itération avec `list.length` non mis en cache serait coûteuse
// à cause du recalcul de `list.length`. Dans les navigateurs modernes, cela est optimisé.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Bien:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Supprimer le code mort

Le code mort est aussi grave qu'un code en double. Il n'y a aucune raison de le garder dans votre base de code. Si ce n'est pas appelé, éliminez-le! Il sera toujours en sécurité dans votre historique de version si vous en avez un jour besoin.

**Mal:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Bien:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ retour au sommet](#table-des-matières)**

## **Objets et Structures De Données**

### Utilisez des getters et des setters

Utiliser des getters et des setters pour accéder aux données sur des objets pourrait être mieux que simplement chercher une propriété sur un objet. "Pourquoi?" vous pourriez demander. Eh bien, voici une liste non organisée des raisons :

- Lorsque vous voulez faire plus qu'obtenir une propriété d'objet, vous n'avez pas pour rechercher et changer tous les accesseurs de votre base de code.
- Faciliter l'ajout d'une validation lors d'un `set`.
- Encapsuler la représentation interne.
- Faciliter l'ajout de la journalisation et de la gestion des erreurs lors de l'obtention et la configuration.
- Charger les propriétés de votre objet de manière paresseuse (lazy loading), par exemple pour obtenir les données d'un serveur.

**Mal:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Bien:**

```javascript
function makeBankAccount() {
  // celui-ci est privé
  let balance = 0;

  // un "getter", rendu public via l'objet renvoyé ci-dessous
  function getBalance() {
    return balance;
  }

  // un "setter", rendu public via l'objet retourné ci-dessous
  function setBalance(amount) {
    // ... valider avant de mettre à jour le solde
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ retour au sommet](#table-des-matières)**

### Faire en sorte que les objets aient des membres privés

Ceci peut être accompli par des fermetures (pour ES5 et moins).

**Mal:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Bien:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ retour au sommet](#table-des-matières)**

## **Classes**

### Préférez les classes ES2015 / ES6 aux fonctions simples de ES5

Il est très difficile d'obtenir un héritage de classe, une construction et une méthode lisibles pour les classes ES5 classiques. Si vous avez besoin d'héritage (et soyez conscient que vous ne pourriez pas), alors préférez les classes ES2015 / ES6. Cependant, préférez les petites fonctions aux classes jusqu'à ce que vous ayez besoin d'objets plus grands et plus complexes.

**Mal:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Bien:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Utiliser la méthode de chaînage

Ce modèle est très utile en JavaScript et vous le voyez dans de nombreuses bibliothèques telles que jQuery et Lodash. Cela permet à votre code d’être expressif et moins bavard.
Pour cette raison, utilisez la méthode de chaînage et regarder à quel point votre code sera propre. Dans vos fonctions de classe, retournez simplement `this` à la fin de chaque fonction, et vous pourrez y chaîner d’autres méthodes de la classe.

**Mal:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Bien:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Retourne 'this' pour le chaînage
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Retourne 'this' pour le chaînage
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Retourne 'this' pour le chaînage
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Retourne 'this' pour le chaînage
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ retour au sommet](#table-des-matières)**

### Préfère la composition à l'héritage

Comme l'a déclaré le Gang of Four dans le célèblre [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns), vous devriez préférer la composition à l'héritage lorsque vous le pouvez. Il y a beaucoup de bonnes raisons d'utiliser l'héritage et beaucoup de bonnes raisons d'utiliser la composition.
Le point principal de cette maxime est que si votre esprit opte instinctivement pour l'héritage, essayez de penser si la composition pourrait mieux modéliser votre problème.

Vous vous demandez peut-être alors "quand devrais-je utiliser l'héritage ?" Cela dépend de votre problème actuel, mais la liste ci-dessous identifie des cas où l'héritage est plus logique que la composition:

1.  Votre héritage représente une relation "est-une" et non une relation "a-une"
         relation (Humain-> Animal contre Utilisateur-> UserDetails).
2.  Vous pouvez réutiliser le code des classes de base (les humains peuvent se déplacer comme tous les animaux).
3.  Vous souhaitez apporter des modifications globales aux classes dérivées en modifiant une classe de base.
         (Changer la dépense calorique de tous les animaux quand ils bougent).

**Mal:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Mauvais parce que 'Employees' «ont» des données fiscales (tax data). 'EmployeeTaxData' n'est pas un type de 'Employee'
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Bien:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

## **SOLID**

### Principe de responsabilité unique (SRP)

Comme indiqué dans Clean Code, "Il ne devrait jamais y avoir plus d'une raison pour qu'une classe change". 
Il est tentant de confier à une seule classe beaucoup de fonctionnalités, comme lorsque vous ne pouvez emporter qu'une seule valise par vol. Le problème avec ceci est que votre classe ne sera pas conceptuellement cohérente et cela lui donnera de nombreuses raisons  de changer. Il est important de minimiser le nombre de fois que vous aurez besoin de changer une classe. C'est important car si trop de fonctionnalités sont dans une classe et que vous modifiez un morceau de celui-ci, il peut être difficile de comprendre comment cela affectera les autres modules dépendants dans votre base de code.

**Mal:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Bien:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe Ouvert / Fermé (OCP)

Selon Bertrand Meyer, "les entités logicielles (classes, modules, fonctions, etc.) devrait être ouvert pour extension, mais fermé pour modification. "
Qu'est-ce que cela signifie ? Ce principe stipule essentiellement que vous devez permettre aux utilisateurs d'ajouter de nouvelles fonctionnalités sans changer le code existant.

**Mal:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transforme 'response' et la renvoi 
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transforme 'response'et la renvoi
      });
    }
  }
}

function makeAjaxCall(url) {
  // requête et retourne une promesse
}

function makeHttpCall(url) {
  // requête et retourne une promesse
}
```

**Bien:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // requête et retourne une promesse
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // requête et retourne une promesse
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe de substitution de Liskov (LSP)

C'est un terme effrayant pour un concept très simple. Il est formellement défini comme "Si S est un sous-type de T, les objets de type T peuvent être remplacés par des objets de type S (c’est-à-dire que les objets de type S peuvent se substituer aux objets de type T) sans modifier des propriétés souhaitables de ce programme (exactitude, tâche effectuée, etc.). "
C’est une définition encore plus effrayante.

La meilleure explication à cela est si vous avez une classe debase et une classe dérivée, alors la classe de base et la classe dérivée peuvent être utilisées de manière interchangeable sans obtenir résultats incorrects. 
Cela peut encore être déroutant, alors jetons un coup d'oeil à la exemple classique de Carré/Rectangle. Mathématiquement, un carré est un rectangle, mais si vous modélisez en utilisant la relation "est-un" via l'héritage, vous aurez rapidement des problèmes.

**Mal:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Renvoi 25 pour 'Square'. Devrait être 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Bien:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe de séparation des interfaces (ISP)

JavaScript n'a pas d'interface donc ce principe ne s'applique pas aussi strictement. Cependant, bien que Javascript ne soit pas un language fortement typé, ce principe est important et pertinent.

ISP déclare que "les clients ne doivent pas être obligés de dépendre des interfaces qu'il n'utilise pas."
Les interfaces sont des contrats implicites en JavaScript (langage non typé).

Un bon exemple à regarder pour démontrer ce principe en JavaScript concerne les classes qui nécessitent des objets de paramètres volumineux. Ne pas obliger les clients à configurer d'énormes quantités d'options est bénéfique, car la plupart du temps, ils n'auront pas besoin de tous les paramètres. Les rendre facultatifs aide à éviter d'avoir une "interface grasse".

**Mal:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {}  // La plupart du temps, nous n'avons pas besoin d'animer lorsque vous traversez.
  // ...
});
```

**Bien:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe d'inversion de dépendance (DIP)

Ce principe énonce deux choses essentielles:

1.  Les modules de haut niveau ne doivent pas dépendre de modules de bas niveau. Les deux devraient dépendre des abstractions.
2.  Les abstractions ne doivent pas dépendre des impémentations. Les implémentations devraient dépendre des abstractions.

Cela peut être difficile à comprendre au début, mais si vous avez travaillé avec AngularJS, vous avez vu une mise en œuvre de ce principe sous la forme d'injection de dépendance (DI). Bien que le concept soit différent, DIP empêche les modules de haut niveau de connaître les détails d'implémentation de ses modules de bas niveau et de les configurer.
Cela peut être accompli grâce à DI. Un énorme avantage de cela est qu'il réduit le couplage entre les modules. Le couplage est un très mauvais schéma de développement car cela rend votre code difficile à refactoriser.

Comme indiqué précédemment, JavaScript n'a pas d'interface, donc les abstractions sont des contrats implicites. C'est-à-dire les méthodes et les propriétés qu'un objet / classe expose à un autre objet / classe. 
Dans l'exemple ci-dessous, le contrat implicite est que tout module utilisant un `DemandeurInventaire` aura accès à une méthode `demanderUnArticle` sans se préoccuper de l'implémentation.

**Mal:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: Nous avons créé une dépendance sur une implémentation de requête spécifique.
    // On devrait juste avoir demanderUnArticles dépendent d'une méthode de requête: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Bien:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// En construisant nos dépendances à l'extérieur et en les injectant, on peut facilement
// substituer notre module de requête à un nouveau module utilisant WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ retour au sommet](#table-des-matières)**

## **Testage**

Le test est plus important que la livraison. Si vous n'avez pas de test ou un
quantité insuffisante, alors chaque fois que vous livrez du code, vous ne serez pas sûr que
n'a rien casser. Décider du nombre de tests adéquat revient à votre équipe, mais avoir une couverture de 100% (tous les états et branches) vous permet d'obtenir une très grande confiance et une tranquillité d'esprit en tant que développeur. Cela signifie qu'en plus d'avoir un excellent framework de test, vous devez également utiliser un
[bon outil de couverture](http://gotwarlost.github.io/istanbul/).

Il n'y a aucune excuse pour ne pas écrire de tests. Il y a [beaucoup de bons frameworks de test JS](http://jstherightway.org/#testing-tools), Alors, choisissez-en un avec votre équipe.
Lorsque vous en trouvez un qui convient à votre équipe, essayez de toujours écrire des tests pour chaque nouvelle fonctionnalité / module. Si votre méthode préférée est 'Test Driven Development' (TDD), c’est génial, mais l’essentiel est de simplement
vous assurez d'atteindre vos objectifs de couverture avant de lancer une fonctionnalité ou refactoriser un existant.

### Concept unique par test

**Mal:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Bien:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ retour au sommet](#table-des-matières)**

## **Simultanéité**

### Utilisez des promesses plutôt que des fonctions de rappel

Les fonctions de rappel ne sont pas propres et provoquent des quantités excessives d'imbrication. Avec ES2015 / ES6, les promesses sont un type global intégré. Utilisez les!

**Mal:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Bien:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ retour au sommet](#table-des-matières)**

### Async / Await sont plus propres que Promises

Les promesses sont une alternative très propre aux callbacks, mais ES2017 / ES8 apporte `async/await` qui offrent une meilleur solution. Tout ce dont vous avez besoin est une fonction préfixée dans un mot clé `async`.Vous pourrez alors écrire votre logique de façon impérative, sans chaîner les fonctions avec `then`. Utilisez-le dès aujourd'hui si vous pouvez tirer parti des fonctionnalités de ES2017 / ES8 !

**Mal:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Bien:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ retour au sommet](#table-des-matières)**

## **La Gestion des Erreurs**

Les erreurs levées sont une bonne chose! Elle veulent dire que le runtime a réussi à
identifier que quelque chose dans votre programme a mal tourné et il vous informe en arrêtant l'exécution de la fonction dans la pile d'appels en cours, tuant le
processus (dans Node), et vous notifiant dans la console avec une pile d'appels.

### Ne pas ignorer les erreurs interceptées

Ne rien faire avec une erreur interceptée ne vous donne pas la possibilité de réparer
ou réagir à cette erreur. Consigner l'erreur dans la console (`console.log`)
n'est pas beaucoup mieux car souvent, elle se perd dans une mer de choses imprimées
dans la console. Si vous intégrez un morceau de code dans un `try / catch`, cela signifie que vous pensez qu'une erreur peut se produire, et vous devriez donc avoir un plan,
ou un chemin de code, pour traiter cette erreur.

**Mal:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Bien:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // Une option (plus bruyante que console.log):
  console.error(error);
  // Une autre option:
  notifyUserOfError(error);
  // Une autre option:
  reportErrorToService(error);
  // OU faire tous les trois!
}
```

### Ne pas ignorer les promesses rejetées

Pour la même raison, vous ne devez pas ignorer les erreurs interceptées
de `try / catch`.

**Mal:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // Une option (plus bruyante que console.log):
    console.error(error);
    // Une autre option:
    notifyUserOfError(error);
    // Une autre option:
    reportErrorToService(error);
    // OU faire tous les trois!
  });
```

**[⬆ retour au sommet](#table-des-matières)**

## **Mise En Forme**

Le formatage est subjectif. Comme beaucoup de règles, il n’existe pas de solution simple et rapide. L'essentiel est de NE PAS LESINER sur le formatage.
Il y a [des tonnes d'outils](http://standardjs.com/rules.html) pour automatiser cela.
Utilisez-en un! Discuter de la mise en forme est une perte de temps et d'argent pour les ingénieurs.

Pour les choses qui ne relèvent pas du formatage automatique
(indentation, tabulations vs espaces, guillemets doubles vs simples, etc.) regardez ici
pour des conseils.

### Utilisez une capitalisation cohérente

JavaScript n'est pas typé, la capitalisation en dit beaucoup sur vos variables,
fonctions, etc. Ces règles sont subjectives, votre équipe peut donc choisir ce que
Ils veulent. Le fait est que, peu importe ce que vous choisissez tous, soyez juste cohérent.

**Mal:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Bien:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ retour au sommet](#table-des-matières)**

### Les appelants et les correspondants doivent être rapprochés

Si une fonction en appelle une autre, gardez ces fonctions verticalement proches dans la source
fichier. Idéalement, laissez l'appelant juste au-dessus de l'appelé. Nous avons tendance à lire le code de
de haut en bas, comme un journal. Pour cette raison, organisez votre code de cette façon.

**Mal:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Bien:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ retour au sommet](#table-des-matières)**

## **Commentaires**

### Ne commentez que les choses qui ont une complexité de logique métier.

Les commentaires sont des explications, pas une obligation. Un bon code s'auto-documente.

**Mal:**

```javascript
function hashIt(data) {
  // Le hash
  let hash = 0;

  // Longeur du string
  const length = data.length;

  // Boucle à travers chaque caractère dans les données
  for (let i = 0; i < length; i++) {
    // Obtenez le code de caractère.
    const char = data.charCodeAt(i);
    // Faire le hash
    hash = (hash << 5) - hash + char;
    // Convertir en entier 32 bits
    hash &= hash;
  }
}
```

**Bien:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convertir en entier 32 bits
    hash &= hash;
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne laissez pas de code commenté dans votre base de code

Le contrôle de version existe pour cette raison. Laissez l'ancien code dans votre historique.

**Mal:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Bien:**

```javascript
doStuff();
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas journaliser les modifications dans les commentaires

Rappelez-vous, utilisez le contrôle de version! Il n'y a pas besoin de code mort, de code commenté, ni de commentaires de journalisation. Utilisez `git log` pour obtenir l'historique!

**Mal:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Bien:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Évitez les marqueurs de position

Ils ne font généralement qu'ajouter du bruit. Laissez les fonctions, les noms de variables ,
l’indentation et le formatage donner la structure visuelle à votre code.

**Mal:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Bien:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ retour au sommet](#table-des-matières)**

## Traduction

Ceci est également disponible dans d'autres langues:
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portugais Brésilien**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Espanol**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Espanol**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinois**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **Allemand**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Coréen**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polonais**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russe**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamien**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japonais**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonésie**:
  [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italien**:
  [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)

**[⬆ retour au sommet](#table-des-matières)**
