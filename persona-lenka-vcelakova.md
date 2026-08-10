# Persona: Lenka Včeláková

> Sestaveno výhradně z přepisu callu 10. 8. 2026 a e-mailových odpovědí. Slouží jako testovací optika pro každou obrazovku: **„Pochopí to Lenka na první pohled? Ulehčí jí to práci, nebo ji to zahltí?"**

## Kdo je

Spolumajitelka rodinné skupiny firem (doprava/logistika + nemovitosti, 3 s.r.o.) a soukromá pronajímatelka. Vedle řízení firmy si sama vede agendu ~20 nájemců: smlouvy, platební kalendáře, jednání o cenách. Rozhoduje rychle a věcně, na detailech jí záleží („složitá jednání o ceně" si pamatuje roky zpátky).

## Vztah k technologiím (klíčové!)

- **Excel je její svět, ale ovládá ho ručně** — velká tabulka, žádná automatizace. Sama říká: „vždycky to pokazíme… já to neumím, no."
- Poznámky si píše **tužkou na papír** i během callu.
- Účetní firmě posílá **všechno v PDF e-mailem**; pracuje na PC, Microsoft.
- **Nedůvěra k cloudu** („mám obavy, kde ty data jsou") — chce data u sebe.
- Nechce být **závislá na IT firmě**; AI vývoji fandí, ale chce ho nejdřív „osahat".
- Paragrafy a IT pojmy ji nezajímají — zajímá ji, **aby doklad byl správně a práce byla hotová**.

## Co ji bolí (z jejích vlastních slov)

1. „Strašný množství platebních kalendářů" — až 12 na nájemce, ručně, s chybami.
2. Ztráta přehledu „co kdo kde má" přes tři firmy a několik Excelů.
3. Historie dohod jen v hlavě a poznámkách („vymalováno → navýšen nájem").
4. Kniha jízd a stazky — ví, že papír je špatně, ale změny se bojí („přechod lidí je strašně složitej").

## Co jí dodá důvěru

- Vidí **svoje vlastní pojmy**: firma (ne „entita"), nájemce, kalendář, kauce.
- Program **sám hlídá termíny** a řekne jí to česky: „první splatnost už 25. 8."
- Nic nejde rozbít: doklady nejdou smazat, každá akce má zřetelnou odezvu.
- Vypadá to jako „chytřejší Excel" — tabulky, filtry, žádná magie.

## Anti-vzory (co ji zahltí)

Menu plné cizích slov · obrazovky s grafy „na parádu" · formulář s 20 poli najednou · paragrafy v UI · tlačítka bez odezvy · cokoli, co vyžaduje čtení návodu.

---

## UX test prototypu optikou Lenky (10. 8. 2026)

| # | Nález | Závažnost | Náprava |
|---|---|---|---|
| 1 | Nástěnka říkala „co existuje", ne „co mám udělat" — bez akcí, bez pořadí naléhavosti, bez souhrnu | vysoká | pozdrav + věta „1 věc spěchá, 4 upozornění"; dlaždice řazené červená→jantar→info; u každé položky tlačítko akce |
| 2 | Slovo **„entita"** — Lenka říká „firma" | vysoká | v celém UI „firma / moje firmy"; „entita" zůstává jen v technické dokumentaci |
| 3 | „Číselníky", „Administrace" — IT žargon | střední | ploché menu bez skupin; „Administrace" → **„Nastavení"** |
| 4 | Paragrafy v UI („osvobozeno §56a", „§ 2218") | střední | česky: „bez DPH", „s DPH 21 %"; § patří na PDF doklad, ne do formuláře |
| 5 | Zkratka „Stálý VS" | nízká | „Variabilní symbol" |
| 6 | Tlačítka bez odezvy → nejistota „rozbila jsem to?" | střední | každé tlačítko reaguje (v prototypu aspoň hláškou, co udělá ostrá verze) |
| 7 | „Volné prostory" tvářící se jako úkol | nízká | přeznačeno „Pro informaci", poslední, zelené |
| 8 | Chyběl semafor stavů | vysoká | zavedeno: zelená = v pořádku · jantarová = upozornění · červená = řešit hned (v detailu nájemce i na nástěnce) |

**Zásada do dalšího vývoje:** každý nový text v UI projde testem „řekla by to takhle Lenka nahlas?" Pokud ne, přeformulovat.
