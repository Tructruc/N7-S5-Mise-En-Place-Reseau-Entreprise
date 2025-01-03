## Choix techniques

![Carte du Réseau d'entreprise](emilien/carteReseau.png)

### Solution de virtualisation

J'ai choisi de réaliser ce projet avec [Docker](https://www.docker.com/) comme solution de virtualisation en raison de ses performances et parce que j'étais déjà familier avec cet outil.

### Configurations communes aux différents conteneurs

Je suis partit d'une image de base Alpine pour tout les conteneurs. J'ai fait ce choit pour la légrèreté d'Alpine Linux (voir [Poid de la simulation](#poid-de-la-simulation). J'y ai installé `iproute2` pour pouvoir parametrer la passerelle par default des conteneurs. J'ai utilisé le client DHCP [udhcpc](https://wiki.alpinelinux.org/wiki/Udhcpc) sur tout les conteneurs sauf le routeur pour permettre au conteneur d'obtenir son paramétrage réseau avec le DHCP. J'ai eu besoin de modifier le script qu'utilise udhcpc pour appliquer les paramètres DNS récupéré avec le DHCP.Le script de base utilise `mv` sur `/etc/resolve.conf` sauf que ce fichier est déja en cours d'utilisation donc on ne peut pas le modifier avec mv. J'ai donc modifié le script pour qu'il écrive dans `/etc/reslove.conf` avec des `>` et des `>>`.

Sur chaque conteneur, au démmarrage, je supprime la passerelle par default qui est crée par default car elle pointe vers le routeur interne de docker. Ensuite j'ajoute une nouvelle route par default vers mon routeur. Sur les conteneurs qui utilisent le DHCP, je supprime l'adresse qui est ajouté par Docker et je lance le client DHCP.

### Conteneur utilisateur

J'ai installé [w3m](https://github.com/acg/w3m), un navigateur open source conçu pour le terminal. Étant donné que j'utilise Docker, je ne peux pas utiliser d'applications graphiques comme Google Chrome ou Firefox. 

Pour empêcher le conteneur de s'arrêter immédiatement après son lancement, sa commande d'exécution consiste en une boucle infinie de `sleep`.

- **Adresse IP** : 192.168.200.2

### Conteneur DNS

J'ai utilisé [dnsmasq](https://dnsmasq.org/doc.html), un serveur dns open source simple à configurer et performant.

#### Parametrage du serveur DNS

Serveurs de DNS des deux autres réseaux :
- 10.10.10.1
- 10.10.10.2

Serveurs de DNS externes :
- 8.8.8.8
- 1.1.1.1

J'ai ajouté l'adresse de mon serveur web : `web.emilien.fr` qui pointe vers l'adresse publique de mon réseau `10.10.10.3`. J'ai aussi ajouté une entrée qui dit que le serveur web n'a pas d'adresse IPV6.

- **Adresse IP** : 192.168.100.3

### Conteneurs serveur web

J'ai crée deux conteneurs serveurs webs avec la meme configuration pour pouvoir faire du load balancing (voir [Conteneur reverse proxy](#conteneur-reverse-proxy)) Pour les différencier, j'ai écris le numero du serveur sur sa page.

J'ai choisi d'utiliser [Caddy](https://caddyserver.com/), un serveur http open source écrit en Go qui peut servir des fichiers statiques. Ce choix s'explique par sa légèreté et ses performances, ainsi que par sa simplicité de configuration par rapport à un serveur web plus complexe comme Apache ou NGINX.
>It's not uncommon for Caddyfiles to be just ~15-25% the size of a less-capable nginx config. (https://caddyserver.com/)

Un serveur web plus puissant comme Apache aurait été inutile ici, car nous ne devons que servir des fichiers statiques.

- **Adresse IP serveur web 1** : 192.168.100.2
- **Adresse IP serveur web 2** : 192.168.100.5

### Conteneur routeur

J'ai utilsé les `iptables` pour configurer le pare-feu de mon routeur. 

J'ai utilisé (kea)[https://www.isc.org/kea/] comme serveur DHCP pour sa légèreté et sa simplicité de configuration avec un fichier JSON. J'ai mis une pool de dhcp pour le réseau privé et une autre pour la dmz. J'ai mis des lease statiques pour tout les serveurs de la dmz.

Le routeur est configuré pour que la DMZ et le réseau privé puissent communiquer ensemble et pour qu'ils puissent accéder à internet, notamment pour pouvoir installer des paquets pendant que le conteneur tourne. J'ai ouvert les ports 80 et 443 qui sont renvoyé vers mon revers proxy. J'ai également ouvert le port 53 en tcp et en udp qui lui est renvoyé vers le serveur dns.

- **Adresses IP** :
  - 192.168.100.250 (Réseau privé)
  - 192.168.200.250 (Réseau DMZ)
  - 10.10.10.3 (Réseau public)
  - 192.168.50.250 (Réseau pour la connexion a Internet)

### Conteneur reverse proxy

J'ai choisi d'utiliser [Caddy](https://caddyserver.com/) en tant que reverse proxy pour les memes raisons que pour le [conteneur serveur web](#conteneurs-serveur-web). J'ai donc fait pointer l'adresse de mon site (`web.emilien`) vers mes deux serveurs web avec du load balancing (répartition de charge), j'ai utilisé la stratégie `round-robin` de Caddy. Elle alterne entre les serveurs webs de manière séquentielle, par exemple la première requette ira au serveur 1 puis la deuxième au serveur 2 et la troisième ira au serveur 1. Cette stratégie permet ici de montrer facilement que le load ballancing fonctionne correctement

- **Adresse IP** : 192.168.100.2

### Edgeshark

J'ai ajouté le docker compose de [Edgeshark](https://edgeshark.siemens.io/) qui est un conteneur open source crée par Siemens qui permet avec le wireshark de d'hote de capturer les packets des réseaux dockers. J'ai utilisé se docker compose comme un override qui me permet de garder le garder séparré de mon docker compose principal. Edge shark permet de créer une interface virtuelle pour chaque connextion a un réseau docker et pour chaque réseau docker. Il rend accessible sur le port 5001 du host un site web qui permet de visualiser a quel réseau correspond chaque interface et également de visualsier le réseau docker.

## Poid de la simulation

Les images de la simulation pèsent en tout environ 500Mo de stockage. Une fois les conteneurs lancés, la simulation ne consomme que 75Mo de RAM. Alpine linux m'a permis d'optimiser ces métriques. Lorsque j'utilisais des images Ubuntu, les images faisait pluseurs gigaoctets et plus de 300Mo de RAM.

## Problèmes rencontrés

### Problème de communication avec le réseau physique

Lorsque j'ai activé le mode macvlan sur le réseau public de mon routeur, Docker me donnait une erreur qui disait que l'interface parente devait avoir le format `eth0.10` sauf que l'interface ethernet de mon ordinateur s'appelle `enp0s3` j'ai donc essayé de la renommer en `eth0` mais cela n'a pas fonctionné. Après des recherches, j'ai découvert qu'en executant les commandes docker avec `sudo` cela fonctionnait alors que mon utilisateur était dans le groupe docker. J'ai donc continué à utiliser `sudo` pour lancer les commandes docker. Après avoir fait de plus amples recherches, j'ai découvert que le problème venait de Docker Desktop. En effet Docker Desktop execute docker dans une machine virtuelle qui elle ne supporte pas le macvlan. Cela marchait avec `sudo` car Docker Desktop ne s'execute que en mode utilisateur. Lorsque l'on execute docker avec `sudo`, il s'execute sur Docker Engine qui lui est installé sur la machine hôte et qui lui supporte le macvlan. Pour résoudre le problème sans devoir utiliser `sudo`, je dois changer de contexte Docker pour passer de `docker-desktop` à `default` avec la commande `docker context use docker-desktop`.


### Passage d'images Ubuntu à Alpine

J'ai commencé le projet avec des conteneur basé sur des images Ubuntu car cela était plus simple mais dans l'optique d'avoir une simulation la plus légère possible, j'ai décidé de passer sur des images Alpine qui sont beaucoup plus légères.

Plusieurs problèmes se sont alors présenté notament le fait que certains paquets que j'utilisais sur ubuntu n'existaient pas sur Alpine. Par exemple `dhclient` qui est le client DHCP que j'utilisais sur les conteneurs utilisateurs n'est pas disponible dans les dernières versions d'Alpine. J'ai donc utilisé `udhcpc` qui est installé par défault sur Alpine. J'ai rencontré un problème avec `udhcpc`, une fois qu'il à récupéré les informations du DHCP, il essaye de les écrire dans `/etc/resolve.conf` avec un `mv` sauf que ce fichier est en cours d'utilisation donc on ne peut pas le modifier avec `mv`. J'ai donc modifié le script pour qu'il écrive dans `/etc/reslove.conf` avec des `>` et des `>>`.


De même, le serveur DHCP `dhcpd` n'est pas disponible sur Alpine. Je l'ai donc remplacé par `kea` qui est un autre serveur DHCP open source qui est aussi dévellopé par ISC.

Au contraire `caddy` qui est le serveur web que j'utilise est disponible sur Alpine en tant que package ce qui m'évite de l'installer manuellement.