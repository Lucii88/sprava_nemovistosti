# Průzkum trhu a best practices

> Rešerše provedena 10. 8. 2026 z veřejných webů výrobců a odborných zdrojů. Ceny platí k srpnu 2026, pokud není uvedeno jinak.
> Slouží jako **zásobník inspirace** — o tom, co se skutečně implementuje, rozhoduje výhradně filtr v `rozsah-a-architektura.md` (zadání → ulehčení vs. zahlcení).
> Legislativní rešerše je v samostatném dokumentu `pruzkum-legislativa.md`.

---

## 1. Konkurence — český trh

### Profesionální správcovské systémy (primárně SVJ/BD)

| Produkt | Pro koho | Cena | Nasazení | Inspirace pro nás |
|---|---|---|---|---|
| **Domsys** | správci, SVJ/BD, obce, soukromí vlastníci; deklaruje i komerční prostory | od 987 Kč/měs, u komerčních dle m² | jen cloud | bankovní integrace (Fio, KB, ČSOB…), platební kalendáře v dokumentaci, hlídání konce smluv, technická správa |
| **STARLIT SSB / SSB2000** | BD, SVJ, správcovské kanceláře | předplatné dle počtu jednotek (pásma nedohledána) | historicky on-premise, dnes hybrid | důkaz, že „instalace u zákazníka" v ČR funguje; integrace na POHODA/ABRA místo vlastního účetnictví |
| **DOMUS (ANASOFT)** | velké správcovské firmy, 800 000+ jednotek | individuální | klient–server, DOMUS X web | oddělení nájemního modulu od účetnictví; mobilní odečty měřidel |
| **WinDomy (OK SOFT)** | správci bytového fondu, obce | nedohledána | desktop on-premise | import odečtů; kontrola insolvenčního rejstříku |

