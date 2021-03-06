---
titre: TP injection NFP121 et GLG204
description: Injection de dépendances, Inversion de contrôle
---


<div class="question">
<img src="images/question1.png" height="80" width="101">
Le framework femtoContainer</div>

<div class="paragraphe">
				&nbsp;
				<b><font size="5">Préambule:</font></b><font size="5"> lire, étudier l'article de  Martin Fowler</font>

<font size="3">&nbsp;&nbsp;&nbsp;
<a href="http://www.martinfowler.com/articles/injection.html">
http://www.martinfowler.com/articles/injection.html</a></font>


<font size="3">Ce TP est accompagné d'une séance d'exercices dirigés sur le même thème</font><blockquote><font size="3">&nbsp; cf. ed injection_dependances_IoC</font>

<p align="left"><font size="4">&nbsp;&nbsp;&nbsp; Dans le paragraphe <b>

<font color="#990044">Setter Injection with
<a href="https://fr.wikipedia.org/wiki/Spring_%28framework%29">Spring</a></font></b> 
l'auteur, Martin Fowler, présente un exemple de configuration de classes à l'aide du framework 
Spring. L'exemple de l'article effectue une recherche d'un titre de film à 
l'aide du nom du réalisateur, la liste des films peut se présenter selon plusieurs 
formats: du fichier texte au format CSV ou avec d'autres séparateurs ou encore 
en base de données ou bien sur 
le web. </font>
			
            <p align="center">
                <font size="4">
<img src="images/wp6s9svj.gif" border="0" height="270" width="512"></font>
            
			

<font size="4">L'usage d'un framework de configuration, 
comme Spring, 
permet de s'affranchir du format de la liste des films, ce choix étant déterminé 
au moment de l'exécution depuis un fichier de configuration. Une séparation claire 
est ainsi effectuée entre la configuration des classes et leur utilisation.</font>


<font size="4">Le fichier de configuration Spring est en XML (spring.xml) :</font>


<blockquote>
  <pre>&lt;beans&gt;<br>    &lt;bean id="<b><font color="#006699" size="3">MovieLister</font></b>" class="spring.MovieLister"&gt;<br>        &lt;property name="<span style="color: rgb(153, 0, 0);">finder"&gt;<br>            &lt;ref local="MovieFinder"/&gt;    <i><b><font color="#0000ff">            &lt;-- ce sera un "bean" défini dans le même contexte</font></b></i>
        &lt;/property&gt;
    &lt;/bean&gt;
    &lt;bean id="<b><font color="#006699" size="3">MovieFinder</font></b>" class="spring.ColonMovieFinder"&gt;  <i><b><font color="#0000ff">&lt;-- le format du fichier est indiqué ici</font></b></i><br>        &lt;property name="<span style="color: rgb(153, 0, 0);">filename"&gt;<br>            &lt;value&gt;movies1.txt&lt;/value&gt;<br>        &lt;/property&gt;<br>    &lt;/bean&gt;<br>&lt;/beans&gt;</pre>
</blockquote>


<font size="4">La lecture de ce fichier indique, que nous avons deux beans :
</font>


<blockquote>
  <ul>
    <li><font size="4"><b><font color="#006699">MovieLister</font></b> et <b>
    <font color="#006699">MovieFinder</font></b></font></li>
    <li><font size="4">dont les propriétés sont respectivement <span style="color: rgb(153, 0, 0);">finder et 
    <span style="color: rgb(153, 0, 0);">filename</font></li>
    <li><font size="4">les mutateurs(setter) associés sont exécutés par Spring : 
    setFinder et setFilename, avec les paramètres indiqués.</font></li>
  </ul>
</blockquote>


<font size="4">En java, ci-dessous l'usage de Spring, extrait de l'article :</font>


