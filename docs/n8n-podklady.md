# Podklady pre portál – n8n automatizácie

Oct 26, 2026 · @Peter

## Prehľad

V n8n Cloud (<your-instance>.app.n8n.cloud) bežia 3 automatizácie a 1 je rozostavaná. Všetky dáta sú vymyslené, podnik je Bar Demo Linz.

| Workflow v n8n | Čo robí | Kedy | Stav |
| --- | --- | --- | --- |
| Bar Demo Linz – Daily Shift Checklist | Pošle e‑mail s dnešnými úlohami a úlohami „každý deň“ z tabuľky úloh | denne 15:00 | beží, test OK |
| Bar Demo Linz – Low Stock Alert | Pošle e‑mail s položkami pod minimom a koľko objednať (2 × minimum − na sklade) | denne 10:00 | beží, test OK |
| Bar Demo Linz – Error Alert | Pošle e‑mail, keď niektorá automatizácia zlyhá | pri chybe | beží, zatiaľ len simulovaný test |
| Bar Demo Linz – Marketing Agent | Z fotky alebo videa a poznámky napíše cez Gemini príspevok na Instagram a Google a uloží ho na schválenie | pri odoslaní formulára | rozostavaný, test fotky spadol na nedostatok pamäte |

Skúšobná verzia n8n končí okolo 4. 11. 2026. Potom treba platený plán (Starter stačí) alebo vlastný server.

## Obrazovky portálu

Portál bude mať päť obrazoviek, ktoré sa napájajú na n8n: štyri v tabuľke nižšie a bota Plánovanie smien (opis je v časti Plánovanie smien). Používatelia do n8n nikdy nechodia, portál posiela dáta do n8n cez webhook (adresu, na ktorú portál pošle dáta) a n8n ich zapíše do Google tabuliek.

| Obrazovka | Kto ju používa | Čo tam robí | Napojenie na n8n |
| --- | --- | --- | --- |
| AI robot: Úlohy na smenu | barman, vedúci | pridá úlohu s dátumom alebo „každý deň“ a kto ju robí | webhook Pridať úlohu → nový riadok v tabuľke úloh |
| AI robot: Inventúra | barman po smene | prepíše počty „Na sklade“ | webhook Aktualizovať zásoby → prepíše riadky v tabuľke inventúry |
| Marketing: Nový príspevok | zákazník (majiteľ podniku) | nahrá fotku alebo video a pár slov | webhook Nový príspevok → rovnaký postup ako dnes formulár |
| Marketing: Na schválenie | zákazník | pozrie návrh, klikne Schváliť alebo Prepísať s poznámkou, potom stiahne a zverejní ručne | portál číta tabuľku návrhov, tlačidlá volajú webhook Schválenie |

Odporúčam formuláre v portáli namiesto vloženej Google tabuľky. Barman tak nepotrebuje Google účet a nemôže omylom zmazať celú tabuľku.

Denník agentov (riadky agent, čo spravil, stav, čas, odkaz) je samostatná časť portálu a s n8n nesúvisí.

## Webhooky

Portál bude posielať do n8n sedem druhov správ: štyri nižšie a tri pre Plánovanie smien v ďalšej časti. Toto je zadanie pre toho, kto bude portál stavať. Adresy webhookov vzniknu v n8n, až keď webhooky postavíme.

| Webhook | Kedy ho portál volá | Čo n8n spraví | Čo vráti portálu |
| --- | --- | --- | --- |
| Pridať úlohu | barman uloží úlohu | pridá riadok do tabuľky úloh | ok alebo chyba |
| Aktualizovať zásoby | barman uloží inventúru | prepíše stĺpec Na sklade podľa Položky | zoznam položiek pod minimom |
| Nový príspevok | zákazník odošle fotku alebo video | kontrola limitu, AI texty, uloženie do Drive a tabuľky | návrh príspevku alebo „Limit je vyčerpaný“ |
| Schválenie | zákazník klikne Schváliť alebo Prepísať | zmení Stav, pri Prepísať nechá AI prepísať text podľa poznámky | nový stav a text |

Dáta, ktoré portál pošle (názvy polí sú pevné, aby im n8n rozumel):

```json
// Pridať úlohu
{"datum": "27.10.2026 alebo každý deň", "uloha": "Vyčistiť výčap", "kto": "Barman"}

// Aktualizovať zásoby
{"polozky": [{"polozka": "Citróny", "na_sklade": 25}, {"polozka": "Aperol", "na_sklade": 3}]}

// Nový príspevok (odosiela sa ako formulár so súborom)
{"zakaznik": "Bar Demo Linz", "typ": "foto", "subor": "<fotka alebo video, najviac 5 MB>", "poznamka": "nový koktail, piatok"}

// Schválenie
{"riadok": 12, "akcia": "schvalit alebo prepisat", "poznamka": "menej emoji"}
```