Zdroje: [domsys.cz](https://www.domsys.cz/), [podpora.domsys.cz](https://podpora.domsys.cz/sprava-pronajmu/sprava-najemniho-bytu-domu), [starlit.cz](https://www.starlit.cz/), [anasoft.com/domus](https://www.anasoft.com/domus/cz/home/inovace/), [oksoft.cz](https://oksoft.cz/windomy.html)

### Cloudové aplikace pro soukromé pronajímatele (rezidenční)

| Produkt | Cena | Inspirace pro nás |
|---|---|---|
| **MojeNájmy.cz** | zdarma do 5 jednotek; placené tarify nedohledány | KPI dashboard portfolia, AI import dokumentů, „revizní automat" |
| **Zvládneme.cz (Repilot)** | zdarma do 3 jednotek; pak 33 Kč/nájemce/měs | párování plateb podle VS+částky; portfolia více vlastníků = nejblíž našemu multi-entitnímu požadavku |
| **eDomovnice** | zkouška zdarma, ceník nedostupný | digitální předávací protokoly, klientská zóna nájemce |
| **Nemovitorium** | od 89 Kč/jednotka/měs degresivně | kontrola nájemců v exekucích/insolvenci; hlídání pojistek |

Zdroje: [mojenajmy.cz](https://mojenajmy.cz/), [zvladneme.cz](https://www.zvladneme.cz/), [edomovnice.cz](https://edomovnice.cz/), [nemovitorium.cz](https://www.nemovitorium.cz/)

**Žádná z těchto aplikací neinzeruje generování platebních kalendářů jako daňových dokladů** — všechny jsou cloud-only a stavěné na jednoho vlastníka a byty.

### ERP / CAFM s modulem nemovitostí (nejblíž komerčnímu use-case)

- **QI — modul Správa nemovitostí** ([qi.cz](https://www.qi.cz/moduly/sprava-nemovitosti/)): komerční prostory, předpisy záloh s párováním plateb, evidence spotřeby energií. Implementační ERP projekt.
- **Chastia FM** ([chastia.com](https://www.chastia.com/en/products/chastia-fm/)): jediný nalezený CZ/SK produkt, který explicitně inzeruje **generování splátkových kalendářů** a oddělenou fakturaci nájem/služby/energie — tedy naši klíčovou funkci, ovšem v enterprise měřítku (individuální implementace).
- pit-FM, FaMa+ CAFM — pro ~20 nájemců předimenzované.

### Co v ČR není software (ale zaznělo v rešerši)

- **Flatio** — marketplace střednědobých pronájmů, ne správa portfolia.
- **UlovDomov / Ideální nájemce** — služba garantovaného nájmu (cena = % z nájmu).
- **iDoklad** — fakturační cloud s opakovanými fakturami; jedna agenda = jedna entita, žádné platební kalendáře. Tarify od 0/240/358/625 Kč/měs ([idoklad.cz/cenik](https://www.idoklad.cz/cenik)).

## 2. Konkurence — zahraničí (stručně)

| Produkt | Segment | Cena | Klíčový vzor |
|---|---|---|---|
| **Re-Leased** (NZ/UK) | komerční pronájmy | od 199 USD/měs do 25 smluv | **kalendář smluvních událostí** (konce, rent reviews, opce); účetnictví neintegrovat, ale exportovat |
| **Stessa** (US) | investoři | 0/15/35 USD/měs | **portfolia = právní entity (LLC)** s agregovaným pohledem — přesný vzor pro naši multi-entitu |
| **TenantCloud** (US) | malý pronajímatel | 15–50 USD/měs | portály správce/vlastník/nájemce |
| **Landlord Studio** (US/UK) | malý pronajímatel | 0–28 USD/měs | mobilní evidence výdajů, daňové reporty |
| **objego / immocloud / VermietenPlus** (DE) | malý pronajímatel | ~5–10 €/měs | modulární platba za funkce; disciplína ročního vyúčtování služeb |
| **Landlord Vision** (UK) | pronajímatel | od 19,97 £/měs | kalendář zákonných povinností per nemovitost |
| **Buildium / AppFolio** (US) | správcovské firmy | od 62 USD / od ~298 USD/měs | owner statements; mimo naši velikost |

## 3. Syntéza: standard / diferenciátor / mezera

**Standard trhu (uživatelé očekávají):** evidence nemovitost → jednotka → nájemce → smlouva; hlídání konce smluv; předpis plateb; automatické párování bankovních plateb; upomínky; vyúčtování služeb; úložiště dokumentů; export XLS/PDF; v ČR nastupuje kontrola insolvence nájemců.

**Diferenciátory:** AI vytěžování dokumentů, nájemnický portál, kalendář smluvních událostí komerčního nájmu, automatická indexace smluv, integrace na účetní SW.

**Mezera na trhu, kterou vyplňujeme (potvrzeno rešerší):**
1. **Platební kalendáře jako daňové doklady** dle českého zákona o DPH — malé CZ aplikace to neumí, enterprise systémy (Chastia, QI) jsou předimenzované. Oddělené kalendáře nájem/energie/voda kvůli různým sazbám DPH nenabízí v hotové podobě nikdo.
2. **Multi-entita malého pronajímatele** (3 s.r.o. + fyzická osoba, vlastní číselné řady a účty, nad tím jeden pohled).
3. **On-premise** — moderní CZ aplikace jsou výhradně cloud; vlastní server = žádná přímá konkurence, ale také žádný hotový produkt k převzetí.
4. **Komerční specifika malého portfolia** (garáže/kanceláře/haly/pozemky, fakturace s DPH firmám).

## 4. Best practices (výběr relevantní pro nás)

### Datový model
- Hierarchie **Entita → Nemovitost/Jednotka → Smlouva → Položky smlouvy**; smlouva je samostatná entita s vlastním životním cyklem, ne atribut nájemce ([DOMSYS](https://podpora.domsys.cz/sprava-pronajmu/sprava-najemniho-bytu-domu), [ER vzory](https://www.geeksforgeeks.org/dbms/how-to-design-er-diagrams-for-real-estate-property-management/)).
- **Multi-entita jako první třída:** entita je povinný atribut smlouvy i dokladu; každá entita má vlastní číselné řady, účet a DPH nastavení; nad tím konsolidovaný pohled ([Re-Leased](https://www.re-leased.com/software/top-property-management-software-for-owner-operators-in-2026), [BPM](https://www.bpm.com/insights/multi-entity-accounting-property-portfolios/)).
- **Sazby s časovou osou platnosti** (změna od data, historie zůstává) — DOMSYS „složky předpisu"; přesně to potřebujeme pro historii cen i oddělené DPH.
- **Změny smluv jako nové verze parametrů s platností od–do**, ne editace na místě; stav smlouvy „k datu" ([Quarem](https://www.prnewswire.com/news-releases/quarem-announces-version-history-functionality-for-its-cre-platform-301447586.html), [HyperStart](https://www.hyperstart.com/blog/lease-amendments/)).
- Inflační doložka v ČR: odkaz na konkrétní index ČSÚ, uplatnění 1× ročně, písemné oznámení nájemci ([Dostupný advokát](https://dostupnyadvokat.cz/blog/inflacni-dolozka-v-najemni-smlouve), [pravopronajimatele.cz](https://www.pravopronajimatele.cz/inflacni-dolozka-v-najemni-smlouve/)).

### Doklady a platby
- Workflow: šablona → **hromadný generační běh s náhledem** → doklady s automatickým číslováním ([DOMSYS](https://podpora.domsys.cz/predpis-jako-faktura-vydana/predpis-jako-faktura-nastaveni-vs-a-par-symbolu)).
- **Číselné řady:** úplné a navazující per entita a rok; číslo přiděluje systém až při potvrzení; storna se nemažou ([SuperFaktura](https://www.superfaktura.cz/blog/jak-cislovat-a-necislovat-faktury/)).
- **Variabilní symbol:** pro opakované platby nájmu je lepší stálý „párovací" VS nájemce než VS z čísla dokladu; párování plateb kombinuje VS + protiúčet + částku ([DOMSYS](https://podpora.domsys.cz/predpis-jako-faktura-vydana/predpis-jako-faktura-nastaveni-vs-a-par-symbolu), [Zvládneme](https://blog.zvladneme.cz/2021/09/27/jak-funguje-parovani-plateb-v-samotne-aplikaci/), [Fakturoid](https://www.fakturoid.cz/podpora/automatizace/parovani-plateb-s-bankou)).
- Vygenerovaný dokument uložit jako **neměnné PDF k smlouvě** — archiv toho, co reálně odešlo.
- Oddělené doklady pro nájem/energie/vodu mají oporu v praxi kvůli různým režimům DPH ([Portál POHODA](https://portal.pohoda.cz/dane-ucetnictvi-mzdy/dph/prefakturace-sluzeb-z-pohledu-dph/), [Sagit](https://www.sagit.cz/info/uplatnovani-dph-pri-preuctovani-energii-a-sluzeb)).
- **QR Platba** dle standardu ČBA na dokladu snižuje chybovost plateb ([qr-platba.cz](https://qr-platba.cz/)).

### UX
- Dashboard: nahoře čísla, pod nimi seznamy s proklikem; vše filtrovatelné per entita ([Pencil & Paper](https://www.pencilandpaper.io/articles/ux-pattern-analysis-data-dashboards)).
- Seznam → detail; detail nájemce jako **časová osa** (smlouvy, doklady, poznámky, dokumenty na jednom místě).
- Export do XLS respektuje aktuální filtr a viditelné sloupce ([UX Planet](https://uxplanet.org/best-practices-for-usable-and-efficient-data-table-in-applications-4a1d1fb29550)).
- Audit trail: append-only log „kdo, kdy, co" u smluv, sazeb a dokladů; v UI jen záložka Historie pro čtení.

### Provoz on-premise
- **Zálohy 3-2-1**: tři kopie, dvě média, jedna mimo lokalitu; alespoň jedna kopie neměnná/offline; synchronizace do Drive/Dropbox **není** záloha; pravidelně testovat obnovu ([Acronis](https://www.acronis.com/en/blog/posts/backup-rule/), [iFeeltech](https://ifeeltech.com/blog/321-backup-rule-guide)).
- Záloha musí zahrnovat i archiv vygenerovaných PDF, ne jen databázi.
- Aplikaci nevystavovat do internetu; vzdálený přístup přes VPN; individuální účty kvůli audit trailu.
- Aktualizace: data oddělená od aplikace, verzované migrace DB, záloha před každou aktualizací.

### Co se nepodařilo dohledat
- Ceníky STARLIT (pásma), DOMUS, WinDomy, QI, Chastia — individuální nabídky.
- Detail funkce „Revizní automat" MojeNájmy (web se nenačetl).
- Specifika evidence kaucí u komerčních nájmů v software (zdroje řeší hlavně byty).
