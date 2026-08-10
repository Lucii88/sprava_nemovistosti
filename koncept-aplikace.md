# Koncept aplikace pro správu nemovitostí

> Pracovní návrh pro brainstorming — vychází z hlavních bodů zadání (viz `zadani-hlavni-body.md`).
> Pouze textový popis, žádný kód ani grafika.

---

## 1. Shrnutí v jedné větě

Interní aplikace, která nahradí excelovou evidenci pronájmů: přehled nemovitostí, nájemců a smluv pod více entitami, s automatickým generováním platebních kalendářů a historií každého nájemce.

## 2. Základní principy

- **Jeden zdroj pravdy** místo několika Excelů — vše se zadává jednou, výstupy (kalendáře, přehledy) se generují.
- **Entita jako první filtr** — všude v aplikaci přepínač: Firma 1 / Firma 2 / Firma 3 / Soukromé / Vše. Entity spravovatelné v administraci (možnost přidat další).
- **Lokální provoz** — aplikace poběží na firemním serveru (ve spolupráci s externí IT firmou), data neopouští firmu. Přístup přes webový prohlížeč v rámci firemní sítě (případně VPN).
- **Nejdřív jednoduchost** — v první verzi žádná banka, žádné účetnictví; jen evidence + generování dokumentů.

## 3. Datový model (slovně)

- **Entita (pronajímatel)** — firma nebo soukromá osoba; název, IČO/DIČ, sídlo, bankovní účet (pro hlavičku kalendáře).
- **Nemovitost** — typ (garáž / kancelář / výrobní hala / skladová hala / pozemek / jiné), adresa/označení, vlastnící entita, poznámka. U nemovitosti evidence dostupných služeb (elektřina ano/ne, voda ano/ne…).
- **Nájemce** — firma či osoba, kontakty, fakturační údaje, poznámky.
- **Smlouva (nájemní vztah)** — spojuje nájemce + nemovitost + entitu; období od–do, den splatnosti (např. 25. den předchozího měsíce), stav (aktivní / ukončená / plánovaná).
- **Položky smlouvy** — oddělené řádky: nájem / energie a služby / voda — každá s částkou a **vlastní sazbou DPH** (proto samostatné platební kalendáře).
- **Platební kalendář** — vygenerovaný dokument navázaný na smlouvu a položku; číslovaný, archivovaný, s historií verzí (když se cena změní, generuje se nový).
- **Poznámky / události** — časová osa u nájemce i u smlouvy (dohody, opravy, „vymalováno → navýšen nájem"…). Jen pro čtení člověkem, nic se z nich negeneruje.
- **Uživatel** — jméno, přihlášení, role.

## 4. Obrazovky

1. **Dashboard** — po přihlášení: počty aktivních smluv podle entity, smlouvy končící do X měsíců, kalendáře čekající na vygenerování, poslední změny.
2. **Nájemci** — kompaktní seznam (~20 řádků) s filtry (entita, typ nemovitosti, stav) → **detail nájemce**: co všechno má pronajato, platební kalendáře, historie cen, poznámky. Přesně model „úzký přehled → rozklik do detailu", který paní Včeláková popsala.
3. **Nemovitosti** — seznam s filtry (typ, entita, obsazená/volná) → detail: kdo tam je a kdo tam byl, jaké služby jsou k dispozici.
4. **Smlouvy** — široká „excelová" tabulka všech nájemních vztahů (nájemce, nemovitost, entita, období, částky, splatnost) s filtry a řazením; export do Excelu pro jistotu zachován.
5. **Platební kalendáře** — přehled všech vygenerovaných kalendářů + tlačítko „vygenerovat“: výběr smlouvy → aplikace nabídne položky (nájem / energie / voda) → náhled → PDF ke stažení/tisku. Hromadné generování (např. všechny kalendáře na nové období jedním klikem).
6. **Historie nájemce** — záložka v detailu nájemce: tabulka po letech (cena nájmu, co měl pronajato) + časová osa poznámek. Možnost ručního dozadání starých let u velkých nájemců.
7. **Administrace** — správa entit, uživatelů a rolí, sazeb DPH, číselníku typů nemovitostí.

## 5. Generování platebních kalendářů (jádro aplikace)

- Vstup: smlouva + položka (nájem / služby / voda) + období (typicky 12 měsíců).
- Výstup: PDF se strukturou dle vzoru paní Včelákové — hlavička (pronajímatel = entita, nájemce), 12 řádků: období (1. 9.–30. 9.), splatnost (25. 8.), základ, DPH, celkem.
- Pravidlo splatnosti („X. den předchozího měsíce") nastavitelné na smlouvě.
- Každá položka = samostatný dokument (kvůli různým sazbám DPH) — přesně kopíruje dnešní praxi 2–3+ kalendářů na nemovitost.
- Kalendáře se archivují; při změně ceny vzniká nová verze, stará zůstává dohledatelná (podklad pro historii jednání).
- Kontroly proti chybám z Excelu: aplikace sama spočítá částky a DPH, pohlídá návaznost období a upozorní na překryvy či díry.

## 6. Role a práva

| Role | Oprávnění |
|---|---|
| Superadmin | vše + správa uživatelů a entit |
| Správce | zadávání a editace dat, generování kalendářů |
| Náhled | pouze čtení, bez editace a generování |

Viditelnost lze případně omezit i po entitách (např. soukromý modul vidí jen paní Včeláková) — k probrání na brainstormingu.

## 7. Technické řešení (stručně)

- **Webová aplikace** provozovaná na firemním serveru (Docker kontejner — snadná instalace pro IT firmu, zálohování = záloha jedné databáze).
- Databáze běžící lokálně u aplikace; **žádná data v cloudu třetí strany**.
- Generování PDF přímo v aplikaci; export přehledů do Excelu.
- Pravidelná automatická záloha databáze na firemní úložiště.

## 8. Etapy

1. **Etapa 0 — klikatelný prototyp** (bez databáze): obrazovky k osahání, nic se neukládá. Cíl: odsouhlasit vzhled a logiku. Ambice: do konce týdne.
2. **Etapa 1 — ostrá verze**: evidence + generování kalendářů + role + nasazení na firemní server, import dat ze stávajícího Excelu.
3. **Etapa 2 — nadstavby dle zájmu**: připravený modul plateb (párování podle výpisů z banky — KB), upomínky před splatností, další entity.
4. **Samostatné projekty (mimo tuto aplikaci):** kniha jízd, digitalizace stazek — až po vyhodnocení etapy 1.

## 9. Otevřené otázky na brainstorming

1. Vzor platebního kalendáře a Excel od paní Včelákové — přesná pole a formát (čekáme na zaslání).
2. Názvy a fakturační údaje tří firemních entit.
3. Přístup jen z firemní sítě, nebo i vzdáleně (VPN)?
4. Má se soukromý modul skrýt ostatním uživatelům?
5. Kolik uživatelů reálně na startu a v jakých rolích?
6. Číslování platebních kalendářů — existuje dnes nějaká řada, na kterou navázat?
7. Upomínky/notifikace na končící smlouvy a blížící se splatnosti — chtít hned v etapě 1, nebo až později?
8. Jednorázový import stávajícího Excelu — kdo připraví/vyčistí data?
