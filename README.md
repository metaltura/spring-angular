# metaltura/spring-angular

La pile Spring Boot + Angular : les patrons qui écrivent, depuis un modèle de domaine, une application avec un serveur Spring et une interface Angular, puis son code. Un modèle qui branche ces patrons sur un domaine obtient un projet Maven et un projet Angular qui se lancent comme des projets ordinaires de leur pile.

## Ce qu'il contient

- `models/DomainToSpringAngular.yaml` — les patrons de la pile, du domaine vers l'application, en trois paquets :
  - `sql` : les tables, leurs colonnes, leurs clés étrangères et les tables de jointure ;
  - `spring` : les entités JPA, les repositories, les services, les controllers, les deux instances de `StackApplication` de la pile (`springApplication` et `springAuditedApplication`) et le patron de tête `domainToSpring`. Il produit trois racines dans la cible de son Mapping : l'Application, le paquet `spring` avec le contexte Spring et le paquet `angular` avec l'interface Angular ;
  - `angular` : le service Angular.
- `models/SpringAngularToCode.yaml` — les patrons de génération de la pile, en deux paquets :
  - `spring` : `SpringPattern`, la base des patrons qui lisent un controller, un service ou un repository Spring (ce que sert un controller, son repository, le nom d'une méthode de service, le type de la clé), puis le projet Maven (`pom.xml` · `application.properties` · la classe d'application), une entité JPA, un repository, un service et un controller par bean, un record par charge utile, le schéma de la base (`schema.sql` par `contextToSchema` : une table par table du modèle SQL dans l'ordre des clés étrangères, que Spring joue au démarrage et qu'Hibernate vérifie), le gestionnaire d'erreurs (`config/ApiExceptionHandler.java` : 400 avec la raison de chaque champ en erreur comme le serveur Node, 409 quand la base refuse une suppression), le JSON écrit comme celui du serveur Node (`config/JsonConfig.java` : dates ISO avec leurs millisecondes, valeur absente omise, nombre entier sans décimale), et le patron de tête `springToCode`.
  - `angular` : le projet Angular 22 d'une interface Angular (`package.json` · `angular.json` · `tsconfig.json` · `proxy.conf.json` qui renvoie le chemin de base de l'API vers l'adresse du serveur · `src/index.html` · `src/main.ts` · `src/app/app.config.ts` · `src/app/app.routes.ts` avec une route par route de l'interface), le composant racine (la barre de navigation avec le titre et la couleur de la Navbar · un lien par entrée de menu), un composant autonome par composant de page (un tableau, une fiche avec son bouton « Delete » ou un formulaire piloté par le gabarit · la liste déroulante d'un champ de relation remplie par la ressource qu'il nomme), un service injectable par service (une méthode par point d'entrée servi), les types des charges utiles par `resourceToPayloadTypes` de `metaltura/application`, et le patron de tête `angularToCode` (option `apiUrl` : l'adresse du serveur · `http://localhost:8080` par défaut). Une page dont le service ne sert pas l'opération qu'elle demande affiche « Not available ». Le patron de tête de pile `springAngularToCode` part d'un contexte Spring et écrit un seul projet : une racine (`README.md` · `.gitignore` · `docker-compose.yml` de MariaDB), le serveur sous `server/` par `springToCode` et le client de l'interface Angular de la même application sous `client/` par `angularToCode` ; chaque projet se lance comme un projet ordinaire de sa pile.

`metaltura/examples` montre la pile branchée sur le domaine d'une boutique (`BoutiqueSpringAngular`).

## Ce dont il dépend

| Dépôt | Ce qu'il en emploie | Ligne `depends` |
| --- | --- | --- |
| `metaltura/domain` | `Domain`, que lit `DomainToSpringAngular` | `metaltura/domain/Domain@v1.0.0` |
| `metaltura/application` | `Application`, `DomainToApplication` et `ApplicationToCode` | `metaltura/application/Application@v1.0.0`<br>`metaltura/application/DomainToApplication@v1.0.0`<br>`metaltura/application/ApplicationToCode@v1.0.0` |
| `metaltura/spring` | `Spring` | `metaltura/spring/Spring@v1.0.0` |
| `metaltura/angular` | `Angular` | `metaltura/angular/Angular@v1.0.0` |

Ces modèles système de Metaltura s'écrivent par leur nom seul :

- `Mapping` ;
- `FileSystem` ;
- `Diagram` ;
- `Uml`.

## L'utiliser depuis un autre dépôt

Un modèle qui branche la pile sur son domaine écrit dans sa liste `depends` les patrons qu'il emploie, avec les métamodèles que la cible de ses Mappings contient :

```yaml
depends:
  - metaltura/application/Application@v1.0.0
  - metaltura/spring/Spring@v1.0.0
  - metaltura/angular/Angular@v1.0.0
  - metaltura/application/DomainToApplication@v1.0.0
  - metaltura/spring-angular/DomainToSpringAngular@v1.0.0
  - metaltura/spring-angular/SpringAngularToCode@v1.0.0
```

C'est la liste de `BoutiqueSpringAngular` dans `metaltura/examples`. Elle nomme en plus, par leur nom seul :

- `Mapping` ;
- son domaine, `BoutiqueDomain` ;
- `FileSystem`.

Metaltura clone chaque dépôt nommé à l'étiquette `v1.0.0`. Toute la librairie est à `v1.0.0` : Metaltura refuse qu'un même dépôt soit demandé à deux versions.

## D'où il vient

Ce dépôt vient du dépôt privé `Kogitec/simple-mda`, `main` à `96a1a75` (PLAT-1461), rangé par sujet par PLAT-1498 en un seul commit : ses modèles étaient sous `models/applications/spring-angular/`, avec `Spring` et `Angular`. L'historique de ses fichiers reste dans `simple-mda`, archivé.
