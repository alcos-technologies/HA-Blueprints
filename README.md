# HA-Blueprints

Blueprints Home Assistant.

| Blueprint | Description |
|---|---|
| [mmwave_profils_seuils](blueprints/automation/mmwave/mmwave_profils_seuils.yaml) | Capteur mmWave 24 GHz (Zigbee2MQTT) : applique un profil au-dessus du seuil 1, un autre sous le seuil 2 et un profil normal entre les deux (température, humidité ou illuminance). |

## Installation

Copier le fichier YAML dans `config/blueprints/automation/<dossier>/`, puis recharger les automatisations,
ou utiliser *Paramètres → Automatisations → Blueprints → Importer un blueprint* avec l'URL brute du fichier.
