# Zadání — hlavní body z callu s paní Včelákovou

**Datum callu:** 10. 8. 2026 (cca 21 minut)
**Účastníci:** Lucie Koucká Kalabisová, paní Včeláková
**Zdroj:** přepis z Plaud (zde pouze hlavní body, ne celý přepis)

---

## Výchozí situace

- Firma pronajímá **různorodé nemovitosti**: garáže, kanceláře, výrobní haly, skladové haly, venkovní pozemky.
- Celkem **cca 20 nájemců**, jeden nájemce může mít pronajato více nemovitostí najednou (a skladba se v čase mění — něco si přibere, něco vrátí).
- Vše se dnes vede v **jedné velké excelové tabulce** — je to pracné a chybovost je vysoká.
- Nemovitosti jsou historicky rozdělené pod **3 různé firmy**, navíc má paní Včeláková **soukromé nemovitosti**, které by chtěla evidovat také.

## Klíčové požadavky

### 1. Přehled nájmů (náhrada Excelu)
- Evidence: kdo, co, kde má pronajato, za kolik, na jak dlouho.
- Každý nájemce odebírá jinou kombinaci služeb (energie, voda / někde voda není…).
- Práce s tabulkou: **široká tabulka s detaily + filtrované pohledy** (např. seznam 20 nájemců → rozklik do detailu).

### 2. Generování platebních kalendářů (nejdůležitější funkce)
- **Nájem se platí dopředu** (nájem na září je splatný k 25. srpna) → nelze řešit klasickou fakturou, používá se **platební kalendář**.
- Struktura kalendáře: hlavička (pronajímatel / nájemce) + rozpis 12 měsíců — období, splatnost, částka, DPH.
- Na **jednu nemovitost minimálně 2–3 samostatné kalendáře**:
  1. nájem,
  2. energie / zálohové či paušální služby,
  3. voda (má **jinou sazbu DPH** než elektřina).
- Jeden nájemce tak může mít i **12 platebních kalendářů** → při ~20 nájemcích obrovské množství dokumentů, dnes ručně v Excelu s velkou chybovostí.
- **Aplikace musí tyto kalendáře umět generovat.**
- Paní Včeláková pošle e-mailem **vzor platebního kalendáře** a svou stávající excelovou tabulku.

### 3. Historie nájemce
- Vývoj ceny nájmu po letech, co měl kdy pronajato, jak platil.
- **Poznámky** k dohodám (např. „vymalováno — vymalovali jsme my, o to navýšen nájem", „smlouva do roku X, poté nová požární zpráva").
- Poznámky jsou jen informační — nic se z nich negeneruje.
- Zpětné doplnění historie **jen u velkých / dlouhodobých nájemců**, u malých není potřeba.

### 4. Více entit
- Samostatné záložky / oddělení pro **3 firmy + soukromý modul**.
- U každé smlouvy se eviduje, **pod kterou entitou** je vedena → počet entit musí být do budoucna rozšiřitelný (nastavitelné v administraci).

### 5. Uživatelé a práva
- Přístup pro **více lidí s odstupňovanými právy** — např. superadmin / správce / jen k nahlédnutí.

### 6. Hosting a data
- Obava, **kde jsou data uložena** → cloud třetí strany nechce.
- **Preferovaná cesta: lokální server firmy** (mají externí IT firmu, která infrastrukturu spravuje).

## Mimo rozsah první verze (do budoucna)

- **Napojení na banku** (hlídání příchozích plateb): teď ne, dnes to hlídá externí účetní firma. Případně jen **připravit modul** do budoucna — „hezká věc s otazníkem". Banka: Komerční banka; notifikace o příchozích platbách zatím nemá.
- **Kniha jízd**: velký zájem („strašný voprus"), ale až jako druhý projekt po odsouhlasení aplikace na nemovitosti.
- **Digitalizace stazek řidičů** (tablety, dispečer posílá práci do apky): dlouhodobý sen, řeší se s externí IT firmou, komplikace s propojením na účetní program.

## Dohodnuté další kroky

1. **Lucie:** připraví návrh architektury + orientační odhad času a ceny → paní Včeláková dá zpětnou vazbu (OK / NOK / OK, ale…).
2. Po odsouhlasení: **klikatelný prototyp bez databáze** (jen vizuální osahání, nic se neukládá) — ambice do konce týdne.
3. Po odsouhlasení prototypu: ostrá verze s ukládáním dat a oficiální cenou.
4. **Paní Včeláková:** pošle vzor platebního kalendáře + stávající Excel s nájemci.
5. Kontext: paralelně poptáno i u stávající externí IT firmy (konzervativní přístup) — bude se porovnávat.
