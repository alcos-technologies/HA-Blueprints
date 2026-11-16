# HA-Blueprints

Blueprints Home Assistant.

| Blueprint | Description |
|---|---|
| [mmwave_profils_seuils](blueprints/automation/mmwave/mmwave_profils_seuils.yaml) | Capteur mmWave 24 GHz (Zigbee2MQTT) : applique des profils de réglages (sensibilités, anti-interférence, fading_time, detection_distance) selon des seuils de température, d'humidité ou d'illuminance. |

## Installation

Copier le fichier YAML dans `config/blueprints/automation/<dossier>/`, puis recharger les automatisations,
ou utiliser *Paramètres → Automatisations → Blueprints → Importer un blueprint* avec l'URL brute du fichier.
