+++
title = "2. Gestion du Maillage"

date = 2018-09-09T00:00:00
# lastmod = 2018-09-09T00:00:00

draft = false  # Is this a draft? true/false
toc = true  # Show table of contents? true/false
type = "docs"  # Do not modify.

math = true
weight = 110
diagram = false
#markup = "mmark"

edit_page = {repo_url = "https://github.com/Bertbk/course_fem", repo_branch = "master", submodule_dir="content/course/fem/"}

[git]
  icon = "github"
  repo = "https://github.com/Bertbk/course_fem"
  submodule_dir = "content/course/fem/"


# Add menu entry to sidebar.
[menu.fem]
  parent = "IV. Mise en Œuvre de la Méthode des Éléments Finis"
  name = "2. Gestion du Maillage"
  weight = 10

+++
$\newcommand{\Cb}{\mathbb{C}}$
$\newcommand{\Nb}{\mathbb{N}}$
$\newcommand{\Rb}{\mathbb{R}}$
$\newcommand{\PS}[2]{\left(#1,#2\right)}$
$\newcommand{\PSV}[2]{\PS{#1}{#2}\_V}$
$\newcommand{\PSL}[2]{\PS{#1}{#2}\_{L^2(\Omega)}}$
$\newcommand{\PSH}[2]{\PS{#1}{#2}\_{H^1(\Omega)}}$
$\newcommand{\norm}[1]{\left\\|#1\right\\|}$
$\newcommand{\normV}[1]{\left\\|#1\right\\|\_{V}}$
$\newcommand{\normH}[1]{\left\\|#1\right\\|\_{H^1(\Omega)}}$
$\newcommand{\normL}[1]{\left\\|#1\right\\|\_{L^2(\Omega)}}$
$\newcommand{\abs}[1]{\left|#1\right|}$
$\newcommand{\xx}{\mathbf{x}}$
$\newcommand{\yy}{\mathbf{y}}$
$\newcommand{\zz}{\mathbf{z}}$
$\newcommand{\nn}{\mathbf{n}}$
$\newcommand{\Ccal}{\mathcal{C}}$
$\newcommand{\Cscr}{\mathscr{C}}$
$\newcommand{\omegai}{\omega\_i}$
$\newcommand{\dsp}{\displaystyle}$
$\newcommand{\diff}{{\rm d}}$
$\newcommand{\conj}[1]{\overline{#1}}$
$\newcommand{\dn}{\partial_\nn}$
$\newcommand{\supp}{\mathrm{supp}}$
$\newcommand{\enstq}[2]{\left\\{#1 \mathrel{}\middle|\mathrel{}#2\right\\}}$
$\newcommand{\Image}{\mathrm{Im}}$
$\newcommand{\Ker}{\mathrm{Ker}}$
$\newcommand{\dxi}{\partial\_{x\_i}}$
$\newcommand{\di}{\partial\_{i}}$
$\newcommand{\dj}{\partial\_{j}}$
$\newcommand{\Ho}{H^1(\Omega)}$
$\newcommand{\Lo}{L^2(\Omega)}$
$\newcommand{\ssb}{\mathbf{s}}$
$\newcommand{\Ns}{N\_s}$
$\newcommand{\Nt}{N\_t}$
$\newcommand{\Ne}{N\_e}$
$\newcommand{\sumit}[1]{\ssb\_{#1}}$
$\newcommand{\sumitK}[2]{\ssb\_{#2}^{#1}}$
$\newcommand{\tri}[1]{T\_{#1}}$
$\newcommand{\loctoglob}{\mathrm{Loc2Glob}}$
$\newcommand{\mphi}[1]{\varphi\_{#1}}$
$\newcommand{\mphiK}[2]{\mphi{#2}^{#1}}$


Le maillage du domaine est une étape que nous ne détaillerons pas. Sachons cependant que cette étape est à la fois compliquée, du point de vue mathématique et algorithmique, et très coûteuse, du point de vue temps CPU, surtout en 3D ! Pour des géométries complexes, le temps de création du maillage peut dépasser celui de la résolution du système linéaire. Nous utiliserons le logiciel libre [GMSH](https://GMSH.info) et un [tutoriel]({{< ref "tutorial/gmsh">}}) pour dessiner puis mailler des domaines.


{{% callout note %}}
Nous nous restreignons ici au cas d'éléments triangulaires à 3 sommets. Cependant, tout est adaptable (et à adapter !) en fonction de la géométrie de l'élément : segment, triangle (à plus que 3 points), quadrangle, prisme, ... Mais aussi en fonction de l'ordre de l'élément.
{{% /callout %}}

## Triangles

### Numérotation

Les $\Nt$ triangles du maillage seront numérotés de 1 à $\Nt$ : $\tri{1}, \tri{2}, \ldots, \tri{\Nt}$. Pour un triangle de numéro arbitraire, nous tâcherons de toujours utiliser les indices $p$ et $q$, pour $p,q=1,\ldots,\Nt$ : $\tri{p}$ et $\tri{q}$.

### Orientation

Pour un triangle d'un maillage de sommets $\ssb\_I, \ssb\_J, \ssb\_K$, deux orientations sont possibles (le choix du premier sommet n'ayant aucune incidence sur l'orientation) :  $\tri{} = [\ssb\_I, \ssb\_J, \ssb\_K]$ ou $\tri{}^{\prime} = [\ssb\_I, \ssb\_K, \ssb\_J]$. La normale unitaire sortante au triangle $\tri{}$ est alors opposé à $ \tri{}^{\prime}$. Il est donc important que chaque triangle d'une même surface soit orienté dans le même sens !

{{< figure src="../orientation.svg" title="Orientation d'un triangle" numbered="true" >}}

## Numérotation des sommets

### Globale

Les $\Ns$ sommets $\sumit{I}$ d'un maillage seront numérotés de 1 à $\Ns$ : $\sumit{1}, \sumit{2}, \ldots, \sumit{\Ns}$. Lorsque le numéro est arbitraire, nous utiliserons une lettre majuscule, par exemple $I$ ou $J$, avec $I,J = 1,2,3,\ldots, \Ns$.

### Locale

Chaque sommet appartient à au moins un triangle et possède alors une numérotation **locale** dans ce triangle, c'est-à-dire 1, 2 ou 3. Pour ne pas confondre avec la numérotation globale, nous indiquerons le numéro du triangle, par exemple $\sumitK{p}{i}$ est le $i^{ème}$ sommet du triangle $\tri{p}$, de sommets $[\sumitK{p}{1}, \sumitK{p}{2}, \sumitK{p}{3}]$.

### Locale vers Globale

Pour un triangle $\tri{p} = [\sumitK{p}{1}, \sumitK{p}{2}, \sumitK{p}{3}]$ donné, nous aurons besoin de passer de la numérotation locale d'un de ses sommets à sa numérotation globale. Pour cela, nous introduisons une fonction `Loc2Glob` définie formellement ainsi :
$$
\begin{array}{l}
  \forall p=1\,\ldots,\Nt, \quad \forall i = 1, 2, 3, \\\\\\
  \loctoglob(p,i) = I \iff \sumitK{p}{i} = \sumit{I}.
\end{array}
$$

Pour simplifier, nous noterons parfois de manière plus compacte :
$$
I(p,i) = I\_{p,i} = \loctoglob(p,i),\quad\text{ ou encore}\quad
J(q,j) = J\_{q,j} = \loctoglob(q,j)
$$


{{< figure src="../loc2glob.svg" title="Numérotation globale (gauche) et numérotation locale (droite) des sommets d'un triangle particulier." numbered="true" >}}


{{< figure class="app-local-to-global" title="<i class='fas fa-play-circle'></i> **Time To Play!**<br> **Cliquez sur un triangle** pour faire apparaitre la **numérotation locale** des sommets du triangle. Recliquez dessus pour revenir en **numérotation globale**" numbered="true" >}}

## Numérotation des fonctions de forme

Pour un sommet $\sumit{I}$, nous continuons de noter $\mphi{I}$ la fonction de forme associée à ce sommet. Cependant et sachant que ce même sommet $\sumit{I}$ dispose d'une numérotation locale $i$ dans un triangle $\tri{p}$, c'est-à-dire $\sumitK{p}{i} = \sumit{I}$, nous noterons également :
$$
\mphiK{p}{i} = \mphi{I} = \mphi{I\_{p,i}} = \mphi{I(p,i)}= \mphi{\loctoglob(p,i)}.
$$

## Structure d'un fichier de maillage

En général, un fichier de maillage contient plusieurs informations :

1. Le **numéro global** et les **coordonnées** des sommets
2. La **connectivité** des éléments

Autrement dit, il est facile de **retrouver les numéros de noeuds composant un élément**, mais il est plus **coûteux de retrouver les éléments contenants un noeud**.

## Exemples

### Sous-marin

<object type="text/html" data="../sub/index.html" width="100%" height="auto"></object>

- [Script GMSH](https://github.com/Bertbk/geogmsh/blob/master/submarine.geo)
- [Plein écran](../sub.html)

### Théière

<object type="text/html" data="../teapot/index.html" width="100%" height="auto"></object>

- [Script GMSH](https://github.com/Bertbk/geogmsh/blob/master/teapot.geo)
- [Plein écran](../teapot/index.html)


{{< js src="https://d3js.org/d3.v5.min.js" >}}
{{< js src="../js/loc2glob/main.js" >}}
