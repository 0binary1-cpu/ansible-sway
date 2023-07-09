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
