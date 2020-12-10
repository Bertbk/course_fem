+++
title = "2. Éléments Finis P1-Lagrange"

date = 2018-09-09T00:00:00
# lastmod = 2018-24-11T00:00:00

draft = false  # Is this a draft? true/false
toc = true  # Show table of contents? true/false
type = "docs"  # Do not modify.

math = true
weight = 90
diagram = false
#markup = "mmark"


edit_page = {repo_url = "https://github.com/Bertbk/course_fem", repo_branch = "master", submodule_dir="content/course/fem/"}

[git]
  icon = "github"
  repo = "https://github.com/Bertbk/course_fem"
  submodule_dir = "content/course/fem/"


# Add menu entry to sidebar.
[menu.fem]
  parent = "III. Méthode des Éléments Finis"
  name = "2. P1-Lagrange"
  weight = 20

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
$\newcommand{\nn}{\mathbf{n}}$
$\newcommand{\ssb}{\mathbf{s}}$
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
$\newcommand{\Tscrh}{\mathscr{T}\_h}$
$\newcommand{\mphi}[1]{\varphi\_{#1}}$
$\newcommand{\uj}{u\_j}$
$\newcommand{\Sscrh}{\hme{\Sscr}}$
$\newcommand{\deltaij}{\delta\_{i,j}}$
$\newcommand{\Kp}{T\_p}$
$\newcommand{\Kq}{T\_q}$
$\newcommand{\Pun}{\Pb\_1}$
$\newcommand{\Punw}{\Pun(\omega)}$

## Contexte : Dimension 2

Dans cette section, on se place en dimension $2$ uniquement, même si les notions se généralisent à la dimension $3$ sans problème. Nous considérons toujours le problème de référence suivant :
$$
\left\\{
  \begin{array}{r c l l}
    -\Delta u + u & =& f & (\Omega), \\\\\\
    \dn u & = & 0 & (\partial \Omega),
  \end{array}
\right.
$$
et la formulation variationnelle associée
$$
\left\\{
  \begin{array}{l}
    \text{Trouver }u\in\Ho \text{ tel que }\\\\\\
    \forall v \in\Ho,\quad a(u,v) = \ell(v),
  \end{array}
\right.
$$
avec
$$
\begin{array}{r c l}
  \dsp a(u,v)  &= & \dsp \int\_{\Omega}\nabla u(x)\cdot\overline{\nabla v(x)}\diff x +
   \int\_{\Omega}u(x)\conj{v(x)}\diff x \\\\\\
  \dsp \ell(v) &=&\dsp \int\_{\Omega}f(x)\conj{v(x)}\diff x.
\end{array}
$$

## Triangulation

Nous construisons un espace discret $\hme{V}\subset\Ho$ basé sur les éléments finis $\Pun-$Lagrange. Celui-ci est basé sur un maillage triangulaire **conforme** du domaine $\Omega$. Pour simplifier, nous supposons également que $\partial\Omega$ est un polygône, de sorte que le maillage *épouse* parfaitement son bord.

{{< thm/thm definition >}}
Une triangulation ou un maillage triangulaire de $\Omega$ est un ensemble $\hme{\Tscr}$ de triangles $T$ vérifiant

1. $\dsp \overline{\Omega} = \cup\_{T\in\hme{\Tscr}}\overline{T}$
2. Tout triangle $T\in\hme{\Tscr}$ n'est pas dégénéré (*i.e.*: est d'intérieur non vide) et $T\subset\overline{\Omega}$ 
3. Pour $T,T' \in \hme{\Tscr}$, alors $T\cap T'$ est soit vide, soit réduite à un sommet commun, soit à la totalité d'une arête en commun
4. Toute arête d'un triangle $T\in\hme{\Tscr}$ est soit une arête complète d'un autre triangle $T'\in\hme{\Tscr}$, soit une partie de $\partial\Omega$.

De plus, nous noterons $\hme{\Sscr}$ l'ensemble des **sommets** ou **nœuds** du maillage $\hme{\Tscr}$ et par convention, le paramètre $h$ représente souvent le diamètre maximal des triangles $T$ de $\hme{\Tscr}$ (c’est-à-dire la plus grande distance entre deux éléments de T).
{{< /thm/thm >}}

Quand ce paramètre $h$ tend vers 0, le maillage devient plus dense et "plus proche" de $\Omega$. On dit que le maillage est *plus raffiné*. Cette notion sera détaillée ultérieurement quand nous étudierons la convergence de cette méthode. Des exemples d'éléments interdits et autorisés sont présentés sur la figure ci-dessous :

{{< figure src="../triangles.svg" title="Triangulation autorisée (gauche) et interdite (les autres)." numbered="true" >}}


## Espace des fonctions P<sub>1</sub>-Lagrange

Pour tout ouvert $\omega \subset \Rb^2$, nous introduisons l'espace de dimension 3 des polynômes de degré $1$ sur $\omega$ :
$$
\Punw = \enstq{p|\_\omega}{\exists a,b,c\in\Cb | \forall (x,y)\in\omega, p(x,y) = ax + by + c}
$$
Nous pouvons maintenant construire l'espace des fonctions $\Pun-$Lagrange des fonctions localement $\Pun$ sur chaque triangle.

{{< thm/thm definition >}}
Pour une triangulation $\hme{\Tscr}$, l'espace $\hme{V}$ des fonctions $\Pun-$Lagrange est défini par:
$$
\hme{V} = \enstq{v\in\Cscr^0(\overline{\Omega})}{v|\_{T}\in\Pun(T) \text{ pour tout } T\in\hme{\Tscr}}.
$$
{{< /thm/thm >}}
{{% callout warning %}}
Une fonction de $\hme{V}$ **n'est pas affine** mais affine **par morceaux**. Il faut bien noter que les paramètres $a,b,c$ du polynôme $p\_T$ dépendent du triangle $T$ considéré !
{{% /callout %}}


{{< thm/thm lemma >}}
L'ensemble $\hme{V}$ est inclus dans $\Ho$.
{{< /thm/thm >}}
{{< thm/proof >}}
Soit $v\in \hme{V}$ alors $v$ est continue sur $\overline{\Omega}$ (qui est borné) et appartient donc à $\Lo$. Il nous faut montrer que $v$ admet des dérivées faibles dans $\Lo$. Sur chaque triangle $T$ de $\hme{\Tscr}$, la fonction $v$ est $\Cscr^{\infty}$ et admet donc une dérivée forte, et donc faible. Par la formule de Green, pour $\varphi\in \CinfcO$ :
$$
  \int\_{\Omega} v(x) \frac{\partial \varphi}{\dxi}\diff x =
  \sum\_{T\in\hme{\Tscr}}\int\_{T} v\restrict\_T(x) \frac{\partial \varphi}{\dxi}\diff x = 
  \sum\_{T\in\hme{\Tscr}}\left(-\int\_{T} \frac{\partial v\restrict\_T}{\dxi}\varphi(x)\diff x + 
  \int\_{\partial T} v\restrict\_T(x)\varphi(x)n\_{i,T}\diff x\right),
$$
où $n\_{i,T}$ est la $i^{\mathrm{ème}}$ composante de la normale extérieure à $T$. 

On s'intéresse ici uniquement au terme d'intégrale sur le bord et on sépare les contributions du bord de $\Omega$ de celle des arêtes internes :
$$
\sum\_{T\in\hme{\Tscr}}\int\_{\partial T} v\restrict\_T(x)\varphi(x)n\_{i,T}\diff x =
\sum\_{T\in\hme{\Tscr}}\left(\int\_{\partial T\cap\partial\Omega} v\restrict\_T(x)\varphi(x)n\_{i,T}\diff x +
\int\_{\partial T\setminus(\partial T\cap\partial\Omega)} v\restrict\_T(x)\varphi(x)n\_{i,T}\diff x\right).
$$
Comme $\varphi\in\CinfcO$ alors $\varphi\restrict\_{\partial\Omega} = 0$ et l'expression ci-dessus se réduit à une somme d'intégrales sur les arêtes "internes" de $\hme{\Tscr}$ : 
$$
\sum\_{T\in\hme{\Tscr}}\int\_{\partial T} v \restrict\_{T}(x)\varphi(x)n\_{i,T}\diff x =
\sum\_{T\in\hme{\Tscr}}\int\_{\partial T\setminus(\partial T\cap\partial\Omega)} v\restrict\_{T}(x)\varphi(x)n\_{i,T}\diff x.
$$
Notons $\Sigma$ l'ensemble des arêtes "internes" $\sigma$ de $\hme{\Tscr}$, autrement dit une arête $\sigma$ appartient à deux triangles $\Kp$ et $\Kq$ et, de plus, sur $\sigma$, les normales $\nn\_p$ et $\nn\_q$ sont opposées : $n\_{i,\Kp} = -n\_{i, \Kq}$. Alors on a :
    $$
      \sum\_{T\in\hme{\Tscr}}\int\_{\partial T} v\restrict\_T(x)\varphi(x)n\_{i,T}\diff x
      =  \sum\_{\sigma\in\Sigma}\int\_{\sigma} (v\restrict\_{\Kp}(x) - v\restrict\_{\Kq}(x))\varphi(x) n\_{i,\Kp}\diff x.
    $$
  Comme $v$ est continue sur $\Omega$, alors le terme ci-dessus est nul et nous avons, pour tout $\varphi$ de $\CinfcO$:
  $$
\int\_{\Omega} v(x) \frac{\partial \varphi}{\dxi}\diff x = 
\sum\_{T\in\hme{\Tscr}}-\int\_{T} \frac{\partial v\restrict\_T}{\dxi}\varphi(x)\diff x.
$$
Nous en déduisons que
$$
\forall T\in\hme{\Tscr},\qquad \left.\frac{\partial v}{\dxi}\right|_T = \frac{\partial v\restrict\_T}{\dxi}
$$
Les fonctions $v$ de $\hme{V}$ admettent donc des dérivées au sens faible et qui sont dans $\Lo$, autrement dit, nous avons bien $v\in\Ho$.
{{< /thm/proof >}}
Il ne nous reste plus qu'à caractériser l'espace $\hme{V}$.

## Caractérisation des fonctions de V<sub>h</sub>

{{< thm/thm proposition >}}
Soit $T$ un triangle non dégénéré de $\Rb^2$ de sommets $\ssb\_1,\ssb\_2,\ssb\_3$. Alors, pour tout jeu de données $\alpha\_1,\alpha\_2,\alpha\_3 \in \Cb$, il existe un unique polynôme de $p\in\Pun(\Rb^2)$ tels que $p(\ssb\_i)=\alpha\_i$.
{{< /thm/thm >}}
{{< thm/proof >}}
En notant $\ssb\_i = (x_i,y_i)$ et $p(x,y) = ax + by + c$  avec $a,b,c\in\Cb$, alors le problème revient à résoudre le système linéaire
$$
\left\\{
  \begin{array}{r c l}
    ax\_1 + by\_1 + c &=& \alpha\_1\\\\\\
    ax\_2 + by\_2 + c &=& \alpha\_2\\\\\\
    ax\_3 + by\_3 + c &=& \alpha\_3
  \end{array}
\right.
\iff
\left(
  \begin{array}{c c c}
    x\_1 & y\_1 & 1\\\\\\
    x\_2 & y\_2 & 1\\\\\\
    x\_3 & y\_3 & 1
  \end{array}
\right)
\left(
  \begin{array}{c}
    a\\\\\\
    b\\\\\\
    c
  \end{array}
\right)  =
\left(
  \begin{array}{c}
    \alpha\_1\\\\\\
    \alpha\_2\\\\\\
    \alpha\_3
  \end{array}
\right)
$$
Le déterminant d'un tel système n'est autre que [deux fois l'aire du triangle](https://fr.wikipedia.org/wiki/Aire_d%27un_triangle) :
$$
\Delta = 
\left|
  \begin{array}{c c c}
    x\_1 & y\_1 & 1\\\\\\
    x\_2 & y\_2 & 1\\\\\\
    x\_3 & y\_3 & 1
  \end{array}
\right| = 2\mathrm{Aire}(T) \neq 0
$$
car le triangle T n'est pas dégénéré. Donc le système est inversible et admet une unique solution $(a,b,c)$.
{{< /thm/proof >}}

Le résultat qui suit montre que deux fonctions de $\hme{V}$ sont égales si et seulement si elles coïncident sur tous les sommets de la triangulation $\hme{\Tscr}$.
{{< thm/thm lemma >}}
Si $\uh,\vh \in \hme{V}$ vérifient $\uh(\ssb) = \vh(\ssb)$ pour tout sommet $\ssb$ de $\hme{\Tscr}$, alors $\uh=\vh$ sur $\Omega$.
{{< /thm/thm >}}
{{< thm/proof >}}
En se plaçant sur le triangle $T = (\ssb\_1,\ssb\_2,\ssb\_3)$ de $\hme{\Tscr}$, nous avons $\uh(\ssb\_i) = \vh(\ssb\_i)$ pour $i=1,2,3$. Le Lemme précédent implique alors que $\uh\restrict\_{T}=\vh\restrict\_{T}$. Le triangle $T$ étant arbitraire, cette relation vaut sur tous les éléments de la triangulation et donc sur $\Omega$ tout entier.
{{< /thm/proof >}}

{{< thm/thm proposition >}}
Pour tout jeu de données complexes $(\alpha\_\ssb)_{\ssb\in\hme{\Sscr}}$, il existe une unique fonction $\uh\in \hme{V}$ vérifiant $\uh(\ssb) = \alpha\_{\ssb}$ pour tout sommet $\ssb$ de $\hme{\Tscr}$.
{{< /thm/thm >}}
{{< thm/proof >}}
L'unicité est démontrée précédemment, il manque donc l'existence. Quitte à renuméroter, prenons un triangle $T=(\ssb\_1,\ssb\_2,\ssb\_3)$ de $\hme{\Tscr}$ et le jeu de valeurs associé $(\alpha\_1,\alpha\_2,\alpha\_3) \in \Cb$. La proposition précédente montre qu'il existe un (unique) polynôme $p\_T$ de $\Pun(T)$ tel que $p\_T(\ssb\_i)=\alpha\_i$ pour $i=1,2,3$. Nous pouvons répéter cette opération pour tous les triangles $T$ et nous introduisons $\uh$ tel que
$$
\forall T\in\hme{\Tscr},\quad \uh \restrict\_{T} = p\_T.
$$
La fonction $\uh$ est affine sur chaque triangle, il nous faut montrer que $\uh\in\Cscr^0(\overline{\Omega})$ pour conclure sur son appartenance à $\hme{V}$. Comme $\uh$ est continue en chaque sommet $\ssb$, il reste à montrer la continuité sur les arêtes.

Prenons $2$ triangles $T$ et $T'$ de $\hme{\Tscr}$ ayant une arête $\Sigma$ en commun. Quitte à renuméroter, notons $\ssb\_1 = (x\_1,y\_1)$ et $\ssb\_2 = (x\_2, y\_2)$ les deux sommets de l'arête $\Sigma$ et notons
$$
  \sigma(t) = \ssb\_1 + t(\ssb\_2-\ssb\_1) =
  \left( x\_1 + t(x\_2-x\_1),  y\_1 + t(y\_2-y\_1)\right)
$$
une paramétrisation de $\Sigma$. Si $p\_T(x,y) = ax+by+c$, nous avons alors, pour tout $t\in[0,1]$ :
$$
\begin{array}{r l}
  p\_T(\sigma(t)) &= a (x\_1 + t(x\_2-x\_1)) + b (y\_1 + t(y\_2-y\_1)) + c\\\\\\
  &= a (x\_1 + t(x\_2-x\_1)) + b (y\_1 + t(y\_2-y\_1)) + c + t(c-c)\\\\\\
  &= [a x\_1+by\_1 +c] + t([a x\_2+by\_2 +c] +[ a\_T x\_1+by\_1 +c])\\\\\\
  &=  p\_T(\ssb\_1) +t(p\_T(\ssb\_2) - p\_T(\ssb\_1))\\\\\\
  &=  p\_{T'}(\ssb\_1) +t(p\_{T'}(\ssb\_2) - p\_{T'}(\ssb\_1))\\\\\\
  &=  p\_{T'}(\sigma(t)).
\end{array}
$$
Autrement dit, les deux polynômes $p\_T$ et $p\_T'$ sont égaux sur l'arête $\Sigma$ et $\uh$ est bien continue sur toutes les arêtes de $\hme{\Tscr}$ en plus de l'être sur tous les triangles et tous les sommets : $\uh$ est donc bien **continue** sur tout $\overline{\Omega}$.
{{< /thm/proof >}}

## Fonctions de forme

Au vu de ce qui précède, deux fonctions de $\Vh$ sont identiques si et seulement si elles possèdent la même valeur sur chaque sommet de $\Tscrh$. En notant $\hme{N} = \mathrm{card}(\Sscrh)$, introduisons l'ensemble des fonctions de forme $(\mphi{j})\_{1\leq j \leq \hme{N}}$ de $\Vh$ défini par
$$
\forall i,j =1,..., \hme{N},\quad
\mphi{j}(\ssb\_i) =
\delta\_{i,j}=
\left\\{
  \begin{array}{l l}
    1 & \text{ si } i=j\\\\\\
    0 & \text{ sinon.}
  \end{array}
\right.
$$
Ces fonctions sont la généralisation en 2D des *fonctions chapeau* unidimensionnelles (elles ressemblent d'ailleurs encore plus à un "chapeau").
{{< thm/thm proposition >}}
  L'espace $\Vh$ est un sous-espace de $H^1(\Omega)$ de dimension $\hme{N}$, le nombre de sommets de la triangulation $\Tscrh$. De plus, la famille $(\mphi{j})_{1\leq j \leq \hme{N}}$ est une base de $\Vh$
\{{< /thm/thm >}}
{{< thm/proof >}}
La première partie a déjà été démontrée, il ne reste plus qu'à montrer le fait que la famille de fonction $(\mphi{i})$ est une base de $\Vh$. Cette famille est libre puisque, pour une famille de données complexes $(\alpha\_i)_{1\leq i\leq \hme{N}}$,
$$
\begin{array}{r l}
  \dsp \sum\_{i=1}^{\hme{N}} \alpha\_i \mphi{i} = 0 &\iff \dsp \forall j=1,\ldots, \hme{N},\quad \sum\_{i=1}^{\hme{N}} \alpha\_i\mphi{i}(\ssb_j) = 0\\\\\\
  &\iff \dsp \forall j= 1,\ldots, \hme{N},\quad \alpha_j \times 1 +  \sum\_{i=1, i\neq j}^{\hme{N}}\alpha\_j\times 0 = 0\\\\\\
  & \iff \dsp \forall j= 1,\ldots, \hme{N},\quad \alpha\_j = 0
\end{array}
$$

La famille de fonctions $(\mphi{i})\_{1\leq i \leq \hme{N}}$ est libre. Pour montrer qu'elle est génératrice, prenons une fonction $\uh\in \Vh$ et plaçons nous sur le triangle $T = (\ssb\_1, \ssb\_2,\ssb\_3)$ (quitte à renuméroter). Le polynôme $\sum\_{i=1}^3\uh(\ssb\_i)\mphi{i}\restrict\_{T}$ coïncide avec $\uh$ sur les sommets du triangle $T$, et donc nous avons l'égalité des polynômes sur tout le triangle :
$$
\uh\restrict\_T = \sum\_{i=1}^3(\uh(\ssb\_i)\mphi{i}\restrict\_{T}).
$$
Cette relation étant valable sur un triangle arbitraire, elle est vraie sur $\Omega$. La famille de fonctions $(\mphi{i})\_i$ est donc une base de $\Vh$.
{{< /thm/proof >}}

{{< thm/thm lemma >}}
Le support d'une fonction de forme $\mphi{j}$ est l'union des triangles ayant pour sommet $\ssb\_j$ :
$$
\mathrm{supp}(\mphi{j}) = \enstq{T\in \Sscrh}{\ssb\_j \text{ est un sommet de } T}.
$$
Autrement dit, en dehors de ces triangles, la fonction $\mphi{j}$ est nulle.
{{< /thm/thm >}}

{{< thm/proof >}}
Prenons une fonction de forme $\mphi{j}$ associée au sommet $\ssb\_j$, et un triangle $T$ dont aucun sommet n'est $\ssb\_j$. Alors dans ce cas, $\mphi{j}$ est nulle sur les trois sommets de $T$, et est donc nulle sur le triangle tout entier.
{{< /thm/proof >}}


## Fonctions de Forme : Illustration interactive

{{< figure class="app-basis-function" title="<i class='fas fa-play-circle'></i> **Time To Play!**<br>**Cliquez sur un sommet** pour faire apparaitre la **fonction de forme P1 associé**. Les triangles où la fonction n'est pas nulle forment **le support de la fonction de forme**." numbered="true" >}}

{{< js src="https://d3js.org/d3.v5.min.js" >}}
{{< js src="https://d3js.org/d3-scale-chromatic.v1.min.js" >}}
{{< js src="../js/basis_function/main.js" >}}