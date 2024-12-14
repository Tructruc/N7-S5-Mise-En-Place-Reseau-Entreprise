# archtecture reseau de l'entreprise
![Alt text](hamza/archi_reseau.jpg)

# choix de virtualisation
pour la solution de virtualsiation j'i choisi virtual box car il est facile a utiliser en plus c'est pas le premiere fois je je l'utilise et pour les CMs j'i choiis des vm kali dbian car c'est leger en plus y a wireshark qui est deja installé et des autres outils necessaire pour faire ce projet
# configuration global
on a defini 2 zone pricipale dans notre reseau privé : le reseau local qui est 192.168.0.0/24 et le reseau de dmz qui est 192.168.1.0/24 et pour l'exterieur c'est l'addresse 10.10.10.0/24 .
appres le dfinitions des reseaux on a affectuer a chaque equipemet leurs addresse ip et leurs default gateway si besoin et en a verifier la conne ction avec les diffirent equipeemnts
# config DHCP
pour faciliter l'attribustion d'adreese ip on a opter a faire une solution de DHCP on a fait une pool de 10 addresse de chque reseau privé en plus on a fixer les addresse ip de nos serveurs a 192.168.1.1 pour le web 192.168.1.2 pour qu'il soit touours accessibles et le clien sais cette addresse .en plus de ca on a defini le serveur dns aussi pour que les client dhcp save a quelle serveru aller pour faire la translation
possibilité bech nzidou tsawer lenna 999999999999999999999999999999
# config serveur
## serveur web
j'ai choisi le serveur apache2 et j'ai fait ue petite page d'acceullle pour que les clients save que c'est mon serveur 
9999999999999 zid taswira lenna 999999999999999999999
## serveur DNS
tout d'abord j'ai essayer avec bind9 mais c'eaait fdefficile a confogurer et j'ai rencontrer beaucoups des problemes doonc j'ai opter a une solutions plus simple qui est dsnmasq .
et pour notre solutio de hierarchie on jste fait des forwarders si notre sotre serveur ne trouve pas la correspandance entre l addresse ip et le nom de domaine il 'envoie a uen autre reseau.
# acces au serveur de lexerieur
tout d'abord il faut assurer que toute les equipemet puissent communiquer a l'exterieur et pour faire ca il faut taper la commande : iptabel e....... pour assurer la traslation de addresse privé a des addresse publiques
apres il faut asssurer que nos serveur puisse etre acceder depuis l'exterireur dont on a fait un dnat et on a faot uen redirection selon le port si le traffic etst de l'exteriru et la port de distination 80 on le derige vers notre serveur web et meme chodse pour le serveuur dns avec le port 53
mais c'est pas pratiqe ca donc on a opter a une autre solution plus pratiqe et plus securisé qui est le reverse prxy 
# politique de firewall





