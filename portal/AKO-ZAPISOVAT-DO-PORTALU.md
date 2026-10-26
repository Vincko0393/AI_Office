# Portál Bar Demo Linz – pokyny pre agentov

Portál: index.html
Všetky dáta sú vymyslené.

Portál má vlastnú databázu. Agent do nej zapisuje nástrojom `ArtifactData` s `url` portálu.

## 1. Denník (kolekcia `dennik`)
Po každej dokončenej alebo rozbehnutej úlohe pridaj jeden dokument (`set`, nové `doc_id`, napr. `marketing-2026-10-26-1230`):

```json
{"agent": "Marketing", "co": "Jedna veta, čo som spravil.", "stav": "pracujem | hotové | chyba",
 "cas": "2026-10-26T12:30:00Z", "odkaz": "<odkaz na svoje vlákno>"}
```

## 2. Úlohy od šéfa barmana (kolekcia `ulohy`)
Portál sem ukladá požiadavky z formulárov. Polia: `bot` (n8n, smeny, marketing, analytik, pr), `akcia`, `nazov`, `data`, `subor` ({id, meno, typ}; súbor je na adrese portálu `/_blob/<id>`), `stav`, `vytvorene`.

Agent si nájde svoje úlohy (`query` kde `bot` == jeho id) a mení `stav` cez `update` s `if_version`:
`nová` → `pracujem` → `hotové` alebo `chyba`. Krátka odpoveď pre Petra ide do poľa `odpoved`.

Marketing: návrh príspevku zapíš do poľa `navrh` ({instagram_text, hashtagy, google_text, google_tlacidlo, preklad_sk, napis_na_foto, poznamka_pre_majitela}) a stav `na schválenie`. Peter klikne Schváliť (stav `schválené`) alebo Prepísať (stav `prepísať` + pole `poznamka_majitela`).

## 3. Stav čašníka (kolekcia `boti`, dokument = id čašníka)
`stav_text`: jedna veta, ktorá sa zobrazí na karte čašníka. Agent n8n môže v dokumente `n8n` meniť aj zoznam `workflows` ({nazov, kedy, stav, pozn}).

## 4. Profil firmy (`firma/profil`)
`nazov`, `odvetvie`, `mesto`, `jazyk_hosti`, `ton`. Pri „zmeň podnik na …“ sa prepíše tento dokument.