<blockquote>
  <pre>public void testWithSpring() throws Exception {<br>    ApplicationContext ctx = new FileSystemXmlApplicationContext("spring.xml");<br>    MovieLister lister = (MovieLister) ctx.getBean("<b><font color="#006699">MovieLister</font></b>");<br>    Movie[] movies = lister.moviesDirectedBy("Sergio Leone");<br>    assertEquals("Once Upon a Time in the West", movies[0].getTitle());<br>}</pre>
</blockquote>


<font size="4">&nbsp;&nbsp;&nbsp; Nous souhaitons utiliser un <span style="font-style: italic;">framework analogue, mais simplifié, développé pour cette unité, basé, comme Spring, sur l'injection de dépendances par les 
mutateurs(setter), les mêmes noms de classe ou d'interface <i>ApplicationContext</i> 
et noms de méthode <i>getBean</i>, <i>getType</i> ont été repris depuis Spring.</font>



<font size="4">Dans la suite de cet énoncé, nous  nommerons ce framework : <font color="#990000">femtoContainer</font>.</font>


<font size="4">Le projet Bluej associé au tp</font>


            <blockquote>


<img src="images/tp_inj1.jpg" border="0" height="282" width="545">


le paquetage container contient femtoContainer, le paquetage config_editor un 
éditeur minimaliste de fichiers de configuration

</blockquote>


<font size="4">&nbsp;&nbsp; Ci-dessous deux exemples avec <font color="#990000">femtoContainer</font>, celui de l'article où l'on cherche toujours le film de Sergio Leone et un autre 
exemple d'une table avec une capacité limitée, table dont le comportement est issu du fichier de configuration, 
cette table peut comporter des doublons ou non. 
<br>
&nbsp;&nbsp; Vous trouverez dans le projet BlueJ dans le paquetage question1 un exemple de patron commande dont le 
choix des receveurs et des commandes  concrètes est déterminé 
dans la configuration.</font>




<br>

 <!-- FIN DE CODE JAVA - JavaVersHtml --><font size="4">Exemple (1) à la recherche d'un 
réalisateur,</font>




<font size="4">Un extrait de la classe de tests unitaires</font>


<div style="border: 1px solid black; margin: 10px; padding: 10px; background: rgb(255, 255, 255) none repeat scroll 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: rgb(0, 0, 0);"> 
<pre><font color="#aa0000"><b>package</b></font> question1;<br><br><font color="#aa0000"><b>import</b></font> java.util.*;<br><br><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> ApplicationContextTest <font color="#aa0000"><b>extends</b></font> junit.framework.TestCase{<br><br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> testWithOurContainer() <font color="#990044"><b>throws</b></font> Exception{<br>    ApplicationContext ctx = Factory.createApplicationContext();<br>    MovieLister lister = ctx.getBean(<font color="#00aa00">"MovieLister"</font>);<br>    List&lt;Movie&gt; movies = lister.moviesDirectedBy(<font color="#00aa00">"Sergio Leone"</font>);<br>    assertEquals(<font color="#00aa00">"Once Upon a Time in the West"</font>,movies.get(0).getTitle());<br>  }<br><br>}<br></pre></div>
 <!-- FIN DE CODE JAVA - JavaVersHtml -->


 <!-- FIN DE CODE JAVA - JavaVersHtml -->
            
            
            
            <b>L'exemple de l'article, suivi du fichier de configuration</b>
<div style="border: 1px solid black; margin: 10px; padding: 10px; background: rgb(255, 255, 255) none repeat scroll 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: rgb(0, 0, 0);"> 
<pre><font color="#aa0000"><b>import</b></font> java.util.*;<br><br><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> ColonMovieFinder <font color="#aa0000"><b>implements</b></font> MovieFinder {<br>  <font color="#990044"><b>private</b></font> String filename;<br>  <br>  <font color="#990044"><b>public</b></font> ColonMovieFinder() {<br>  }<br>  <br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> setFilename(String filename) {<br>    this.filename = filename;<br>  }<br>  <br>  <font color="#990044"><b>public</b></font> List&lt;Movie&gt; findAll(){<br>    List&lt;Movie&gt; list = <font color="#990044"><b>new</b></font> ArrayList&lt;Movie&gt;();<br>    <font color="#777777"><i>// la liste simulée extraite d'un fichier texte</i></font>
    list.add(<font color="#990044"><b>new</b></font> Movie(<font color="#00aa00">"Dans les forêts de Sibérie"</font>,<font color="#00aa00">"NEBBOU Safy"</font>));<br>    list.add(<font color="#990044"><b>new</b></font> Movie(<font color="#00aa00">"Demain"</font>,<font color="#00aa00">"LAURENT Mélanie / DION Cyril"</font>));<br>    list.add(<font color="#990044"><b>new</b></font> Movie(<font color="#00aa00">"Once Upon a Time in the West"</font>,<font color="#00aa00">"Sergio Leone"</font>));<br>    <font color="#990044"><b>return</b></font> list;<br>  }<br>  <br>  <font color="#990044"><b>public</b></font> String toString(){<br>    <font color="#990044"><b>return</b></font> <font color="#00aa00">"ColonMovieFinder"</font>;<br>  }<br>}</pre> 
<pre>__________________________________________________________________</pre> 
<pre><font size="4"><font color="#aa0000"><b><font size="2">import</font></b></font><font size="2"> java.util.*;<br><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> MovieLister{<br>  <font color="#990044"><b>private</b></font> MovieFinder finder;   <br><br>  <br>  <font color="#990044"><b>public</b></font> MovieLister() {<br>  }<br>  <br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> setFinder(MovieFinder finder) {<br>    this.finder = finder;<br>  }<br>  <br>  <font color="#990044"><b>public</b></font> List&lt;Movie&gt; moviesDirectedBy(String arg) {<br>    List&lt;Movie&gt; allMovies = finder.findAll();<br>    <font color="#990044"><b>for</b></font> (Iterator&lt;Movie&gt; it = allMovies.iterator(); it.hasNext();) {<br>        Movie movie = it.next();<br>        <font color="#990044"><b>if</b></font> (!movie.getDirector().equals(arg)) it.remove();<br>    }<br>    <font color="#990044"><b>return</b></font> allMovies;<br>  }<br>}</font></font></pre> 
<pre>__________________________________________________________________</pre> 
<pre><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>interface</b></font> MovieFinder {<br>    List&lt;Movie&gt; findAll();<br>}</pre> 
<pre>__________________________________________________________________</pre> 
<pre><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> Movie{<br>    <font color="#990044"><b>private</b></font> String title;<br>    <font color="#990044"><b>private</b></font> String director;<br><br>  <font color="#990044"><b>public</b></font> Movie(String title, String director){<br>    this.title = title;<br>    this.director = director;<br>  }<br>  <br>  <font color="#990044"><b>public</b></font> String getTitle(){<br>    <font color="#990044"><b>return</b></font> title;<br>  }<br>  <br>  <font color="#990044"><b>public</b></font> String getDirector(){<br>    <font color="#990044"><b>return</b></font> director;<br>  }<br>}</pre> 
<pre>&nbsp;</pre> 
</div>
 <!-- FIN DE CODE JAVA - JavaVersHtml -->
            
            


<font size="4">

&nbsp;<!-- DEBUT DE CODE JAVA - JavaVersHtml -->
<!-- DEBUT DE CODE JAVA - JavaVersHtml -->
&nbsp;</font><font size="5"><!-- FIN DE CODE JAVA - JavaVersHtml --><!-- DEBUT DE CODE JAVA - JavaVersHtml -->
<img src="images/tp_inj22.jpg" border="0" height="574" width="553"></font>


<font size="4">
Exemple (2) une table au comportement issu du fichier de configuration</font><font size="5"> </font>
(cf. README.TXT), ce fichier a été choisi pour sa présence en icône depuis&nbsp; 
bluej<font size="5"><div style="border: 1px solid black; margin: 10px; padding: 10px; background: rgb(255, 255, 255) none repeat scroll 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: rgb(0, 0, 0);"> 
<pre><font size="3">
</font><font size="2"><font color="#aa0000"><b>import</b></font> java.util.Collection;<br><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> Table&lt;T&gt;{<br>  <font color="#990044"><b>private</b></font> Collection&lt;T&gt; liste;<br>  <font color="#990044"><b>private</b></font> <font color="#ff0000"><b>int</b></font> capacite;<br>  <br>  <font color="#990044"><b>public</b></font> Table(){}<br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> setListe(Collection&lt;T&gt; liste){<br>    this.liste = liste;<br>  }<br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> setCapacite(<font color="#ff0000"><b>int</b></font> capacite){<br>    this.capacite = capacite;<br>  }<br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> ajouter(T t){<br>    <font color="#990044"><b>if</b></font>(liste.size()==capacite) <font color="#990044"><b>throw</b></font> <font color="#990044"><b>new</b></font> RuntimeException(<font color="#00aa00">"capacity reached"</font>);<br>    liste.add(t);<br>  }<br>  <font color="#990044"><b>public</b></font> String toString(){<br>    <font color="#990044"><b>return</b></font> liste.toString();<br>  }<br>}</font></pre> 
<pre><font color="#aa0000"><b><font size="2">package</font></b></font><font size="2"> question1;<br><br><font color="#aa0000"><b>import</b></font> java.util.*;<br><br><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> TestsTable <font color="#aa0000"><b>extends</b></font> junit.framework.TestCase{<br><br><br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> testTableArrayList() <font color="#990044"><b>throws</b></font> Exception{<br>    <font color="#990044"><b>try</b></font>{<br>      ApplicationContext ctx = Factory.createApplicationContext("./question1/exemples/README.TXT");<br>      Table&lt;Integer&gt; table = ctx.getBean(<font color="#00aa00">"table"</font>);<br>      table.ajouter(4);table.ajouter(2);table.ajouter(3);table.ajouter(1);<br>      assertEquals(<font color="#00aa00">"[4, 2, 3, 1]"</font>,table.toString());<br>      <font color="#990044"><b>try</b></font>{<br>        table.ajouter(5);<br>        fail(<font color="#00aa00">"Une exception doit être levée, la capacité de la table est atteinte..."</font>);<br>      }<font color="#990044"><b>catch</b></font>(Exception e){<br>      }<br>      assertEquals(Table.class,ctx.getType(<font color="#00aa00">"table"</font>));<br>      Table&lt;Integer&gt; table2 = ctx.getBean(<font color="#00aa00">"table"</font>);<br>      assertSame(table, table2);<br>    }<font color="#990044"><b>catch</b></font>(Exception e){<br>      fail(<font color="#00aa00">"Exception inattendue :"</font> + e.getMessage());<br>    }<br>  }<br><br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> testTableArrayList2() <font color="#990044"><b>throws</b></font> Exception{<br>    <font color="#990044"><b>try</b></font>{<br>      ApplicationContext ctx = Factory.createApplicationContext("./question1/exemples/README.TXT");<br>      Table&lt;Integer&gt; table = ctx.getBean(<font color="#00aa00">"table2"</font>);<br>      assertEquals(Table.class,ctx.getType(<font color="#00aa00">"table2"</font>));<br>      table.ajouter(4);table.ajouter(2);table.ajouter(2);table.ajouter(1);<br>      assertEquals(<font color="#00aa00">"[4, 2, 2, 1]"</font>,table.toString());<br>      <font color="#990044"><b>try</b></font>{<br>        table.ajouter(5);<br>        fail(<font color="#00aa00">"Une exception doit être levée, la capacité de la table est atteinte..."</font>);<br>      }<font color="#990044"><b>catch</b></font>(Exception e){<br>      }<br>    }<font color="#990044"><b>catch</b></font>(Exception e){<br>      fail(<font color="#00aa00">"Exception inattendue :"</font> + e.getMessage());<br>    }<br>  }<br><br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> testTableSet() <font color="#990044"><b>throws</b></font> Exception{<br>    <font color="#990044"><b>try</b></font>{<br>      ApplicationContext ctx = Factory.createApplicationContext("./question1/exemples/README.TXT");<br>      Table&lt;Integer&gt; table = ctx.getBean(<font color="#00aa00">"tableSet"</font>);<br>      table.ajouter(4);table.ajouter(2);table.ajouter(2);table.ajouter(1);<br>      assertEquals(<font color="#00aa00">"[1, 2, 4]"</font>,table.toString());<br>      <font color="#990044"><b>try</b></font>{<br>        table.ajouter(3);<br>        assertEquals(<font color="#00aa00">"[1, 2, 3, 4]"</font>,table.toString());<br>        table.ajouter(6);<br>        fail(<font color="#00aa00">"Une exception doit être levée, la capacité de la table est atteinte..."</font>);<br>      }<font color="#990044"><b>catch</b></font>(Exception e){<br>      }<br>      assertEquals(Table.class,ctx.getType(<font color="#00aa00">"table"</font>));<br>    }<font color="#990044"><b>catch</b></font>(Exception e){<br>      fail(<font color="#00aa00">"Exception inattendue :"</font> + e.getMessage());<br>    }<br>  }	<br><br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> testContainerContent()<font color="#990044"><b>throws</b></font> Exception{<br>    <font color="#990044"><b>try</b></font>{<br>      ApplicationContext ctx = Factory.createApplicationContext("./question1/exemples/README.TXT");<br>      List&lt;String&gt; liste = <font color="#990044"><b>new</b></font> ArrayList&lt;String&gt;();<br>      <font color="#990044"><b>for</b></font>(String bean : ctx){ <br>        liste.add(bean);<br>      }<br>      assertTrue(liste.contains(<font color="#00aa00">"MovieLister"</font>));<br>      assertTrue(liste.contains(<font color="#00aa00">"MovieFinder"</font>));<br>      assertTrue(liste.contains(<font color="#00aa00">"table"</font>));<br>    }<font color="#990044"><b>catch</b></font>(Exception e){<br>      fail(<font color="#00aa00">"Exception inattendue :"</font> + e.getMessage());<br>    }<br>  }<br><br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> testTabelSet2()<font color="#990044"><b>throws</b></font> Exception{<br>    <font color="#990044"><b>try</b></font>{<br>      ApplicationContext ctx = Factory.createApplicationContext("./question1/exemples/README.TXT");<br>      Table&lt;Integer&gt; table = ctx.getBean(<font color="#00aa00">"tableSet"</font>);<br>      table.ajouter(4);table.ajouter(2);table.ajouter(2);table.ajouter(1);<br>      assertEquals(<font color="#00aa00">"[1, 2, 4]"</font>,table.toString());<br><br>      ApplicationContext ctx2 = Factory.createApplicationContext();<br>      Table&lt;Integer&gt; table2 = ctx2.getBean(<font color="#00aa00">"tableSet"</font>);<br>      table2.ajouter(4);table2.ajouter(2);table2.ajouter(2);table2.ajouter(1);<br>      assertEquals(<font color="#00aa00">"[1, 2, 4]"</font>,table2.toString());<br><br>      table.ajouter(3);<br>      assertEquals(<font color="#00aa00">"[1, 2, 3, 4]"</font>,table.toString());<br>      assertEquals(<font color="#00aa00">"[1, 2, 4]"</font>,table2.toString());<br>    }<font color="#990044"><b>catch</b></font>(Exception e){<br>      fail(<font color="#00aa00">"Exception inattendue :"</font> + e.getMessage());<br>    }<br>  }<br>}<br></font>
</pre></div>
 <!-- FIN DE CODE JAVA - JavaVersHtml --></font><font size="4">
Ci-dessous, le fichier de configuration, ici à l'aide d'un fichier de <i>properties.</i>
<blockquote>
  <blockquote>
<font color="#0000ff"><i># le fichier de configuration en xml, cf. 
l'article de Martin Fowler</i></font><br>
#&lt;beans&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp; &lt;bean id="MovieLister" class="MovieLister"&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;property name="finder"&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;ref 
local="MovieFinder"/&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;/property&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp; &lt;/bean&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp; &lt;bean id="MovieFinder" class="ColonMovieFinder"&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;property name="filename"&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 
&lt;value&gt;movies1.txt&lt;/value&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &lt;/property&gt;<br>
#&nbsp;&nbsp;&nbsp;&nbsp; &lt;/bean&gt;<br>
#&lt;/beans&gt;<br>
&nbsp;
<font color="#0000ff"><i># Adapté au properties de java le fichier de configuration<br>
# fichier martin_fowler.README.TXT</i></font><br>
<font color="#0000ff"><i># MovieLister identifiant du bean</i></font><br>
bean.id.1=<font color="#aa0000">MovieLister</font><br>
<font color="#0000ff"># à quelle classe java ce bean est-il associé ?</font><br>
<font color="#aa0000">MovieLister</font>.class=martin_fowler.MovieLister<br>
<i><font color="#0000ff"># Quelle propriété est à affecter, ici une seule</font></i><br>
<font color="#aa0000">MovieLister</font>.property.1=finder<br>
<font color="#0000ff"><i># Le mutateur n'a qu'un paramètre</i></font><br>
<font color="#aa0000">MovieLister</font>.property.1.param.1=MovieFinder<br>
<br>
bean.id.2=<font color="#aa0000">MovieFinder</font><br>
<font color="#aa0000">MovieFinder</font>.class=martin_fowler.ColonMovieFinder<br>
<font color="#aa0000">MovieFinder</font>.property.1=filename<br>
<font color="#aa0000">MovieFinder</font>.property.1.param.1=movies1.txt
&nbsp;
# fichier question1.exemples.README.TXT<br>
bean.id.1=table<br>
table.class=question1.exemples.Table<br>
table.property.1=liste<br>
table.property.1.param.1=listeArray<br>
table.property.2=capacite<br>
table.property.2.param.1=4<br>
<br>
bean.id.2=listeArray<br>
listeArray.class=java.util.ArrayList<br>
<br>
bean.id.3=table2<br>
table2.class=question1.exemples.Table<br>
table2.property.1=liste<br>
table2.property.1.param.1=listeLinked<br>
table2.property.2=capacite<br>
table2.property.2.param.1=4<br>
<br>
bean.id.4=listeLinked<br>
listeLinked.class=java.util.LinkedList<br>
<br>
bean.id.5=tableSet<br>
tableSet.class=question1.exemples.Table<br>
tableSet.property.1=liste<br>
tableSet.property.1.param.1=set<br>
tableSet.property.2=capacite<br>
tableSet.property.2.param.1=4<br>
<br>
bean.id.6=set<br>
set.class=java.util.TreeSet
<br>
&nbsp;
  </blockquote>
</blockquote>

Question


&nbsp;&nbsp;&nbsp; Proposez un exemple d'utilisation de femtoContainer et la 
classe de tests unitaires associée, vos exemples seront intégrés à la prochaine 
version de cet énoncé.


            </font>


<font size="5">


<font size="4">

            </font></font><font size="5"><font size="4">L'interface ApplicationContext, contient les services proposés 
            par ce framework :</font><font size="4"><div style="border: 1px solid black; margin: 10px; padding: 10px; background: rgb(255, 255, 255) none repeat scroll 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: rgb(0, 0, 0);"> 
<pre><font color="#990044"><b><font size="2">public</font></b></font><font size="2"> <font color="#aa0000"><b>interface</b></font> ApplicationContext <font color="#aa0000"><b>extends</b></font> Iterable&lt;String&gt;{<br>  <br>  <font color="#0000ff"><i>/** Obtention d'une instance d'un bean géré par le conteneur.<br>   *  Il n'existe qu'une seule instance avec cet id, c'est un singleton.<br>   * @param id l'identifiant unique du bean<br>   * @return l'instance associée ou null si cet identifiant est inconnu<br>   */</i></font>
  <font color="#990044"><b>public</b></font> Object getBean(String id);<br>  <br>  <font color="#0000ff"><i>/** Obtention du type du bean à partir de son identifiant.<br>   * param id l'identifiant unique du bean<br>   * @return le type du bean ou null<br>   */</i></font>
  <font color="#990044"><b>public</b></font> Class&lt;?&gt; getType(String id);<br>  <br>  <font color="#0000ff"><i>/** Obtention d'un itérateur sur les beans déjà créés.<br>   * L'opération de retrait, méthode remove, est sans effet.<br>   * @return un itérateur des identifiants du conteneur<br>   */</i></font>
  <font color="#990044"><b>public</b></font> java.util.Iterator&lt;String&gt; iterator();<br>}</font>
</pre></div>

 <!-- FIN DE CODE JAVA - JavaVersHtml -->
            </font>


            </font>


<font size="4">

&nbsp;

N'oubliez pas d'utiliser JNEWS afin de déposer vos exemples, menu Outils, 
item submit..., Scheme tp_injection_question1_deposer

<img src="images/tp_deposer.jpg" border="0" height="194" width="534">

&nbsp;
 <!-- FIN DE CODE JAVA - JavaVersHtml --></font>

            <hr>
			<br>
			
<div class="question">
                <img src="images/question2.png" height="80" width="101"> Utilisation du femtoContainer 
                et des patrons, contribution</div>

<div class="paragraphe">
				&nbsp;
Les patrons de par leur structure se prêtent assez 
                    bien à cette séparation configuration/utilisation.  
<b>Question2-1) Proposez  exemple d'utilisation conjointe 
                d'un patron et de femtoContainer </b>
                <blockquote>
                  <ul>
                    <li>L'utilisation d'un patron
                    <a href="http://jfod.cnam.fr/NFP121/supports/extras_designpatternscard.pdf">
                    http://jfod.cnam.fr/NFP121/supports/extras_designpatternscard.pdf</a> 
                    sera privilégiée</li>
                    <li>Vous pouvez aussi vous inspirer d'un exemple du web, en 
                    citant la source.</li>
                  </ul>
                </blockquote>
<b><font color="#aa0000">Utilisez JNEWS, item tp_inject_q2_contribuer</font>, </b>
                afin d'apporter votre contribution aux exemples de cet énoncé, 
                vos exemples avec les tests unitaires associés feront partie de 
                l'énoncé l'an prochain.
&nbsp;&nbsp;&nbsp; Montrez que seule 
                la modification du fichier de configuration permet de changer le 
                comportement d'une application utilisant ce patron
&nbsp;
&nbsp;
<b>Question2-2) Vos deux exemples sont maintenant accessibles via une 
                instance du patron ServiceLocator, </b>
