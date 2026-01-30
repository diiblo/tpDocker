# Exercice 1

- Création du volume :

```bash
docker volume create mydata
mydata
```

- Lancer un conteneur qui écrit dans le volume

```bash
docker run --name writer \
> -v mydata:/data \
> busybox \
> sh -c "echo 'Hello mon King'>/data/hello.txt"
```

- Suppression du conteneur

```bash
docker rm writer
```

- Lancer un autre conteneur et vérifier le fichier

```bash
docker run --rm \
> -v mydata:/data \
> busybox \
> cat /data/hello.txt
Hello mon King
```

**Sauvegarde et restauration du volume**

- Sauvegarde

```bash
docker run --rm \
> -v mydata:/data \
> -v $(pwd):/backup \
> busybox \
> tar czf /backup/mydata_backup.tar.gz -C /data .
```
- Suppression du volume

```bash
docker volume rm mydata
```

- Créer un volume

```bash
docker volume create mydata
```
- Restauration

```bash
docker run --rm \
>-v mydata:/data \
>-v $(pwd):/backup \
>busybox \
>tar xzf /backup/mydata_backup.tar.gz -C /data
```
# Exercice 2

- Création du réseau

```bash
docker network create mynet
```


## Démarrer 2 conteneurs sur `mynet`

- Conteneur 1 — serveur

```bash
docker run -d \
--name web \
--network mynet \
nginx
```


- Conteneur 2 — client

```bash
docker run --rm \
--name client \
--network mynet \
busybox \
wget -qO- http://web
```

---

## Exposition de port : host ↔ conteneur

```bash
docker run -d \
--name web-exposed \
--network mynet \
-p 8080:80 \
nginx
```


## Accès depuis la machine hôte

```bash
curl http://localhost:8080
```

# Quiz Rapide

1. Volume vs bind mount (prod) ?
- Volume : géré par Docker, portable, plus sûr en production.
- Bind mount : couplé à l’hôte, réservé au développement.

2. `docker volume prune` ?
- Supprime tous les volumes non utilisés par des conteneurs.
- Risque de perte de données.

3. Pourquoi `host network` est risqué ?
- Supprime l’isolation réseau, exposition directe des ports de l’hôte.

4. `internal: true` en Compose ?
- Crée un réseau accessible uniquement aux conteneurs internes.

5. Overlay : à partir de quel mode ?
- Uniquement en multi-host (Docker Swarm, Kubernetes).

6. Outil n°1 pour diagnostiquer ?
- `docker inspect` (configuration réelle des conteneurs, réseaux, volumes).
