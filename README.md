# EW Guidelines

Les différentes recommandations de ce document ont pour but de décrire des façons de développer pour le web côté client, selon une méthode qui se veut :
- facile à lire
- facile à maintenir et faire évoluer
- tout en respectant des standards globaux (accessibilité, performances, sécurité).

## Global

- le projet utilise des règles d'indentation cohérentes (nature de l'indentation, etc). Si besoin, se référer au fichier eslint du projet, ou utiliser un outil comme `Prettier` (sur un hook git par ex)
- le choix d'une techno (jQuery, React, etc) ne peut être proposé / soutenu que s'il présente un intérêt pour le projet (soit en temps de dev, soit en maintenance / évolutivité)
- sauf si la techno propose une nomenclature spéficique (React, classes PHP), nous utiliserons une syntaxe `kebab-case` pour tous les fichiers (Sass, JS, HTML)
- ces guidelines partent du principe que le projet utilise un module bundler (en l'occurrence : Webpack)

## Tout est composant

Ces recommandations servent, dans la grande majorité, un but commun : penser chaque élément d'interface comme composant, ou comme composant de composant (cf. Atomic Design, https://atomicdesign.bradfrost.com/table-of-contents/).

Cela permet un meilleure découpage de l'application ou du site et une meilleure lisibilité du code par rapport à l'interface utilisateur.
En gros, le but est d'avoir quelque chose comme :
- un template (PHP, Twig, React, etc) = un composant
- un module JS = la couche fonctionnelle du composant
- un fichier Sass = la couche graphique du composant

## Nommage

Bien que ce ne soit pas obligatoire, une méthode de nommage permettant de refléter cette logique de composants (ex: BEM, http://getbem.com/) est très recommandée.

### Restez cohérent
Préférez nommer les choses en anglais :
- les langages sont anglophones
- les équipes / clients peuvent être internationaux

Attention : essayez de limiter les erreurs / francisations -> 
- un fonction getVehicule ? 🤨 = on comprend, mais bon...
- un composant my-card pour une carte géographique ? 🤔 = un contresens qui peut faire perdre du temps
- une fonction getUserRoute pour avoir le trajet d'un utilisateur ou la route conduisant à la page "Mon compte" ? 🤷

### BEM

BEM définit un cadre pour nommer les éléments d'une interface selon une grammaire simple : Block, Element, Modifier.
Nous utiliserons les séparateurs "habituels" (`__`, `--`) couplés à une casse "kebab-case".

Exemple : `.main-block__my-element--my-modifier`

Les objectifs sont de :
- pouvoir copier / déplacer des composants sur tout le projet, sans effet de bord
- limiter les effets de bords en cas d'évolution / modification d'un composant
- apporter une lisibilité sur ce qui est représenté dans le code

### Profondeur des sélecteurs BEM

BEM admet plusieurs utilisations concernant la profondeur des sélecteurs. Toutefois, il me semble préférable de ne pas représenter des enfants et petits enfants avec ces sélecteurs, comme `.main-block__element__inner-element`

Ce genre de sélecteurs sont lourds et n'ont pas d'intérêts concernant la lisibilité du code. Tout au plus, ils reflètent la structure du DOM, ce qui est quelque chose qu'on essaye d'éviter au maximum (couplage trop fort entre HTML et CSS). Pour reprendre le cas du dessus, on utilisera plutôt : `.main-block__inner-element` (la référance à `element` étant inutile). 

*Le but est de représenter sémantiquement des éléments d'une interface graphique, pas une structure HTML.*

### Exemples, dos & don'ts

#### 1. un bouton

![bouton](img/button.png "Dos & don'ts : bouton")


Explications
- on ne nomme pas les blocks, elements et modifiers selon leurs aspects graphiques (`.button--blue`) mais selon un état, une sémantique. Exemple : `.button--disabled` ou `.button--disabled`
- les modifiers ne doivent être appliqué que sur le block (le composant, pas ses éléments) : c'est le composant qui change d'état, ses enfants ne font que suivre le changement d'état de leur parent
- ne pas empiler les classes pour atteindre le résultat graphique attendu : un composant à un état donné a un rendu. S'il faut partager des propriétés graphiques, on peut se référer aux variables CSS ou bien aux extends, mixins, functions et placeholders Sass.


![bouton](img/article.png "Dos & don'ts : article")

Explications
- on ne nomme pas les blocks, elements et modifiers selon leurs aspects graphiques (`.article--small`) mais selon un état, une sémantique. Exemple : `.article-thumbnail`, à noter que `.article.article--thumbnail` aurait pu aussi faire l'affaire.
- ne pas représenter la structure du DOM via BEM, c'est inutile (`.article__article-inner__content`). Le fait de le préciser n'améliore pas la compréhension du code et sera source de problèmes. *De toute façon, on ne dit jamais "Je vais me gratter le nez du visage de mon corps" ? Je vais me gratter le nez suffit à comprendre.* 
- le cas du bouton est plus compliqué. `.read-more-button` n'est pas la meilleure solution : elle ne fait pas référence à un composant qu'on suppose déjà exister (dans le premier exemple), donc on aura du code CSS en double. Par ailleurs, en l'état, c'est un composant indépendant, il pourrait faire référence à son contexte (en tant qu'enfant de l'article).
`.button.button--read-more` semble plus pratique : il décrit une déclinaison du composant `.button`. 
`.button.article-thumbnail__button` peut être pratique également, il précise la filiation avec le composant `.button` et également le contexte : `.article-thumbnail` et des variations graphiques liées à ce contexte particulier du bouton dans une miniature d'article. 
Selon le projet, l'une ou l'autre technique peut être plus utile / appropriée.

## Commentaires
De façon générale, les commentaires peuvent être très pratique. Toutefois :
- ça ne sert à rien d'en mettre partout, il faut qu'ils restent pertinent (ex : description de fonctions via JSDoc)
- ils ne doivent pas apparaitre en prod

## HTML

### Qualité

Quelques points à respecter pour produire un code HTML clean :

- le code HTML doit rester sémantique et ne doit contenir aucun aspect concernant sa représentation graphique. Par exemple, si un texte est tout en majuscule sur la maquette finale, ce sera fait via CSS, en HTML, le texte doit respecter les règles de typo standards.
- le code produit est valide (https://validator.w3.org/).
- les images déclarent systématiquement un attribut `alt`, qui doit reporter l'information donnée par l'image. C'est généralement le texte qu'elle contient. Pour proposer une description à l'image, on préférera l'attribut `title`.
- les ancres (`<a>`) ne sont utilisées que pour représenter des liens à un document exterieurs à la page, ou une section de la page. L'attribut href ne doit pas être vide ou contenir seulement `#`. Pour représenter des éléments cliquables (dont l'action est gérée en JS, par exemple), préférez un élément `<button>`.


### Performances

Quelques points à respecter pour produire un code HTML performant :
- limiter l'imbrication d'éléments HTML afin d'alléger la complexité du DOM et le poids de la page.
- utiliser les attributs `async` / `defer` tant que possible, ou reporter certaines ressources juste avant la fermeture de l'élément `body` afin de ne pas empêcher le rendu de la page
- limiter / ne pas utiliser d'appels à des ressources externes, sauf exceptions (services tiers, etc.). Exemple : les typos Google Font doivent être rappatriées dans le projet, idem pour les libs JS.

### Commentaires
Des commentaires HTML peuvent être pratique, notamment dans le cas de fermeture de balise sur des structures importantes. 
Exemple :
```
<section class="grid-wrapper">
  <!-- pleins de trucs -->
</section><!-- / .grid-wrapper -->
```

Par contre, ces commentaires ne doivent pas apparaître en prod.

## Templating

Si une logique de templating est utilisée :
- essayez de conserver une logique de composants, en découpant les fichiers de template de façon à pouvoir les réutiliser sur tout le projet, indépendamment du contexte
- pas non plus trop découper

## CSS / Sass

- essayez de garder une structure cohérente : un fichier Sass <-> un composant BEM
- utilisez tant que possible (= tant que ça améliore la lisibilité du code) les opérateurs de concaténation (`&`), variables et boucles
- n'oubliez pas le reset.css

### Frameworks

- bien peser le pour et le contre du choix d'un framework, notamment Bootstrap ou Foundation : s'ils ne sont utilisé que pour leur grille, pourquoi ne pas se tourner vers CSS Grid layouts ?

### Ordre des déclarations
Bien que le fait d'organiser ses déclarations CSS (par groupe, ex : layout, positionnement, puis style de texte, etc) soit quelque chose considéré comme une bonne pratique (https://www.alsacreations.com/actu/lire/497-de-lordre-que-diable-.html, https://dev.to/thekashey/happy-potter-and-the-order-of-css-5ec), ce n'est pas quelque chose qui me semble essentiel, ni pour le développement, ni pour la maintenance.

Toutefois, un minimum me semble appréciable. Prenons cet exemple : 
```
.alert-box {
    top: 20px; 
    margin-left: 20px;
    display: flex; 
    position: absolute; 
    height: 100%;  
    margin-bottom: 20px;
    border-radius: 5px;
    color: red;
    justify-content: center;
    margin-left: 1rem;
    left: 0;
    width: 100%;
}
```
- `top` est déclaré, mais `position` devrait arriver avant, ou au moins être collé (pour vraiment avoir les deux ensemble et bien comprendre ce qui se passe
- `left` : idem, elle est super loin de `top` et `position`
- `height` et `width` pourraient être regroupé
- `display` pourrait être mise en premier, dans la mesure où cette propriété influe sur beaucoup des propriétés applicables sur l'élément

Donc, au final, regroupez un peu, en plaçant `display` en premier, les déclarations de positionnement ensuite.


### !important ?

`!important` est **vraiment** à prescrire :
- ça rend la maintenance difficile : très dur à surcharger
- on perd complètement la main sur la spécificité constante donnée par BEM
- il y a toujours un moyen de faire mieux en respectant la spécificité à l'intérieur du composant, même si ça prend 30 secondes de plus


### Les unités
- les tailles de texte sont décrit en REM, si besoin en utilisant une mixin Sass (ou un CSS calc(), mais...) pour la conversion
- si la valeur est 0, on ne rajoute pas d'unité
- tant que possible, pour décrire des largeurs de blocs, on préférera utiliser des `%` ou `vw` (si le projet le permet)
- minimiser le nombre de points de rupture. Par exmple, plutôt que de définir des largeurs de conteneurs fixes, en `px`, avec points de rupture les redéfinissant 4 ou 5 fois. Ce genre de bout de code peut être facilement remplacé en utilisant les `%` et `max-width`.
```
.container {
  width: 1200px;
  @media screen and (max-width: 1200px) {
    width: 1000px;
  } /* 😡 */
  @media screen and (max-width: 1000px) {
    width: 800px;
  } /* 😡😡 */
  @media screen and (max-width: 800px) {
    width: 600px;
  } /* 😡😡😡 */
  @media screen and (max-width: 6000px) {
    width: 400px;
  } /* 😡😡😡😡 */
  @media screen and (max-width: 400px) {
    width: 320px;
  } /* 😡😡😡😡😡 */
} 

/* 🙏🙏🙏🙏🙏🙏🙏 */
.container {
  width: 90%;
  max-width: 1200px;
}
/* 🙏🙏🙏🙏🙏🙏🙏 */
```
### Dépendances
Les dépendances externes doivent être gérées via un package manager (et un seul, évitez le mix Yarn / NPM) et vérouillées sur un commit précis -> chaque rebuild du projet doit donner le même résultat si le code n'a pas changé.

Dans le cas de Sass, les imports aux dépendances devraient se faire via le système de module, via le préfixe `~`.

Exemple :
`@import "~bulma"` plutôt que `@import "../../../node_modules/bulma/src/bulma.scss"`

### Commentaires
Des commentaires Sass peuvent être pratique :
- pour décrire un composant
- ses fichiers de template cible
- décrire des hacks (si nécessaire). Exemple :

```
.card {
  backface-visibility: hidden; // Prevent logo flickering on CSS transition
}
```

## JS

### Structure

- essayez de garder une structure cohérente : un module JS <-> un composant BEM

### Composants

- tester la présence d'un composant / élément avant d'appeler du code dessus
- le module JS exporte une méthode ou une fonction `init` pour initialiser le composant
- le module JS doit être structuré (soit en fonctions, soit en définissant une classe + méthodes associées)


### Performances

Tant que possible :
- n'utilisez JS que pour ajouter / supprimer des classes sur le DOM (évitez d'injecter / supprimer directement des éléments du DOM)
- évitez l'utilisation de fonctions / méthodes (en particulier celles qui manipule le DOM) dans une boucle

### Dépendances
Les dépendances externes doivent être gérées via un package manager (et un seul, évitez le mix Yarn / NPM) et vérouillées sur un commit précis -> chaque rebuild du projet doit donner le même résultat si le code n'a pas changé.

Exemple :
`@import "~bulma"` plutôt que `@import "../../../node_modules/bulma/src/bulma.scss"`

## TODO
- ajouter des exemples de projet et de modules JS, fichiers Sass et fichier de template dans un dossier `examples`
- ajouter un dossier de starters (avec structure de base, utilitaires, conf)
- relire un coup 😅
- garder à jour 😅
