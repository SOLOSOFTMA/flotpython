# w8-s2

## Résumé de la vidéo

La dernière fois nous avons vu des
généralités sur la programmation
asynchrone, et nous avons dit que la
librairie asyncio était un bon candidat
pour ça. Dans cette vidéo nous allons
faire tourner quelques exemples pour
illustrer les concepts de base.

Pour commencer bien entendu j'ai besoin
d'importer le module asyncio

==========[run cell]

j'évalue ma cellule avec Shift-Enter

==========[slide coroutines]

Voyons d'abord la notion de coroutine;
c'est quoi une coroutine ? à la base
cela ressemble beaucoup à une fonction,
mais dont on sait qu'elle va faire son
traitement en plusieurs segments de code
séquentiels.


==========[fragment code de la routine]

En voici un exemple; on commence par
définir la coroutine presque comme une
fonction mais avec 'async def' au lieu
de simplement 'def'

Cette coroutine est aussi simple que
possible, elle fait un traitement en
trois morceaux, qui impriment
respectivement 'debut', 'milieu' et
'fin', et on simule des temps morts
entre ces trois morceaux.

Il faut surtout remarquer la nouvelle
directive 'await', qui nous permet
d'indiquer les étapes du traitement où
on sait qu'un délai est attendu - ici
j'utilise la coroutine 'asyncio.sleep'
qui nous permet d'attendre pendant un
temps fixe; dans une application
réaliste on va plutôt attendre un
événement, du genre que des données
soient prêtes en lecture, on verra
d'autres exemples tout à l'heure.

Je vais évaluer ce code

==========[run cell]

avant de vous montrer ce qu'on peut en
faire.

==========[slide coroutines]

Tout d'abord regardons l'objet
'morceaux', il s'agit bien d'une
fonction, jusque là tout va bien.

==========[fragment morceaux()]

Si j'appelle cette fonction, comme on
pourrait avoir envie de le faire

==========[run cell morceaux("run")]

comme vous le voyez, il ne se passe rien
de ce qu'on attendait, l'appel retourne
immédiatement, et rien n'est imprimé.

Le résultat de cet appel est un objet de
type coroutine; une des façons
d'exploiter un objet de ce type est de
l'inclure dans une boucle d'événements,
comme ceci

==========[slide Boucle d'événements]

Pour commencer il nous faut créer une
boucle d'événements, ça se présente
comme ceci

==========[fragment get_event_loop]
==========[run cell]

Cet objet loop, c'est en fait le
scheduler qui va se charger de faire
avancer en parallèle toutes les
coroutines. Pour commencer voyons
comment lui faire exécuter un seul
exemplaire de notre coroutine

==========[fragment run_until_complete]
==========[run cell]

Cette fois ça marche comme on s'y
attend. même si naturellement avec une
seule coroutine ça n'est pas très
impressionnant. Voyons tout de suite ce
que ça donne avec plusieurs traitements
en parallèle.

==========[slide 'plusieurs traitements']

Pour cela je vais utiliser une coroutine
fournie par la librairie, qui s'appelle
'gather'.

==========[fragment gather]

Elle est très importante, car elle prend
en arguments plusieurs coroutines, et
renvoie la liste des résultats. C'est
donc ce qu'il nous faut pour exécuter
deux instance de la coroutine 'morceaux'
en parallèle.

==========[run cell]

Si j'évalue, les deux segments de 3
morceaux sont bien exécutés en
parallèle, c'est-à-dire plus ou moins en
même temps, les deux début, puis les
deux milieu, etc..

==========[slide figure scheduling]

Pour faire le lien avec ce qu'on avait
vu dans la première séquence, voici
comment ça se passe en termes de
chronologie. L'objet loop, qui fait donc
office de scheduler, va orchestrer tout
ceci. Au début naturellement, il n'est
pas possible de savoir à coup sûr
laquelle des deux coroutines sera
appelée en premier, puisqu'elles sont
censées se dérouler en même temps, on en
choisit donc une au hasard. Ce qui est
important, c'est qu'une fois qu'on est
entré dans disons run1(), cette
coroutine va garder le processeur
jusqu'à ce qu'elle rende la main
explicitement en appelant 'await'.

À ce stade, run1 est mise en standby -
avec tout son contexte d'exécution - et
le scheduler cherche ensuite une autre
coroutine à qui donner la main. Dans
notre cas il se trouve que run2() est
prête, on l'exécute donc également, mais
à nouveau jusqu'à rencontrer un
'await'. À ce stade on a exécuté les
deux débuts, pas tout à fait en même
temps mais coup sur coup, et on a mis de
coté les deux coroutines, avec leur
état, prêtes à être réactivées.

Après le délai qui est codé en dur dans
ce petit exemple, la boucle se rend
compte que run1 devient prête à
reprendre, on lui passe la main, puis à
run2, etc. jusqu'à rencontrer un
'return'; une fois que les coroutines
sont toutes terminées, la méthode
'run_until_complete' nous rend la main.

Une première chose à remarquer, on l'a
dit dans la vidéo précédente, c'est que
tout ceci est executé dans un seul
thread, et donc aucun des blocs que j'ai
dessinés ici ne peut être interrompu une
fois qu'il a commencé.

Et ça c'est très important, parce que ça
veut dire que je vais pouvoir me
débarrasser de tout un tas de problèmes
que j'aurais eus si j'avais utilisé des
threads; quand on utilise plusieurs
threads, c'est l'OS qui fait la
commutation et ça peut arriver n'importe
quand, et ça ça crée des besoins d'accès
exclusifs à certains objets; c'est toute
une famille de problèmes qui sont
beaucoup moins sévères avec asyncio.

==========[slide 'ce qu'il ne faut pas faire']

Mais ce que ça veut dire aussi, c'est
qu'il ne faut pas garder le processeur
trop longtemps sans rendre la main avec
un await. Voyons ça sur une version
légèrement différente de notre coroutine
morceaux,

==========[fragment famine]

que j'appelle famine; elle fait presque
comme morceaux, mais j'ai remplacé un
await sleep(1) par un appel bloquant à
time.sleep(1), qui est un appel
synchrone. Du coup on n'a plus 3 petits
fragments, mais 1 petit et un gros de 1
seconde.

==========[eval famine]

==========[slide]

Si on fait tourner ça, on observe que
les impressions ne sont plus,
naturellement, mélangés entre les deux
coroutines; ce qui se passe c'est ceci

==========[slide chronologie]

Dit comme ça c'est évident, mais dans la
pratique ce type de comportement se
produit plus souvent qu'on ne voudrait,
et quand ça arrive ça a un gros impact
sur la réactivité de votre application.

[[AL: ça serait bien de donner oralement un exemple de ce type de
problème qui est en fait une erreur de programmation où l'on mélange
traitement asynchrone et CPU intensive]]

Vous pouvez toujours insérer une
instruction comme await asyncio.sleep(0)
où vous voulez pour faire respirer votre
code.


==========[slice conclusion]

Pour récapituler cette séquence, on a vu
que

. on écrit le code asynchrone sous forme
de coroutines, qui se présentent presque
comme des fonctions - et donc qui
peuvent implémenter n'importe quelle
logique

. on peut exécuter une ou plus
généralement plusieurs coroutines en
parallèle par l'intermédiaire d'une
boucle d'événements

. on peut appeler une coroutine depuis
une autre coroutine grâce à await

. une coroutine peut bien entendu
appeler une fonction traditionnelle, en
faisant attention à ne pas bloquer trop
longtemps

À bientôt
