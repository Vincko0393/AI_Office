# Rozpis smien – Bar Demo Linz

Oct 26, 2026 · @Peter

Každý týždeň: želania do štvrtka, návrh v piatok, schválený rozpis v sobotu. Zamestnanci aj dáta sú vymyslené.

## Ako to bude fungovať

1. **Štvrtok do 18:00:** zamestnanci zadajú želania a voľno. Zároveň načítam rezervácie a eventy na ďalší týždeň.
2. **Piatok:** z rezervácií, eventov a obedového menu vypočítam, koľko ľudí treba v každej smene. Návrh poskladám tak, aby sedel na zmluvné hodiny.
3. **Sobota:** rozpis schváliš alebo upravíš, potom ide tímu.
4. **Počas týždňa:** pri novej veľkej rezervácii alebo evente upozorním a navrhnem posilu.

Smeny a pravidlá:

- **Ranná** 7:30 – 15:30 (8 h): raňajky a obed
- **Obedová posila** 11:00 – 15:00 (4 h): Po – Pi kvôli obedovému menu
- **Večerná** 15:30 – 0:30 (8 h, 1 h prestávka)
- **Večerná posila** 18:00 – 23:00 (5 h): pri plnom večere alebo evente
- Po večernej nikto nejde na rannú ani obedovú (oddych aspoň 11 h); po posile nikto na rannú
- Každý má aspoň 2 dni voľna
- Hodiny sedia na zmluvu s odchýlkou najviac ±4 h, ktorá sa vyrovná do konca mesiaca
- Každá smena má vedúceho

## Rezervácie, eventy a menu

Počet ľudí v smene určuje táto tabuľka. Hranicu 40 hostí môžeme posunúť.

| Situácia | Obsadenie |
| --- | --- |
| Obed Po – Pi (obedové menu) | 1 ranná + 1 obedová posila |
| Ráno a obed So – Ne | 2 ranné |
| Večer do 40 rezervovaných hostí | 2 večerné |
| Večer od 40 hostí (špička) | + 1 večerná posila |
| Event (live hudba, oslava) | + 1 večerná a + 1 posila |
| Nedeľa večer | 1 večerná |

Vymyslené vstupy na týždeň 2. – 8. 11.:

| Deň | Večer rezervovaní hostia | Event | Obedové menu |
| --- | --- | --- | --- |
| Po 2. 11. | 18 | – | áno |
| Ut 3. 11. | 22 | – | áno |
| St 4. 11. | 26 | – | áno |
| Št 5. 11. | 44 | – | áno |
| Pi 6. 11. | 58 | Jazzový piatok | áno |
| So 7. 11. | 52 | Oslava, 30 osôb | nie |
| Ne 8. 11. | 15 | – | nie |

Prepojenie: rezervácie a eventy pôjdu do Google tabuľky (hárky *rezervacie* a *eventy*). n8n ich každý štvrtok načíta a podľa tabuľky vyššie vypočíta potrebu ľudí.

- [ ] Rezervačný systém zatiaľ nie je. Rezervácie sa zatiaľ zapisujú priamo do Google tabuľky, neskôr sa dá napojiť akýkoľvek systém cez API.

## Tím

Osem vymyslených zamestnancov má spolu 228 zmluvných hodín. Na týždeň 2. – 8. 11. treba 227 hodín.

| Meno | Rola | Zmluva | Hodiny zmluva | Hodiny rozpis | Rozdiel | Obmedzenia |
| --- | --- | --- | --- | --- | --- | --- |
| Lena | Vedúca rán | Plný úväzok | 40 | 40 | 0 | Len ranné |
| Tobias | Vedúci večerov | Plný úväzok | 40 | 40 | 0 | Radšej večerné |
| Jonas | Obsluha | Plný úväzok | 40 | 40 | 0 | – |
| Mira | Barmanka, zástup večerov | Čiastočný úväzok | 30 | 29 | −1 | – |
| Emil | Zástup rán, víkendy | Čiastočný úväzok | 30 | 28 | −2 | Nie v piatok |
| Sara | Obsluha, študentka | Študentská brigáda | 20 | 20 | 0 | Po – Št škola |
| Felix | Barman | Brigáda s limitom zárobku | 16 | 16 | 0 | Len večerné |
| Nina | Obsluha | Brigáda s limitom zárobku | 12 | 14 | +2 | Nie v piatok |

## Rozpis na týždeň 2. – 8. 11. 2026

Návrh spĺňa všetky pravidlá, zmluvy aj obsadenie podľa rezervácií. Vedúci smeny je prvý v bunke.

