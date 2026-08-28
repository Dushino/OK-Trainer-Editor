# Aplikace pro úpravu souborů kartiček pro OK Trainer

Tento editor slouží k vytváření a úpravě JSON souborů s kartičkami (flashcards), které pak používá trenažér OK Trainer.

- Editor (tato aplikace) běží zde: https://dushino.github.io/OK-Trainer-Editor/
- Trenažér, který naimportované kartičky používá k procvičování, je zde: https://dushino.github.io/OK-Trainer/

## Datový model

Kartičky jsou uspořádány hierarchicky:

```
balíček (deck)
└─ oblast (area)
   └─ podoblast (subarea)
      └─ kartička (card): přední a zadní strana
```

Balíček je identifikován svým **krátkým názvem** a má nastavený **jazyk**, ve kterém se čte veškerý text, který není zabalený do složených závorek `{}`. Každá oblast může navíc obsahovat nepovinné omezení maximálního počtu chyb.

## Práce s editorem

- Všechny změny se ukládají **automaticky** do úložiště prohlížeče (localStorage) — není potřeba nic ručně ukládat.
- Nahoře v editoru vyberete **balíček**, ve kterém chcete pracovat (`Balíček karet`). Tlačítkem 📥 naimportujete JSON soubor s balíčkem, tlačítkem 📤 aktuálně vybraný balíček exportujete zpět do JSON souboru (pro použití v trenažéru OK Trainer nebo pro zálohu).
- Import balíčku se stejným krátkým názvem přepíše existující balíček stejného jména.
- Pod výběrem balíčku následuje výběr **oblasti** a **podoblasti** (tlačítky ➕ přidáte novou, 🗑️ smažete aktuálně vybranou — u smazání se ptá na potvrzení, protože smaže i všechny kartičky uvnitř). Nová oblast/podoblast se vytvoří s jednou prázdnou kartičkou.
- Kartičkami v rámci vybrané podoblasti se listuje tlačítky **◀ Předchozí / Další ▶**, aktuální pozice je zobrazena nad nimi (např. `Oblast › Podoblast — karta 2/5`).
- Editor v tuto chvíli neumožňuje přidávat nebo mazat jednotlivé kartičky v rámci podoblasti tlačítkem — kartičky se do souboru dostávají importem hotového JSON souboru (viz níže formát balíčku). Chcete-li přidat další kartičky k existující podoblasti, upravte exportovaný JSON soubor ručně (zkopírujte objekt kartičky a doplňte další) a balíček znovu naimportujte.
- U každého textového pole, které se dá přehrát hlasem, je tlačítko 🔊 pro okamžité vyzkoušení výslovnosti přímo v editoru.

## Popis jednotlivých polí

### Balíček

| Pole | Význam |
|---|---|
| **Krátký název balíčku** | Jednoznačný identifikátor balíčku (musí být v rámci uložených balíčků unikátní). Používá se i jako název exportovaného souboru. |
| **Krátký název — text pro TTS** | Nepovinné. Pokud je vyplněné, použije se místo krátkého názvu při přehrávání hlasem (např. když krátký název obsahuje zkratky, které se mají vyslovit jinak). Prázdné pole = použije se krátký název. |
| **Popis balíčku** | Delší, čitelný název/popis balíčku zobrazovaný uživateli v trenažéru. |
| **Popis — text pro TTS** | Nepovinné. Alternativní text pro přehrání popisu hlasem. Prázdné pole = použije se popis výše. |
| **Jazyk balíčku (např. cs-CZ)** | Jazykový kód hlasu, kterým se čte veškerý text v balíčku (mimo úseků v `{}`) — název, popis, oblasti, podoblasti i kartičky. Musí odpovídat jazyku, pro který má systém/prohlížeč nainstalovaný hlas (např. `cs-CZ`, `en-US`). |

### Oblast

| Pole | Význam |
|---|---|
| **Oblast** | Výběr aktuální oblasti v balíčku; ➕ přidá novou, 🗑️ smaže vybranou (balíček musí mít vždy aspoň jednu oblast). |
| **Název oblasti** | Zobrazovaný název oblasti (např. „Základy ovládání“). |
| **Název oblasti — text pro TTS** | Nepovinné. Alternativní znění pro přehrání hlasem. Prázdné pole = použije se název výše. |
| **Max. počet chyb v oblasti** | Nepovinné číslo. Používá ho trenažér OK Trainer k vyhodnocení procvičování dané oblasti (kolik chyb je v ní ještě přípustných, např. při simulaci zkoušky). Prázdné pole = bez omezení. |

### Podoblast

| Pole | Význam |
|---|---|
| **Podoblast** | Výběr aktuální podoblasti v rámci oblasti; ➕ přidá novou, 🗑️ smaže vybranou (oblast musí mít vždy aspoň jednu podoblast). |
| **Název podoblasti** | Zobrazovaný název podoblasti. |
| **Název podoblasti — text pro TTS** | Nepovinné. Alternativní znění pro přehrání hlasem. Prázdné pole = použije se název výše. |

