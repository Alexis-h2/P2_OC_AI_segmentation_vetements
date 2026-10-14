# Projet 2 Requêtez des services IA
Github pour projet 2 formation OC AI engineer segmentation de vetements par image 

# Pipeline de Segmentation Textile — SegFormer B3

Pipeline d'évaluation et de déploiement pour la segmentation sémantique d'articles vestimentaires, basé sur le modèle `sayeed99/segformer_b3_clothes`. 

Ce projet comprend l'analyse qualitative et quantitative sur un échantillon test (50 images), la gestion robuste des flux de données d'inférence (Base64), et une étude de cadrage technique pour le passage à l'échelle industrielle (500 000 images en 30 jours).

---

## Architecture & Modèle

* **Architecture :** SegFormer B3 (encodeur hiérarchique Vision Transformer + décodeur MLP léger).
* **Poids pré-entraînés :** `sayeed99/segformer_b3_clothes`.
* **Dataset source :** ATR (*Annotated Clothes Tracker Dataset*), couvrant 18 classes sémantiques (vêtements, accessoires et parties anatomiques).
* **Particularité :** Perception multi-échelle permettant d'associer la vue d'ensemble de la silhouette et la détection fine des bordures textiles.

---

## Résultats & Métriques (Échantillon de test)

L'évaluation a été menée sur un échantillon de 50 images de test via l'Inference API.

| Métrique | Score | Observation métier |
| :--- | :---: | :--- |
| **Accuracy globale** |
| **Recall moyen** |
| **Précision moyenne** |
| **mIoU moyen** |

> **Note sur le calcul :** Le mIoU est calculé en excluant l'arrière-plan (*Background*, classe 0) afin de refléter la performance réelle sur les textiles et les accessoires sans surestimation artificielle.

### Diagnostic d'erreur (Label Noise)
L'analyse comparative met en évidence un phénomène marqué de *label noise* dans la vérité terrain :
* Les masques humains de référence présentent des découpages simplifiés voire faussés au moment des annotations
* SegFormer produit des contours continus et relativement fidèles aux plis naturels du vêtement, sanctionnés à tort

---

## Structure du Pipeline de Données

Le notebook implémente la chaîne complète de traitement des réponses de l'API :

1. **Requête HTTP distante :** Transmission des images brutes vers l'Inference API Hugging Face.
2. **Désérialisation & Décodage :** Conversion des flux compressés Base64 en mémoire tampon via `io.BytesIO`.
3. **Alignement spatial :** Redimensionnement au format source par interpolation au plus proche voisin (`NEAREST`) pour préserver l'intégrité des indices de classe.
4. **Fusion matricielle 2D :** Encodage textuel vers entiers (`CLASS_MAPPING`) et génération du masque final NumPy.
5. **Visualisation contextuelle :** Rendu comparatif en 3 colonnes (*Originale*, *Vérité terrain*, *Prédiction*) avec légende dynamique n'affichant que les classes présentes à l'écran.



## Arborescence du Projet

```text
├── data/
│   ├── images/              # Échantillon de test
│   └── ground_truth/        # Masques annotés originaux
├── notebooks/
│   └── evaluation_segformer.ipynb  # Pipeline complet, calculs et visualisations
├── assets/                  # Captures des grilles comparatives et schémas
├── .env.example             # Modèle des variables d'environnement requises
├── .gitignore               # Fichiers ignorés par Git (dont .env, data/, checkpoints)
├── requirements.txt         # Dépendances Python
└── README.md