# Configuration du clavier avec xkb

xkb ca date de 1996 pour le document de spec qui fait autorité. xkb est issue de x-windows alors pourquoi s'embeter avec ça quand son bureau est sous wayland natif (via sway)?

Alors comme xkb reste un standard de description de clavier et de touches, il reste pas mal utilisé par de nombreux composants actuels. Et puis, de nombreux composants Wayland utilisent la bibliothèque *xkbcommon* qui s'appuie sur la spécification xkb (pas dans son intégralité).

En conclusion Sway utilise complètement xkb dans sa configuration du clavier. Donc, si on veut déclarer correctement son clavier, il reste toujours important de maitriser un minimum xkb. C'est aussi pour ça qu'on a des directives xkb dans sway.

## xkb quésaco?
On trouve la base de donnée dans */usr/share/X11/xkb/*. Sous Debian, c'est porté par le paquet *xkb-data*, lui meme un sous ensemble du paquet source *xkeyboard-config* qui vient tout droit de chez *freedesktop*.
L'organisation se passe ainsi:

* **geometry**: liste de fichiers qui décrivent l'aspect physique du clavier. C'est un truc qui sert juste à visualiser à quoi ressemble le clavier. Ca n'a pas de conséquences sur l'affectation des touches. C'est d'ailleurs pour ça que ce n'est pas géré par xkbcommon par exemple car la spec de géometrie date de 1996 et à l'époque, pas de SVG ou de trucs comme ça.
* **rules**: les fichiers qui définissent les modèles de clavier.
* **symbols**: les fichiers qui font le lien entre des keycodes et des symboles. C'est ici qu'on trouve les agencements et les variantes.
* **keycodes**: Les fichiers qui font le lien entre les keycodes et les valeurs brutes des touches. Les valeurs brutes sont celles que votre clavier sort au niveau hardware quand vous appuyez sur une touche. Ça permet de dire que la valeur 49 correspond au keycode TLDE (le tilde si c'est bien défini au niveau des symboles).

Au delà des fichiers, il existe aussi des concepts qui se retrouvent éparpillés dans les fichiers de définition:

* **XkbRules**: Règles pour gérer l'arrangement de votre clavier.
* **XkbModel**: Détermine le modèle de claver.
* **XkbLayout**: Détermine l'arrangement du clavier. Un même modèle peut avoir différents arrangements. Par exemple, on peu avoir un clavier Gigabyte au format azerty français ou au format qwerty. Dans ce cas, on peut même, en bougeant physiquement les touches sur le clavier le mettre dans un format Dvorak ou Bepo.
* **XkbVariant**: Certains arrangements ont des variantes, c'est ici qu'on le spécifie.
* **XkbOptions**: Des options spécifiques à XKB comme dire qu'on affecte plusieurs layouts à un même clavier, ce qui permet d'en changer avec une combinaison de touches par exemple.

Au niveau des touches, on a les concepts suivants:

* Touche de composition (alt/shift/Ctrl/etc.)
* **Groupe**: C'est ce qui est à droite ou à gauche de la touche.
* **Niveau/level**: c'est ce qui est en haut ou en bas d'une touche.
* **Key code**: c'est un code/chiffre que le clavier hardware renvoi lors de l'appui sur une touche.
* **Symbolic key name**: c'est ce qui transforme un key code en un nom symbolique.
* **Key symbol**: c'est ce qui est affiché sur votre écran lors de l'appui sur une (ou plusieurs) touche(s). C'est dérivé sur nom symbolique.

Le tout est éclaté dans plusieurs fichiers inter-dépendants les uns les autres. Essayons de comprendre comment ça marche avec un exemple.

## L'exemple du pc105 en fr

Dans ma config sway, j'ai la configuration suivante:

```
input * {
   xkb_model pc105
   xkb_layout fr
   xkb_variant oss
   xkb_numlock enabled
}
```

On en déduit que le modèle est pc105 et le layout/agencement fr avec la variante oss. Essayons de voir ce que ça donne dans les définitions xkb.

On trouve trace de pc105 dans les fichiers rules/base et rules/evdev où on a bien pc105 dans la liste des modèles. Mais si on creuse plus loin, on voit que pc105 est défini au niveau des symbols dans symbols/pc:

```
    xkb_symbols "pc105" {

    key <ESC>  {	[ Escape		]	};

    // The extra key on many European keyboards:
    key <LSGT> {	[ less, greater, bar, brokenbar ] };
    ...
```

Donc le modèle suit également des symboles. Et de fait, on comprend que dans xkb un modèle correspond forcément à un ensemble de symboles de base. Si on lit tout le fichier symbols/pc, on voit qu'il n'y a que des touches génériques de décrites, pas les éléments relatifs aux touches qui peuvent changer suivant l'arrangement, le layout du clavier. Celà permet d'avoir une définition des symboles commun à tous les claviers du modèle pc105. Dans mon cas, ça ajoute même la prise en charge d'un keypad alors que je n'en n'ai pas un, même si on le verra, il est pris en charge mais au niveau hardware.

Ensuite, si on fouille dans le fichier symbols/fr, on constate que c'est aussi une liste de xkb_symbols mais qui cette fois s'attaque aux touches françaises. Ça commence par une liste nommée "basic" puis une autre nommée "oss". Et là, on fait le rapport avec notre xkb_layout nommé fr et notre xkb_variant nommée oss. En fait, en en utilisant cette dénomination, on indique qu'il faut charger le fichier de symboles fr avec la liste interne oss. Si on analyse ce layout, on voit qu'il permet de faire des choses sympas comme les majuscules des charactères accentués ou cédille.

Et c'est tout pour cette analyse. Pour résumer, mettre un modèle à pc105 indique qu'on charge les symboles 105 qui se chargent des trucs communs + la définition des touches mortes. Puis le layout fr(oss) permet de charger la liste de symboles oss du fichier de symboles nommé fr.

De cette manière, on voit bien que c'est finalement très rare d'avoir à redéfinir des touches qui n'existent pas. Même les trucs "exotiques" comme les touches "multimédia" sont déjà référencées dans xkb. Voilà pourquoi il n'y a pas plétore de définitions de claviers et de modèles, même exotiques car la plupart des claviers sortent des keycodes normalisés.

Voilà pourquoi la config sway de base sus-citées convient à 99% des claviers en français. En la déployant sur toutes mes machines, j'arrive à faire fonctionner toutes les touches de tous les claviers français, y compris les touches multimédia. Ce qui me manquait sur certaines configuration, c'était que je n'utilisais pas la variante oss mais la latin9 qui ne gère pas les majuscules accentuées. Voilà le fin mot de l'histoire.

Selon moi, le seul cas utile de créer une configuration xkb spécifique, c'est lorsque vous décidez de bouger les touches physiques de votre clavier pour faire une nouvelle affectation des keycodes. Par exemple si vous avez un clavier azerty et que vous voulez le transformer physiquement en dvorak en déplaçant les touches (en les retirant du clavier pour les placer ailleurs). Mais ce n'est pas mon utilisation.
Comment trouver les keycodes du clavier

Pour vous convaincre de ce que j'ai expliqué plus haut, ça peut être intéressant de savoir comment on fait pour récupérer les keycodes qui sortent du hardware du clavier.

Pour ça, un bon vieux xev des familles fait le job plutôt bien. Je vous laisse taper sur votre clavier avec, ça donne la sortie suivante lors de l'appui sur une touche:

KeyRelease event, serial 34, synthetic NO, window 0x800001,
root 0x522, subw 0x0, time 3413735, (443,329), root:(1128,350),
state 0x4, keycode 37 (keysym 0xffe3, Control_L), same_screen YES,
XLookupString gives 0 bytes:
XFilterEvent returns: False

La valeur qui vous intéresse, c'est keycode 37. C'est la valeur de la touche 37 qui est convertie par xkb en Control_Left.

En utilisant xev sur mon clavier de portable, j'ai pu faire un certain nombre de découvertes. Par exemple, la touche Fn n'est pas une vraie touche dans le sens où quand on appuie dessus, xev ne capture rien. C'est donc que c'est géré uniquement du côté hardware. Ou encore, je me suis rendu compte que quand je frappe Fn-s, ça renvoie le keycode 107 qui correspond à PrintScreen, alors que rien n'est affiché sur la touche s à ce sujet. Encore un truc généré par le hardware. De plus, Fn-o génère un keycode de keypad (85).

Vous pouvez aussi utiliser wev, packagé dans Debian pour avoir l'équivalent de xev sous Wayland: `wev -f wl_keyboard`
