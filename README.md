# Extraction du plan du collet d'anévrismes - Implementation Python
## Projet complexe de troisième année  en partenariat avec le laboratoire LATIM & CHU de Brest - Spécialisation santé à l'IMT Atlantique


![Python](https://img.shields.io/badge/Python-3.8+-blue?logo=python)
![MATLAB](https://img.shields.io/badge/MATLAB-R2020a+-yellow?logo=mathworks)
![Medical Imaging](https://img.shields.io/badge/Medical-Imaging-brightgreen)
![NumPy](https://img.shields.io/badge/NumPy-1.24+-blue?logo=numpy)
![SciPy](https://img.shields.io/badge/SciPy-1.11+-orange?logo=scipy)
![scikit-image](https://img.shields.io/badge/scikit--image-0.21+-yellowgreen)
![Matplotlib](https://img.shields.io/badge/Matplotlib-3.8+-red?logo=matplotlib)
![skfmm](https://img.shields.io/badge/skfmm-latest-lightblue)
![Computer Vision](https://img.shields.io/badge/Computer-Vision-lightgrey)
![Research Project](https://img.shields.io/badge/Research-Project-black)


Le travail étant soumis à des contraintes de propriété intellectuelle, seul le readme servant d'évaluation du projet peut être mis à disposition publique.

Traduction Python de la méthode ACP (Automated Cutting Plane) pour l'analyse morphologique des anévrismes intracrâniens, originellement implémentée en MATLAB par Tim Jerman (https://github.com/timjerman/AneurysmNeckPlaneExtraction).

Cette méthode permet le positionnement du plan de coupe (dit plan du collet), via l'extraction de 4 paramètres morphologiques principaux, ainsi que l'extraction de la ligne décrivant le contour du plan du collet autour de l’anévrisme, illustrée ci-dessous : 

<img width="678" height="221" alt="téléchargement" src="https://github.com/user-attachments/assets/a9f9899b-7c54-4eb0-a4b5-35716c63b9ac" />


## Introduction

Les anévrismes intracrâniens, des dilatations anormales des parois artérielles cérébrales, touchent 3 à 5% de la population. Leur rupture peut entraîner une hémorragie sous-arachnoïdienne, une condition grave avec un taux de mortalité élevé.

Cette implémentation Python permet :
- **Le positionnement automatique d'un plan de coupe (ACP)**
- **L'extraction de 4 paramètres morphologiques clés :**
  1. **Taille** : diamètre maximal du dôme
  2. **Hauteur du dôme** : distance apex/plan de coupe
  3. **Ratio d'aspect** : hauteur/largeur du collet
  4. **Indice de non-sphéricité** : déviation par rapport à une sphère parfaite

## Contexte Clinique
L'évaluation du risque de rupture repose sur ces caractéristiques morphologiques. La méthode ACP améliore :
- La reproductibilité des mesures
- La précision du positionnement du plan de coupe
- L'aide à la décision clinique (pose de coil)

*Référence :* [Automated Cutting Plane Positioning for Intracranial Aneurysm Quantification](https://ieeexplore.ieee.org/document/8721569) (IEEE TBME 2020)

## Méthodologie ACP
La méthode repose sur 4 étapes clés :

#### A. Extraction des lignes centrales des vaisseaux parents
#### B. Localisation du centre de l'anévrisme
#### C. Orientation principale de l'anévrisme 
#### D. Positionnement optimal du plan de coupe

## Lancement 

Prérequis :
- Licence matlab récente (R2024b)
- Python 3.11.9
### Code matlab original - Tim Jerman  

- Télécharger les toolbox graph, MinGW-w64 C/C++/Fortran Compiler et fast_marching sur l'application Add-Ons de Matlab
- Télécharger la toolbox fast_marching sur le github suivant : https://github.com/gpeyre/matlab-toolboxes/tree/master/toolbox_fast_marching
- Mettre la toolbox dans le dossier Toolbox du path matlab
- Ouvrir le fichier compiler_mex.m
- Mettre en commentaire les lignes suivantes :
```
    %mex mex/anisotropic-fm//perform_front_propagation_anisotropic.cpp
    %mex mex/anisotropic-fm-feth/fm2dAniso.cpp
```
En cas de soucis de compatibilité entre C++ et la version de matlab installée : 
- Modifier le fichier perform_front_propagation_3d_mex.cpp à la ligne 86, en remplaçant int par mwSize.
- Lancer le fichier compiler_mex.m
- Lancer le fichier examplePlaneComputation.m

Remarques :

Le temps d'éxecution de l'implémentation Matlab est long.

Un executable du matlab est existant, contacter Mr. Coupet ou Manel Meftah pour l'obtenir si le lancement du code Matlab du Github ne fonctionne pas.

### Traduction python
#### Bibliothèques requises
Liste complète des bibliothèques Python nécessaires :

- numpy (np)
- scipy
- skfmm
- skimage
- matplotlib

Pour installer toutes les dépendances, éxecuter : 

```
pip install numpy scipy scikit-fmm scikit-image matplotlib 
````
Ou via conda : 
````
conda install -c conda-forge numpy scipy scikit-fmm scikit-image matplotlib 
````

Il est possible de lancer l'étape C de la méthode ACP (détermination de la direction principale de l'anévrisme) à part entière en lançant le script : **computeAneurysmPrincipalDirectionMultipleCenterlines_complet.py**

Le répositoire contient également chaque fonction principale correspondant à un script Matlab du Github initial dans le dossier **scripts** du dossier **etapeC**.

## Etape C - Orientation principale de l'anévrisme

Cette partie de la méthodologie a pour objectif principal de déterminer la direction principale de l’anévrisme à partir des lignes centrales extraites de l’étape A et du centre de l'anévrisme extrait dans l'étape B.

Codes traduits pour le fonctionnement de cette étape :
- computeAneurysmPrincipalDirectionMultipleCenterlines.m,
- distanceAcumulatorFromMesh.m,
- interparc.m
- L’équivalent des fonctions perform_fast_marching (en 3D) et compute_geodesic de la toolbox fast_marching de matlab n’existant pas en python, ces fonctions ont également été traduites et se trouvent dans les scripts du répositoire.

L’importation des données nécessaire à faire tourner cette partie du code a été faite à l’aide des scripts matlab de Tim Jerman. Les fonctions suivantes ont été utilisées et rajoutées dans le script matlab pour récupérer les données (1) puis pour les introduire dans le script python (2) : 

```matlab
writematrix(variable, 'C:\Users\...\variable.txt');
``` 

```python
    with open('C:/Users/.../variable.txt', "r", encoding="utf-8") as fichier:
        variable_str = fichier.read()
        variable = np.loadtxt('C:/Users/.../variable.txt', delimiter=',')
```

### Paramètres d'entrée :

-  Les coordonnées des sommets du maillage 3D (vertex)
- Un masque identifiant la région interne du maillage (meshInnerMask)
- Les coordonnées du centre géométrique de l'anévrisme (aneurysmCenter)
- Un ensemble de lignes centrales représentant l'axe vasculaire (aneurysmCenterlines)
- Le point d'apex de l'anévrisme (aneurysmApex)

### Résultats :

- Le point le plus proche de l’apex de l’anévrisme situé sur les lignes centrales (aneurysmCenterlinePoint)
- Une trajectoire représentant la direction principale de l'anévrisme (aneurysmPrincDir), qui parcourt la distance entre l’apex et la ligne centrale du vaisseau sanguin.

<img width="1032" height="821" alt="téléchargement (1)" src="https://github.com/user-attachments/assets/33c56236-ce4b-46f0-9186-a9a60c8f2a45" />

<img width="999" height="855" alt="téléchargement (2)" src="https://github.com/user-attachments/assets/af686f86-ff18-45f5-82de-829c84f4393a" />

<img width="1063" height="937" alt="téléchargement (3)" src="https://github.com/user-attachments/assets/a4c3b322-009d-4630-98ab-5de428e35b71" />


### Fonctionnement :

On génère d'abord une carte des distances euclidiennes à partir des sommets du maillage grâce à la fonction distanceAcumulatorFromMesh. Cette fonction extrait une carte de distances qui associe la distance de chaque point de l’espace au point du maillage (bords de l’anévrisme) le plus proche. Elle contient donc un tableau des coordonnées de chaque point du maillage et de la distance calculée associée. Elle sert de base au calcul de la carte de distances pondérée extraite à l’aide de la fonction perform_fast_marching. Cette carte pondérée permet d'évaluer les distances entre le point de départ (centre ou apex) et tous les points des lignes centrales après les avoir projetés dans l'espace de l’anévrisme, en se basant sur les distances de chaque point de l’espace avec le maillage de l’anévrisme (donc les bords). 
Le centerLinePoint est ensuite extrait, c’est le point présentant la distance pondérée la plus courte par rapport à l’apex. On extrait le chemin le plus court issu de l’algorithme fast_marching en calculant la trajectoire géodésique entre le centerLinePoint et l’apex de l’anévrisme. Ce chemin géodésique est finalement interpolé avec 1000 points. Il représente la direction principale de l’anévrisme. 
Chaque étape majeure de cette partie est contenue dans chacune des fonctions principales listées ci-dessus.

### Structure du code :

#### Fonctions principales du script computeAneurysmPrincipalDirectionMultipleCenterlines_complet.py, également disponibles sous formes de scripts à part entière :
*distanceAcumulatorFromMesh()* - Crée une carte de distances euclidiennes à partir des sommets du maillage, conversion du maillage en volume de distances

*perform_fast_marching()* - Calcul des distances pondérées à partir de l'apex/centre

*calc_aneurysmCenterlinePoint* - Sélection du point de la ligne centrale le plus proche de l'apex/centre de l'anévrisme, qui sera le point d'arrivée du chemin géodésique utilisé pour déterminer la direction principale de l'anévrisme.

*compute_geodesic()* - Extraction du chemin le plus court de l'apex à la ligne médiane

*interparc()* - Interpolation du chemin géodésique courbe Génère une courbe lissée de la direction principale (1000 points)

*computeAneurysmPrincipalDirection()* - Workflow principal

*resultats()* - Visualisation 3D des résultats finaux

#### Fonctions auxiliaires :


_segkernel()_: Noyau d'intégration pour le calcul de longueur d'arc

_ode45()_: Solveur ODE adapté de MATLAB

_bilinear_interpolate3D()_: Interpolation 3D pour la descente de gradient

_visualisation3D_accumulateur()_: Visualisation de la carte de distances de l'accumulateur

_safe_physical_to_voxel()_: Conversion coordonnées physiques → indices voxels

### Plan de progression :

_Terminé :_

✅ Traduction des fonctions principales de MATLAB vers Python

✅ Implémentation de l'accumulateur de distance

✅ Algorithme de propagation rapide fast_marching fonctionnel

✅ Calcul du chemin géodésique

✅ Interpolation 3D

✅ Visualisation de la direction principale de l'anévrisme correcte 



Lors de la traduction du code MATLAB vers Python, une légère divergence a été observée dans le calcul du aneurysmCenterlinePoint. Ce point, qui sert de point de départ pour le calcul de la direction principale, présente un décalage par rapport aux résultats MATLAB. → voir résultats ci-dessus.



_Prochaines étapes/améliorations possibles :_ 

✏️ Revoir la précision des calculs des distances pondérées de l'algorithme fast marching 

✏️ Etudier la piste des différences d'arrondi dans les conversions entre coordonnées physiques et indices voxels

✏️ Tester une alternative à la carte des distances pondérées produite par l'algorithme de fast marching dans le calcul de la position du point _aneurysmCenterlinePoint_

✏️ Vérification du bon fonctionnement du code dans sa totalité, de l'étape A à C, puis A à D, et s'assurer de la compatibilité entre les différentes étapes pour pouvoir lancer le code traduit dans sa globalité





