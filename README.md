# jekyll-theme-haal-centraal

Dit theme wordt gebruikt voor het deployen van de documentatie in de Haal Centraal repositories naar GitHub Pages.

## Generatie logica

Centraal staan daarbij de bestanden in de folders:
* _layouts
* _includes
* assets

Hieronder lichten we de folders en zo nodig de bestanden daarin toe.

### _layouts

Deze folder bevat de layouts (of templates) voor de 4 type pagina's die we met dit theme genereren. Layouts werken volgens het volgende principe.

Minmaal 1 layout (bijv. 'default.html') bevat een basis html content met daarbinnen de jekyll code ``{{ content }}`` (een variabele). Dus zoals het volgende:
```
<html>
...
{{ content }}
...
</html>
```

Waarbij in dit voorbeeld met ``...`` andere html elementen worden bedoelt. De ``content`` variabele wordt vervangen door de (naar html omgezette) inhoud van een md bestand of de inhoud van een andere layout bestanden. Deze verwijzen in hun header dan op de volgende wijze naar de basis layout
```
---
layout: default
---
```
Andere layouts bestaan op hun beurt weer uit html tags (bijv. 'div') met evt. weer de jekyll code ``{{ content }}``. Daarvoor geldt weer hetzelfde principe als hierboven is toegelicht. Zo kan een html bestand worden opgebouwd door opeenvolgende lagen van layouts die steeds de bovenliggende laag in hun header definiëren en de content uit de md bestanden. Hieronder een voorbeeld:

![Include principe](Include-principe.jpg)

De layouts kunnen overigens ook andere variabelen bevatten die in de md bestanden weer in de header of in de '\_config.yml' gedefinieerd kunnen worden.
En tenslotte kunnen de layouts ook verwijzingen bevatten naar bestanden in de '\_includes' folder. Vaak geldt dan dat dat alleen in specifieke situaties moet gebeuren. Zo wordt met de code:

```
    {% if page.head_include %}
      {% include {{ page.head_include }} %}
    {% endif %}
```
geregeld dat als in een md bestand in de header een variabele 'head_include' is gedefinieerd het in die variabele gedefinieerde bestand uit de '\_includes' folder op de betreffende plaats moet worden opgenomen. Het kan bij bestanden in de '\_includes' folder gaan om bestanden met 'html' fragmenten, verwijzingen naar code of een css bestand.

#### default.html

Deze layout bevat de basis indeling van de Haal Centraal pagina's. Naast de variabele ``content`` worden hierin ook de variabelen ``head_include`` en ``body_include`` gebruikt. 

Indien er voor een md  bestand in de basis GitHub Pages folder (afhankelijk van de gekozen instelling de 'docs' folder, de 'root' folder of een specifieke branch) geen layout is gedefinieerd wordt automatisch deze layout toegekend.
Duidelijker is echter het gebruik af te dwingen door in de header de vermeldde code op te nemen.

#### landing-page.html

Deze layout wordt alleen gebruikt door het 'index.md' bestand in de repository 'Haal-Centraal' (de landingspagina). Het bevat slechts een 'div' element dat dus aan de default layout wordt toegevoegd. Om deze layout te kunnen gebruiken wordt de code:
```
---
layout: landing-page
---
```
in het md bestand gebruikt.

#### page-with-side-nav.html

Deze layout wordt door de meeste pagina's in de Haal Centraal site gebruikt. Naast een content deel bevat het ook een side navigatie. Ook dit bevat slechts een 'div' element waarmee het content deel van de pagina's en de side navigatie wordt gedefinieerd. Gebruik
```
---
layout: page-with-side-nav
---
```
in de header van de md bestanden om deze layout te kunnen gebruiken.

#### page.html

Layout die op dit moment niet gebruikt wordt. 

### _includes

Hier zijn building blocks opgeslagen waarmee de content van de GitHub Pages pagina's worden opgebouwd. Te weten:

* nav.html
* redoc-body.html
* side-nav.html
* swagger-ui-body.html
* swagger-ui-head.html
* topnavigatie.html

De inhoud van deze bestanden kan bestaan uit alle in html toegestane content maar kan daarnaast ook condities bevatten zoals:

```
{% for item in include.nav %}
	...
{% endfor %}
```

