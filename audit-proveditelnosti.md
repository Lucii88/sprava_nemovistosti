# Audit proveditelnosti prototypu — aplikace bez AI

> Kontrolní otázka pro **každý** prvek prototypu: *odkud se ten údaj v ostré verzi vezme?*
> Přípustné zdroje: **(A)** data zadaná uživatelem, **(B)** výpočet z dat pevným pravidlem, **(C)** statický text rozhraní napsaný jednou při vývoji.
> Nepřípustné: text, který by musel někdo formulovat případ od případu.
> Aplikace poběží na serveru klientky **bez připojení k jakékoli AI službě**.

Provedeno 10. 8. 2026 nad prototypem `prototyp/index.html`.

---

## 1. Nástěnka

| Prvek | Zdroj | Jak přesně |
|---|---|---|
| „Dobrý den, Lenko" | A | pole *oslovení (5. pád)* v profilu uživatele; předvyplní se pravidlem skloňování, lze přepsat |
| „pondělí 10. srpna 2026" | B | systémové datum + tabulka názvů dnů a měsíců |
| Semafor „1 spěchá · 8 termínů · jinak klid" | B | počty záznamů podle pravidel níže |
| Portfolio (8 nájemců, 11 nemovitostí, 10 smluv, 1 volný) | B | prosté `COUNT` nad tabulkami; volný prostor = nemovitost bez aktivní smlouvy |
| Hero „chybí platební kalendář na nájem od 9/2026, splatnost 25. 8." | B | pravidlo: aktivní smlouva má položku, k níž pro dané období neexistuje vystavený kalendář; datum splatnosti = pravidlo splatnosti ze smlouvy |
| Termín „vystavit platební kalendář — energie od 9/2026" | B+C | stejné pravidlo; text je šablona `vystavit platební kalendář — {typ položky} od {období}` |
| Termín „Zkontrolovat vrata garáže." | A | **text poznámky doslova** + datum termínu z poznámky |
| Termín „končí smlouva na kancelář K1" | B+C | datum konce smlouvy; šablona `končí smlouva na {nemovitost}` |
| „Termíny do 28. 2. 2027 · 8 položek" | B | max. datum a počet v seznamu |
| Kalendář — dny s termíny | B | tytéž termíny umístěné podle data |
| Kalendář — „Splatnost 6 plateb" | B | počet řádků vystavených platebních kalendářů se splatností v daný den |

**Pozn.:** kalendář ukazuje, kdy má platba **přijít podle vystavených kalendářů** — nikoli zda nájemce zaplatil. To by vyžadovalo modul plateb z banky (etapa 2).

## 2. Detail nájemce

| Prvek | Zdroj | Jak přesně |
|---|---|---|
| Jméno, IČO, DIČ, účet, variabilní symbol | A | pole nájemce |
| Semafor „Řešit hned / upozornění / vše v pořádku" | B | počet úkolů podle úrovně |
| Nájem / měs. bez DPH | B | součet položek typu *nájem* platných k dnešku |
| „3 nemovitosti" | B | počet + **pevné pravidlo skloňování** (1 / 2–4 / 5+) |
| Kauce, Smlouva do | A | pole smlouvy |
| „končí brzy — řešit prodloužení" | B+C | konec smlouvy − výpovědní doba ≤ dnes + rezerva |
| Platné kalendáře „2 · 1 chybí" | B | počet vystavených vs. počet položek smlouvy bez kalendáře |
| Úkol „Chybí platební kalendář…" | B+C | pravidlo + šablona |
| Úkol „15. 10. 2026 — Zajistit novou požární zprávu." | A | datum + **text poznámky doslova** |
| Úkol „Položka energie platí jen do 31. 12. 2026…" | B+C | porovnání platnosti položky s obdobím |
| Smlouvy a kauce | A | pole smlouvy a jejích položek |
| Graf vývoje ceny | B | řádky platnosti položky *nájem* (od–do, částka) vykreslené na časovou osu |
| Platební kalendáře + archiv | A/B | vystavené doklady; stav (vystaven / nahrazen / ukončen / připraven) podle období a nahrazení |
| Poznámky | A | text zadaný uživatelem, zobrazen doslova |

