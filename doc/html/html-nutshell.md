# HTML5 & CSS
## Basic rules

 1. Niemals zusätzliche Klassen für das Grid-System definieren, weil das sonst das ganze layout durcheinander bringen könnte.
 2. Man sollte die Elemente verwenden, die auch die Funktionalität erfüllt. Man soll z.B. einen anker Element für die Links nehemen und nicht Buttons. Man kann die Links nachher durch CSS als Buttons aussehen lassen.
 3. Eine Webseite muss immer ein Impressum haben. Das ist das Gesetz in Deutschland.



## Generic Template

enter code here
> div äquivalente Elemente:
``` html
<!-- Mit diesem Block lässt sich eine Seite symantisch unterteilen -->
<section>
    <!-- Damit kann man ein Bereich definieren, der quasi als Einleitung in die Seite fungiert -->
    <head> </head>
    <!-- Ein Artikel -->
    <article> </article>
</section>
```

## Emmet    


``` html
<!-- emmet: div.container*2 > h1.heading{ÜBERSCHRIFT} -->
<div class="container">
    <h1 class="heading">ÜBERSCHRIFT</h1>
</div>
<div class="container">
    <h1 class="heading">ÜBERSCHRIFT</h1>
</div>
```

``` html
<!-- einfaches Element emmet: div -->
<div></div>
<!-- mehrfache Element emmet: div*3 -->
<div></div>
<div></div>
<div></div>
<!-- Element mit class emmet: div.myclass -->
<div class="myclass"></div>
<!-- Element mit class emmet: div#myid -->
<div id="myid"></div>
<!-- Element mit Kindelement emmet: div>h1 -->
<div> 
    <h1></h1> 
</div>
<!-- Element mit Geschwister emmet: div+h1 -->
<div></div>
<h1></h1>

<!-- Element mit Geschwister emmet: div>h1^p -->
<div>
    <h1></h1>
</div>
<p></p>
<!-- {Hier kann man einen Inhalt mit emmet definieren} -->
```


## Layout methods

### Display