### Kartička

| Pole | Význam |
|---|---|
| **Přední strana — text** | Text otázky/přední strany kartičky, jak se zobrazí uživateli. |
| **Přední strana — text pro TTS** | Nepovinné. Alternativní znění pro přehrání přední strany hlasem (užitečné, když se má text přečíst jinak, než jak je napsaný — např. rozepsat zkratku nebo použít jinou interpunkci). Prázdné pole = použije se text výše. |
| **Zadní strana — text** | Text odpovědi/zadní strany kartičky. |
| **Zadní strana — text pro TTS** | Nepovinné, funguje stejně jako u přední strany. |

### Hláskovací abeceda

Pole **Hláskovací abeceda (pro značky {X})** vybírá, která sada hláskovacích slov se použije pro text zapsaný ve složených závorkách (viz další kapitola). Editor obsahuje předdefinovanou abecedu **ITU/NATO English** (Alpha, Bravo, Charlie…) a bundlovanou **Českou hláskovací abecedu** (Adam, Božena, Cyril…). Tlačítkem 📥 lze naimportovat vlastní hláskovací abecedu ze souboru JSON ve formátu:

```json
{
  "spellId": "muj-id",
  "spellName": "Zobrazovaný název",
  "lang": "cs-CZ",
  "letters": { "A": "Adam", "B": "Božena", "0": "nula", "-": "až" }
}
```

Vybraná hláskovací abeceda je nastavení editoru (ne konkrétního balíčku) a použije se při přehrávání kteréhokoli textu obsahujícího značku `{}` — u krátkého názvu i popisu balíčku, u názvu oblasti/podoblasti i u přední/zadní strany kartičky.

## Výslovnost slov a značky `{}`

Text kteréhokoli pole je možné přehrát tlačítkem 🔊 přímo v editoru — použije se přitom hlas odpovídající **jazyku balíčku** (pole „Jazyk balíčku“).

### Co dělá `{X}`

Kdykoli je část textu obalená ve složených závorkách, např. `{DA-DR}`, `{OK2ABC}`, `{73}` nebo `{QRV?}`, přehraje se **hláskovaná po jednotlivých znacích** — každý znak (písmeno, číslice i některé speciální znaky jako `-` nebo `?`) se nahradí příslušným slovem z aktuálně vybrané **hláskovací abecedy** (viz výše) a přečte se hlasem v jazyce této abecedy (`lang` v souboru abecedy), bez ohledu na to, jaký je jazyk balíčku.

Příklad s vybranou abecedou ITU/NATO English:

- `{DA-DR}` → „Delta Alpha to Delta Romeo“
- `{OK2ABC}` → „Oscar Kilo Two Alpha Bravo Charlie“
- `{73}` → „Seven Three“
- `{QRV?}` → „Quebec Romeo Victor Question mark“

Se stejným textem, ale vybranou Českou hláskovací abecedou, se `{DA-DR}` přečte jako „David Adam až David Rudolf“.

Text mimo `{}` se čte normálně, hlasem podle jazyka balíčku — hláskovací abeceda se na něj nevztahuje. Značka `{}` funguje ve všech přehratelných polích: krátký název a popis balíčku (i jejich TTS varianty), název oblasti a podoblasti (i jejich TTS varianty) a přední/zadní strana kartičky (i jejich TTS varianty).

Pokud znak uvnitř `{}` není v hláskovací abecedě definovaný (např. neobvyklý symbol), přečte se tak, jak je napsaný.

### Velká písmena bez `{}`

Text mimo `{}` se čte normálně jako běžná řeč. U delších úseků psaných **velkými písmeny** (typicky zkratky nebo volací značky napsané verzálkami) má ovšem hlasový hlas tendenci automaticky přejít na hláskování po jednotlivých písmenech — toto chování ale zajišťuje samotný hlasový engine/prohlížeč (např. espeak-ng), ne editor, takže se nepoužije zvolená hláskovací abeceda ani jiný jazyk — písmena se přečtou svými běžnými jmény v jazyce balíčku a chování se může lišit podle použitého prohlížeče/systému.

Pokud tedy chcete zaručeně a konzistentně slyšet celá slova hláskovací abecedy (např. „Delta Alpha“, „David Adam“), musí být text obalený ve `{}`. Velká písmena bez `{}` (např. ABC) jsou hláskována systémem TTS (na Andoridu "á bé cé") a mohou být hláskována jinak na jiných zařízeních.

## Instalace TTS na Ubuntu

Pro vyzkoušení výslovnosti přímo v editoru (tlačítka 🔊) potřebuje prohlížeč funkční systémový hlas. Na Ubuntu lze nainstalovat a otestovat takto:

```
sudo apt update
sudo apt install speech-dispatcher speech-dispatcher-espeak-ng espeak-ng espeak-ng-data

espeak-ng -v cs "Toto je český test."

espeak-ng --voices | grep -i czech
```
