# An application for editing flashcard files for OK Trainer

Česká verze je zde: https://github.com/Dushino/OK-Trainer-editor/blob/main/CZ_README.md

This editor is used to create and edit the JSON flashcard files that are then used by the OK Trainer app.

- Editor (this application) is here: https://dushino.github.io/OK-Trainer-Editor/
- The trainer that uses the imported flashcards for practice is here: https://dushino.github.io/OK-Trainer/

## Data model

Flashcards are organized hierarchically:

```
deck
└─ area
   └─ subarea
      └─ card: front and back
```

A deck is identified by its **short name** and has a **language** set, in which all text that is not wrapped in curly braces `{}` is read out. Each area can additionally have an optional maximum-errors limit.

## Working with the editor

- All changes are saved **automatically** to the browser's storage (localStorage) — nothing needs to be saved manually.
- At the top of the editor you select the **deck** you want to work on (`Balíček karet`). The 📥 button imports a deck JSON file, the 📤 button exports the currently selected deck back to a JSON file (for use in the OK Trainer app or as a backup).
- Importing a deck with the same short name overwrites the existing deck of that name.
- Below the deck selector are the **area** and **subarea** selectors (➕ adds a new one, 🗑️ deletes the currently selected one — deleting asks for confirmation, since it also deletes all the cards inside it). A new area/subarea is created with one empty card.
- Cards within the selected subarea are browsed with the **◀ Previous / Next ▶** buttons; the current position is shown above them (e.g. `Area › Subarea — card 2/5`).
- The editor currently has no button to add or delete individual cards within a subarea — cards get into the file by importing a ready-made JSON file (see the deck format below). To add more cards to an existing subarea, edit the exported JSON file manually (copy a card object and add more) and re-import the deck.
- Every text field that can be played has a 🔊 button for instantly testing its pronunciation right in the editor.

## Field descriptions

### Deck

| Field | Meaning |
|---|---|
| **Krátký název balíčku** (Short deck name) | The deck's unique identifier (must be unique among the stored decks). Also used as the exported file's name. |
| **Krátký název — text pro TTS** (Short name — TTS text) | Optional. If filled in, it is used instead of the short name when played back as speech (e.g. when the short name contains abbreviations that should be pronounced differently). Empty = the short name is used. |
| **Popis balíčku** (Deck description) | The longer, readable name/description of the deck shown to the user in the trainer. |
| **Popis — text pro TTS** (Description — TTS text) | Optional. Alternative text for playing the description as speech. Empty = the description above is used. |
| **Jazyk balíčku (např. cs-CZ)** (Deck language) | The voice's language code used to read all text in the deck (outside of `{}` spans) — name, description, areas, subareas, and cards. Must match a language for which the system/browser has an installed voice (e.g. `cs-CZ`, `en-US`). |

### Area

| Field | Meaning |
|---|---|
| **Oblast** (Area) | Selects the current area in the deck; ➕ adds a new one, 🗑️ deletes the selected one (a deck must always have at least one area). |
| **Název oblasti** (Area name) | The displayed name of the area (e.g. "Basics"). |
| **Název oblasti — text pro TTS** (Area name — TTS text) | Optional. Alternative wording for playback as speech. Empty = the name above is used. |
| **Max. počet chyb v oblasti** (Max. number of errors in the area) | Optional number. Used by the OK Trainer app to evaluate practice in that area (how many errors are still allowed in it, e.g. when simulating an exam). Empty = no limit. |

### Subarea

| Field | Meaning |
|---|---|
| **Podoblast** (Subarea) | Selects the current subarea within the area; ➕ adds a new one, 🗑️ deletes the selected one (an area must always have at least one subarea). |
| **Název podoblasti** (Subarea name) | The displayed name of the subarea. |
| **Název podoblasti — text pro TTS** (Subarea name — TTS text) | Optional. Alternative wording for playback as speech. Empty = the name above is used. |

### Card

