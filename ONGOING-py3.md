## compréhensions

`w2-s7-c2-comprehension-de-liste` et `w4-s4-c1-comprehensions-imbriquees` ont un overlap non vide, les compréhensions imbriquées.

## `w3-s8-c4-iterateurs-et-performances`

* La partie sur les itérateurs est OK

* Il faut sans doute isoler la partie sur %%timeit ailleurs
 http://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time

* `w3-s8-c4bis-iterateurs`
  * pas très sûr si ça a encore du sens de toutes façons..

## `w3-s8-c5-iterable-vs-iterateur`

* J'ai écrit au départ ce notebook parce qu'Arnaud avait beaucoup mélangé itérable et itérateur dans la vidéo
* on n'aura pas forcément besoin de le préserver


## `w4-s6-c2-exception-unboundlocalerror`

* faut-il introduire `nonlocal` ?

## `w4-s8-c1-passage-arguments`

* très vague
  * ne fait pas la différence entre paramètre et argument,
  * ne détaille pas comment les associations sont faites
* p.r. à python3, manque
  * le fait que l'on peut mettre un * avant des paramètres nommés
* envisager de le couper en deux ?

## `w5-s3-c1-import-as`

* il semble qu'il y a ici des redites par rapport à des choses vues en semaine 3 (`import as`)

## `w5-s6-c2-surcharge-operateurs-2`

* 2ème tiers: on introduit `__getattr__` en suggérant que c'est la seule façon de customiser `obj.attribute`; selon ce qu'on décide de faire par rapport aux `__get__`, `__getattribute__`, et autres, il faudra rectifier ça...

## `w5-s8-c1-espaces-de-nommage`

Le complément avancé dans ce notebook ne me paraît pas forcément très utile, et de plus je ne sais pas dire s'il est toujours réellement courant; cette histoire de `global` et `nonlocal` ne m'inspire pas vraiment de toutes les façons..

## divers

* renommer `w5-s6-e2-classes` en `w5-s6-e2-classes-shipdict` ?

* ajouter exercice pairwise ? -- voir nbautoeval et ExerciseClass


# mini-projets

## dropped for the 2017 session

## `diskusage`

* pourrait constructivement utiliser `pathlib` plutôt que ces vieux trucs

## `crawler`

* passé en python3 mais nécessite sans doute une passe par Arnaud
* notamment les outputs obtenus sur sa page ne sont pas à jour


# commentaires généraux

## CLASSES


## Type hints
* un impact sans doute sur le ou les compléments `w5-s5-c3-heritage-multiple` et/ou `w5-s5-c4-heritage-typage`

## Introspection
* parler du module `inspect` et de `inspect.signature` ?

