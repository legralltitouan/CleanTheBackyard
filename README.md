# 🧹 Clean The Backyard

![Paper 1.21.11](https://img.shields.io/badge/Paper-1.21.11-blue) ![Version](https://img.shields.io/badge/version-0.2.0--SNAPSHOT-orange) ![Java](https://img.shields.io/badge/Java-21-red)

> Un plugin Minecraft **Idle‑Clicker** où vous nettoyez votre jardin en cassant des cartons, améliorez votre équipement, achetez des drones automatiques et participez à des événements temporaires.

---

## 📖 Contexte

**Clean The Backyard** est un mini‑jeu pour serveur **Paper 1.21.11**.  
Le joueur évolue dans un monde sécurisé (pas de dégâts, faim gelée, pas de monstres). L’objectif est de casser des **cartons** (blocs configurables, par défaut `NOTE_BLOCK`) pour les stocker dans un **sac**, puis de les **vendre** dans une zone dédiée afin de gagner de l’argent.

Cet argent permet d’améliorer :

- la **capacité du sac**,
- la **vitesse et les bonus** de l’outil (cooldown, cartons multiples),
- la **réserve d’énergie** et sa régénération.

Avec un bon niveau, vous pouvez acheter des **drones** qui récoltent et vendent automatiquement les cartons. Des **événements** (OVNI, Drone Doré) viennent ponctuellement booster les gains.

Le plugin est **totalement autonome** : il gère la régénération des cartons par secteurs, l’énergie, les buffs temporaires, une boutique, une machine distributrice, et une interface utilisateur complète (GUI, action bar).

---

## 🚧 État actuel (v0.2.0)

Le plugin est **fonctionnel** et prêt à être testé sur un serveur de jeu.  
Toutes les mécaniques principales sont implémentées :

- ✅ Collecte et vente des cartons
- ✅ Énergie et fatigue
- ✅ Améliorations (sac, outil, énergie)
- ✅ Drones (4 niveaux)
- ✅ Événements : OVNI (multiplicateur global x2), Drone Doré
- ✅ Distributeur d’objets énergisants
- ✅ Commandes administrateur complètes
- ✅ Persistance des données (YAML)
- ✅ GUI dynamiques (garage, boutique drones, distributeur)

**Points d’attention** :
- La régénération des secteurs nécessite que les positions des cartons soient pré‑enregistrées (via `SectorManager.registerSector`). Le code fourni ne contient pas de génération automatique du monde – il faut soit pré‑placer les cartons à la main, soit ajouter un script de scan.
- Les drones utilisent l’entité `BEE` (silencieuse, sans IA). Cela fonctionne sur Paper 1.21.

---

## ⚙️ Fonctionnalités détaillées

### 🎮 Mécaniques de jeu

| Mécanique | Description |
|-----------|-------------|
| **Cartons** | Blocs cassables (clic gauche). Chaque cassage consomme de l’énergie, ajoute un carton au sac. |
| **Sac** | Stocke les cartons en attente de vente. Capacité augmentable. |
| **Outil** | Détermine le temps de cassage (`cooldown-ms`). Peut donner plusieurs cartons en un seul clic (`boxes-per-collection` + `extra-box-chance`). |
| **Énergie** | Ressource qui se régénère passivement. Tombe à 0 → le joueur est ralenti et aveugle. |
| **Vente** | En entrant dans la zone de vente (`sell-zone`), tous les cartons du sac sont vendus instantanément. Prix unitaire configurable, multipliable par buffs. |
| **Améliorations** | Accessibles via le **garage** (zone physique). Coût en argent, niveaux jusqu’à 8. |
| **Distributeur** | Permet d’acheter des objets (soda, chocolat, boisson énergisante) qui restaurent l’énergie. |
| **Drones** | Entités volantes (abeilles) qui se déplacent automatiquement, ramassent les cartons et les vendent. Revenu passif. |
| **Événement OVNI** | Apparition périodique de caisses en `GLOWSTONE`. Chaque joueur peut les casser pour obtenir : argent, `x2 vente` personnel, ou `énergie illimitée`. Si le serveur atteint un quota de cartons normaux cassés pendant l’événement, tout le monde gagne un **multiplicateur de vente global x2** pendant 1 heure. |
| **Drone Doré** | Une chauve‑souris nommée apparaît près d’un joueur. Celui qui la frappe gagne une récompense en argent (basée sur ses niveaux). |

### 🧑‍💻 Commandes administrateur

Toutes les commandes sont sous `/ctb` et nécessitent la permission `ctb.admin.<sous-commande>`.

| Commande | Description |
|----------|-------------|
| `/ctb stats [joueur]` | Affiche toutes les stats d’un joueur. |
| `/ctb money give\|set\|take <joueur> <montant>` | Modifie l’argent. |
| `/ctb level bag\|tool\|energy <joueur> <niveau>` | Force le niveau d’un équipement. |
| `/ctb bag fill\|clear\|set <joueur> [quantité]` | Remplit/vide/modifie le sac. |
| `/ctb energy fill\|set\|drain <joueur> [valeur]` | Gère l’énergie. |
| `/ctb drone give\|remove <joueur> <clé_drone>` | Donne ou retire un drone (clés : `basic`, `advanced`, `military`, `quantum`). |
| `/ctb buff doublesell\|unlimitedenergy <joueur> <secondes>` | Applique un buff temporaire. |
| `/ctb event drone\|ufo` | Déclenche manuellement un événement. |
| `/ctb sell [joueur]` | Vend le sac du joueur (sans zone). |
| `/ctb reload` | Recharge la configuration et sauvegarde les données. |

### 🧩 GUI automatiques

Les interfaces s’ouvrent automatiquement lorsque le joueur entre dans les zones définies dans `config.yml` :

- **Garage** → interface d’améliorations
- **Distributeur** → achat d’objets énergisants
- **Boutique de drones** → achat de drones
- **Zone de vente** → vend le contenu du sac (pas de GUI, juste l’action)

---

## 📁 Configuration

Le fichier `config.yml` est généré au premier démarrage.  
Extrait des principales sections :

```yaml
game-world: "world"

sell-zone:
  x: 81
  y: 118
  z: -25
  radius: 3

garage: { x: 73, y: 118, z: -20, radius: 2.5 }
vending-machine: { x: 72, y: 117, z: -31, radius: 2.5 }
drone-shop: { x: 68, y: 117, z: -15, radius: 2.5 }

box-sell-price: 5.0
global-sell-multiplier: 1.0
energy-cost-per-box: 3.0

bag-upgrades:  ...   # capacité et coût
tool-upgrades: ...   # cooldown, boxes-per-collection, extra-box-chance
energy-upgrades: ... # max-energy, regen-per-sec

drone-types:        # basic, advanced, military, quantum
  basic:
    income: 2
    interval-sec: 10
    cost: 500
    speed: 2.5
    pickup-range: 15

vending-machine-items:
  soda: { energy-restore: 25, cost: 10 }