# Rozsah V1 a architektura — rozhodnutí

> **Řídicí dokument vývoje.** Každá funkce, která se objevila v zadání, konceptu nebo průzkumu, zde prošla filtrem. Nic se neimplementuje, dokud tímto filtrem neprojde — platí i pro budoucí nápady.
> Aktualizováno 10. 8. 2026 podle odpovědí klientky (viz `zadani-hlavni-body.md`, oddíl „Doplnění zadání").

## Filtr (v tomto pořadí)

1. **Chtěla to žadatelka v zadání?** (zdroj pravdy: `zadani-hlavni-body.md`) → ano = V1.
2. Pokud ne: **vyžaduje to zákon**, aby výstupy byly správně? → ano = V1 (s odkazem na `pruzkum-legislativa.md`).
3. Pokud ne: **ulehčí jí to práci, nebo ji to zahltí?** Ulehčí = kandidát (V1 jen pokud je levný a přímo řeší její bolest; jinak etapa 2). Zahltí nebo neřeší její problém = zamítnuto.
4. Nejasné? → **otázka na brainstorming**, do té doby neimplementovat.

Bolesti ze zadání, proti kterým se „ulehčení" měří: (a) chybovost a pracnost ručních kalendářů v Excelu, (b) ztráta přehledu kdo-co-kde má, (c) roztříštěnost do více Excelů podle firem.

---

## A. Funkce ze zadání → V1 (žadatelka je výslovně chtěla)

| Funkce | Opora v zadání |
|---|---|
| Evidence nemovitostí, nájemců a smluv (náhrada Excelu) | „obrovská excelovská tabulka, co kdo kde má" |
| Široká tabulka s detaily + filtrované pohledy + rozklik do detailu | popsala přesně tento způsob práce |
| **Generování platebních kalendářů** — oddělené pro nájem / energie / vodu, rozpis 12 měsíců, splatnost dopředu | jádro zadání |
| Historie nájemce: ceny po letech, co měl pronajato, jak platil | „první rok tuhle cenu, další rok tuhle" |
| Poznámky k nájemci/smlouvě (volný text, nic se negeneruje) | „vymalováno → navýšen nájem", „do konce smlouvy nová požární zpráva" |
| Ruční dozadání historie u velkých nájemců | výslovně |
| Multi-entita: N firem + soukromý modul, konfigurovatelné v administraci | výslovně; názvy entit jsou jen konfigurace |
| Role: superadmin / správce / jen náhled | výslovně |
| Provoz na lokálním serveru firmy | výslovná preference |

Implicitně ze zadání (bez nich zadané funkce nefungují):
- **Hromadné generování kalendářů** — její bolest je množství (až 12 kalendářů × 20 nájemců); generovat po jednom by bolest neřešilo.
- **Import stávajícího Excelu** při nasazení — data existují, ruční přepis by byl přesně ta pracnost, kterou odstraňujeme. (Pošle vzorovou tabulku.)

## B. Funkce vynucené zákonem → V1 (žadatelka je neřekla, ale bez nich jsou výstupy špatně)

| Funkce | Proč (detail v `pruzkum-legislativa.md`) |
|---|---|
| Režim DPH per smlouva: osvobozeno / zdaněno volbou / **povinně 21 % (garáže, parkování!)** | § 56a ZDPH — garáže jsou vždy zdaněné; klientka garáže pronajímá |
| Náležitosti daňového dokladu na kalendáři (ev. číslo, DIČ, rozpis základ/sazba/daň, text osvobození) | § 29 + § 31a ZDPH |
| Číselné řady dokladů per entita, číslo přiděluje systém při vystavení | úplnost a návaznost řad, samostatné daňové subjekty |
| Sazby DPH jako číselník s časovou platností (21/12 %) | sazby se mění zákonem, nesmí být v kódu |
| Pravidlo splatnosti konfigurovatelné per smlouva | placení dopředu je smluvní odchylka od § 2218 NOZ |
| Neměnnost vystavených kalendářů: změna ceny = nový kalendář, starý zůstává v archivu | § 35 ZDPH, retence 10 let; zároveň přesně kopíruje její dnešní praxi a plní požadavek historie cen |
| Tvrdé oddělení entit ve všech datech a výstupech | samostatné daňové subjekty (§ 9 ZDP fyzická osoba vs. s.r.o.) |

## C. Funkce navíc — posouzení filtrem

| Funkce (zdroj: koncept/průzkum) | Chtěla? | Ulehčí × zahltí | **Verdikt** |
|---|---|---|---|
| Nástěnka „co je potřeba udělat" (kalendáře k vygenerování, smlouvy končící do X měsíců) | ne | ulehčí — nahrazuje její papírové poznámky, žádná data navíc se nezadávají | **V1 minimální**: 2–3 seznamy s proklikem; žádné KPI, žádné grafy |
| Kontrola chyb při generování (výpočet DPH, návaznost období, překryvy) | ne | přímo řeší bolest (a) „vždycky to pokazíme" | **V1** |
| Export do Excelu (respektuje filtr) | ne | ulehčí — přechodová jistota, žije v Excelu | **V1** (levné) |
| Audit log „kdo, kdy, co změnil" | ne | více uživatelů s právy → pojistka; žádná práce navíc pro uživatele | **V1 light** (jen záznam na pozadí + záložka Historie, čtení) |
| Poznámka s volitelným termínem (zobrazí se na nástěnce) | dodatečně ano („skvělé") | pokrývá její vlastní příklad („do konce smlouvy nová požární zpráva") | **V1** — potvrzeno klientkou |
| E-mailové notifikace / upomínky | ne | zahltí — další kanál, nastavování; nástěnka stačí | **Zamítnuto pro V1** |
| QR platba na PDF kalendáře | ne | ulehčí spíš nájemcům a účetní než jí; levné, ale není její bolest | **Etapa 2** — nabídnout na brainstormingu |
| Párování plateb z banky (KB) | výslovně „teď ne, možná připravit modul" | — | **Architektonická příprava bez implementace** (viz D) |
| Podklady pro kontrolní hlášení | ne | KH sestavuje externí účetní z přijatých plateb; bez modulu plateb to ani nejde správně | **Etapa 2** (spolu s modulem plateb) |
| Roční vyúčtování záloh na služby (odečty měřidel, lhůta 4 měsíce) | ne (dodatečně: **dělá ho sama**, část nájemců paušál, část vyúčtování) | reálná bolest, ale velký modul — nesmí zdržet jádro V1 | **Etapa 2 — potvrzený kandidát č. 1.** Ve V1 jen příznak položky „paušál / záloha k vyúčtování", aby etapa 2 nevyžadovala předělávku dat |
| Inflační doložka / automatická indexace nájmu | ne (dodatečně: doložky mají, ale **neuplatňují pravidelně**) | automatika by řešila proces, který u nich neexistuje | **Zamítnuto pro V1** — ruční změna ceny (nová platnost + nový kalendář) pokrývá i nepravidelnou indexaci |
| Evidence kaucí/jistot | dodatečně ano („to jsem zapomněla dodat") | kauce vybírají; jednoduchá evidence u smlouvy | **V1** — výše, datum složení, datum vrácení, poznámka; mimo kalendáře a mimo DPH (viz legislativa) |
| Kontrola nájemců v insolvenci/exekucích | ne | hezké při novém nájemci, ale externí integrace; neřeší žádnou bolest ze zadání | **Etapa 2+** (jen nápad) |
| Generování smluv a dodatků ze šablon | ne | smlouvy má právník/vzory; zadání chtělo jen kalendáře | **Zamítnuto pro V1** |
| Nájemnický portál, chat, mobilní aplikace | ne | 20 nájemců, komunikace funguje; velká zátěž vývoje i uživatelů | **Zamítnuto** |
| Napojení na účetní software (Pohoda apod.) | ne (dodatečně: účetní má vlastní program, **klientka jí vše posílá v PDF**) | integrace není potřeba — PDF workflow už existuje | **Zamítnuto trvale** |
| Hromadný export PDF pro účetní (ZIP všech kalendářů za období) | ne | přímo kopíruje její dnešní workflow „posílám účetní všechno v PDF" — bez toho by PDF stahovala po jednom | **V1** (levné, pár řádků nad existujícím archivem) |
| AI vytěžování dokumentů, přeceňování nemovitostí, hypotéky | ne | mimo problém | **Zamítnuto** |
| Uložené pohledy filtrů, globální vyhledávání | ne | při ~20 nájemcích stačí filtry | **Zamítnuto pro V1** |
| Technická správa / revizní modul (elektro, požární…) | ne (jen zmínka v poznámce) | plný modul by zahltil; zmínku pokryje poznámka s termínem | **Zamítnuto pro V1** |
| GDPR retention/anonymizační automat | ne | ~20 nájemců, převážně firmy; stačí role, účty, log a neevidovat zbytečná pole | **Zamítnuto pro V1** (ruční postup stačí) |

## D. Připraveno v architektuře, neimplementováno

**Modul Platby** — klientka: „možná by bylo fajn ten modul tam připravit."
- V1: v datovém modelu existuje volitelná vazba `platba → řádek kalendáře` a modulová hranice (samostatný balík s vlastním rozhraním). Žádné UI, žádný import.
- Etapa 2 (pokud si ji vyžádá): import výpisů KB → párování dle VS + částky → přehled „kdo nezaplatil" → podklady pro KH. Do té doby platby hlídá externí účetní.

## E. Výsledný rozsah V1 (definitivní)

Evidence (entity, nemovitosti, nájemci, smlouvy s položkami vč. příznaku paušál/záloha) · generátor platebních kalendářů (jednotlivě i hromadně, s validacemi a archivem PDF) · hromadný export PDF pro účetní (ZIP za období) · evidence kaucí · historie nájemce + poznámky (s volitelným termínem) · minimální nástěnka · filtry + export do Excelu · role a účty **s přístupem per entita** (soukromá entita viditelná jen klientce) · audit log · administrace (entity, sazby DPH, typy nemovitostí, uživatelé) · jednorázový import z Excelu. **Nic víc.**

Zjednodušení potvrzená klientkou: přístup jen z PC v kanceláři (žádná mobilní verze, žádná VPN pro V1), výstupy pro účetní pouze PDF (žádná integrace).

## F. Otevřené body

Otázky 1–8 z brainstormingu **zodpovězeny 10. 8. 2026** (viz `zadani-hlavni-body.md`, „Doplnění zadání") a zapracovány do verdiktů výše. Zbývá:

1. **Vzor platebního kalendáře + Excel s nájemci** — klientka pošle (bez cen). Ověřit pole šablony 1:1 a strukturu importu.
2. **Formát číslování kalendářů** — dnes nejednotné („PK 1, PK 2…"); klientka ověří preferenci s účetní firmou. Do té doby výchozí návrh: nové řady od nasazení, formát `<prefix entity>-<rok>-<pořadí>` (např. `TP-2026-001`), konfigurovatelný v administraci.

## G. Architektura (rozhodnutí)

**Provoz:** monolitická webová aplikace v Docker kontejneru na firemním serveru (spravuje jejich IT firma). Přístup z firemní sítě, případně VPN — aplikace se nevystavuje do internetu. Žádná data v cloudu.

**Stack:** aplikační server + **PostgreSQL** (verzované migrace) + úložiště PDF archivu na disku. UI = serverem renderované stránky (tabulky, filtry, formuláře) — žádný těžký frontend; aplikace této velikosti ho nepotřebuje a IT firmě se snáz udržuje. PDF se generuje na serveru z dat.

**Doménový model (jádro):**
- `Entita` — název, IČO/DIČ, plátce DPH, bankovní účet, číselné řady. Vše ostatní patří právě jedné entitě.
- `Nemovitost` — typ (číselník), označení/adresa, dostupné služby (elektřina/voda/…).
- `Nájemce` — firma/osoba, kontakty, DIČ (kvůli režimu DPH).
- `Smlouva` — entita + nájemce + nemovitost(i); právní režim (nájem / prostor k podnikání / pacht), doba určitá/neurčitá, období, **pravidlo splatnosti** („X. den předchozího měsíce"), **režim DPH** (osvobozeno / volba / povinně); **kauce** (výše, datum složení, datum vrácení, poznámka — mimo kalendáře a DPH).
- `Položka smlouvy` — typ (nájem / energie-služby / voda / …), částka, sazba DPH (odkaz na číselník s časovou platností), režim (plnění s DPH / přeúčtování mimo DPH), **příznak paušál / záloha k vyúčtování** (podklad pro etapu 2), **platnost od–do** — změna ceny = nový řádek platnosti, ne přepis (⇒ historie cen zdarma).
- `Platební kalendář` — doklad: číslo z řady entity, vazba na smlouvu + položku, řádky (období, splatnost, základ, sazba, daň), stav (vystaven/nahrazen/stornován), **immutable PDF v archivu**.
- `Poznámka` — k nájemci/smlouvě/nemovitosti, volný text + volitelný termín.
- `Uživatel` — role + **seznam entit, které smí vidět** (soukromá entita přiřazena jen klientce); `AuditLog` — append-only.
- Modulová hranice **Platby** (viz D) — v V1 prázdná.

**Zásady:**
1. Doklady jsou strukturovaná data, PDF jen render — připraveno na případnou budoucí e-fakturaci (EN 16931), bez přepisu systému.
2. Nic se nemaže: doklady immutable + storno, ostatní soft-delete; retence 10 let.
3. Sazby, typy, entity, splatnosti = konfigurace, ne kód.
4. Zálohy: noční dump databáze + archiv PDF, předat IT firmě doporučení 3-2-1 (jedna kopie mimo server, testovat obnovu).
5. Každá nová funkce projde filtrem z tohoto dokumentu.

## H. Etapy

1. **Etapa 0 — klikatelný prototyp** bez databáze (odsouhlasení vzhledu a logiky obrazovek; podle vzoru kalendáře od klientky).
2. **Etapa 1 — V1** v rozsahu bodu E + import Excelu + nasazení na server ve spolupráci s IT firmou.
3. **Etapa 2 — jen na vyžádání po zkušenosti s V1:** kandidát č. 1 je **roční vyúčtování služeb** (klientka ho dělá sama — potvrzeno; V1 na něj připravuje data příznakem paušál/záloha); dále modul Platby (KB výpisy, párování, podklady KH), QR platba, kontrola insolvence.
4. Samostatné projekty mimo aplikaci: kniha jízd, digitalizace stazek.