| Deň | Ranná 7:30 – 15:30 | Obed 11 – 15 | Večerná 15:30 – 0:30 | Posila 18 – 23 | Prečo |
| --- | --- | --- | --- | --- | --- |
| Po 2. 11. | Lena | Nina | Tobias, Jonas | – | Obedové menu, 18 hostí |
| Ut 3. 11. | Lena | Emil | Tobias, Jonas | – | Obedové menu, 22 hostí |
| St 4. 11. | Lena | Emil | Tobias, Mira | – | Obedové menu, 26 hostí |
| Št 5. 11. | Lena | Emil | Mira, Jonas | Nina | 44 hostí, špička |
| Pi 6. 11. | Lena | Sara | Tobias, Jonas, Felix | Mira | Jazzový piatok, 58 hostí |
| So 7. 11. | Emil, Sara | – | Tobias, Mira, Felix | Nina | Oslava 30 osôb, 52 hostí |
| Ne 8. 11. | Emil, Sara | – | Jonas | – | 15 hostí |

Kontrola: všetky smeny obsadené, hodiny každého do ±2 h od zmluvy, nikto nejde z večernej na rannú, každý má aspoň 2 dni voľna. Cez víkend ráno vedie Emil.

## Pre Google tabuľku

Tento text vložíš do Google tabuľky (Súbor → Importovať) a n8n ho potom môže čítať. Každý riadok je jedna smena jedného človeka; čiarky oddeľujú stĺpce: dátum, smena, meno, či je vedúci.

```csv
datum,smena,od,do,meno,veduci
2026-11-02,ranna,07:30,15:30,Lena,ano
2026-11-02,obed,11:00,15:00,Nina,nie
2026-11-02,vecerna,15:30,00:30,Tobias,ano
2026-11-02,vecerna,15:30,00:30,Jonas,nie
2026-11-03,ranna,07:30,15:30,Lena,ano
2026-11-03,obed,11:00,15:00,Emil,nie
2026-11-03,vecerna,15:30,00:30,Tobias,ano
2026-11-03,vecerna,15:30,00:30,Jonas,nie
2026-11-04,ranna,07:30,15:30,Lena,ano
2026-11-04,obed,11:00,15:00,Emil,nie
2026-11-04,vecerna,15:30,00:30,Tobias,ano
2026-11-04,vecerna,15:30,00:30,Mira,nie
2026-11-05,ranna,07:30,15:30,Lena,ano
2026-11-05,obed,11:00,15:00,Emil,nie
2026-11-05,vecerna,15:30,00:30,Mira,ano
2026-11-05,vecerna,15:30,00:30,Jonas,nie
2026-11-05,posila,18:00,23:00,Nina,nie
2026-11-06,ranna,07:30,15:30,Lena,ano
2026-11-06,obed,11:00,15:00,Sara,nie
2026-11-06,vecerna,15:30,00:30,Tobias,ano
2026-11-06,vecerna,15:30,00:30,Jonas,nie
2026-11-06,vecerna,15:30,00:30,Felix,nie
2026-11-06,posila,18:00,23:00,Mira,nie
2026-11-07,ranna,07:30,15:30,Emil,ano
2026-11-07,ranna,07:30,15:30,Sara,nie
2026-11-07,vecerna,15:30,00:30,Tobias,ano
2026-11-07,vecerna,15:30,00:30,Mira,nie
2026-11-07,vecerna,15:30,00:30,Felix,nie
2026-11-07,posila,18:00,23:00,Nina,nie
2026-11-08,ranna,07:30,15:30,Emil,ano
2026-11-08,ranna,07:30,15:30,Sara,nie
2026-11-08,vecerna,15:30,00:30,Jonas,ano
```

## Vstupy cez portál a API

Po kliknutí na bota Plánovanie smien sa v portáli otvoria tri možnosti. Každá má aj API adresu, takže to isté môže poslať rezervačný systém alebo n8n.

| Možnosť v portáli | Čo zadáš | Čo spravím | API (cez n8n webhook) |
| --- | --- | --- | --- |
| Nahrať súbor | CSV alebo Excel: rezervácie, želania, zmluvy | Načítam a prepočítam potrebu ľudí | POST /smeny/subor |
| Pridať event | Dátum, čas, názov, počet hostí | Pridám posily podľa pravidiel | POST /smeny/event |
| Nahlásiť chorobu | Meno, od – do | Nájdem náhradu, ktorá sedí na zmluvu a oddych, a pošlem ti ju na schválenie | POST /smeny/choroba |

Príklad choroby: Jonas je chorý v piatok 6. 11. Návrh: večernú zoberie Mira namiesto posily (+3 h) a posilu Sara po obedovej smene (+5 h, nad zmluvou, treba tvoj súhlas).