## 3. Seznamy a průvodci

| Prvek | Zdroj | Jak přesně |
|---|---|---|
| Nemovitosti: „obsazeno do 31. 8. 2027" / „volné od 1. 7. 2026" | B | z aktivních smluv; volné = bez smlouvy, datum = konec poslední smlouvy |
| Smlouvy: položky s DPH („nájem 21 %", „voda 12 % · záloha") | A/B | typ položky + sazba z číselníku + příznak paušál/záloha |
| Průvodce smlouvou — „Garáž má ze zákona nájem vždy s DPH 21 %" | C | statická nápověda navázaná na *typ nemovitosti = garáž*; nastavení sazby je pravidlo |
| Generování kalendáře — náhled řádků | B | období × pravidlo splatnosti × částka × sazba DPH |
| Kontroly „položka platí po celé období", „navazuje na AD-2025-041" | B | porovnání dat, vyhledání předchozího dokladu |
| Hromadné generování — „našel 3 smlouvy, kterým chybí kalendář" | B | tentýž dotaz jako na nástěnce |
| PDF dokladu (hlavička, řádky, text o splatnosti, § 31a) | B+C | šablona dokumentu s poli; právní věty jsou pevné texty podle režimu DPH |
| Nastavení (firmy, uživatelé, sazby, řady, typy, zálohy) | A | konfigurace; „poslední záloha dnes 02:00" = stav úlohy zálohování |

## 4. Co audit odhalil a co se opravilo

1. **Přeformulované poznámky (skutečný nález).** Poznámka „Zkontrolovat vrata garáže." se na nástěnce zobrazovala jako „kontrola vrat garáže" a v detailu jako „Do 1. 9. 2026 zkontrolovat vrata garáže" — tedy **přepsaná jinými slovy**, což by bez AI nikdo neudělal. Opraveno: úkoly z poznámek se skládají jako `{datum} — {text poznámky doslova}`.
2. **Nesprávný pojem.** „chybí kalendář nájmu" → **„chybí platební kalendář na nájem"** (kalendář je typ dokladu, nájem je jeho předmět).
3. **Komentář pod grafem** („1× změna ceny od 1/2024 · aktuálně kancelář K1") odstraněn — počítal se sice z dat, ale neříkal nic, co v grafu není.
4. **Skloňování počtů** („1 nemovitosti") — doplněno pevné pravidlo 1 / 2–4 / 5+.
5. **„jeden chybí!"** → „1 chybí" (číslo z výpočtu, ne slovem).
6. **Termín u poznámky jako samostatné pole.** Dřív se termín psal do textu poznámky („Dodělat dveře. 1.9.2026") — aplikace by ho musela z věty vyčíst, což je bez AI nespolehlivé a s AI nevhodné (u dokladů a termínů nesmí nic „hádat"). Nyní má poznámka **text** a **datum termínu** jako dvě oddělená pole; jen díky tomu se termín objeví na nástěnce i v kalendáři. Formulář pro novou poznámku je nahoře, aby se k němu nemuselo rolovat.

## 5. Závěr

**Ano, celý prototyp je dodatelný bez AI.** Neobsahuje žádný prvek, který by vyžadoval jazykový model — všechny texty jsou buď zadané uživatelem, spočítané pevným pravidlem, nebo napsané jednou při vývoji jako součást rozhraní.

Pravidlo pro další vývoj: **u každého nového textu v rozhraní musí být zřejmé, do které ze tří kategorií (A/B/C) patří.** Pokud by odpověď zněla „to by musel někdo formulovat", funkce se do aplikace nedostane.

AI se používá **při psaní programu**, ne v běžícím programu — hotová aplikace je obyčejný software: databáze, pravidla, šablony.
