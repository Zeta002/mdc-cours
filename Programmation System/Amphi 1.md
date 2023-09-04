
* Sockets (bas niveau)

* RPC (Remote Procedure Call)

* RMI (Remote Method Invocation)

* Services Web: plus d'interopérabilité

Modèle client-server:

* Server qui se lance en premier, en attente de requêtes
* Client initie la communication en envoyant une requête

Une même machine peux offrir plusieurs services à la fois, l'adresse IP ne sera plus suffisante alors on utilise des ports logique de 1 à 65535

Port du service web: 80
SMTP (envoi de mail): 25
daytime: 13
echo: 7

Ont peux donc définir nos propres services sur les ports disponibles

## RAPPEL

**Protocole TCP:** définir certains paramètres avant de dialoguer, très fiable, les données peuvent être découpé en paquets et arrivé dans un ordre arbitraire et d'emprunté des chemins différents

**Protocole UDP:** Envoie un message mais n'est pas fiable mais plus rapide, adapté à des applications en temps réel comme la visio conférence, utilisé dans des applications de dialogue simple comme des requêtes d'une adresse IP, peux multicast (envoyer un message vers plusieurs destinataire en une seul fois)

## Sockets (PAS TOUT COMPRIS A REVOIR)

Est un mécanismes de bas niveau le plus proche du system d'exploitation d'IPC (communication inter processus) permet l'échange de données entre applications sur une même machine ou sur des machines distantes connectées via un réseau.

Types de sockets:

* Sockets du domaine UNIX (en local seulement)
* Sockets du domaine internet (IPv4 et IPv6) : mode TCP, mode UDP, etc..

Example:

Server: 192.168.1.181 service web (80) + nouvelle socket active

	Socket passive 192.168.1.181 (20) cette socket attend les requêtes des clients

Client: Socket active 192.168.2.82 + port local 192.168.1.181

Côté server une socket passive, à chaque nouvelle connexion crée une socket active pour se client

C'est un mode de communication bidirectionnelle

On utilisera l'API en C car c'est celle de référence et qu'elle est la plus proche du system d'exploitation

**Voir feuille 1 pour le schéma**

```c
#include <sys/socket.h>

int socket (int domain, int type, int protocol);
```

AF_UNIX (ou AF_LOCAL): communication pathname (chemin de fichier) d'adresse = struct sockaddr_un
**AF_INET: communication IPv4** adresse socket = couple adresse IP + port (struct sockaddr_in)
AF_INET6: communication IPv6 adresse socket = coupe adresse IP + port (struct sockaddr_in6)

mode TCP: SOCK_STREAM
mode UDP: SOCK_DGRAM

### Service daytime

Le client demande la date et l'heure, le server lui envoi

```c
int main(

)

{

int sock_server = socket (AF_INET, SOCK_STREAM, 0);

// ...

return 0;

}
```

## Bind

```c
int bind (int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

retourne 0 en cas de succès, -1 en cas de d'échec

## Struct sockaddr_in

```c
{ sa_family_t } sin_family; /*vaut AF_INET*/

uint16_t sin_port; /*port dans l'ordre réseau*/

struct in_addr sin_addr; /*Adresse IP*/
```

RETENIR: C'est une adresse IP (struct qui contient un entier non signé sur 32bit) + un port

## inet_aton

```c
int inet_aton (const char *cp, struct in_addr *inp);
```

La fonction inet_aton() ...

## inet_ntoa

```c
int inet_ntoa ...
```

La fonction inet_aton()

Exemple:

```c
int sock_server = socket (AF_INET, SOCK_STREAM, 0);

struct sockaddr_in sockaddr_serveur;

sockaddr_serveur.sin_family = AF_INET;

sockaddr_serveur.sin_port = httons(50013);

inet_aton("192.168.1.1", &sockaddr_serveur.sin_addr);

// sockaddr_serveur.sin_addr = "192.168.1.1"

bind (sock_serveur, (struct sockaddr *) &sockaddr_serveur, sizeof(struct sockaddr_in) );
```

Convertir des entier non signés de l'ordre machine vers l'ordre réseau:

uint_t httons(uint16_t) ...

## Listen

```c
int listen (int sockfd, int backlog);
```

## Accept

```c
int accept (int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```

Exemple: serveur daytime TCP (suite)

```c
listen (sock_server, 128);

int sock_client;

sock_client = accept(sock_serveur, NULL, NULL);

return 0;
```

## Read

```c
size_t read (int sockfd, void *buf , size_t len);
```

Retourne le nombre d'octet récupérer, déconnexion 0, erreur -1, Si aucun message n'est disponible dans le buffer, read se bloque par défaut

## Write

```c
int write (int sockfd, const void *buf, size_t len);
```

```c
char *msg; time_t date ; date = time(NULL); msg = ctime(&date);

// msg contient la date et l'heure

write (sock_client, msg, strlen(msg) );
```

On utilise les socket active pour le write et read, la socket d'écoute est utilisé notamment dans le accept pour l'écoute

On verras en TP la création du client

## Connect 

```c
int connect (int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

const struct sockaddr \*addr = adresse du server