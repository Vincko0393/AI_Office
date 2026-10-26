# Odkiaľ brať dáta o predajoch

Oct 26, 2026 · @Peter

Tento dokument pomôže dohodnúť s kupujúcim, odkiaľ bude týždenný report brať čísla o predajoch. Pre Bar Demo Linz zatiaľ používame len vymyslené dáta.

## Prečo to riešime

Týždenný report potrebuje každý pondelok čísla za predošlý týždeň: čo sa predalo, koľko kusov, za koľko a v ktorý deň. Dnes si tieto čísla vymýšľam sám, preto treba určiť jedno stále miesto, odkiaľ ich budem brať.

Všetky tri možnosti dodajú rovnaký report s grafmi a 3 odporúčaniami. Líšia sa tým, kto čísla dodá, koľko práce to dá a ako podrobný report môže byť.

## Porovnanie možností

Najrýchlejší štart je A, najmenej práce dlhodobo dáva C.

|  | A: Google tabuľka | B: Export z pokladne | C: n8n z pokladne |
| --- | --- | --- | --- |
| Kto dodá čísla | Personál alebo majiteľ ich zapíše | Majiteľ raz týždenne stiahne súbor | Automaticky, nikto |
| Práca pre podnik | Cca 10–15 min denne | Cca 5 min týždenne | Žiadna po nastavení |
| Čo treba mať | Google účet | Pokladňu s exportom do CSV/Excel | Pokladňu s API prístupom, n8n |
| Čas na spustenie | Hneď (už existuje ukážka) | 1 deň | 1–2 týždne |
| Graf podľa hodín | Nie, len denné súčty | Áno | Áno |
| Riziko chýb | Vyššie (ručné prepisovanie) | Nízke | Nízke |
| Hodí sa, keď | Podnik nemá modernú pokladňu | Pokladňa vie export, ale nie API | Pokladňa má API a chceme to nadlho |

## Ako by fungovala každá možnosť

### A: Google tabuľka

1. Na Google Drive je jedna tabuľka „Sales“. Jeden riadok = jeden produkt za jeden deň (dátum, produkt, kategória, kusy, cena, tržba).
2. Na konci každého dňa niekto prepíše čísla z pokladne alebo z denného výkazu.
3. V pondelok ráno si tabuľku prečítam a spravím report.

Ukážka s vymyslenými dátami už existuje: Bar Demo Linz – Sales (FICTIONAL DATA) (Google link removed).

### B: Export z pokladne

1. Majiteľ v pondelok v pokladni klikne na export predajov za minulý týždeň (CSV alebo Excel).
2. Súbor mi nahrá sem do projektu.
3. Report spravím z neho, vrátane grafu podľa hodín.

### C: n8n ťahá dáta z pokladne

1. Agent n8n postaví automatizáciu, ktorá sa každú noc pripojí na pokladňu.
2. Stiahne predaje za deň a zapíše ich do Google tabuľky z možnosti A.
3. V pondelok si tabuľku prečítam a spravím report. Nikto nič nepreklikáva.

A a C sa dajú spojiť: začneme ručnou tabuľkou a neskôr ju začne plniť n8n.

## Otázky pre kupujúceho

Odpovede na tieto otázky rozhodnú, ktorá možnosť sedí.

- [ ] Akú pokladňu (pokladňičný systém) podnik používa? Názov a verzia.
- [ ] Vie pokladňa exportovať predaje do CSV alebo Excelu?
- [ ] Má pokladňa API (prepojenie pre iné programy)? Ak áno, kto dá prístup?
- [ ] Kto bude zodpovedný za dáta: majiteľ, manažér, alebo nikto (automaticky)?
- [ ] Stačia denné súčty, alebo chce vidieť aj predaje po hodinách?
- [ ] Ktorý deň a o koľkej má report prísť a komu (e-mail, portál)?
- [ ] Smú čísla o predajoch ležať v Google Drive (ochrana dát)?