| Field | Meaning |
|---|---|
| **Přední strana — text** (Front side — text) | The question/front-side text of the card, as shown to the user. |
| **Přední strana — text pro TTS** (Front side — TTS text) | Optional. Alternative wording for playing the front side as speech (useful when the text should be read differently than it is written — e.g. spelling out an abbreviation or using different punctuation). Empty = the text above is used. |
| **Zadní strana — text** (Back side — text) | The answer/back-side text of the card. |
| **Zadní strana — text pro TTS** (Back side — TTS text) | Optional, works the same way as for the front side. |

### Spelling alphabet

The **Hláskovací abeceda (pro značky {X})** (Spelling alphabet, for `{X}` markers) field selects which set of spelling words is used for text written inside curly braces (see the next section). The editor comes with a built-in **ITU/NATO English** alphabet (Alpha, Bravo, Charlie…) and a bundled **Czech spelling alphabet** (Adam, Božena, Cyril…). The 📥 button lets you import a custom spelling alphabet from a JSON file in this format:

```json
{
  "spellId": "my-id",
  "spellName": "Display name",
  "lang": "en-US",
  "letters": { "A": "Alpha", "B": "Bravo", "0": "Zero", "-": "to" }
}
```

The selected spelling alphabet is a setting of the editor (not of a specific deck) and is applied whenever playing back any text containing a `{}` marker — in the deck's short name and description, in the area/subarea name, and in the card's front/back.

## Pronunciation and the `{}` markers

The text of any field can be played back with the 🔊 button right in the editor — the voice used matches the **deck's language** (the "Jazyk balíčku" field).

### What `{X}` does

Whenever a part of the text is wrapped in curly braces, e.g. `{DA-DR}`, `{OK2ABC}`, `{73}`, or `{QRV?}`, it is played back **spelled out character by character** — each character (letter, digit, and some special characters like `-` or `?`) is replaced with the corresponding word from the currently selected **spelling alphabet** (see above) and read in that alphabet's language (`lang` in the alphabet file), regardless of the deck's language.

Example with the ITU/NATO English alphabet selected:

- `{DA-DR}` → "Delta Alpha to Delta Romeo"
- `{OK2ABC}` → "Oscar Kilo Two Alpha Bravo Charlie"
- `{73}` → "Seven Three"
- `{QRV?}` → "Quebec Romeo Victor Question mark"

With the same text but the Czech spelling alphabet selected, `{DA-DR}` is read as "David Adam až David Rudolf".

Text outside `{}` is read normally, in the voice matching the deck's language — the spelling alphabet does not apply to it. The `{}` marker works in every playable field: the deck's short name and description (and their TTS variants), the area/subarea name (and their TTS variants), and the card's front/back (and their TTS variants).

If a character inside `{}` is not defined in the spelling alphabet (e.g. an unusual symbol), it is read out as written.

### Uppercase letters without `{}`

Text outside `{}` is read normally as regular speech. For longer runs written in **uppercase letters** (typically abbreviations or call signs written in caps), the voice does tend to automatically switch to spelling them out letter by letter — but this behavior comes from the speech engine/browser itself (e.g. espeak-ng), not from the editor, so it does not use the selected spelling alphabet or a different language — the letters are read with their ordinary names in the deck's language, and the behavior can vary between browsers/systems.

So if you want to reliably and consistently hear the full spelling-alphabet words (e.g. "Delta Alpha", "David Adam"), the text must be wrapped in `{}`. Uppercase letters without `{}` (e.g. ABC) are spelled out by the device's TTS system (on Android as "ay bee cee") and may be spelled differently on other devices.

## TTS installation on Ubuntu

To try out pronunciation directly in the editor (the 🔊 buttons), the browser needs a working system voice. On Ubuntu it can be installed and tested like this:

```
sudo apt update
sudo apt install speech-dispatcher speech-dispatcher-espeak-ng espeak-ng espeak-ng-data

espeak-ng -v cs "Toto je český test."

espeak-ng --voices | grep -i czech
```