<b>&nbsp; Modifiez la configuration (./service_locator/README.TXT) 
                et enrichissez la classe de tests unitaires en conséquence (./service_locator/ServiceLocatorTests.java)...</b>


<font size="4">

N'oubliez pas d'utiliser JNEWS afin de déposer vos contribution, menu Outils, 
item submit..., Scheme tp_injection_question1_contribuer

<img src="images/tp_item_contribuer.jpg" border="0" height="194" width="534">
                </font>

&nbsp;
<b>Un exemple parmi bien d'autres possibles : L'usage du patron Observateur lors d'un 
                changement d'état de la lampe</b>
<b>La classe Light et son observateur suivis d'un test de bon 
                fonctionnement</b>
<!-- DEBUT DE CODE JAVA - JavaVersHtml -->
<div style="border: 1px solid black; margin: 10px; padding: 10px; background: rgb(255, 255, 255) none repeat scroll 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: rgb(0, 0, 0);"> 
<pre><font color="#aa0000"><b>package</b></font> question1;<br><br><font color="#aa0000"><b>import</b></font> java.util.Observable;<br><font color="#aa0000"><b>import</b></font> java.util.Observer;<br><br><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> Light <font color="#aa0000"><b>extends</b></font> Observable <font color="#aa0000"><b>implements</b></font> Receiver{<br>  <font color="#990044"><b>private</b></font> String state=<font color="#00aa00">"Off"</font>;</pre> 
<pre>  <br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> setObserver(Observer obs){<br>    this.addObserver(obs);<br>  }<br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> on(){<br>    <font color="#990044"><b>if</b></font>(<font color="#00aa00">"Off"</font>.equals(getState())){<br>      state = <font color="#00aa00">"On"</font>;setChanged();notifyObservers();<br>    }<br>  }<br>  <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> off(){<br>    <font color="#990044"><b>if</b></font>(<font color="#00aa00">"On"</font>.equals(getState())){<br>      state = <font color="#00aa00">"Off"</font>;setChanged();notifyObservers();<br>    }<br>  }<br>  <font color="#990044"><b>public</b></font> String getState(){<font color="#990044"><b>return</b></font> state;}<br>}</pre> 
<pre>_________________________________________________________</pre> 
<pre><font color="#aa0000"><b>package</b></font> question1;<br><font color="#aa0000"><b>import</b></font> java.util.*;<br><br><font color="#990044"><b>public</b></font> <font color="#aa0000"><b>class</b></font> LightObserver <font color="#aa0000"><b>implements</b></font> Observer{<br>    <font color="#990044"><b>public</b></font> <font color="#ff0000"><b>int</b></font> compte;<br><br>    <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> update(Observable o, Object obj){<br>      compte++;<br>    }<br>}</pre> 
<pre></pre></div>
 <!-- FIN DE CODE JAVA - JavaVersHtml --><b>&nbsp;</b>Un test : l'observateur 
                est bien notifié<div class="paragraphe">
                </div>
                <!-- DEBUT DE CODE JAVA - JavaVersHtml -->