De bestanden 'redoc-body.html', 'swagger-ui-body.html' en 'swagger-ui-header.html' bevatten stabiele content. Hierop hoeven normaliter geen aanpassingen op uitgevoerd te worden.

Voor 'side-nav.html' en 'nav.html' geldt eigenlijk hetzelfde maar hier is enige uitleg op zijn plaats en ook de top navigatie behoeft enige uitleg.

#### De side navigatie

Het bestand 'side-nav.html' bevat een script waarmee de structuur van de side navigatie wordt opgebouwd. Dit ziet er als volgt uit:

```
{% for item in site.nav %}
	<div class="header">{{ item.title }}</div>
	<nav class="nav flex-column">
    {% if item.subnav %}
      {% include nav.html nav=item.subnav %}
    {% endif %}
  {% endfor %}
</nav>
```
De eerste regel verwijst naar het json element 'nav' in de repo's noodzakelijk bestand '\_config.yml'. Als dat dus aanwezig is worden dus i.i.g. de volgende 2 regels geplaatst. Bevat dat 'nav' element dan vervolgens ook nog een 'subnav' element dan wordt ook de include in de 5e regel uitgevoerd.
Daarmee wordt het bestand 'nav.html' opgeroepen waarbij als inhoud van de variabele 'nav' alle 'subnav' elementen wordt toegekend. Het bestand 'nav.html' heeft weer de volgende content:

