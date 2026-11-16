# HA-Blueprints

Blueprints Home Assistant.

| Blueprint | Description |
|---|---|
| [mmwave_profils_seuils](blueprints/automation/mmwave/mmwave_profils_seuils.yaml) | Capteur mmWave 24 GHz (tout réseau : Z2M, ZHA, Z-Wave, Wi-Fi, Matter) : applique un profil au-dessus du seuil 1, un autre sous le seuil 2 et un profil normal entre les deux (température, humidité ou illuminance). |
| [chauffage_fenetre_ouverte](blueprints/automation/chauffage/chauffage_fenetre_ouverte.yaml) | Coupe le chauffage d'une pièce (mode off ou consigne réduite) quand une porte/fenêtre reste ouverte, puis rétablit le mode et la consigne d'origine quand tout est refermé. Délais réglables. |

## Installation

Copier le fichier YAML dans `config/blueprints/automation/<dossier>/`, puis recharger les automatisations,
ou utiliser *Paramètres → Automatisations → Blueprints → Importer un blueprint* avec l'URL brute du fichier.
