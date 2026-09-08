# TP1 Programmation Embarquée

## Flasher la carte SD

**Pourquoi est-il nécessaire de définir ces informations avant le premier démarrage d'une Raspberry Pi utilisée sans écran ?**

Sans écran ni clavier, la Raspberry Pi n'autorise aucune configuration interactive au démarrage. Le seul accès possible passe par le réseau, ce qui impose de définir certains paramètres dès le flashage.

Le hostname permet de la joindre par son nom via mDNS, le nom d'utilisateur et le mot de passe fournissent le compte de connexion, et l'activation de SSH ouvre le canal distant. Ces trois éléments doivent exister avant le premier démarrage, sinon aucun accès ne sera possible.

## Première connexion par UART

**Question 1**

TX veut dire transmission, c'est ce qui envoie. RX veut dire réception, c'est ce qui reçoit.

Pour communiquer, ce qu'un appareil envoie doit arriver sur l'entrée de réception de l'autre. On croise donc les fils. Le TX de la Raspberry va vers le RX de l'adaptateur, et le TX de l'adaptateur va vers le RX de la Raspberry.

**Question 2**

GND veut dire masse. C'est la référence commune entre les deux appareils.

Sans elle, chaque appareil mesure ses signaux à sa façon, et un signal envoyé par l'un peut être mal lu par l'autre. En reliant les GND, les deux appareils partagent le même niveau de référence, ce qui rend la communication fiable.

**Question 3**

115200 est la vitesse de transmission, exprimée en bauds. C'est le nombre de bits envoyés chaque seconde. Les deux appareils doivent utiliser exactement la même vitesse pour se comprendre.

8N1 décrit le format des données. Le 8 veut dire que chaque caractère est codé sur 8 bits. Le N veut dire qu'il n'y a pas de bit de parité, donc pas de vérification d'erreur intégrée. Le 1 veut dire qu'un seul bit de stop marque la fin de chaque caractère envoyé.

**Question 4**

La broche 5V alimente l'adaptateur lui même, elle ne sert pas à transmettre des données.

Les broches GPIO de la Raspberry Pi fonctionnent en logique 3,3V. Elles ne sont pas conçues pour recevoir une tension plus élevée. Si on branche la broche 5V de l'adaptateur sur un GPIO, ce dernier reçoit une tension trop forte, ce qui peut l'endommager de façon irréversible, voire détruire toute la carte.

**Question 5**

Si les vitesses ne correspondent pas, la Raspberry et le terminal ne découpent pas le signal électrique au même rythme. Chaque bit envoyé est donc mal interprété par l'autre côté.

**Comparez la connexion UART et la connexion SSH**

Pour l'UART, il faut un adaptateur USB-UART branché avec des fils, et avoir activé enable_uart=1 dans config.txt avant le démarrage. Pas besoin de réseau.

Pour le SSH, il faut que le Wi-Fi soit configuré sur la Raspberry, que SSH soit activé, et connaître son hostname ou son adresse IP.

L'avantage de l'UART, c'est qu'il marche même sans réseau. Sa limite, c'est qu'il faut un câble et être physiquement à côté de la carte.

L'avantage du SSH, c'est qu'on s'y connecte à distance, sans câble. Sa limite, c'est qu'il dépend entièrement du réseau, donc rien ne marche si le Wi-Fi ne fonctionne pas.

## Configuration du Wi-Fi

**Adresse IP obtenue**

10.10.3.246

**Pourquoi la commande sudo fonctionne ici**

Le compte utilisateur créé dans Raspberry Pi Imager a été configuré avec les droits administrateur par défaut. C'est pour ça que sudo fonctionne sur la Raspberry, contrairement au compte student des PC de la salle, où les droits ont été retirés
## Sécuriser SSH avec une clé

**Question 1**

La clé privée reste sur le PC, dans le dossier ~/.ssh, sous le nom id_ed25519. La clé publique est copiée sur la Raspberry Pi, dans le fichier ~/.ssh/authorized_keys.

**Question 2**

La clé privée prouve l'identité de l'utilisateur. Si elle se retrouve dans authorized_keys, n'importe qui ayant accès à ce fichier pourrait l'utiliser pour se faire passer pour l'utilisateur. Elle doit donc rester uniquement sur le PC, et jamais copiée ailleurs.

**Question 3**

authorized_keys est la liste des clés publiques autorisées à se connecter sur ce compte. Quand un utilisateur se connecte, la Raspberry vérifie que sa clé publique s'y trouve avant d'accepter la connexion.

**Question 4**

Le mot de passe du compte Raspberry Pi protège l'accès au système lui même. La phrase secrète protège uniquement la clé privée stockée sur le PC, elle empêche quelqu'un qui volerait le fichier de la clé de l'utiliser directement.

**Question 5**

Si l'authentification par mot de passe est désactivée avant d'avoir vérifié que la connexion par clé fonctionne, et que la clé ne marche pas pour une raison quelconque, tout accès à la Raspberry est perdu, sans aucun moyen de se reconnecter.

**Question 6**

Le dossier .ssh doit avoir les droits 700, c'est à dire accessible uniquement par le propriétaire. Le fichier authorized_keys doit avoir les droits 600, lisible et modifiable uniquement par le propriétaire. Ça empêche d'autres utilisateurs de la même machine de lire ou modifier ces fichiers importants.
