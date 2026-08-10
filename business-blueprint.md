# Business blueprint — aplikace pro správu nemovitostí

> Strategický přehled: proč aplikace vzniká, jak teče hodnota a co je (ne)součástí řešení.
> Detailní verdikty k funkcím: `rozsah-a-architektura.md` · uživatelský pohled: `koncept-aplikace.md` · prototyp: `prototyp/index.html`.

## 1. Problém a cíl

**Dnes:** ~20 nájemců, různorodé komerční nemovitosti (garáže, kanceláře, haly, pozemky) pod 3 firmami + soukromý majetek. Evidence ve více Excelech; platební kalendáře (daňové doklady!) se vyrábí ručně — vysoká pracnost a chybovost; historie dohod v hlavě a v poznámkách.

**Cíl:** jeden zdroj pravdy. Zadá se evidence, všechno ostatní (kalendáře, přehledy, historie) se generuje. Méně práce, méně chyb, klid při kontrole.

## 2. Tok hodnoty

```
ENTITY            NEMOVITOSTI        SMLOUVY + POLOŽKY        GENERÁTOR              PDF ARCHIV           VÝSTUPY
3 firmy +    →    garáže, haly,  →   nájem / energie /   →    platebních        →    doklady se       →   ZIP pro účetní,
soukromé          kanceláře,         voda; sazby DPH;         kalendářů (§ 31a)      nemění, nemažou      Excel exporty,
(IČO, DIČ,        pozemky            splatnost dopředu;       kontroly chyb,         (10 let), verze      historie cen
účty, řady)                          kauce                    hromadné běhy          = historie cen
```

- **Entita je první dimenze všeho** — každý záznam patří právě jedné entitě (samostatné daňové subjekty, vlastní číselné řady). Soukromou entitu vidí jen majitelka.
- **Kalendář je daňový doklad** — číslo z řady entity, rozpis DPH (21/12 %), režim per smlouva (osvobozeno / volba / povinně u garáží). Vystavený doklad je neměnný; změna ceny = nový doklad → historie vzniká sama.
- **Uživatelé a role:** superadmin / správce / náhled + viditelnost entit.

## 3. Uživatelé

| Kdo | Co v aplikaci dělá |
|---|---|
| Paní Včeláková (superadmin) | vše; jediná vidí soukromou entitu |
| Kolega (správce) | evidence, generování kalendářů |
| Účetní firma (mimo aplikaci) | dostává ZIP PDF za období; v budoucnu případně strukturovaný export |

## 4. Rozsah

**V1 (jádro):** evidence (entity, nemovitosti, nájemci, smlouvy s položkami vč. paušál/záloha) · generátor kalendářů s kontrolami, jednotlivě i hromadně · kauce · historie cen + poznámky s termínem · nástěnka úkolů · filtry, Excel export, ZIP PDF · role + viditelnost entit · audit log · import stávajícího Excelu.

**Etapa 2 (na vyžádání):** roční vyúčtování záloh (kandidát č. 1 — klientka ho dělá sama), platby z KB + párování dle VS, strukturovaný export pro účetní SW, QR platba.

**Schválně nebude:** nájemnický portál, mobilní aplikace, e-mailové notifikace, automatická indexace (doložky se neuplatňují pravidelně), vlastní účetnictví, AI funkce.

Filtr pro každou budoucí funkci: **chtěla to žadatelka? → vyžaduje to zákon? → ulehčí, nebo zahltí?**

## 5. Provoz a peníze

- **Provoz:** Docker + PostgreSQL na serveru klientky (spravuje její IT firma); data neopouští firmu; přístup z PC ve firemní síti; noční zálohy (doporučení 3-2-1). Bez licencí třetích stran (open-source stack).
- **Model dodávky:** etapa 0 prototyp (odsouhlasení) → V1 fixní cenou → servisní paušál (aktualizace, podpora) → etapa 2 jen na vyžádání.
- **Konkurenční kontext:** kombinaci „platební kalendáře dle českého DPH + multi-entita + on-premise" hotový trh nenabízí (viz `pruzkum-trh.md`); alternativou je konzervativní zakázkový vývoj IT firmy — pomalejší a dražší.

## 6. Rizika a jak je držíme

| Riziko | Ošetření |
|---|---|
| Špatné částky/DPH na dokladech | výpočty dělá systém, sazby v číselníku, kontroly období; vzor kalendáře 1:1 |
| Rozsah se rozjede („ještě by to mohlo…") | filtr rozsahu + řídicí dokument; nové nápady = etapa 2 |
| Závislost na dodavateli | standardní stack, dokumentace, export dat kdykoli; doklady jako strukturovaná data |
| Ztráta dat | neměnné doklady, soft-delete, zálohy s kopií mimo server, test obnovy |
| Nedůvěra v „AI vývoj" | klikací prototyp před závazkem; srovnání s nabídkou IT firmy |
