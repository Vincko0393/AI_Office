# n8n automatizácie – Bar Demo Linz (zhrnutie k 26. 10. 2026)

Všetky dáta sú vymyslené (ukážkový podnik).

## Účet n8n
- n8n Cloud, skúšobná verzia: <your-instance>.app.n8n.cloud
- Prihlásenie: <owner-email>
- Skúška končí približne 4. 11. 2026 (k 26. 10. ukazovalo „9 days left“). Potom workflowy prestanú bežať, ak sa neprejde na platený plán.
- Prepojené účty v n8n: „Gmail account“ a „Google Sheets account“ (oba <owner-email>).
- Workflowy staviame cez n8n AI asistenta (+ → New AI chat): Peter vloží anglické zadanie a schvaľuje kroky cez „Allow once“.

## Bežiace automatizácie
| Workflow v n8n | Čo robí | Kedy | Stav |
|---|---|---|---|
| Bar Demo Linz – Daily Shift Checklist | Prečíta tabuľku úloh, vyberie úlohy s dnešným dátumom a tie s „každý deň“, pošle e‑mail „Bar Demo Linz – úlohy na smenu DD.MM.YYYY“ | denne 15:00 (Europe/Vienna) | publikovaný, test OK (5 úloh) |
| Bar Demo Linz – Low Stock Alert | Prečíta tabuľku inventúry, nechá položky s „Na sklade“ pod „Minimum“, pošle e‑mail „treba objednať“. Objednať = Minimum × 2 − Na sklade. Ak nič nechýba, nepošle nič. | denne 10:00 | publikovaný, test OK (5 položiek) |
| Bar Demo Linz – Error Alert | Pri zlyhaní ktoréhokoľvek z dvoch workflowov pošle e‑mail „⚠️ n8n chyba: …“ s krokom, chybou, časom a odkazom | pri chybe | publikovaný a priradený k obom. Skutočný e‑mail ešte neprišiel (test bol simulovaný). |

## Google tabuľky (Peterov Drive)
- Úlohy na smenu (stĺpce Dátum | Úloha | Kto; Dátum = DD.MM.YYYY alebo „každý deň“):
  <private Google link removed>
- Inventúra (stĺpce Položka | Jednotka | Na sklade | Minimum | Dodávateľ):
  <private Google link removed>
- Recenzie (nepoužíva sa, automatizácia recenzií zrušená na Petrovo želanie):
  <private Google link removed>

## Petrove rozhodnutia
- Každá úloha má v tabuľke dátum a n8n ju pošle v ten deň.
- Odpovede na recenzie nerobíme.
- Strážca chýb áno.
- Klient a barman nebudú chodiť do n8n. V portáli bude „AI robot“, ktorý barmanovi otvorí tabuľku (úlohy, inventúru) a ten do nej zapisuje. n8n číta stále tie isté tabuľky, takže workflowy sa meniť nebudú.

## Otvorené
1. Portál je hotový: ../portal/index.html. Denník sa zapisuje cez ArtifactData do kolekcie `dennik`, úlohy pre bota n8n sú v `ulohy` (bot == n8n), stav v `boti/n8n`. Pokyny: portal/AKO-ZAPISOVAT-DO-PORTALU.md. CSV zápisy už preniesol koordinátor.
2. AI robot v portáli pre barmana. Treba rozhodnúť: zdieľať Google tabuľku s barmanovým Google účtom, alebo spraviť v portáli formulár, ktorý pošle dáta do n8n (webhook) a n8n ich zapíše do tabuľky.
3. Voliteľne: „Track time saved“ v n8n (argument pri predaji klientovi) a MCP prístup (Claude by mohol stavať workflowy priamo).
4. Pred koncom skúšky rozhodnúť o platenom pláne.

## Nová úloha: marketingový agent (od 26. 10., zadal Peter cez marketingové vlákno)
Cieľ: zákazník nahrá fotku/video a pár slov → Claude napíše text príspevku a hashtagy → návrh čaká na schválenie → zákazník stiahne a zverejní ručne (bez Metricoolu). Limit: 20 foto a 5 video príspevkov mesačne na zákazníka.
Kým portál neexistuje, testujeme cez **n8n Form** (formulár s nahraním súboru, má vlastný odkaz):
1. n8n Form: Zákazník, Typ (foto/video), Súbor, Pár slov, Sieť (Instagram / Google profil).
2. Kontrola limitu: spočíta riadky zákazníka v tomto mesiaci v tabuľke návrhov (foto 20, video 5). Nad limit → formulár odpovie „limit vyčerpaný“.
3. Súbor uloží do Drive priečinka.
4. Claude (Anthropic node): pri fotke pozrie obrázok, pri videe len popis (video nevidí) → text + hashtagy v nemčine a slovenčine.
5. Zapíše riadok so stavom „čaká na schválenie“ a pošle e‑mail s návrhom a odkazom na stiahnutie.
6. Schválenie: zatiaľ prepísaním stavu v tabuľke na „schválené“, neskôr tlačidlo v portáli.
Neskôr portál pošle to isté do n8n cez webhook namiesto formulára.
- Drive priečinok: <private Google link removed>
- Tabuľka návrhov (Dátum | Zákazník | Typ | Súbor | Poznámka od zákazníka | Instagram text | Hashtagy | Google text | Google tlačidlo | Preklad SK | Nápis na foto | Poznámka pre majiteľa | Stav): <private Google link removed>
- Krok 1: Peter si vytvorí Anthropic API kľúč (console.anthropic.com, predplatený kredit).
- Systémový prompt a výstupný JSON pre Claude pripravil agent Marketing: ../docs/marketing-agent.md (model claude-sonnet-5-5).
- Obmedzenie n8n Cloud: video sa nedá rozložiť na zábery (chýba ffmpeg / Execute Command). Pri videu Claude píše len z poznámky. Fotka: zmenšenie, orez 4:5 a nápis cez uzol Edit Image (treba overiť, či funguje na Cloude).
- Peter momentálne nechce platiť za Claude API. Rozhodnutie (26. 10.): testujeme zadarmo s Google Gemini (kľúč z aistudio.google.com). Na Claude prepneme, keď bude platiaci klient.
- 26. 10. 12:00: Peter zastavil testovanie marketingu ("nechajme to tak") a chce mať všetko pripravené na portál. Test fotky spadol na nedostatok pamäte v kroku Resize Photo, oprava zatiaľ nebola spravená.
- Podklady pre portál (obrazovky, webhooky, tabuľky, ďalšie kroky): ../docs/n8n-podklady.md
