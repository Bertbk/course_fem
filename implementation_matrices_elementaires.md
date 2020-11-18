+++
title = "3. Calcul des Matrices Élémentaires"

date = 2018-09-09T00:00:00
# lastmod = 2018-09-09T00:00:00

draft = false  # Is this a draft? true/false
toc = true  # Show table of contents? true/false
type = "docs"  # Do not modify.

math = true
weight = 130
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
  name = "3. Calcul des Matrices Élémentaires"
  weight = 30

+++

$\newcommand{\Cb}{\mathbb{C}}$
$\newcommand{\Nb}{\mathbb{N}}$
$\newcommand{\Pb}{\mathbb{P}}$
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
$\newcommand{\ee}{\mathbf{e}}$
$\newcommand{\nn}{\mathbf{n}}$
$\newcommand{\ssb}{\mathbf{s}}$
$\newcommand{\sK}{\mathbf{s}^K}$
$\newcommand{\xx}{\mathbf{x}}$
$\newcommand{\yy}{\mathbf{y}}$
$\newcommand{\zz}{\mathbf{z}}$
$\newcommand{\Ccal}{\mathcal{C}}$
$\newcommand{\Cscr}{\mathscr{C}}$
$\newcommand{\Sscr}{\mathscr{S}}$
$\newcommand{\Tscr}{\mathscr{T}}$
$\newcommand{\omegai}{\omega\_i}$
$\newcommand{\dsp}{\displaystyle}$
$\newcommand{\diff}{{\rm d}}$
$\newcommand{\conj}[1]{\overline{#1}}$
$\newcommand{\dn}{\partial_\nn}$
$\newcommand{\supp}{\mathrm{supp}}$
$\newcommand{\restrict}{\mathclose{}|\mathopen{}}$
$\newcommand{\enstq}[2]{\left\\{#1 \mathrel{}\middle|\mathrel{}#2\right\\}}$
$\newcommand{\Image}{\mathrm{Im}}$
$\newcommand{\Ker}{\mathrm{Ker}}$
$\newcommand{\dxi}{\partial\_{x\_i}}$
$\newcommand{\di}{\partial\_{i}}$
$\newcommand{\dj}{\partial\_{j}}$
$\newcommand{\dxj}{\partial x\_{j}}$
$\newcommand{\Ho}{H^1(\Omega)}$
$\newcommand{\Lo}{L^2(\Omega)}$
$\newcommand{\Cinfc}{\Cscr^{\infty}\_c}$
$\newcommand{\CinfcO}{\Cinfc(\Omega)}$
$\newcommand{\hme}[1]{#1_h}$
$\newcommand{\vh}{v\_h}$
$\newcommand{\Vh}{V\_h}$
$\newcommand{\uh}{u\_h}$
$\newcommand{\Nh}{N\_h}$
$\newcommand{\ui}{u\_i}$
$\newcommand{\uj}{u\_j}$
$\newcommand{\Sscrh}{\hme{\Sscr}}$
$\newcommand{\deltaij}{\delta\_{i,j}}$
$\newcommand{\Kp}{K\_p}$
$\newcommand{\Kq}{K\_q}$
$\newcommand{\Kl}{K\_\ell}$
$\newcommand{\Pun}{\Pb\_1}$
$\newcommand{\Punw}{\Pun(\omega)}$
$\newcommand{\grandO}[1]{O\left(#1\right)}$
$\newcommand{\qh}{\widehat{\qq}}$
$\newcommand{\sh}{\widehat{\ssb}}$
$\newcommand{\phih}{\widehat{\phi}}$
$\newcommand{\Meh}{\widehat{M}}$
$\newcommand{\varphih}{\widehat{\varphi}}$
$\newcommand{\psih}{\widehat{\psi}}$
$\newcommand{\gh}{\widehat{g}}$
$\newcommand{\Kh}{\widehat{\tri{}}}$
$\newcommand{\sumit}[1]{\ssb\_{#1}}$
$\newcommand{\sumitK}[2]{\ssb\_{#2}^{#1}}$
$\newcommand{\tri}[1]{T\_{#1}}$
$\newcommand{\loctoglob}{\mathrm{Loc2Glob}}$
$\newcommand{\aK}[1]{a\_{#1}}$
$\newcommand{\ellK}[1]{\ell\_{#1}}$
$\newcommand{\Ns}{N\_s}$
$\newcommand{\mphi}[1]{\varphi\_{#1}}$
$\newcommand{\mphiK}[2]{\mphi{#2}^{#1}}$
$\newcommand{\xK}[2]{x^{#1}\_{#2}}$
$\newcommand{\yK}[2]{y^{#1}\_{#2}}$
$\newcommand{\TK}[1]{F\_{#1}}$
$\newcommand{\BK}[1]{B\_{#1}}$
$\newcommand{\JK}[1]{J\_{#1}}$
$\newcommand{\Me}[1]{M^e\_{{#1}}}$
$\newcommand{\Mep}{M^{p}}$
$\newcommand{\De}[1]{K\_{{#1}}}$
$\newcommand{\Dep}{\De{p}}$

## Matrices de Masse et de Rigidité

Nous devons maintenant calculer les *contributions élémentaires*, c'est à dire les quantités $\aK{p}(\mphiK{p}{j}, \mphiK{p}{i})$ et $\ellK{p}(\mphiK{p}{i})$. Pour simplifier, les sommets d'un triangle $\tri{p}$ seront notés $(\sumitK{p}{1}, \sumitK{p}{2}, \sumitK{p}{3})$ et ordonnés dans le sens trigonométrique. Nous noterons $\sumitK{p}{i}=(\xK{p}{i},\yK{p}{i})$ et $\mphiK{p}{j}$ une fonction de forme du triangle $\tri{p}$ sans la supposer linéaire. D'autre part, nous scindons les contributions élémentaires en deux parties :

- **La matrice de masse**, notée $M$ de coefficient $M\_{I,J}$ donné par
$$
M\_{I,J} = \int\_{\Omega}\mphi{J}(\xx)\overline{\mphi{I}(\xx)}\diff\xx.
$$
- **La matrice de rigidité**, notée $K$ de coefficient $K\_{I,J}$ donné par
$$
K\_{I,J}=  \int\_{\Omega}\nabla\mphi{J}(\xx)\overline{\nabla\mphi{I}(\xx)}\diff\xx.
$$

{{% callout note%}}
La matrice $M$ représente l'opérateur identité dans la base des éléments finis (ce n'est pas la matrice diagonale remplie de 1...). En effet, si "l'EDP" était juste $u = f$, alors on aurait $a(u,v) = \int\_{\Omega}u\overline{v}$ et sa matrice $\Pun$ serait la matrice de masse.
{{% /callout %}}

## Matrice de masse élémentaire

Nous nous focalisons sur la matrice de masse, le principe est similaire pour la matrice $K$ et est détaillé juste après.

Pour construire la matrice $M$, nous avons vu qu'il était préférable de parcourir les triangles plutôt que les sommets, autrement dit, plutôt que de calculer $M\_{I,J}$ directement, mieux vaut calculer, pour tout triangle $p$, la **contribution élémentaire** $M^p\_{i,j}$ pour $i,j = 1,2,3$, définie par :
\begin{equation}
\label{eq:matelem}
M^p\_{i,j} = \int\_{\tri{p}} \mphiK{p}{j}(\xx)\ \overline{\mphiK{p}{i}(\xx)}\diff\xx.
\end{equation}
Chaque contribution élémentaire $M^p\_{i,j}$ est ensuite ajoutée à $M\_{I,J}$, avec $I=\loctoglob(p,i)$ et $J=\loctoglob(p,j)$. 

{{% callout note %}}
Les coefficients $M^p\_{i,j}$, pour $i,j=1,2,3$ peuvent être regroupés pour former la **matrice de masse élémentaire** $\Mep$ de taille $3\times 3$ et du triangle $\tri{p}$.
{{% /callout %}}

## Triangle de référence

Pour calculer la quantité élémentaire \eqref{eq:matelem}, plaçons nous tout d'abord dans un triangle "simple" $\Kh$, appelé **triangle de référence**. Celui-ci est souvent choisi comme étant le triangle rectangle de sommets $\sh\_{1}=(0,0)$, $\sh\_{2}=(1,0)$ et $\sh\_{3}=(0,1)$, ordonnés dans le sens trigonométrique. Pour différencier ce triangle d'un triangle du maillage, nous lui adjoignons un repère $(\xi,\eta)$
 dit **repère paramétrique**.

{{< figure src="../triangle_ref.svg" title="Triangle de référence $\Kh$ et son repère paramétrique $(\xi,\eta)$." numbered="true" >}}

Plutôt que d'indicer par $p$, nous notons $\varphih\_i \in \Pun(\Kh)$ les trois fonctions de forme associés aux sommets $\sh\_{i}$, pour $i=1,2,3$, définies par $\varphih\_j(\sh\_{i}) = \delta\_{ij}$. Ces fonctions $\varphih\_j$ étant des polynômes de degré un, nous pouvons même les calculer analytiquement :
$$
\left\\{
  \begin{array}{l}
    \varphih\_1(\xi,\eta) = 1-\xi-\eta\\\\\\
    \varphih\_2(\xi,\eta) = \xi\\\\\\
    \varphih\_3(\xi,\eta) = \eta\\\\\\
  \end{array}
\right.
$$
{{< thm/thm lemma >}}
Dans le triangle $\Kh$, la matrice de masse élémentaire $\Meh = (\Meh\_{i,j})\_{1\leq i,j\leq 3}$ de coefficient 
$$
\Meh\_{i,j} = \int\_{\Kh} \varphih\_j(\xi)\overline{\varphih\_j(\eta)} \diff(\xi,\eta),
$$
est donnée par
$$
\Meh = \frac{1}{24}\left(
  \begin{array}{c c c}
    2 & 1 & 1\\\\\\
    1 & 2 & 1\\\\\\
    1 & 1 & 2
  \end{array}
\right).
$$
{{< /thm/thm >}}

{{< thm/proof >}}
Prenons tout d'abord le cas $i=j=2$, soit $\overline{\varphih\_i} = \varphih\_j = \varphih\_2(\xi,\eta) = \xi$. Dans ce cas :
$$
\int\_{\Kh} \xi^2 \diff (\xi,\eta) = \int\_0^1\int\_0^{1-\xi} \xi^2 \diff\eta\diff\xi = \int\_0^1(1-\xi)\xi^2\diff\xi =
\left[\frac{\xi^3}{3} - \frac{\xi^4}{4}\right]\_0^1=\frac{1}{3}-\frac{1}{4} = \frac{1}{12}.
$$
Les calculs sont similaires pour $j=1$ et $j=3$. Prenons maintenant $i\neq j$, par exemple $i=3$ et $j=2$ :
$$
  \int\_{\Kh} \xi\eta \diff (\xi,\eta) = \int\_0^1\left(\int\_0^{1-\xi} \eta \diff\eta\right)\xi\diff\xi
  =  \frac{1}{2}\int\_0^1(1-\xi)^2\xi\diff\xi  
  =  \frac{1}{2}\left[ \frac{1}{2} - \frac{2}{3} +\frac{1}{4}\right] =\frac{1}{24}.
$$
Les calculs sont similaires pour les autres combinaisons.
{{< /thm/proof >}}

## Triangle quelconque

Soit un triangle $\tri{p}$ du maillage et supposons que nous disposions d'une transformation bijective et linéaire $\TK{p}$ permetteant de transformer le triangle de référence $\Kh$ en $\tri{p}$ avec en plus $\TK{p}(\sh\_i) = \sumitK{p}{i}$. Cette fonction $\TK{p}$ transforme les  **coordonnées paramétriques** $(\xi,\eta)$ en **coordonnées physiques** $(x,y)$ avec $(x,y)=\TK{p}(\xi,\eta)\in\tri{p}$, et conserve "l'ordre des sommets".


{{< figure src="../ref.svg" title="Passage du triangle de référence $\Kh$ vers un triangle $\tri{p}$ par la transformation $\TK{p}$." numbered="true" >}}

### Changement de coordonnées

Nous avons $\varphi\_j^p(x,y) = \varphi\_j^p(\TK{p}(\xi,\eta))$ avec $\varphi\_j^p\circ\TK{p}\in\Pun(\Kh)$ et $\varphi\_j^p\circ\TK{p}(\sh\_i) = \delta\_{ij}$. Par unicité, nous avons $\varphi\_j^p\circ\TK{p} = \varphih\_j$.

En notant $\JK{p}$ la matrice Jacobienne de $\TK{p}$, alors la quantité $M^p\_{i,j}$ peut alors s'écrire, par changement de variables :
$$
\begin{array}{r c l}
M^p\_{i,j} &=& \dsp\int\_{\tri{p}}\mphiK{p}{j}(x,y)\overline{\mphiK{p}{i}(x,y)} \diff(x,y)\\\\\\
&=&\dsp \abs{\det(\JK{p})}\underbrace{\int\_{\Kh}\varphih\_{j}(\xi,\eta)\overline{\varphih\_{i}(\xi,\eta)}\diff(\xi,\eta)}\_{\text{Déjà calculé !}}\\\\\\
\end{array}
$$

Ainsi, pour calculer la matrice élémentaire d'un triangle $\tri{p}$ quelconque, nous n'avons besoin que du déterminant de la Jacobienne : $\det(\JK{p})$.

### Expression et Jacobienne de la transformation

La transformation que nous cherchons, $\TK{p}$, est linéaire et "conserve" les sommets et leur ordre. Pour obtenir son expression, nous construisons des fonctions **d'interpolation géométrique**, $(\psih\_i)\_{1\leq i \leq 3}$, linéaires sur $\Kh$ et telles que :
$$
\forall i,j=1,2,3, \quad \psih\_i(\sh\_{j}) = \deltaij.
$$
La transformation aura alors pour expression :
$$
\begin{array}{r c c l}
  \TK{p}\colon & \Kh & \to & \tri{p}\\\\\\
  & (\xi,\eta) & \mapsto & \TK{p}(\xi,\eta) = (x,y) = \psih\_{1}(\xi,\eta) \sumitK{p}{1} + \psih\_{2}(\xi,\eta) \sumitK{p}{2} + \psih\_{3}(\xi,\eta) \sumitK{p}{3}.
\end{array}
$$

En d'autres termes, les fonctions d'interpolation géométrique $\psih\_i$ sont ici identiques aux fonctions de forme $\varphih\_i$ :
$$
\left\\{
  \begin{array}{l}
  \psih\_{1}(\xi,\eta) = 1 - \xi - \eta\\\\\\
  \psih\_{2}(\xi,\eta) = \xi\\\\\\
  \psih\_{3}(\xi,\eta) = \eta\\\\\\
  \end{array}
\right.
$$

La matrice Jacobienne de la transformation est alors donnée par
$$
\JK{p} = 
\left(
  \begin{array}{c c}
    \dsp\frac{\partial x}{\partial \xi} &\dsp \frac{\partial x}{\partial \eta} \\\\\\
    \dsp\frac{\partial y}{\partial \xi} &\dsp \frac{\partial y}{\partial \eta}
  \end{array}
\right) =
\left(
  \begin{array}{c c}
    \xK{p}{2} - \xK{p}{1} & \xK{p}{3} - \xK{p}{1}\\\\\\
    \yK{p}{2} - \yK{p}{1} & \yK{p}{3} - \yK{p}{1}
  \end{array}
\right),
$$
et son déterminant vaut
$$
\begin{align}
\abs{\det(\JK{p})} &= \abs{(\xK{p}{2}-\xK{p}{1})(\yK{p}{3}-\yK{p}{1}) - (\xK{p}{3}-\xK{p}{1})(\yK{p}{2}-\yK{p}{1})}\\\\\\
&= 2|\tri{p}| \neq 0,
\end{align}
$$
ce qui implique que le déterminant est non nul puisque le triangle n'est pas dégénéré : la transformation $\TK{p}$ est bien inversible.

{{% callout note %}}
Quand $\psih\_i = \varphih\_i$, nous parlons d'éléments finis **isoparamétriques**. Il convient de retenir que ce choix n'est pas obligatoire et les fonctions $\psih\_i$ et $\varphih\_i$ sont *indépendantes*. En particulier, pour obtenir des éléments courbes, les fonctions $\psih\_i$ pourraient être quadratiques par exemple.
{{% /callout %}}

{{< figure class="app-jacobi" title="<i class='fas fa-play-circle'></i> **Time To Play!**<br>**Déplacez les sommets du triangle** pour modifier la valeur du **Jacobien**. Quand il est négatif cela signifie que le triangle est **\"retourné\"** par rapport au triangle de référence." numbered="true" >}}

### Expression finale de la matrice élémentaire

{{< thm/thm lemma >}}
La matrice de masse élémentaire $M^p = (M^p\_{i,j})\_{1\leq i,j\leq 3}$ du triangle $\tri{p}$ a pour expression
$$
\Mep =   \frac{\abs{\tri{p}}}{12}
\left(
  \begin{array}{c c c}
    2 & 1 & 1\\\\\\
    1 & 2 & 1 \\\\\\
    1 & 1 & 2
   \end{array}
  \right).
$$
{{< /thm/thm >}}

## Matrice de rigidité élémentaire

Nous appliquons la même procédure pour la matrice de rigidité $K$, autrement dit, nous calculons les matrices de rigidité élémentaire $\Dep$ définies par
$$
\Dep(i,j) = \int\_{\tri{p}}\nabla \mphiK{p}{j}(x,y)\cdot \overline{\nabla\mphiK{p}{i}(x,y)}\diff(x,y).
$$

### Triangle de référence

{{< thm/thm lemma >}}
Dans le triangle de référence $\Kh$, la matrice de rigidité élémentaire $\hat{K}= (\hat{K}\_{i,j})\_{1\leq i,j\leq 3}$ de coefficient
$$
\hat{K}\_{i,j} = \int\_{\Kh}\nabla \varphih\_{j}(\xi,\eta)\cdot \overline{\nabla\varphih\_{i}(\xi,\eta)}\diff(\xi,\eta),
$$
a pour expression
$$
\hat{K} =  \frac{1}{2}
  \left(
    \begin{array}{l l c}
      2 & -1 & -1 \\\\\\
      -1 & 1 & 0 \\\\\\
      -1 & 0 & 1
    \end{array}
  \right)
$$
{{< /thm/thm >}}

{{< thm/proof >}}
Les gradients des fonctions de forme $\varphih\_j$ sont donnés par :
$$
\nabla\_{\xi,\eta}\varphih\_{1} =
\left(
  \begin{array}{l}
    -1\\\\\\
    -1
  \end{array}
\right)
,
\quad
\nabla\_{\xi,\eta}\varphih\_{2} =
\left(
  \begin{array}{l}
    1\\\\\\
    0
  \end{array}
\right),
\quad
\nabla\_{\xi,\eta}\varphih\_{3} =
\left(
  \begin{array}{l}
    0\\\\\\
    1
  \end{array}
\right).
$$
La matrice étant symétrique, nous pouvons limiter les calculs à la partie triangulaire supérieure :
$$
\begin{aligned}
\hat{K}\_{1,1} &=
  \int\_{\Kh}\nabla\varphih\_1\cdot\overline{\nabla\varphih\_1} \diff (\xi,\eta) =
  \int\_{\Kh} (-1,-1)\begin{pmatrix}-1\\\\\\ -1\end{pmatrix}\diff (\xi,\eta) =
  2 \int\_{\Kh} \diff(\xi,\eta) &&= 1\\\\\\
\hat{K}\_{2,2} &=
  \int\_{\Kh}\nabla\varphih\_2\cdot\overline{\nabla\varphih\_2} \diff (\xi,\eta) =
  \int\_{\Kh} (1,0)\begin{pmatrix}1\\\\\\ 0\end{pmatrix} \diff (\xi,\eta) =
    \int\_{\Kh} \diff(\xi,\eta) &&= \frac{1}{2} =\hat{K}\_{3,3}\\\\\\
\hat{K}\_{1,2} &=
  \int\_{\Kh}\nabla\varphih\_1\cdot\overline{\nabla\varphih\_2} \diff (\xi,\eta) =
  \int\_{\Kh} (-1,-1)\begin{pmatrix}1\\\\\\ 0\end{pmatrix} \diff (\xi,\eta) =
    -\int\_{\Kh} \diff(\xi,\eta) &&= -\frac{1}{2}\\\\\\
\hat{K}\_{1,3} &=
  \int\_{\Kh}\nabla\varphih\_1\cdot\overline{\nabla\varphih\_3} \diff (\xi,\eta) =
  \int\_{\Kh} (-1,-1)\begin{pmatrix}0\\\\\\ 1\end{pmatrix} \diff (\xi,\eta) =
    -\int\_{\Kh} \diff(\xi,\eta)&& = -\frac{1}{2}\\\\\\
\hat{K}\_{2,3} &=
  \int\_{\Kh}\nabla\varphih\_2\cdot\overline{\nabla\varphih\_3} \diff (\xi,\eta) =
  \int\_{\Kh} (1,0)\begin{pmatrix}0\\\\\\ 1\end{pmatrix} \diff (\xi,\eta) &&=
  0.
\end{aligned}
$$
{{< /thm/proof >}}

### Triangle quelconque

Pour calculer les dérivées partielles selon $x$ et $y$ de $\varphih\_{j}$, nous utilisons la dérivée de fonction composée :
$$
\left(
  \begin{array}{c}
    \dsp \frac{\partial \mphiK{p}{j}}{\partial x}\\\\\\
    \dsp \frac{\partial \mphiK{p}{j}}{\partial y}
  \end{array}
\right) = 
\left(
  \begin{array}{c c}
    \dsp \frac{\partial \xi}{\partial x} & \dsp \frac{\partial \eta}{\partial x}\\\\\\
    \dsp \frac{\partial \xi}{\partial y} & \dsp \frac{\partial \eta}{\partial y}
  \end{array}
\right)
\left(
  \begin{array}{c}
    \dsp \frac{\partial \varphih\_{j}}{\partial \xi}\\\\\\
    \dsp \frac{\partial \varphih\_{j}}{\partial \eta}
  \end{array}
\right)
$$
En notant $\BK{p}$ la matrice de passage, nous avons
$$
\nabla\_{x,y}\mphiK{p}{j}(x,y) = \BK{p}\nabla\_{\xi,\eta}\varphih\_{j}(\xi,\eta).
$$
L'opération "inverse" nous donne :
$$
  \left(
    \begin{array}{c}
      \dsp \frac{\partial \varphih\_{j}}{\partial \xi}\\\\\\
      \dsp \frac{\partial \varphih\_{j}}{\partial \eta}
    \end{array}
  \right)  =
  \left(
    \begin{array}{c c}
      \dsp \frac{\partial x}{\partial \xi} & \dsp \frac{\partial y}{\partial \xi}\\\\\\
      \dsp \frac{\partial y}{\partial \eta} & \dsp \frac{\partial y}{\partial \eta}
    \end{array}
  \right)
  \left(
    \begin{array}{c}
      \dsp \frac{\partial \mphiK{p}{j}}{\partial x}\\\\\\
      \dsp \frac{\partial \mphiK{p}{j}}{\partial y}
    \end{array}
  \right)
\iff
\nabla\_{\xi,\eta}\varphih\_{j}(\xi,\eta) = (\JK{p})^T\nabla\_{x,y}\mphiK{p}{j}(x,y).
$$
Nous en déduisons que $\BK{p} = (\JK{p}^T)^{-1}$, en particulier, dans le cas d'une transformation linéaire de triangle, nous obtenons :
$$
\BK{p} =
\frac{1}{\det(\JK{p})}
  \left(
  \begin{array}{c c}
    \yK{p}{3}-\yK{p}{1} & \yK{p}{1}-\yK{p}{2}\\\\\\
    \xK{p}{1}-\xK{p}{3} & \xK{p}{2}-\xK{p}{1}
  \end{array}
\right).
$$
Au final, comme $X\cdot Y = X^TY$, nous obtenons
\begin{equation}\label{eq:intRigidite}
\int\_{\tri{p}} (\nabla\mphiK{p}{j})^T\overline{\nabla\mphiK{p}{i}} \diff(x,y)
  = \abs{\det(\JK{p})}\int\_{\Kh} (\nabla\varphih\_{j})^T  (\BK{p}^T \overline{\BK{p}})\overline{\nabla\varphih\_{i}} \diff (\xi,\eta).
\end{equation}
La matrice $\BK{p}$ étant réelle, nous pouvons supprimer la conjugaison portant sur $\BK{p}$.

{{< thm/thm lemma >}}
Les coefficients a matrice de rigidité élémentaire $\Dep = (K^p\_{i,j})\_{1\leq i,j\leq 3}$ sont obtenus pas la relation suivante
$$
\begin{align}
K^p\_{i,j} &= \int\_{\tri{p}}\nabla \mphiK{p}{j}(x, y)\cdot \overline{\nabla\mphiK{p}{i}(x,y)}\diff(x,y),\\\\\\
  &= \abs{\tri{p}}(\nabla\varphih\_{j})^T  (\BK{p}^T \overline{\BK{p}})\overline{\nabla\varphih\_{i}}.
\end{align}
$$
{{< /thm/thm >}}
{{< thm/proof >}}
Pour les éléments finis $\Pun$, les gradients $\nabla\varphih\_{j}$ sont constants et peuvent être sortis de l'intégrale. De plus, comme $\abs{\det(\JK{p})} = 2\abs{\tri{p}}$ et $\abs{\Kh}= \frac{1}{2}$, nous avons
$$
\int\_{\tri{p}} \nabla\mphiK{p}{j}\cdot\overline{\nabla\mphiK{p}{i}} \diff(x,y) =
  \abs{\tri{p}}(\nabla\varphih\_{j})^T  (\BK{p}^T \overline{\BK{p}})\overline{\nabla\varphih\_{i}}.
$$
{{< /thm/proof>}}

## Quadratures

### Sur un triangle

Étudions maintenant les termes du membre de droite comme
$$
\int\_{\tri{p}}f(\xx)\overline{\mphiK{p}{i}(\xx)}\diff \xx.
$$
Sauf pour certaines fonctions $f$ particulières, nous ne pourrons certainement pas calculer explicitement ce terme, nous devons approcher cette intégrale à l'aide d'une formule de quadrature en passant à l'éléments de référence :
$$
\begin{array}{r l}
\dsp \int\_{\tri{p}}f(\xx)\overline{\mphiK{p}{i}(\xx)}\diff \xx &=
\dsp \abs{\det(\JK{p})}\int\_{\Kh}f(\xx(\xi,\eta))\overline{\varphih\_i(\xi,\eta)}\diff (\xi,\eta) \\\\\\
& \dsp \simeq \abs{\det(\JK{p})}\sum\_{m=1}^M\omega\_m f(\xx(\xi\_m,\eta\_m))\overline{\varphih(\xi\_m,\eta\_m)}.
\end{array}
$$
Les points $(\xi\_m,\eta\_m)$ sont appelés **points de quadrature** (parfois **points de Gauss**, même si la règle de quadrature utilisée n'est pas de Gauss) et les quantités $\omega\_m\in\Rb$ les **poids** associés. Notons que le point $\xx\_m = \xx(\xi\_m,\eta\_m)$ s'obtient par l'expression vue précédemment :
$$
\xx\_m = \sum\_{i=1}^3\sumitK{p}{i}\psih\_i(\xi\_m,\eta\_m).
$$
Nous présentons ici deux règles de quadrature pour l'intégrale $\int\_{\Kh}\gh(\xx)\diff\xx$ sur $\Kh$ d'une fonction $g$ quelconque. La première règle est exacte pour des polynômes de degré 1, la deuxième pour des polynômes de degré 2 (règles de Hammer) :

| $\xi\_m$ | $\eta\_m$ | $\omega\_m$ |Degré de précision|
| -------- | --------  | --------   | ---------------- |
|    1/3 | 1/3 | 1/6 | 1 |
|    1/6 | 1/6 | 1/6 | 2 |
|    4/6 | 1/6 | 1/6 |   |
|    1/6 | 4/6 | 1/6 |   |

###  Sur une arête

Voici quelques formules de quadrature sur un segment $[\sumitK{p}{1}, \sumitK{p}{2}]$ avec le degré de précision, *i.e* la formule est exacte si $g$ est un polynôme de degré égal ou inférieur. Nous notons $\abs{\sigma} = \norm{\sumitK{p}{1} - \sumitK{p}{2}}$ la taille du segment et $\sumitK{p}{12} = \frac{\sumitK{p}{1} + \sumitK{p}{2}}{2}$ le milieu du segment:

| Nom | Degré de <br>précision | $\dsp \int\_{\sumitK{p}{1}}^{\sumitK{p}{2}}g(x)\diff x\simeq \ldots$    |
| -------- |-------- |  ---------------- |
|    Point du milieu | 1| $\dsp g(\sumitK{p}{12})$ |
|    Trapèze | 1  | $\dsp\frac{\abs{\sigma}}{2}\left(g(\sumitK{p}{1}) + g(\sumitK{p}{2})\right)$ |
|    1/3 Simpson | 2 | $\dsp\frac{\abs{\sigma}}{6}\left(g(\sumitK{p}{1}) + 4g(\sumitK{p}{12}) + g(\sumitK{p}{2})\right)$ |


{{% callout note %}}
Les formules de quadrature ont évidemment un impact sur la qualité de l'approximation, toutefois, elles jouent un rôle relativement mineur par rapport aux autres approximations (et l'on peut choisir plus de points d'intégration !).
{{% /callout %}}


{{< js src="https://d3js.org/d3.v5.min.js" >}}
{{< js src="../js/jacobi/main.js" >}}
