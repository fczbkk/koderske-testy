# Kóderské testy (backoffice)

Toto sú úlohy pre kódera, ktorý sa hlási na pozíciu v backoffice.

Človek na tejto pozícii bude pracovať s reklamnými kódmi vkladanými na rôzne weby. Nebude sa môcť spoľahnúť na to, že bude mať k dispozícii konkrétne JS frameworky (napr. Jquery). Takže požadované schopnosti sú:

- Vedieť čítať a písať čistý JavaScript.
- Robiť úpravy existujúceho kódu tak, aby nespôsobili kolíziu s už existujúcim kódom.
- Upravovať DOM pomocou JavaScriptu.


## Pochopenie cudzieho kódu a práca s ním

Máte k dispozícii takýto kód:

```html
<script>
  (function () {
    var ad_content = "Moja reklama";
    document.write("<div id='myAd'>" + ad_content + "</div>");
    document.write("<style>#myAd {background: red;}</style>");
    var ad_element = document.getElementById('myAd')
    ad_element.style.color = 'yellow';
  })();
</script>
```

Úlohy:

> Čo tento kód robí? Čo sa stane, keď ho vložíme do dokumentu?

- Uchádzač by mal vedieť, že celý kód je zabalený do anonymného bloku (IIFE), aby nenastávali konflikty s pôvodným kódom dokumentu.
- Mal by pochopiť, že kód vloží do dokumentu element `DIV` s ID `myAd`, ktorý bude obsahovať text "Moja reklama", bude mať červené pozadie a žltý text.

> Uprav tento kód pomocou JavaScriptu tak, aby pozadie vloženého elementu bolo modré a text biely.

Tu má uchádzač niekoľko rôznych možností.

- Môže sa pokúsiť napodobiť štýl pôvodného kódu a použiť `document.write` a pod. Ak to bude fungovať správne, tak fajn. Ale neurobí to na mňa dobrý dojem.
- Lepší dojem urobí, keď to urobí jednoducho a čisto. Napríklad:

```javascript
var elm = document.getElementById('myAd');
elm.style.background = 'blue';
elm.style.color = 'white'
```


## Úprava DOM pomocou JavaScriptu

Máte k dispozícii takýto kód:

```html
<div>
  <p id="myElement">...</p>
  <p>...</p>
</div>
<script>
  document.getElementById('myElement').onclick = function () {
    console.log('Klikol si na odstavec.');
  }
</script>
```

Úloha:

> Vlož medzi dva odstavce nový odstavec, ktorý bude obsahovať text "Druhý odstavec"

- Ak uchádzača napadne vložiť tam nový element pomocou `innerHTML`, bude ma zaujímať, ako sa postará o to, aby neprišiel o ten `onclick` event, ktorý je zadefinovaný v jednom z elementov.
- Oveľa lepší dojem na mňa urobí, ak si bude schopný nový element vytvoriť a vložiť ho na správne miesto. K tomu bude potrebovať znalosť toho, čo je `node`, ako po nich traverzovať, vytvárať ich a modifikovať pomocou týchto metód:
  - `document.createElement`
  - `document.createTextNode`
  - `node.appendChild`
  - `node.insertBefore`
  - `node.parentNode`
  - `node.nextSibling`

Výsledný kód môže vyzerať napríklad takto:

```javascript
var new_element = document.createElement('div');
new_element.appendChild(document.createTextNode('Druhý odstavec'));
var ref_element = document.getElementById('myElement');
ref_element.parentNode.insertBefore(new_element, ref_element.nextSibling);
```


# `window.onload` event

Máte k dispozícii takýto kód:

```javascript
window.onload = function () {
  console.log 'OK';
};
```

Úlohy:

> Napíš JavaScript, ktorý bude vložený niekam do dokumentu za tento kód. Tvoj kód by mal do konzoly vpísať "Nazdar!" keď nastane `window.onload`. Pričom pôvodný kód musí fungovať bez zmeny.

- Uchádzač by si mal uvedomiť, že nemôže len tak použiť `window.onload`, pretože by tým prepísal pôvodný kód.
- Najjednoduchšie riešenie by bolo použiť event listener:

```javascript
window.addEventListener('load', function () {
  console.log('Nazdar!');
});
```

- Ak sa uchádzač pokúsi použiť `window.onload`, mal by byť schopný zabezpečiť, aby sa pôvodný kód vykonal tiež:

```javascript
var original_window_onload = window.onload;
window.onload = function () {
  console.log('Nazdar!');
  original_window_onload();
};
```

> Dosiahni to isté čo v predchádzajúcej úlohe. S tým rozdielom, že nevieš, či tvoj kód bude do dokumentu vložený pred eventom `window.onload`, alebo až po tom, čo nastane. V prípade, že už event nastal, mal by sa tvoj kód vykonať okamžite.

- Uchádzač by mal vedieť zistiť stav dokumentu pomocou `document.readyState` a vedieť to použiť v podmienke:

```javascript
if (document.readyState === 'complete') {
  console.log('Nazdar!');
} else {
  window.addEventListener('load', function () {
    console.log('Nazdar!');
  });
}
```

- Dobrý dojem uchádzač urobí, keď nebude zbytočne písať duplicitný kód:

```javascript
function showMyMessage() {
  console.log('Nazdar!');
}

if (document.readyState === 'complete') {
  showMyMessage()
} else {
  window.addEventListener('load', showMyMessage);
}
```
