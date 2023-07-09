# Sway

Sway est un gestionnaire de fenêtre en pavage manuel (tiling en anglais) permettant maximiser l’utilisation de l’écran et l’utilisation du clavier. Il est également capable de mettre certaines fenêtres en « floating », c’est-a-dire avec le même comportement qu’un gestionnaire de fenêtre classique (GNOME, Openbox, etc).

**Note:** Certaines applications ne tournent pas uniquement avec Wayland, il faut potentiellement installer xwayland afin d’exécuter ces applications.

## Configuration
La configuration de sway se fait dans le fichier "config" suivant: *$HOME/.config/sway/config*. Pour mieux organiser notre configuration et permettre de retrouver plus facilement une partie particulière, la configuration est séparée comme ci dessous:
```
├── conf.d
│   ├── gaps.conf
│   ├── inputs.conf
│   ├── outputs.conf
│   ├── startup.conf
│   ├── system-operation.conf
│   └── theme.conf
└─── config
```

La configuration par défault se trouve dans */etc/sway/config*

### Input
Pour avoir un aperçu des inputs de votre ordinateur:
`swaymsg -t get_inputs`

Pour voir la totalié des options disponibles
`man 5 sway-input`



### Ouput


## Usage de Sway
### Controle de rétro-éclairage (Ordinateur portable)
### Controle sonore


sources
* (https://swaywm.org/)
* (https://debian-facile.org/atelier:chantier:sway)
* (https://medspx.fr/blog/Debian/configuration_clavier_avec_xkb/)