<div style="border: 1px solid black; margin: 10px; padding: 10px; background: rgb(255, 255, 255) none repeat scroll 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: rgb(0, 0, 0);"> 
<pre>   <font color="#990044"><b>public</b></font> <font color="#aa0000"><b>void</b></font> testLightObserver()<font color="#990044"><b>throws</b></font> Exception{<br>    <font color="#990044"><b>try</b></font>{<br>      ApplicationContext ctx = Factory.createApplicationContext();<br>      Invoker invoker = ctx.getBean(<font color="#00aa00">"invoker"</font>);<br>      Receiver receiver =  ctx.getBean(<font color="#00aa00">"lampe"</font>);<br>      LightObserver observer = ctx.getBean(<font color="#00aa00">"observateur"</font>);</pre> 
<pre>      assertEquals(<font color="#00aa00">"Off"</font>, receiver.getState());<br>      invoker.on();<br>      assertEquals(1,observer.compte);<br>      invoker.off();invoker.on();<br>      assertEquals(3,observer.compte);<br>    }<font color="#990044"><b>catch</b></font>(Exception e){<br>      fail(<font color="#00aa00">"Exception inattendue :"</font> + e.getMessage());<br>    }<br>  }<br></pre></div>
 <!-- FIN DE CODE JAVA - JavaVersHtml -->
		<!-- DEBUT DE CODE JAVA - JavaVersHtml -->
