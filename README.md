# HA-Blueprints

Ma collection de blueprints [Home Assistant](https://www.home-assistant.io/), prêts à importer.

| Blueprint | En bref |
|---|---|
| [Capteur mmWave – profils selon seuils](#-capteur-mmwave-24-ghz--profils-selon-seuils) | Ajuste les réglages d'un capteur de présence mmWave selon la température, l'humidité ou la luminosité |
| [Chauffage – fermeture sur porte/fenêtre ouverte](#-chauffage--fermeture-sur-portefenêtre-ouverte) | Coupe le chauffage d'une pièce quand une porte ou une fenêtre reste ouverte, puis le rétablit à la fermeture |

---

## Installation

**Méthode rapide :** cliquez sur le bouton *Import Blueprint* du blueprint voulu. Votre instance Home Assistant s'ouvre directement sur l'écran d'importation.

**Méthode manuelle :**
1. Dans Home Assistant, ouvrez *Paramètres → Automatisations et scènes → Blueprints*.
2. Cliquez sur *Importer un blueprint*.
3. Collez l'URL du fichier YAML (indiquée sous chaque blueprint), puis cliquez sur *Aperçu* et sur *Importer*.
4. Créez une automatisation à partir du blueprint et donnez-lui un nom explicite (par exemple, `Chauffage – Chambre – fenêtre ouverte`).

**Mise à jour :** dans *Blueprints*, ouvrez le menu ⋮ du blueprint et choisissez *Réimporter le blueprint*. Les automatisations existantes conservent leurs paramètres.

---

## 📡 Capteur mmWave 24 GHz – profils selon seuils

[![Importer dans Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Falcos-technologies%2FHA-Blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fmmwave%2Fmmwave_profils_seuils.yaml)

```
https://github.com/alcos-technologies/HA-Blueprints/blob/main/blueprints/automation/mmwave/mmwave_profils_seuils.yaml
```

### À quoi ça sert

Les capteurs de présence mmWave 24 GHz se règlent : sensibilité, distance de détection, délai d'absence, anti-interférence. Un même réglage n'est pas idéal en toutes circonstances. Par exemple, quand le chauffage ou un ventilateur fonctionne, un capteur trop sensible peut détecter une présence fantôme.

Ce blueprint applique automatiquement un **profil de réglages** différent selon la valeur d'un capteur de votre choix (température, humidité ou illuminance).

### Logique

| Valeur du capteur choisi | Profil appliqué |
|---|---|
| Au-dessus du **seuil 1** | Profil 1 |
| Sous le **seuil 2** | Profil 2 |
| Entre les deux seuils | Profil normal (facultatif) |

- Le seuil 1 doit être plus élevé que le seuil 2. Si les plages se chevauchent, le profil 1 a priorité.
- Les réglages sont envoyés **seulement quand la valeur franchit un seuil** et y reste pendant le délai de stabilisation. On évite ainsi les envois répétés, ce qui ménage la pile des capteurs sans fil.
- Exécuter l'automatisation manuellement applique tout de suite le profil qui correspond à la valeur actuelle.
- Chaque changement de profil est inscrit dans le journal (logbook).

### Réglages pris en charge

| Réglage | Type d'entité |
|---|---|
| Sensibilité au mouvement (`motion_detection_sensitivity`) | `number` |
| Sensibilité statique (`static_detection_sensitivity`) | `number` |
| Délai d'absence (`fading_time`) | `number` |
| Distance de détection (`detection_distance`) | `number` |
| Anti-interférence (`anti_interference`) | `switch` ou `select` |

Un réglage dont l'entité est laissée vide n'est pas modifié.

### Compatibilité

Le blueprint fonctionne avec **tout type de réseau**, parce qu'on choisit directement les entités de réglage : Zigbee2MQTT, ZHA, Z-Wave, Wi-Fi (Tuya, ESPHome, etc.) ou Matter.

Il a été conçu pour un capteur Tuya *24 GHz millimeter wave + T&H* sous Zigbee2MQTT. Il convient aussi à tout capteur qui expose des réglages équivalents.

### Paramètres

| Section | Paramètres |
|---|---|
| Capteur et appareil | Capteur déclencheur, délai de stabilisation (2 min par défaut) |
| Entités de réglage | Les 5 entités du capteur mmWave (facultatives) |
| Seuil 1 / Seuil 2 | Valeur du seuil et réglages du profil |
| Profil normal | Activation et réglages appliqués entre les deux seuils |

### À savoir

- Les limites min et max de chaque entité `number` s'appliquent. Une valeur hors limites est refusée et l'erreur s'affiche dans les traces de l'automatisation.
- Pour un réglage `select` d'anti-interférence, le blueprint cherche une option du type ON/OFF, true/false ou enable/disable.
- La luminosité peut osciller autour d'un seuil. Prévoyez un délai de stabilisation plus long ou un écart suffisant entre les deux seuils.

---

## 🔥 Chauffage – fermeture sur porte/fenêtre ouverte

[![Importer dans Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Falcos-technologies%2FHA-Blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fchauffage%2Fchauffage_fenetre_ouverte.yaml)

```
https://github.com/alcos-technologies/HA-Blueprints/blob/main/blueprints/automation/chauffage/chauffage_fenetre_ouverte.yaml
```

### À quoi ça sert

Évite de chauffer l'extérieur. Quand une porte ou une fenêtre d'une pièce reste ouverte, le chauffage de cette pièce est coupé. Il est rétabli dans son état d'origine quand tout est refermé.

### Fonctionnement

1. **Ouverture :** un des capteurs choisis reste ouvert plus longtemps que le délai (1 min par défaut). Le blueprint mémorise alors le mode et la consigne des thermostats, puis coupe le chauffage.
2. **Fermeture :** **tous** les capteurs choisis sont fermés depuis le délai (1 min par défaut). Le blueprint remet alors le mode et la consigne d'origine.

- Plusieurs capteurs et plusieurs thermostats par pièce sont pris en charge.
- Un thermostat déjà éteint avant l'ouverture reste éteint après la fermeture.
- Ouvrir une deuxième fenêtre pendant que le chauffage est coupé n'écrase pas les réglages mémorisés.
- Chaque coupure et chaque retour est inscrit dans le journal (logbook).

### Paramètres

| Section | Paramètres |
|---|---|
| Capteurs et thermostats | Capteurs de porte ou de fenêtre (`binary_sensor` de type door, window, opening ou garage_door) et thermostats (`climate`) |
| Délais | Avant la coupure et avant le retour à la normale |
| Action à l'ouverture | **Éteindre** (mode off) ou **Consigne réduite** (7 °C par défaut), pour les thermostats qui n'acceptent pas le mode off |

### Compatibilité

Le blueprint fonctionne avec tout capteur d'ouverture (Zigbee, Z-Wave, Wi-Fi, Matter, etc.) et tout thermostat exposé comme entité `climate`.

### À savoir

- Créez **une automatisation par pièce**.
- Les réglages d'origine sont mémorisés dans une scène temporaire, qui disparaît si Home Assistant redémarre. Si HA redémarre pendant qu'une fenêtre est ouverte, le chauffage n'est pas rétabli automatiquement.
- Ne changez pas l'`entity_id` de l'automatisation pendant qu'une fenêtre est ouverte. La renommer ne pose aucun problème.

---

## Problèmes et suggestions

Un bogue ou une idée d'amélioration ? Ouvrez une [issue](https://github.com/alcos-technologies/HA-Blueprints/issues).
