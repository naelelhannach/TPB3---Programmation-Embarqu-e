# TP1 Programmation Embarquée

## Flasher la carte SD

**Pourquoi est-il nécessaire de définir ces informations avant le premier démarrage d'une Raspberry Pi utilisée sans écran ?**

Hostname, necb-pi. Nom d'utilisateur, necb.

Sans écran ni clavier, la Raspberry Pi n'autorise aucune configuration interactive au démarrage. Le seul accès possible passe par le réseau, ce qui impose de définir certains paramètres dès le flashage. Le hostname permet de la joindre par son nom via mDNS, le nom d'utilisateur et le mot de passe fournissent le compte de connexion, et l'activation de SSH ouvre le canal distant. Ces trois éléments doivent exister avant le premier démarrage, sinon aucun accès ne sera possible.

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

**Question 6**

<img width="616" height="821" alt="IMG_1186" src="https://github.com/user-attachments/assets/c98c95c5-501f-49f8-bcb8-1ff2acefd494" />

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

La clé privée est restée sur le PC, dans /home/student/.ssh/id_ed25519. La clé publique a été copiée sur la Raspberry, dans /home/necb/.ssh/authorized_keys, avec ssh-copy-id.

**Question 2**

La clé privée prouve l'identité de l'utilisateur. Si elle se retrouvait dans authorized_keys sur la Raspberry, n'importe qui ayant accès à ce fichier pourrait s'en servir pour se faire passer pour l'utilisateur. Elle doit rester uniquement sur le PC.

**Question 3**

authorized_keys liste les clés publiques autorisées à se connecter sur le compte necb. Quand une connexion arrive, la Raspberry vérifie que la clé publique présentée correspond à une entrée de ce fichier.

**Question 4**

Le mot de passe protège l'accès au compte necb sur la Raspberry elle même. La phrase secrète protège uniquement la clé privée stockée sur le PC, si le fichier venait à être volé. Ici aucune phrase secrète n'a été mise, la clé n'a donc que la protection du PC lui même.

**Question 5**

Une nouvelle connexion a été testée avec ssh necb@10.10.3.247 avant toute désactivation du mot de passe, pour confirmer que la clé fonctionnait bien. Si elle n'avait pas marché et que le mot de passe avait déjà été coupé, plus aucun accès n'aurait été possible.

**Question 6**

Le dossier .ssh doit avoir les droits 700, accessible uniquement par le propriétaire, et le fichier authorized_keys les droits 600, lecture et écriture réservées au propriétaire. C'est ce que ssh-copy-id a mis en place automatiquement.
