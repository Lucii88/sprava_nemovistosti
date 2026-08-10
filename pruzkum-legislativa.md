# Legislativa — rešerše pro návrh aplikace

> Stav právní úpravy ověřen k 10. 8. 2026. Rešerše pro účely návrhu softwaru, **nikoli právní ani daňové poradenství** — konkrétní nastavení (zejména režim DPH u jednotlivých smluv a přeúčtování energií) ověřit s daňovým poradcem.

---

## 1. Občanský zákoník (89/2012 Sb.)

- **Typy vztahů:** nájem obecně (§ 2201+), **nájem prostoru sloužícího podnikání** (§ 2302–2315 — kanceláře, haly, garáže pronajaté podnikatelům), **pacht** u pozemků k užívání *a požívání* (§ 2332+; pozemek jen k užívání = nájem). → Aplikace eviduje u smlouvy typ právního režimu (určuje výpovědní doby a šablony).
- **Splatnost:** zákonné pravidlo je „nájemné se platí měsíčně **pozadu**" (§ 2218). Placení dopředu (září splatné 25. 8.) je odchylka, která **musí být výslovně ve smlouvě**. → Pravidlo splatnosti konfigurovatelné per smlouva, ne napevno v kódu.
- **Výpovědní doby** (prostor sloužící podnikání): doba určitá 3 měsíce (§ 2308–2310), doba neurčitá 6 měsíců (§ 2312); zemědělský pacht 12 měsíců (§ 2347). → Předstih upozornění na konec smlouvy odvozovat od typu (3/6/12 měsíců).
- **Zvyšování nájemného:** u komerčních nájmů plná smluvní volnost — bez ujednání jen dohodou (dodatek); standard jsou inflační doložky vázané na index ČSÚ. → Historie výše nájemného v čase; indexace jako případná nadstavba.
- **Jistota (kauce):** limit 3× nájemné a povinné úročení platí jen pro byty (§ 2254); u komerčních nájmů vše smluvní. Jistota není plnění pro DPH. → Evidovat mimo platební kalendáře a mimo DPH.

