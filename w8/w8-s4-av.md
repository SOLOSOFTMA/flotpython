# w8-s4

## Résumé de la vidéo

Vous avez maintenant compris dans les
grandes lignes à quoi sert asyncio;
on a vu aussi très sommairement
comment commencer, avec les notions
de coroutines et de boucle
d'événement, maintenant dans cette
vidéo nous allons voir des exemples,
toujours assez simples mais plus
réalistes, de codes asyncio.


Pour commencer nous allons voir
==========[slide accès HTTP]
comment faire par exemple des accès
concurrents à plusieurs pages web.

==========[run cell]

Imaginons qu'on a plusieurs pages web
à aller chercher

==========[run cell]

Comme on l'a vu en introduction,

==========[slide accès séquentiel]

si on va chercher ces 4 pages
séquentiellement

==========[run cells]

ça peut prendre un certain temps,
selon les conditions réseau ... ici
environ 12s.

Très bien, alors souvenez-vous, on a
bien dit qu'avec asyncio

==========[slide]

on allait pouvoir simplement faire
la même chose mais de manière
concurrente, sans bloquer entre tous
les délais réseau qui interviennent
ici.

Alors voyons cela. Je commence par
importer la librairie aiohttp

==========[run import x2]

qui est, vous l'avez compris, une version
asynchrone pour aller chercher des
pages web.

Je dois vous signaler que cette
librairie n'est pas dans la librairie
standard, en tous cas dans python3.6 qui
je vous le rappelle est notre référence
pour ce cours. Vous devez l'installer
comme d'habitude avec pip ou conda.

========== [fragment]

sur quoi je peux construire une
coroutine qui va chercher une page
web, je l'ai appelée fetch.

Comment ça marche ? de manière assez
standard, je construis un objet session;
à l'intérieur de cette session, je crée
une requête avec session.get(), pour
obtenir un objet response, sur lequel je
peux lire le contenu brut de la page
web.

Ce qui est intéressant ici, c'est la
syntaxe 'async with' - vous en voyez ici
deux exemplaires; de même qu'en python
synchrone on peut faire with sur un
context manager, on peut faire async
with sur un context manager asynchrone.

==========[slide context managers async]

Vous vous souvenez qu'un objet context
manager normal a les méthodes spéciales
__enter__ et __exit__, eh bien un
context manager asynchrone va
implémenter `__aenter__` et `__aexit`.

Et l'instruction 'async with' conduira à
l'invocation des ces deux méthodes au
travers d'un await; ça se prête donc
très bien à toutes les applications
réseau ou autres bases de données, où la
création du contexte, et aussi sa
destruction, sont au moins aussi
asynchrones que ce qu'on fait avec.

Bref, la construction 'async with' est
tres fréquente dans du code asynchrone.

Je vous signale à cet égard la PEP-0492,
==========[fragment]
si vous voulez creuser, qui explique
ce protocole de context manager
asynchrone, et dont je vous recommande
la lecture si vous décidez de vous
lancer avec asyncio.


==========[slide code]
Je peux maintenant utiliser gather pour
aller chercher mes 4 URLs en même
temps. Pour cela je crée une coroutine
fetch_urls, que je peux


==========[run]
==========[fragment]
ensuite invoquer au travers d'une boucle
d'événements, comme on l'a déjà vu, et
cette fois avec ce code
==========[run]
je vais aller chercher mes quatre pages,
tout est fait en même temps, je n'ai
pas perdu de temps à attendre, les 4 requêtes
se déroulent complètement en même temps,
vous observez que ça ne prend que 2
secondes environ, soit le délai pour le
plus lent.

========== temps estimé jusqu'ici : 4'

==========[slide itérateurs asynchrones]

De la même façon, le langage supporte
maintenant les itérateurs asynchrones,
et même les compréhensions asynchrones,
qui ont été ajoutées dans la 3.6, ce qui
explique qu'elles sont définies dans un
PEP à part, la PEP-530.

Voyons tout d'abord un exemple pratique

==========[slide fetch2]

Je vais reprendre le même exemple, ça a
le mérite d'être simple, mais plutôt que
de lire d'un seul coup tout le contenu
de la page web, imaginons que je veuille
traiter les lignes au fur et à mesure
qu'elles arrivent.

C'est un exemple un peu artificiel, mais
ce serait la même chose avec par exemple
une requête de base de données, ou
similaire.

J'ai repris le code de fetch, mais cette
fois j'utilise un for asynchrone
==========[surligner la ligne async for]
pour lire les lignes au fur et à mesure.

Malgré le fait que je ne fais aucun
await explicitement dans ce for, les
lignes sont bel et bien lues de manière
asynchrone et pour le voir, j'imprime en
vrac l'indice de l'URL, que l'on va bien
voir arriver dans le désordre.

==========[slide résumé]

À ce stade, c'est certainement utile que
l'on fasse un résumé de ce que nous
avons vu jusqu'ici au sujet des
extensions asynchrones du langage à
proprement parler;

Nous avons vu

==========[fragment]

* la notion de coroutine; j'insiste bien
  à nouveau sur la différence entre une
  fonction coroutine (qui est définie
  par async def) et ce qu'elle renvoie,
  un objet coroutine.

==========[fragment]

* le fait d'appeler une coroutine
  n'exécute rien, cela renvoie
  immédiatement un objet coroutine qui
  ne provoquera une exécution que si on
  lui applique un await, ou si on la
  passe à une boucle d'évènements

==========[fragment]

* une coroutine peut appeler une autre
  coroutine avec 'await'; on ne peut pas
  faire await dans une fonction
  ordinaire, seulement dans une coroutine

==========[slide]

* tout ceci n'a de sens qu'au travers
  d'une boucle d'événements, qui joue le
  rôle de scheduler. On a vu jusqu'ici
  une première façon d'utiliser une
  boucle d'événements, via la méthode
  'run_until_complete' qui, combinée
  avec gather, permet de faire tourner
  plusieurs traitements en parallèle, on
  verra bientôt d'autres façons de gérer
  le contenu d'une boucle d'événements.

==========[fragment]

* enfin on a vu les notions de context
  managers et d'itérateurs asynchrones,
  qui indiquent au langage que les
  méthodes spéciales en jeu sont des
  coroutines.

À bientôt pour la suite

xxxx

(en fait on verra que de façon plus
générale, on peut faire await sur une
famille plus large d'objets python qui
implémentent le protocole 'awaitable'

c'est une façon naturelle de
généraliser; un peu comme en partant des
listes on a généralisé à la notion de
'iterable', et en partant des fichiers
on a défini la notion de 'context manager'


ici encore le langage défini
de la même façon qu'on peut faire un
  for 