<pre></pre>
                </div>
 <!-- FIN DE CODE JAVA - JavaVersHtml -->	
                <span lang="FR"><font size="3">
            # le fichier de configuration
            bean.id.12=lampe<br>
            lampe.class=question1.Light<br>
            lampe.property.1=observer<br>
            lampe.property.1.param.1=observateur
            <pr>
            lampe.property.1=observer<br>
            lampe.property.1.param.1=observateur
            <p		
            
			
<div class="question">
                <img src="images/question3.png" height="80" width="101"> 
                Optionnelle contribution</div>

            </font><font size="4">
            </font><font size="4">&nbsp;&nbsp;&nbsp; F</font><font size="3"><font size="4">emtoContainer 
            est bien entendu perfectible, tout ajout, toutes modifications sont 
            les bienvenus et seront intégrés, à l'outil pour l'an prochain.
</font></font>
            <font size="4">&nbsp;&nbsp;&nbsp; Les traces en mode verbeux, 
            l'exécution possible par introspection de n'importe quelle méthode, 
            la gestion des erreurs issue d'un fichier de configuration erroné 
            pourrait être ajoutées ou améliorées. </font>
            <font size="4">&nbsp;&nbsp;&nbsp; Un autre format du fichier de configuration XML, JSON 
... peut être proposé. L'interface graphique engendrant le fichier de 
configuration XMLou JSON pourrait elle aussi être enrichie ou réécrite.</font>
            <font size="4">JSON : à l'aide de cette librairie par exemple, 
            voir <a href="https://www.jmdoudoux.fr/java/dej/chap-gson.htm">
            https://www.jmdoudoux.fr/java/dej/chap-gson.htm</a><br>
            </font>
<font size="3">            </font>
			

			
			<br>
			
        </div>
        <div id="footer">
            <a class="lien" href="http://jfod.cnam.fr/" target="_blank">JFOD</a> | <a class="lien" href="http://deptmedia.cnam.fr/phpBB2/viewforum.php?f=40" target="_blank">FORUM</a> | <a class="lien" href="http://www.cnam.fr/" target="_blank">CNAM</a>
        </div>
    </div>