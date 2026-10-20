# bus-boulot

Webapp perso des prochains bus Metlink (Wellington) pour deux arrêts.

**En ligne :** https://nikowha.github.io/bus-boulot/

| Onglet | Arrêt | Nom Metlink |
|---|---|---|
| **MAISON** (à gauche, ouvert par défaut) | 7096 | Ahuriri Street at Tukanae Street |
| **WETA** | 6080 | Miramar Avenue at Tauhinu Road |

## Fonctions

- **Onglets par arrêt** : les 4 prochains bus de l'arrêt choisi, avec le numéro de ligne dans sa couleur officielle Metlink.
- **Filtre par ligne** dans chaque onglet (« Toutes », « 2 », « 4 »…), mémorisé par arrêt.
- **Horaires** : `heure prévue · retard/avance · nouvelle heure` (ex. `14:30 · +3 min · 14:33`) et le temps restant en grand.
  Retard en orange, avance en bleu ; « prévu, pas de temps réel » ou « annulé » sinon.
- **Carte en temps réel** (toucher un bus) : position GPS du bus, arrêt, tracé de la ligne.
  Le bus suit le tracé, avance à sa vitesse estimée entre deux positions GPS (compense le retard du GPS),
  avec une flèche dans son sens de marche. Fond de carte Esri (sans clé).
- **Rafraîchissement toutes les 5 s** (départs et GPS), mis en pause quand la page n'est pas visible.
- **App iPhone** : icône de bus, nom « Bus », plein écran (manifest + balises Apple).

## Clé API Metlink

La clé n'est jamais en clair dans ce dépôt.

- **`key.enc.json`** contient la clé **chiffrée par mot de passe** (AES-GCM 256, clé dérivée par PBKDF2-SHA256,
  600 000 itérations). Sur un nouvel appareil : ouvrir « Clé API Metlink » → mot de passe → **Déverrouiller**.
  La clé déchiffrée est ensuite gardée dans le navigateur de l'appareil (le mot de passe, lui, n'est pas gardé).
- Pour **changer de clé ou de mot de passe** : « Clé API Metlink » → « Chiffrer ma clé pour GitHub » (tout se fait
  dans le navigateur), puis remplacer le contenu de `key.enc.json` par le texte généré.
  Le chiffrement ne marche qu'en https (GitHub Pages), pas en ouvrant le fichier en local.
- On peut aussi coller la clé directement, et « Oublier la clé sur cet appareil » l'efface.
- Clé gratuite sur [opendata.metlink.org.nz](https://opendata.metlink.org.nz/).

## Installer sur iPhone

1. Ouvrir https://nikowha.github.io/bus-boulot/ dans **Safari**.
2. Déverrouiller la clé avec le mot de passe.
3. **Partager** → **« Sur l'écran d'accueil »**.
4. Ouvrir l'app installée et déverrouiller une fois de plus (son stockage est séparé de Safari).

## Développement

- Tout est dans `index.html` (HTML, CSS et JS, sans build). Leaflet 1.9.4 est chargé depuis cdnjs.
- Arrêts : constante `STOPS` (ordre = ordre des onglets, le premier est ouvert par défaut).
- Réglages : `MAX_DEPARTURES` (4), `REFRESH_MS` et `VEHICLE_REFRESH_MS` (5 000 ms).
- API Metlink utilisée (en-tête `x-api-key`) :
  - `/v1/stop-predictions?stop_id=…` : départs d'un arrêt
  - `/v1/gtfs-rt/vehiclepositions` : positions GPS des bus
  - `/v1/gtfs/routes`, `/v1/gtfs/trips?route_id=…`, `/v1/gtfs/shapes?shape_id=…` : couleurs et tracés
- Icônes : `icon-180.png` (Apple), `icon-192.png` et `icon-512.png` (manifest).
- Déploiement : pousser sur `main`, GitHub Pages met le site à jour en une minute environ.

## Limites connues

- Les notifications iPhone (ex. « le 4 arrive dans 4 min ») demanderaient un serveur (Cloudflare Worker + ntfy) :
  une webapp est suspendue par iOS quand l'écran est verrouillé.
- Seuls les bus suivis en temps réel ont une position sur la carte.