AI vracia pri príspevku polia instagram\_text, hashtagy, google\_text, google\_tlacidlo, preklad\_sk, napis\_na\_foto a poznamka\_pre\_majitela. Presné pravidlá sú v [zadaní od agenta Marketing](marketing-agent.md).

Každý webhook musí mať tajný kľúč v hlavičke, aby do n8n nemohol posielať dáta ktokoľvek, kto pozná adresu.

## Plánovanie smien

Po kliknutí na bota Plánovanie smien sa v portáli otvoria tri možnosti. Každá má aj vlastný webhook, takže to isté môže poslať aj rezervačný systém. Zadanie pripravil agent Plánovanie smien v dokumente [Rozpis smien – Bar Demo Linz](rozpis-smien.md).

| Možnosť v portáli | Čo zadá používateľ | Čo sa stane | Webhook |
| --- | --- | --- | --- |
| Nahrať súbor | CSV alebo Excel: rezervácie, želania, zmluvy | agent načíta súbor a prepočíta potrebu ľudí | POST /smeny/subor |
| Pridať event | dátum, čas, názov, počet hostí | agent pridá posily podľa pravidiel | POST /smeny/event |
| Nahlásiť chorobu | meno, od – do | agent nájde náhradu, ktorá sedí na zmluvu a oddych, a pošle ju majiteľovi na schválenie | POST /smeny/choroba |

```json
// /smeny/subor (odosiela sa ako formulár so súborom)
{"druh": "rezervacie alebo zelania alebo zmluvy", "subor": "<CSV alebo XLSX>"}

// /smeny/event
{"datum": "06.11.2026", "cas_od": "19:00", "cas_do": "23:00", "nazov": "Firemný večierok", "pocet_hosti": 40}

// /smeny/choroba
{"meno": "Jonas", "od": "06.11.2026", "do": "06.11.2026"}
```

Pri chorobe sa návrh náhrady nezapíše sám. Majiteľ ho v portáli schváli, najmä keď náhrada ide nad zmluvu. Názvy polí v JSON sú môj návrh, agent Plánovanie smien ich môže upraviť.

## Tabuľky a priečinky

Všetko je v Google Drive účtu <owner-email> a s vymyslenými dátami.

| Názov | Stĺpce | Používa |
| --- | --- | --- |
| Úlohy na smenu (Google link removed) | Dátum (DD.MM.YYYY alebo každý deň), Úloha, Kto | Daily Shift Checklist, AI robot Úlohy |
| Inventúra (Google link removed) | Položka, Jednotka, Na sklade, Minimum, Dodávateľ | Low Stock Alert, AI robot Inventúra |
| Marketing návrhy (Google link removed) | Dátum, Zákazník, Typ, Súbor, Poznámka od zákazníka, Instagram text, Hashtagy, Google text, Google tlačidlo, Preklad SK, Nápis na foto, Poznámka pre majiteľa, Stav | Marketing Agent, obrazovka Na schválenie |
| Marketing súbory (priečinok) (Google link removed) | upravené fotky a videá | Marketing Agent |
| Recenzie (Google link removed) | Dátum, Meno, Hviezdičky, Recenzia, Návrh odpovede, Stav | nepoužíva sa, odpovede na recenzie sme zrušili |

## Čo spravíme, keď bude portál

Keď bude portál, v n8n pribudnú 4 webhooky a marketingový formulár sa vymení za webhook. Bežiace automatizácie sa nemenia, čítajú stále tie isté tabuľky.

- [ ] Peter pošle odkaz na portál a prenesieme zápisy agenta (zatiaľ v súbore n8n-agent.csv v projekte)
- [ ] V n8n postaviť webhooky Pridať úlohu a Aktualizovať zásoby
- [ ] Marketing Agent: vymeniť formulár za webhook Nový príspevok a pridať webhook Schválenie
- [ ] Marketing Agent: opraviť pád pri zmenšovaní fotky (vyhodiť zmenšenie pred AI, limit súboru 5 MB) a znova otestovať
- [ ] Rozhodnúť pred 4. 11. 2026: platený n8n Starter alebo vlastný server
- [ ] Pri prvom platiacom klientovi prepnúť Gemini na Claude
- [ ] V n8n postaviť webhooky /smeny/subor, /smeny/event a /smeny/choroba podľa zadania agenta Plánovanie smien

Video AI nevidí, n8n Cloud z neho nevie vybrať zábery. Pri videu sa text píše len z poznámky zákazníka.