```
{% for item in include.nav %}
	{% capture pathwithextension %}{% if item.path == '/' %}{{ item.path }}{% else %}{{ item.path }}.html{% endif %}{% endcapture %}
	<a class="nav-link{% if page.url == pathwithextension %} active{% endif %}" href="{%if item.url %}{{item.url}}{% elsif item.path %}{{ site.baseurl }}{{ item.path }}{% endif %}" target="{%if item.target == 'blank' %}_blank{% endif %}">{{ item.title }}</a>
{% endfor %}
```
Op alle items van de variabele 'nav' (dus eigenlijk alle 'subnav' elementen) worden de script regels 2 en 3 toegepast zodat uiteindelijk de side navigatie items worden opgebouwd. De uitleg van dit deel van het script laat ik hier achterwege. Mocht je dat willen uitpluizen dan verwijs ik je naar [deze liquid tutorial](https://shopify.github.io/liquid/). In de paragraaf ' Hoe maken repo's gebruik van dit theme' leg ik uit hoe je het ' nav' deel van het '\_config.yml' bestand vult om een nette side navigatie te verkrijgen.

#### De top navigatie

Ten opzichte van de side-navigatie is de top navigatie redelijk straight forward. Het bevat html code welke kan worden aangepast als er iets wijzigt in m.b.t. de standaarden of waaraan content kan worden toegevoegd als er nieuwe standaarden aan het lijstje met producten moet worden toegevoegd. Dit doe je in het bestand ' topnavigatie.html' .

### assets

Tenslotte bevat de folder 'assets' het noodzakelijke css bestand en alle benodigde images. Hiernaar wordt in de bestanden in de voorgaande folders verwezen.
Hierop gaan we in dit document niet verder in.

## Hoe maken repo's gebruik van dit theme

Om een repo van dit theme gebruik te laten maken moet als eerste in de GitHub Pages sectie van de Settings aangegeven worden welke branch en folder als bron voor de GitHub Pages moet worden beschouwd. Meer hoeft niet te worden aangegeven. Binnen Haal Centraal gebruiken wij in principe altijd de 'docs' folder van de 'master/main' branch. Aan het bestand '\_config.yml' dat daarop in die folder wordt aangemaakt moet de volgende regel toegevpoegd worden om de repo het onderliggende theme te laten gebruiken:

```
remote_theme: VNG-Realisatie/jekyll-theme-haal-centraal
```

Het '\_config.yml' bestand speelt een centrale rol bij toepassen van dit theme, in het vervolg van deze uitleg komen we daar dus nog vaker op terug.

### Content

De content van de Haal Centraal GitHub Pages site bestaat uit markdown bestanden die in de 'docs' folder of lager staan. Elke repository die aansluit bij de Haal Centraal GitHub Pages site heeft enkele standaard markdown bestanden die overigens niet altijd voor hoeven te komen. Ik benoem ze hieronder:

| Bestand | Omschrijving |
| --- | --- |
| index.md | Bevat de hoofdpagina van de betreffende API standaard. In het algemeen is de content nagenoeg gelijk aan die van het 'README.md' bestand. |
| goals-canvas.md | Bevat een beschrijving van de epics. Er wordt beschreven wat de API wil bereiken, hoe het dat bereikt wordt, wat daarvoor de input en output is en met welk doel. Tevens zijn referenties naar de gerelateerde userstories opgenomen. |
| getting-started.md | Beschrijft welke stappen moeten worden doorlopen om aan te sluiten op de API van de betreffende basisregistratie. |
| redoc.md | Bevat gegevens om de gerelateerde API specificatie in de Redoc interface te tonen. |
| swagger-ui.md | Bevat gegevens om de gerelateerde API specificatie in de Swagger interface te tonen. |
| user-stories-prod.md | Gegenereerd bestand met een lijst van alle GitHub userstories die in de in productie versie van de standaard API zijn geïmplementeerd en de daarbij horende links naar de betreffende userstories in de gerelateerde GitHub repository. |
| user-stories-dev.md | Gegenereerd bestand met een lijst van alle GitHub userstories die aan de in ontwikkeling versie van de standaard API zijn gekoppeld en de daarbij horende links naar de betreffende userstories in de gerelateerde GitHub repository. |
| features.md | Lijst met links naar Feature bestanden in de gerelateerde GitHub repository.  |



**Deze paragraaf is nog in bewerking**

## Geautomatiseerde handelingen

### Rebuild

Zodra er in een repository, die dit theme gebruikt, een wijziging wordt aangebracht in een bestand in de basis GitHub Pages folder wordt er automatisch een rebuild uitgevoerd van de deployment waardoor wijzigingen redelijk snel zichtbaar worden. Echter als er een wijziging wordt aangebracht in de onderliggende GitHub repository (en dus in dit theme) dan wordt die wijziging pas zichtbaar in de GitHub pages van een repository als er in die repsitory een wijziging wordt aangebracht in een bestand in de basis GitHub Pages folder. 

Dat laatste is meestal ongewenst. Vaak willen we nl. dat die wijziging direct wordt doorgevoerd op de GitHub pages van alle repositories die dit theme gebruiken.
Het zou dan erg omslachtig en ongewenst zijn om in elke repository dan maar even een hele kleine wijziging door te voeren om daarmee de rebuild te triggeren.
Daarom is een action (script) gedefinieerd waarmee we een rebuild op al deze repositories afdwingen. Het script kun je in de onderliggende repository vinden in '.github/workflows/gh-pages-rebuild.yml' en kan afgetrapt worden in de 'Actions' tab van deze repository.

**Let op!** Zodra een nieuwe repository dit theme gebruikt moet het worden toegevoegd aan dit script.

### Generate-user-stories

De pagina van een Haal Centraal API standaard kan maximaal 2 versies van de standaard bevatten, de versie die in produktie is en de versie die in ontwikkeling is.
Natuurlijk kunnen deze bestanden handmatig vervaardigd worden maar dat kost ten eerste erg veel tijd en is ten tweede erg foutgevoelig. Om die reden wordt in elke repository die het onderliggende theme gebruikt het script 'generate-user-stories' geplaatst (in de folder '.github/workflows/'). Het script genereert zo gewenst (afhankelijk van welke versie getoond moet worden) een lijst met userstories die geïmplementeerd zijn in de produktie versie en een lijst met userstories die gepland staan voor implementatie in de in ontwikkeling versie.
Aangezien elke repository een andere status kan hebben en dus een andere produktie en in ontwikkeling versie moet het script aangepast worden aan de status van de repository.
Ook gedurende de tijd moet dit script onderhouden worden. Als in een repository de produktie versie 1.2.0 versie 1.1.0 gaat vervangen en versie 1.3.0 in ontwikkeling gaat heeft dat dus gevolgen voor het script. Het voert te ver om hier over de werking van het script uit te wijden dus daarvoor verwijs ik naar [de uitleg van het script in de API Kennisbank](https://github.com/VNG-Realisatie/API-Kennisbank/blob/master/GitHub%20Actions/haal-centraal.md).