The [display](https://developer.mozilla.org/en-US/docs/Web/CSS/display) property — Standard values such as block, inline or inline-block can change how elements behave in normal flow, for example, by making a block-level element behave like an inline-level element (see [Types of CSS boxes](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model#block_and_inline_boxes) for more information). We also have entire layout methods that are enabled via specific display values, for example, [CSS grid](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids) and Flexbox, which alter how child elements are laid out inside their parents.

- Block: elements are displayied one below the other
- Inline: display next to eachother
- Block-inline: 
- Flex: name for the Flexible Box Layout, helps to lay things in one dimension, in a row or in a column. Needs to be set to the parent element. Default is row. If the child elements will get the property flex=1, the childs will use the whole space in the row.
- Grid: (Grid Layout) with a container and some child elements. In addition to using display: grid, we also define some row and column tracks for the parent using the grid-template-rows and grid-template-columns properties respectively.
    
> From <https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Introduction> 

>**inline**
>- `<a>` anker
>- `<b>` bold text 
>- `<br>` Zeilenumbruch
>- `<p>` paragraph
>- `<cita>` Zitat
>- `<code>` Inline code
>- `<data>` Verbundene Daten 
>- `<dfn>` Definition
>- `<em>` emphasized text
>- `<i>` Italic text
>- `<img>` Image
>- `<input>` Form input
>- `<label>` Label
>- `<mark>` Marked text 
>- `<s>` striked trough text 
>- `<small>` small text 
>- `<span>` inline container 
>- `<strong>` Bold text 
>- `<sub>`tief gestellter text
>- `<sup>` hoch gestellter text
>- `<time>` Time stamp
>- `<u>` Underlined text
>**block Strukturelemente**
>- `<div>` generic block
>- `<section>`
>- `<article>`
>- `<header>`
>- `<footer>`
>- `<aside>` Nebeninhalt z.B. Seitenleiste
>- `<main>` Main part of the page 
>- `<nav>` Navigation bar 
>- `<figure>` Group for figured and diagramms
>- `<figcapture>` Bildunterschrift für *figure* 

>**block Textelemente**
>- `<h1..6>` Header 
>- `<p>` Absatz
>- `<blockquote>`
>- `<hr>` horizontal line 
>- `<ol>` ordered list 
>- `<ul>` unordered list 
>- `<li>` List element
>- `<dl>` Definitionsliste 
>- `<dt>` Begriff in einer Definitionsliste 
>- `<dd>` Definition in einer Definitionsliste

>**block form elements**
>- `<form>` Formular
>- `<fieldset>` Gruppierung der Formularfelder
>- `<legend>`
>- `<textarea>` Mehrzeiliges Textfeld
>- `<video>` Video
>- `<audio>` 
>- `<canvas>` Zeichenfläche
>- `<svg>` Scalable vector graphic
>- `<iframe>` inline Frame
>- `<address>` Kontakinformation
>- `<details>` Zusammenklappbarer Bereich
>- `<summary>` Zusammenfassungsüberschrift für *details* 
>- `<dialog>` Dialogfester


The [position](https://developer.mozilla.org/en-US/docs/Web/CSS/position) property — Allows you to precisely control the placement of boxes inside other boxes. static positioning is the default in normal flow, but you can cause elements to be laid out differently using other values, for example, as fixed to the top of the browser viewport.

Table layout — Features designed for styling parts of an HTML table can be used on non-table elements using display: table and associated properties.

[Multi-column layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_multicol_layout) — The Multi-column layout properties can cause the content of a block to lay out in columns, as you might see in a newspaper.

#### Floats

Floats — Applying a float value such as left can cause block-level elements to wrap along one side of an element, like the way images sometimes have text floating around them in magazine layouts. The floated element is moved to the left or right and removed from normal flow, and the surrounding content floats around it.

- left — Floats the element to the left.
- right — Floats the element to the right.
- none — Specifies no floating at all. This is the default value.
- inherit — Specifies that the value of the float property should be inherited from the element's parent element.

### Flexbox

[Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout) layout besteht aus einem Flex-Container und Flex-Items. Es gibt jeweils css Eigenschaften für Flex-container und css Eigenschaften für Flex-Items.

``` html
<!-- das ist der Container -->
<div class="container">
    <!-- Das ist ein Item -->
    <h1 class="heading">ÜBERSCHRIFT</h1>
</div>

```

#### Flex Container

- **flex-direction** - horizonatal oder vertikal. Die default Eigenschaft ist row.

- **flex-wrap**: wie sollen die items umbrechen: Damit sagt man, dass die items umbrechen sollen, wenn es nicht genug Platz auf dem Bildschirm da ist um den Inhalt richtig darzustellen. -> Responsive.
> Achtung: Das kann wegen z.B. der Textbreite bereits von Anfang a passieren. Das kann man fixen indem man den Items eine feste Breite vergibt.
- **justify-content**: damit kann man die Items im Container ausrichten.
    - center: die Items werden zentirert
    - space-between: der gleiche Abstand zwischen den Items
    - space-arround: der gleiche Abstand zwischen den Items und dem Container

- **align-items**: damit richtet man die Items vertikal aus
    - stretch: der Item nimmt die gesamte Höhe des Containers ein
    - center: werden sogroß wie der Platz benötigt wird und wird vertikal zentral ausgerichtet.
    - flex-end/start: Ausrichtng entweder oben oder unten an dem Container.
    - baseline: Ausrichtung an der Grundlinie des Containers, also in Abhängigkeit, ob es horizontal oder vertikal ausgerichtet wird.

- **align-content**: Beim Umbruch werden zusätzliche Flex-Lines entstehen und diese Eigenschaft steuert diese Flex-Lines. 
> Achtung: muss mit **align-Items** synchron gehalten werden, sonst ist das Verhalten bei einer Zeile unterschiedlich, als wenn die Items umbrechen. Es sind die gleichen Eigenschaften.
    - stretch: (default) sorgt dafür, dass die beiden Lines die gesamte Höhe des Containers ausfüllen.
    - center: zentriert vertikal.

#### Flex-Items

- **flex-basis**: definiert die intiale Größe des Items. Kann wie width angegeben in Pixel etc.
    - auto: richtet sich nach Inhalt 
- **flex-grow**: gibt an wie sich das Item wachsen um den Container auszufüllen. Wird in Verhältnis zu einander 1, 2 etc. angegeben. Die Eigenschaft wird pro Item angegeben. Defaul ist 0 und die Items wachsen nicht.
- **flex-shrink**: Gibt an ob und wie die Item schrumpfen wenn der Platz nicht ausreicht um die gesamte **flex-basis** anzuzeigen. Je größer die Zahl, desto stärker schrumpft der Item in Vergleich zu den anderen Items. Default ist 1.
- **flex**: Ist die zusammengefasste Schreibweise: grow shrink basis
- **order**: Kann die Reihenfolge der Items verändern. Die Angaben sind relativ zu einander. Wie Priorität. Nützlich bei media-queries.
- **align-self**: default ist **auto** übernimmt die Einstellung von **align-item** des Containers. Das lässt sich hier überschreiben, wenn z.B. ein Item sich anders verhalten soll.
