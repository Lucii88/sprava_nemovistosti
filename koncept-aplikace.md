# Koncept aplikace pro správu nemovitostí

> Pracovní návrh pro brainstorming — vychází z hlavních bodů zadání (`zadani-hlavni-body.md`).
> **O rozsahu rozhoduje `rozsah-a-architektura.md`** (filtr: chtěla to žadatelka? → vyžaduje to zákon? → ulehčí, nebo zahltí?). Tento dokument popisuje, jak odsouhlasený rozsah vypadá z pohledu uživatele.

---

## 1. Shrnutí v jedné větě

Interní aplikace, která nahradí excelovou evidenci pronájmů: přehled nemovitostí, nájemců a smluv pod více entitami, s automatickým generováním platebních kalendářů a historií každého nájemce.

## 2. Základní principy

- **Jeden zdroj pravdy** místo několika Excelů — vše se zadává jednou, výstupy (kalendáře, přehledy) se generují.
- **Entita jako první filtr** — všude přepínač: entita 1…N / Soukromé / Vše. Entity (názvy, IČO, DIČ, účty, číselné řady) jsou čistá konfigurace v administraci — libovolný počet.
- **Lokální provoz** — aplikace běží na firemním serveru, data neopouští firmu; přístup prohlížečem z firemní sítě (případně VPN).
- **Nic navíc** — žádná funkce, kterou zadání nechce a která neulehčí práci (viz filtr v `rozsah-a-architektura.md`).

## 3. Obrazovky V1

1. **Nástěnka** — po přihlášení 2–3 seznamy s proklikem: kalendáře k vygenerování na nové období, smlouvy končící do X měsíců (předstih dle výpovědní doby), poznámky s blížícím se termínem. Žádné grafy, žádná KPI.
2. **Nájemci** — kompaktní seznam (~20 řádků) s filtry (entita, typ nemovitosti, stav) → **detail nájemce**: co má pronajato, kalendáře, historie cen po letech, poznámky. Přesně model „úzký přehled → rozklik do detailu" ze zadání.
3. **Nemovitosti** — seznam s filtry (typ, entita, volná/obsazená — odvozeno z aktivních smluv) → detail: kdo tam je a byl, dostupné služby.
4. **Smlouvy** — široká „excelová" tabulka všech nájemních vztahů s filtry a řazením; export do Excelu respektující aktuální filtr.
5. **Platební kalendáře** — přehled vygenerovaných dokladů + generování: jednotlivě (smlouva → položka → náhled → PDF) i **hromadně** („všechny kalendáře na nové období" jedním během s náhledem a kontrolami). Archiv všech verzí. **Hromadný export PDF (ZIP) za období** — přesně pro dnešní workflow „posílám účetní všechno v PDF".
6. **Administrace** — entity, uživatelé a role **vč. přiřazení entit** (soukromou entitu vidí jen paní Včeláková), sazby DPH (s platností od–do), typy nemovitostí, formát číselných řad.

## 4. Platební kalendáře (jádro)

- Vstup: smlouva + položka (nájem / energie-služby / voda) + období (typicky 12 měsíců).
- Výstup: PDF dle vzoru paní Včelákové — hlavička (entita jako pronajímatel, nájemce), 12 řádků: období, splatnost (dle pravidla smlouvy, např. „25. den předchozího měsíce"), základ, DPH, celkem.
- **Režim DPH per smlouva** (viz `pruzkum-legislativa.md`): osvobozeno dle § 56a (kalendář = předpis plateb s textem osvobození) / zdaněno volbou 21 % (nájemce-plátce) / **povinně 21 % u garáží a parkování**. Voda 12 %, elektřina/plyn 21 % — sazby z číselníku, ne z kódu.
- Náležitosti daňového dokladu (§ 29 + § 31a): evidenční číslo z řady entity, DIČ obou stran, rozpis základ/sazba/daň u každé platby.
- Každá položka = samostatný dokument — kopíruje dnešní praxi 2–3+ kalendářů na nemovitost.
- **Neměnnost:** vystavený kalendář se nemění ani nemaže; změna ceny = nový kalendář od data změny, starý zůstává v archivu (historie jednání + zákonná retence 10 let).
- Kontroly proti chybám z Excelu: výpočty částek a DPH dělá systém, hlídá návaznost období, překryvy a díry.

## 5. Historie a poznámky

- Historie nájemce vzniká automaticky z položek smluv s platností od–do (změna ceny = nový řádek) — tabulka po letech bez dodatečné práce.
- Ruční dozadání starých let u velkých nájemců (jednoduchý formulář).
- Poznámky k nájemci/smlouvě/nemovitosti: volný text + volitelný termín (zobrazí se na nástěnce). Nic se z nich negeneruje. *(Potvrzeno klientkou.)*
- **Kauce**: u smlouvy výše, datum složení a vrácení, poznámka. Kauce nevstupuje do platebních kalendářů ani do DPH.
- U položek služeb příznak **paušál / záloha k vyúčtování** — vyúčtování samotné je etapa 2 (klientka ho dělá sama, potvrzeno), V1 na něj jen připravuje data.

## 6. Role a práva

| Role | Oprávnění |
|---|---|
| Superadmin | vše + správa uživatelů a entit |
| Správce | zadávání a editace dat, generování kalendářů |
| Náhled | pouze čtení |

Každý uživatel má vlastní účet (audit log zaznamenává kdo-kdy-co; záložka Historie jen pro čtení) a **seznam entit, které smí vidět** — soukromá entita je přiřazena jen paní Včelákové (potvrzeno). Přístup jen z PC ve firemní síti (potvrzeno — žádná mobilní verze, žádná VPN pro V1).

## 7. Technické řešení

Monolitická webová aplikace v Dockeru na firemním serveru; PostgreSQL; serverem renderované UI; PDF se generuje na serveru ze strukturovaných dat (připraveno na případnou budoucí e-fakturaci). Noční záloha databáze + archivu PDF, doporučení 3-2-1 pro IT firmu. Detaily a doménový model: `rozsah-a-architektura.md`, oddíl G.

## 8. Etapy

1. **Etapa 0 — klikatelný prototyp** bez databáze: osahání obrazovek, odsouhlasení logiky (dle vzoru kalendáře od klientky).
2. **Etapa 1 — V1**: rozsah dle `rozsah-a-architektura.md` oddíl E + jednorázový import stávajícího Excelu + nasazení.
3. **Etapa 2 — jen na vyžádání**: kandidát č. 1 roční vyúčtování služeb (klientka ho dělá sama — potvrzeno), dále modul Platby (výpisy KB, párování dle VS, podklady pro kontrolní hlášení), QR platba na kalendáři.
4. Samostatné projekty mimo tuto aplikaci: kniha jízd, digitalizace stazek.

## 9. Otevřené body

Otázky z brainstormingu byly 10. 8. 2026 zodpovězeny a zapracovány (viz `zadani-hlavni-body.md` — „Doplnění zadání" a verdikty v `rozsah-a-architektura.md`). Zbývá: vzor platebního kalendáře + Excel od klientky a formát číslování dokladů (ověřuje s účetní firmou).
