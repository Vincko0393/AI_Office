# Agent Marketing pre portál – zadanie

Oct 26, 2026 · @Peter

Zákazník nahrá na portál fotku alebo video s pár slovami, agent napíše príspevok na Instagram a Google a pošle ho na schválenie. Zverejňovanie je ručné.

## Ako to funguje

&#91;embedded content: cesta príspevku · 6 krokov, 2 rozhodnutia\]

Ak majiteľ príspevok neschváli, Claude prepíše text podľa jeho poznámky. Zverejňuje sa až po schválení, ručne.

## Čo agent dostane a čo vráti

Agent dostane 4 veci z portálu a vráti 6 polí, ktoré portál uloží ako príspevok na schválenie.

**Vstup (posiela portál cez n8n)**

| Pole | Čo obsahuje | Príklad |
| --- | --- | --- |
| `obrazky` | 1 fotka, pri videu zatiaľ prázdne | foto.jpg |
| `poznamka` | pár slov od zákazníka | nový koktail, piatok |
| `profil` | názov podniku, mesto, jazyk hostí, tón | Bar Demo Linz, Linz, nemčina, priateľský |
| `typ` | foto alebo video | foto |

**Výstup (vracia Claude)**

| Pole | Čo obsahuje |
| --- | --- |
| `instagram_text` | text príspevku, 2 až 4 vety |
| `hashtagy` | 3 až 6 hashtagov |
| `google_text` | kratší text bez hashtagov |
| `google_tlacidlo` | Zavolať, Rezervovať alebo Viac info |
| `preklad_sk` | slovenský preklad pre majiteľa |
| `napis_na_foto` | krátky nápis do obrázka, najviac 5 slov, alebo prázdne |

## Pokyny pre Claude API

Tento text sa vloží do n8n ako „System prompt“ uzla Claude. Odporúčaný model je `claude-sonnet-5-5`, lebo je rýchly a lacnejší ako Opus.

```text
Si marketingový asistent pre malé podniky (bary, kaviarne, reštaurácie).
Dostaneš fotku alebo zábery z videa, krátku poznámku od majiteľa a profil podniku.

Tvoja úloha: napíš jeden príspevok na Instagram a jeden na Google profil.

Pravidlá:
1. Píš v jazyku z profilu (pole jazyk). Slovenský preklad daj len do preklad_sk.
2. Používaj tón z profilu. Píš krátko, priateľsky, bez prehnaných sľubov.
3. Opíš len to, čo je na obrázku alebo v poznámke.
4. Nikdy si nevymýšľaj ceny, dátumy, časy ani akcie. Ak chýbajú, vynechaj ich.
5. Instagram: 2 až 4 vety, najviac 2 emoji, 3 až 6 hashtagov vrátane názvu podniku a mesta.
6. Google: 1 až 2 vety, bez hashtagov a bez emoji.
7. Ak je na obrázku niečo nevhodné alebo nejasné, napíš to do poznamka_pre_majitela.
8. Ak nedostaneš žiadny obrázok (napríklad pri videu), píš len z poznámky a profilu, nehovor, čo je na videu, a napis_na_foto nechaj prázdne.

Odpovedz iba týmto JSON, bez ďalšieho textu:
{
  "instagram_text": "",
  "hashtagy": [],
  "google_text": "",
  "google_tlacidlo": "Zavolať | Rezervovať | Viac info",
  "preklad_sk": "",
  "napis_na_foto": "",
  "poznamka_pre_majitela": ""
}
```

**Čo robí každá časť:**

- Prvé dva riadky povedia Claudovi, kým je a čo dostane.
- „Tvoja úloha“ hovorí, čo má vyrobiť.
- Pravidlo 1 zabezpečí, že príspevok bude v jazyku hostí, napríklad po nemecky v Linzi.
- Pravidlo 4 je najdôležitejšie: agent nesmie sľubovať zľavy alebo ceny, ktoré zákazník nezadal.
- Pravidlá 5 a 6 určujú dĺžku a hashtagy pre každú sieť.
- Pravidlo 7 upozorní majiteľa, ak je fotka rozmazaná alebo nevhodná. Pravidlo 8 rieši video: Claude ho nevidí, tak si nesmie vymýšľať, čo na ňom je.
- JSON na konci je pevný formulár. Claude ho vyplní a n8n z neho vie vybrať každé pole a uložiť ho na portál.

K výstupu pribudlo pole `poznamka_pre_majitela`, ktoré portál ukáže len majiteľovi.

## Fotka, video a limity

Claude API vie čítať obrázky, ale nie video. Preto n8n spracuje súbor pred aj po Claudovi.

**Pred Claudom (n8n):**

1. Skontroluje limit zákazníka na tento mesiac: 20 príspevkov s fotkou a 5 s videom. Ak je vyčerpaný, Clauda nevolá a portál ukáže správu „Limit na tento mesiac je vyčerpaný“.
2. Pri fotke ju zmenší na šírku 1080 px, aby bolo volanie lacnejšie.
3. Pri videu zatiaľ pošle len poznámku a profil, bez obrázkov. n8n Cloud nevie z videa vybrať zábery (chýba mu ffmpeg), preto je dobré, keď zákazník k videu napíše viac slov.

**Po Claudovi (n8n):**

1. Pri fotke ju oreže na formát Instagramu 4:5 (1080 × 1350 px).
2. Ak `napis_na_foto` nie je prázdny, vloží nápis do spodnej časti fotky.
3. Uloží príspevok na portál so stavom „Na schválenie“ a pripočíta 1 k limitu.
4. Video zostane nezmenené, ukladá sa len text.

Po schválení si majiteľ fotku a text stiahne a zverejní ručne. Limity 20 a 5 sú na začiatok, upravíte ich po prvom mesiaci podľa skutočných nákladov.
