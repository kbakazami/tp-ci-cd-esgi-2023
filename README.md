# Projet CI/CD

## Reproduire les étapes de la CI
La CI est constituée de 2 étapes lors d'une pull request ou d'un push


### 1 - Checkout
Cette étape permet de vérifier le repository et l'utiliser

### 2 - Use composite
Cette étape permet de jouer les étapes correspondante sans avoir à les dupliquer pour les différents jobs. Les étapes sont situées dans le fichier suivant : `.github/actions/symfony-ci/action.yml`

Les différentes étapes à reproduire sont donc les suivantes :

#### 1 - Install Composer dependencies
Cette étape permet d'installer les dépendances dont le projet à besoin pour fonctionner. Pour réaliser cette étape il suffit de lancer la commande suivante :

```bash
composer install
```

L'étape échouera si l'installation n'a pas pu aller jusqu'au bout à cause de la version PHP ou des versions des dépendances. Il faut donc corriger les erreurs rencontrées pour réussir cette étape.

#### 2 - Unit Test
Cette étape permet de jouer les tests présents dans le projet afin de vérifier certains éléments. Pour pouvoir jouer les tests il faut lancer cette commande :

```bash
php bin/phpunit --stop-on-failure
```

L'étape échouera si les tests ont échoués. Il faut donc corriger les tests pour qu'ils renvoient un succès.

#### 3 - Code sniffer
Cette étape permet de valider et vérifier le code. Afin de vérifier le code il faut lancer cette commande :

```bash
php vendor/bin/phpcs src/ tests/ templates/
```
La commande ci-dessus permet de vérifier les fichiers qui sont situés dans les dossiers `src/` `tests/` et `templates/`

L'étape peut échouer si elle rencontre des erreurs mais l'étape suivante peut les corriger.


#### 4 - Code beautifier
Cette étape vient compléter la précédente. Elle permet de corriger au maximum les erreurs détectées. Pour corriger les erreurs il faut lancer cette commande :

```bash
php vendor/bin/phpcbf src/ tests/ templates/
```

Si cette étape échoue, cela veut dire que la commande n'a pas pu corriger certaines erreurs, il faudra donc le faire à la main.

---

## Déploiement Continu et Livraison Continue
Le déploiement continu se joue à la suite de la CI et uniquement lorsqu'il y a un push ou une pull request sur la branche principale (main).

Le troisième "job" qui se joue est nommé `create-prerelease` et comme son nom l'indique permet de créer une pre-release Github qui est le livrable produit par la CD.

Pour déployer une nouvelle version il faut tout d'abord créer une pull request pour merge develop dans main. Si toutes les étapes ont été validée lors de la pull request alors il faut la merge puis une autre action se lancera qui va créer la pre release GitHub avec le tag `latest`.

Lorsque la pre release a été effectuée, il faut créer un tag à la main et le workflow se lancera automatiquement pour déployer la nouvelle version avec le nom du tag associé.

Pour créer le tag il faut le faire en ligne de commande de cette façon (la version du tag doit obligatoirement commencer par `v` sinon le workflow skip l'étape de création) :

```bash
# Création du tag
git tag -a v1.0.0 -m "Version 1.0.0"

# Push du tag sur le repo GitHub
git push origin --tags
```

/!\ Attention à bien être sur la branche principale /!\

---

TP réalisé par STRUYS Wivin et KHALDI Sofia