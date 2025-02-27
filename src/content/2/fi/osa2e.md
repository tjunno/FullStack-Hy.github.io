---
mainImage: ../../../images/part-2.svg
part: 2
letter: e
lang: fi
---

<div class="content">

Sovelluksemme ulkoasu on tällä hetkellä hyvin vaatimaton. Osaan 0 liittyvässä [tehtävässä 0.2](/osa0/web_sovelluksen_toimintaperiaatteita#tehtavia) tutustuttiin Mozillan [CSS-tutoriaaliin](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics).

Katsotaan vielä tämän osan lopussa nopeasti kahta tapaa liittää tyylejä React-sovellukseen. Tapoja on useita, ja tulemme tarkastelemaan muita myöhemmin. Ensimmäisenä liitämme CSS:n sovellukseemme vanhan kansan tapaan yksittäisenä tiedostona, joka on kirjoitettu käsin ilman [esiprosessorien](https://developer.mozilla.org/en-US/docs/Glossary/CSS_preprocessor) apua (tulemme myöhemmin huomaamaan, että tämä ei ole täysin totta).

Tehdään sovelluksen hakemistoon <i>src</i> tiedosto <i>index.css</i> ja liitetään se sovellukseen lisäämällä tiedostoon <i>index.js</i> seuraava import:

```js
import './index.css'
```

Lisätään seuraava sääntö tiedostoon <i>index.css</i>:

```css
h1 {
  color: green;
}
```

**Huom:** kun tiedostoon <i>index.js</i> lisätään sisältöä, React ei välttämättä havaitse muutosta automaattisesti, eli saatat joutua refreshaamaan selaimen, jotta näet muutokset! 

CSS-säännöt koostuvat valitsimesta eli <i>selektorista</i> ja määrittelystä eli <i>deklaraatiosta</i>. Valitsin määrittelee, mihin elementteihin sääntö kohdistuu. Valitsimena on nyt <i>h1</i> eli kaikki sovelluksessa käytetyt <i>h1</i>-otsikkotägit.

Määrittelyosa asettaa ominaisuuden _color_ eli fontin värin arvoksi vihreän (<i>green</i>).

Sääntö voi sisältää mielivaltaisen määrän määrittelyjä. Muutetaan edellistä siten, että tekstistä tulee kursivoitua eli fontin tyyliksi asetetaan <i>italic</i>:

```css
h1 {
  color: green;
  font-style: italic;  // highlight-line
}
```

Erilaisia selektoreja eli tapoja valita tyylien kohde on [lukuisia](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors).

Jos haluamme kohdistaa tyylejä esim. jokaiseen muistiinpanoon, voisimme käyttää selektoria <i>li</i>, sillä muistiinpanot ovat <i>li</i>-tagien sisällä:

```js
const Note = ({ note, toggleImportance }) => {
  const label = note.important 
    ? 'make not important' 
    : 'make important';

  return (
    <li>
      {note.content} 
      <button onClick={toggleImportance}>{label}</button>
    </li>
  )
}
```

Lisätään tyylitiedostoon seuraava (koska osaamiseni tyylikkäiden web-sivujen tekemiseen on lähellä nollaa, nyt käytettävissä tyyleissä ei ole sinänsä mitään järkeä):

```css
li {
  color: grey;
  padding-top: 3px;
  font-size: 15px;
}
```

Tyylien kohdistaminen elementtityyppeihin on kuitenkin ongelmallista. Jos sovelluksessa on myös muita <i>li</i>-tageja, kaikki saavat samat tyylit.

Jos haluamme kohdistaa tyylit nimenomaan muistiinpanoihin, on parempi käyttää [class selectoreja](https://developer.mozilla.org/en-US/docs/Web/CSS/Class_selectors).

Normaalissa HTML:ssä luokat määritellään elementtien attribuutin <i>class</i> arvona:

```html
<li class="note">tekstiä</li>
```

Reactissa tulee kuitenkin classin sijaan käyttää attribuuttia [className](https://reactjs.org/docs/dom-elements.html#classname), joten muutetaan komponenttia <i>Note</i> seuraavasti:

```js
const Note = ({ note, toggleImportance }) => {
  const label = note.important 
    ? 'make not important' 
    : 'make important';

  return (
    <li className='note'> // highlight-line
      {note.content} 
      <button onClick={toggleImportance}>{label}</button>
    </li>
  )
}
```

Luokkaselektori määritellään syntaksilla _.classname_ eli:

```css
.note {
  color: grey;
  padding-top: 5px;
  font-size: 15px;
}
```

Jos nyt lisäät sovellukseen muita li-elementtejä, ne eivät saa muistiinpanoille määriteltyjä tyylejä.

### Parempi virheilmoitus

Aiemmin toteutimme olemassa olemattoman muistiinpanon tärkeyden muutokseen liittyvän virheilmoituksen <em>alert</em>-metodilla. Toteutetaan se nyt Reactilla omana komponenttinaan.

Komponentti on yksinkertainen:

```js
const Notification = ({ message }) => {
  if (message === null) {
    return null
  }

  return (
    <div className="error">
      {message}
    </div>
  )
}
```

Jos propsin <em>message</em> arvo on <em>null</em>, ei renderöidä mitään. Muussa tapauksessa renderöidään viesti div-elementtiin. Elementille on liitetty tyylien lisäämistä varten luokka <i>error</i>.

Lisätään komponentin <i>App</i> tilaan kenttä <i>errorMessage</i> virheviestiä varten. Laitetaan kentälle heti jotain sisältöä, jotta pääsemme testaamaan komponenttia:

```js
const App = () => {
  const [notes, setNotes] = useState([]) 
  const [newNote, setNewNote] = useState('')
  const [showAll, setShowAll] = useState(true)
  const [errorMessage, setErrorMessage] = useState('some error happened...') // highlight-line

  // ...

  return (
    <div>
      <h1>Notes</h1>
      <Notification message={errorMessage} /> // highlight-line
      <div>
        <button onClick={() => setShowAll(!showAll)}>
          show {showAll ? 'important' : 'all' }
        </button>
      </div>      
      // ...
    </div>
  )
}
```

Lisätään sitten virheviestille sopiva tyyli:

```css
.error {
  color: red;
  background: lightgrey;
  font-size: 20px;
  border-style: solid;
  border-radius: 5px;
  padding: 10px;
  margin-bottom: 10px;
}
```

Nyt olemme valmiina lisäämään virheviestin logiikan. Muutetaan metodia <em>toggleImportanceOf</em> seuraavasti:

```js
  const toggleImportanceOf = id => {
    const note = notes.find(n => n.id === id)
    const changedNote = { ...note, important: !note.important }

    noteService
      .update(changedNote).then(returnedNote => {
        setNotes(notes.map(note => note.id !== id ? note : returnedNote))
      })
      .catch(error => {
        // highlight-start
        setErrorMessage(
          `Note '${note.content}' was already removed from server`
        )
        setTimeout(() => {
          setErrorMessage(null)
        }, 5000)
        // highlight-end
        setNotes(notes.filter(n => n.id !== id))
      })
  }
```

Virheen yhteydessä asetetaan tilaan <em>errorMessage</em> sopiva virheviesti. Samalla käynnistetään ajastin, joka asettaa viiden sekunnin kuluttua tilan <em>errorMessage</em>-kentän arvoksi <em>null</em>.

Lopputulos näyttää seuraavalta:

![](../../images/2/26e.png)

Sovelluksen tämänhetkinen koodi on kokonaisuudessaan [GitHubissa](https://github.com/fullstack-hy2020/part2-notes/tree/part2-7), branchissa <i>part2-7</i>.

### Inline-tyylit

React mahdollistaa tyylien kirjoittamisen myös suoraan komponenttien koodin joukkoon niin sanoittuina [inline-tyyleinä](https://react-cn.github.io/react/tips/inline-styles.html).

Periaate inline-tyylien määrittelyssä on erittäin yksinkertainen. Mihin tahansa React-komponenttiin tai elementtiin voi liittää attribuutin [style](https://reactjs.org/docs/dom-elements.html#style), jolle annetaan arvoksi JavaScript-oliona määritelty joukko CSS-sääntöjä.

CSS-säännöt määritellään JavaScriptin avulla hieman eri tavalla kuin normaaleissa CSS-tiedostoissa. Jos haluamme asettaa jollekin elementille esimerkiksi vihreän, kursivoidun ja 16 pikselin korkuisen fontin, määrittely ilmaistaan CSS-syntaksilla seuraavasti:

```css
{
  color: green;
  font-style: italic;
  font-size: 16px;
}
```

Vastaava tyyli kirjoitetaan Reactin inline-tyylin määrittelevänä oliona seuraavasti:

```js
{
  color: 'green',
  fontStyle: 'italic',
  fontSize: 16
}
```

Jokainen CSS-sääntö on olion kenttä, joten ne erotetaan JavaScript-syntaksin mukaan pilkuilla. Pikseleinä ilmaistut numeroarvot voidaan määritellä kokonaislukuina. Merkittävin ero normaaliin CSS:ään on väliviivan sisältämien CSS-ominaisuuksien kirjoittaminen camelCase-muodossa.

Voimme nyt lisätä sovellukseemme alapalkin muodostavan komponentin <i>Footer</i> ja määritellä sille inline-tyylit seuraavasti:

```js
const Footer = () => {
  const footerStyle = {
    color: 'green',
    fontStyle: 'italic',
    fontSize: 16
  }

  return (
    <div style={footerStyle}>
      <br />
      <em>Note app, Department of Computer Science, University of Helsinki 2022</em>
    </div>
  )
}

const App = () => {
  // ...

  return (
    <div>
      <h1>Notes</h1>

      <Notification message={errorMessage} />

      // ...  

      <Footer /> // highlight-line
    </div>
  )
}
```

Inline-tyyleillä on tiettyjä rajoituksia, esim. ns. pseudo-selektoreja ei ole mahdollisuutta käyttää (ainakaan helposti).

Inline-tyylit ja muutamat myöhemmin kurssilla katsomamme tavat lisätä tyylejä Reactiin ovat periaatteessa täysin vastoin vanhoja hyviä periaatteita, joiden mukaan web-sovellusten ulkoasujen määrittely eli CSS tulee erottaa sisällön (HTML) ja toiminnallisuuden (JavaScript) määrittelystä. Vanha koulukunta pyrkiikin siihen, että sovelluksen CSS, HTML ja JavaScript kirjoitetaan omiin tiedostoihinsa.

CSS:n, HTML:n ja JavaScriptin erottelu omiin tiedostoihinsa ei kuitenkaan ole välttämättä erityisen skaalautuva ratkaisu suurissa ohjelmistoissa. Reactissa onkin periaatteena jakaa sovelluksen koodi eri tiedostoihin noudattaen sovelluksen loogisia toiminnallisia kokonaisuuksia.

Toiminnallisen kokonaisuuden strukturointiyksikkö on React-komponentti, joka määrittelee niin sisällön rakenteen kuvaavan HTML:n, toiminnan määrittelevät JavaScript-funktiot kuin komponentin tyylinkin yhdessä paikassa siten, että komponenteista tulee mahdollisimman riippumattomia ja yleiskäyttöisiä.

Sovelluksen lopullinen koodi on kokonaisuudessaan [GitHubissa](https://github.com/fullstack-hy2020/part2-notes/tree/part2-8), branchissa <i>part2-8</i>.

</div>

<div class="tasks">

<h3>Tehtävät 2.19.-2.20.</h3>

<h4>2.19: puhelinluettelo step11</h4>

Toteuta osan 2 esimerkin [parempi virheilmoitus](/osa2/tyylien_lisaaminen_react_sovellukseen#parempi-virheilmoitus) tyyliin ruudulla muutaman sekunnin näkyvä ilmoitus, joka kertoo onnistuneista operaatioista (henkilön lisäys ja poisto sekä numeron muutos):

![](../../images/2/27e.png)

<h4>2.20*: puhelinluettelo step12</h4>

Avaa sovelluksesi kahteen selaimeen. **Jos poistat jonkun henkilön selaimesta 1** hieman ennen kuin yrität <i>muuttaa henkilön numeroa</i> selaimesta 2, tapahtuu virhetilanne:

![](../../images/2/29b.png)

Korjaa ongelma osan 2 esimerkin [promise ja virheet](/osa2/palvelimella_olevan_datan_muokkaaminen#promise-ja-virheet) hengessä ja siten, että käyttäjälle ilmoitetaan operaation epäonnistumisesta. Onnistuneen ja epäonnistuneen operaation ilmoitusten tulee erota toisistaan: 

![](../../images/2/28e.png)

**HUOM**: Vaikka käsittelet poikkeuksen koodissa, virheilmoitus tulostuu silti konsoliin.

Tämä oli osan viimeinen tehtävä ja on aika sekä puskea koodi GitHubiin että merkitä tehdyt tehtävät [palautussovellukseen](https://study.cs.helsinki.fi/stats/courses/fullstack2022).

</div>