Zdroje: [NOZ](https://www.zakonyprolidi.cz/cs/2012-89), [§ 2302+ komentář](https://www.podnikatel.cz/zakony/novy-obcansky-zakonik/f4586224/), [epravo.cz — jistota](https://www.epravo.cz/top/clanky/uroceni-jistoty-kauce-kterou-sklada-podnajemce-najemci-ii-dil-121055.html)

## 2. DPH (235/2004 Sb.)

### Nájem: osvobození a výjimky (§ 56a)
- Nájem nemovité věci je **osvobozen bez nároku na odpočet**; plátce může u nájmu **jinému plátci** pro jeho ekonomickou činnost zvolit zdanění 21 % (§ 56a odst. 3; od 1. 7. 2025 i nájemci registrovaní v jiném státě EU).
- **Zásadní pro nás: nájem garáží a míst pro parkování vozidel je zdaněn VŽDY (21 %)** — bez ohledu na status nájemce (§ 56a odst. 1, [informace GFŘ](https://financnisprava.gov.cz/assets/cs/prilohy/d-novinky/Info_DPH-u-najmu-NV-od-20210101.pdf)). Jen jako vedlejší plnění k hlavnímu nájmu sdílí jeho režim.
- Osvobozené nájmy krátí odpočet koeficientem (§ 76). U osvobozeného plnění není povinnost vystavit daňový doklad — kalendář je pak jen předpis plateb.

→ **Aplikace: režim DPH per smlouva** (osvobozeno / zdaněno volbou / povinně zdaněno — garáže, parkování) + příznak plátce DPH per entita a per nájemce.

### Platební kalendář jako daňový doklad (§ 31a)
- Je daňovým dokladem, pokud: má náležitosti § 29, úplata se poskytuje **před uskutečněním plnění** a obsahuje **rozpis plateb na předem stanovené období**. Nemusí obsahovat DUZP ani den přijetí úplaty. Přesně náš model.
- Rozdíl od splátkového kalendáře (§ 31, dílčí plnění placená pozadu): u platebního kalendáře se daň přiznává **až z přijaté úplaty** (§ 20a) — nezaplatí-li nájemce, povinnost z dané splátky nevzniká; odběratel má odpočet až po zaplacení.
- Náležitosti § 29: označení a DIČ obou stran, **evidenční číslo dokladu** (jedno pro celý kalendář, z řady entity), předmět, den vystavení, rozpis základ / sazba / daň v Kč u každé platby; u osvobozeného plnění odkaz na § 56a.

→ **Aplikace: kalendář = doklad s číslem z řady entity, validace náležitostí, rozpis po platbách; neměnnost po vystavení (změna = nový kalendář).**

### Sazby DPH (od 1. 1. 2024, beze změn 2025–2026)

| Plnění | Sazba |
|---|---|
| Nájem (zdaněný volbou / garáže, parkování) | 21 % |
| Elektřina, plyn | 21 % |
| Teplo, chlad | 12 % |
| Vodné a stočné | 12 % |

Jeden doklad smí obsahovat více sazeb (rozpis po sazbách) — oddělené kalendáře nájem/energie/voda jsou **provozní volba klientky, ne zákonná povinnost**; podporují ale odlišný režim energií. → Sazby jako číselník s časovou platností, ne konstanty.

### Přeúčtování energií (§ 36 odst. 13)
Dvě varianty: (1) **přeúčtování mimo DPH** — jménem a na účet nájemce, bez odpočtu, max. v pořízené výši → nesmí vzniknout doklad s DPH; (2) **samostatné zdanitelné plnění** se sazbou komodity (standard, s odpočtem). Dle SDEU C-42/14: měří-li si nájemce spotřebu, jde o samostatná plnění; paušál sdílí režim nájmu. → Režim per služba na smlouvě.

### Kontrolní hlášení
Osvobozený nájem do KH nevstupuje (jen ř. 50 přiznání). U zdaněných kalendářů se limit 10 000 Kč posuzuje ze **součtu plateb celého kalendáře**; nad limit jde každá **přijatá platba** do A.4 se stejným ev. číslem a datem přijetí. **KH se tedy sestavuje z přijatých plateb — ty dnes hlídá externí účetní firma** (má napojení na banku). → Aplikace v1 KH neřeší; musí ale účetní firmě dávat kompletní podklady o dokladech (číslo, DIČ, rozpis po sazbách). Plné podklady pro KH by vyžadovaly párování plateb → budoucí modul Platby.

Zdroje: [§ 31a](https://www.kurzy.cz/zakony/235-2004-zakon-o-dani-z-pridane-hodnoty-dph/paragraf-31a/), [behounek.eu](https://www.behounek.eu/l/platebni-kalendar/), [POHODA — kalendáře v KH](https://portal.pohoda.cz/dane-ucetnictvi-mzdy/dph/platebni-a-splatkovy-kalendar-v-kontrolnim-hlaseni/), [POHODA — přefakturace](https://portal.pohoda.cz/dane-ucetnictvi-mzdy/dph/prefakturace-sluzeb-z-pohledu-dph/), [sazby 2026](https://www.taxorio.cz/blog/sazby-dph-2026)

## 3. Služby (zákon 67/2013 Sb.)

Formálně platí pro byty a nebytové prostory **v domě s byty**; Nejvyšší soud (26 Cdo 5212/2017) ale požadavky na řádné vyúčtování vztáhl **i na komerční nájmy** → bezpečné je držet zákonný standard: vyúčtování záloh **do 4 měsíců** od konce zúčtovacího období, vypořádání do 4 měsíců od doručení, členění po službách, pokuta až **50 Kč/den** prodlení. Nedoplatek není splatný bez řádného vyúčtování. → Pokud klientka dělá roční vyúčtování záloh (ověřit na brainstormingu), je to kandidát na etapu 2 s hlídáním lhůty. ([PEYTON legal](https://www.peytonlegal.cz/narocne-pozadavky-na-radne-vyuctovani-spotreby-energii-plati-i-pro-komercni-najmy/), [zákon](https://www.zakonyprolidi.cz/cs/2013-67))

## 4. Archivace

- Daňové doklady (vč. platebních kalendářů): **10 let** od konce zdaňovacího období (§ 35, § 35a ZDPH); elektronicky výslovně povoleno.
- Účetní doklady 5 let, závěrky 10 let (563/1991 Sb.); smlouvy prakticky po dobu nájmu + 10 let (promlčení, úprava odpočtu § 78 ZDPH).
- Nový zákon o účetnictví: účinnost nejdřív 1. 1. 2028 — sledovat, teď bez dopadu.

→ **Aplikace: doklady nikdy fyzicky nemazat** (opravy jen novým/opravným dokladem), retence min. 10 let, archiv PDF.

## 5. GDPR

Nájemci-fyzické osoby a kontaktní osoby: právní tituly plnění smlouvy + právní povinnost + oprávněný zájem (souhlas se nepoužívá). Minimalizace (žádná rodná čísla, kopie dokladů), uchování po dobu nájmu + promlčecí lhůty; doklady 10 let (výjimka z práva na výmaz čl. 17 odst. 3). Pro ~20 nájemců: stačí řízení přístupů, individuální účty, log změn, neevidovat nadbytečná pole. DPO/DPIA netřeba. ([GDPR](https://eur-lex.europa.eu/legal-content/CS/TXT/?uri=CELEX%3A32016R0679), [ÚOOÚ](https://uoou.gov.cz/))

## 6. Daň z příjmů — proč tvrdé oddělení entit

- **Fyzická osoba (§ 9 ZDP):** dílčí základ, výdaje skutečné nebo paušál 30 % (max. 600 tis. Kč), sazby 15/23 %, bez pojistných; potřebuje **hotovostní** roční sestavu (dle přijatých plateb).
- **s.r.o.:** DPPO 21 %, podvojné účetnictví — potřebuje **akruální** podklady (předpisy).
- Každá entita = samostatný daňový subjekt: vlastní DIČ, přiznání, obrat pro plátcovství (limit 2 mil. Kč), KH, číselné řady. Transakce mezi entitami = spojené osoby (§ 23 odst. 7 ZDP).

→ **Aplikace: každý záznam patří právě jedné entitě; žádné sdílené číselné řady; reporting vždy per entita.**

## 7. Výhled — e-fakturace (ViDA)

Směrnice (EU) 2025/516: povinná strukturovaná e-fakturace od 1. 7. 2030 jen pro **přeshraniční B2B**; konvergence národních systémů do 2035. **V ČR k 8/2026 žádný tuzemský mandát neexistuje ani není navržen.** → Teď bez povinností; architektonicky držet doklady jako strukturovaná data (PDF se jen renderuje), aby byl budoucí export do EN 16931/ISDOC doplňkem. ([Accace](https://accace.cz/smernice-vida/), [MF ČR](https://mf.gov.cz/cs/dane-a-ucetnictvi/elektronicka-fakturace))

---

## Souhrn: co z legislativy MUSÍ umět už V1

1. Režim DPH per smlouva (osvobozeno / volba / **povinně 21 % garáže a parkování**) a plátcovství per entita i nájemce.
2. Platební kalendář s náležitostmi § 29 + § 31a: evidenční číslo z řady entity, rozpis základ/sazba/daň, text osvobození u osvobozených plnění.
3. Sazby DPH jako číselník s časovou platností (21/12 %).
4. Pravidlo splatnosti konfigurovatelné per smlouva (odchylka od § 2218 musí být ve smlouvě).
5. Neměnnost vystavených dokladů, žádné mazání, retence 10 let.
6. Tvrdé oddělení entit (data, číselné řady, sestavy).
7. Role a individuální účty (GDPR + interní kontrola).
