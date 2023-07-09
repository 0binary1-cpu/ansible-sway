# Configuration du clavier avec xkb

## Pourquoi étudier xkb en 2023?
XKB ca date de 1996 pour le document de spec qui fait autorité. XKB est issue de x-windows alors pourquoi s'embeter avec ça quand son bureau est sous wayland natif (via sway)?

Alors comme XKB reste un standard de description de clavier et de touches, il reste pas mal utilisé par de nombreux composants actuels. Et puis, de nombreux composants Wayland utilisent la bibliothèque *xkbcommon* qui s'appuie sur la spécification XKB (pas dans son intégralité).

En conclusion Sway utilise complètement XKB dans sa configuration du clavier. Donc, si on veut déclarer correctement son clavier, il reste toujours important de maitriser un minimum XKB. C'est aussi pour ça qu'on a des directives xkb dans sway.

## xkb quesako?
On trouve la base de donnée dans */usr/share/X11/xkb/*. Sous Debian, c'est porté par le paquet *xkb-data*, lui meme un sous ensemble du paquet source *xkeyboard-config* qui vient tout droit de chez *freedesktop*.
L'organisation se passe ainsi:

* **geometry**: Liste de fichiers qui décrivent l'aspect physique du clavier. Ca n'a pas de conséquences sur l'affectation des touches. C'est d'ailleurs pour cela que ce n'est pas géré par xkbcommon par exemple car la spécification de géométrie date de 1996 et à l'époque, pas de SVG ou consors.
* **rules**: Les fichiers

