# Pakendimuudatuste aruanne (Power BI) – Lõputöö

**Autor:** Mariliis Toon  
**Teema:** Pakendimuudatuste (IMD) ülevaade ja analüüs Power BI abil  
**Eesmärk:** luua Power BI aruanne, mis toetab otsustamist: lühendada keskmist läbitöötlusaega ja tõsta SLA ≤30 päeva katvust, hoides avatud vs suletud IMD-de voo tasakaalus.

> Lõputöö ülesehitus järgib Google Data Analytics capstone’i protsessi **Ask – Prepare – Process – Analyze – Share/Act** ning BCS koolituse õpiväljundeid (andmete ettevalmistus, analüüs, visualiseerimine, eetika/andmekaitse, otsustugi). [1](https://github.com/marcoshsq/GoogleDataAnalyticsCapstone)[2](https://www.bcskoolitus.ee/koolitus/andmeanaluutikute-taiendkoolitusprogramm/)

---

## 1. Probleemipüstitus

Kuidas vähendada pakendimuudatuste (IMD) **keskmist läbitöötlusaega** ja tõsta **SLA ≤30 päeva** katvust, samal ajal hoides **Opened vs Closed** IMD-de voo tasakaalus?

**Edu mõõdikud:**
- Avg duration (days) ↓ (eesmärk nt −10% kvartali jooksul)
- SLA ≤30 päeva (%) ↑ (eesmärk nt +8 p.p.)
- Opened vs Closed tasakaal (WIP ei kasva mitmel järjestikusel kuul)

---

## 2. Metoodika (Ask – Prepare – Process – Analyze – Share/Act)

### ASK
- Äriküsimus (ülal) + tööhüpoteesid:  
  - H1: teatud BusinessCase’id (nt *Change of packaging material*) annavad ebaproportsionaalse osa mahust ja kestusest.  
  - H2: perioodidel, mil **Opened > Closed**, kasvab **WIP** ja **Avg duration** pikeneb.

### PREPARE (andmed, kvaliteet)
- Allikad: IMD logi (IMD ID, OpenDate, CloseDate, IMDType, BusinessCase, CountryGroup).  
- Kvaliteet: duplikaadid, puuduvad kuupäevad, kestus ≤ 0, ebanormaalsed tipud (märgistada/ eemaldada).  
- Andmekaitse: vt peatükk 5.

> Capstone’i protsess eeldab selget äriküsimust, andmete valikut ja ettevalmistust, analüüsi ning tulemuste kommunikeerimist/soovitusi. [1](https://github.com/marcoshsq/GoogleDataAnalyticsCapstone)

### PROCESS (Power Query, loogika)
- Tuleta väljad:
  - `OpenMonth = Date.StartOfMonth(OpenDate)`
  - `CloseMonth = Date.StartOfMonth(CloseDate)`
  - `DurationDays = Duration.Days(CloseDate - OpenDate)`
- Moodusta agregaatvaated kuutasemel (Opened/Closed).
- Väike‑N kaitse: kombinatsioonid, kus N<5, ei avaldata (vt ptk 5).

### ANALYZE (visualiseeritavad järeldused)
- Trend: **Avg duration by Month**; hooajalisus/kõikumine.  
- Voo tasakaal: **Opened vs Closed by Month**, **WIP** dünaamika.  
- SLA: **% Closed ≤30 days by Month** ja segmentide kaupa.  
- Pareto: **Total IMDs by BusinessCase** (Top‑N + “Other”).

### SHARE/ACT (otsustugi)
- Soovitused: eeltšekk/gating, prioriseerimine case‑mix’i põhjal, parimate praktikate ülekandmine regioonide vahel.
- Mõõdetavad sihid: Avg duration −10%, SLA +8 p.p., WIP stabiilne.

---

## 3. Andmemudel (lihtne “tärn”)

**F_IMD (fakt):**  
`IMD_Token`, `OpenDate`, `CloseDate`, `IMDType`, `BusinessCase`, `CountryGroup`, *(tuletatud)* `DurationDays`, `OpenMonth`, `CloseMonth`, `IsClosed`.

**Dimensioonid:**  
- `D_Date` (kalender)  
- `D_IMDType`  
- `D_BusinessCase`  
- `D_CountryGroup`

Seosed: F_IMD → dimensioonid (1:*). Kuutasemel analüüs, vajadusel eraldi vaated Open/Close järgi.

---

## 4. Mõõdikud (definitsioonid)

- **Avg duration (days)** = keskmine(`CloseDate - OpenDate`) (ainult suletud IMD-d).  
- **Opened (Month)** = arv IMD-sid, mille `OpenMonth` on valitud kuu.  
- **Closed (Month)** = arv IMD-sid, mille `CloseMonth` on valitud kuu.  
- **WIP (MonthEnd)** = eelmise kuu WIP + Opened − Closed.  
- **SLA ≤30 päeva (%)** = suletud ≤30 päeva / kõik suletud (filtri kontekstis).

*(Soovi korral lisada DAX-implementatsioon lisasse.)*

---

## 5. Andmekaitse ja anonüümistamine

**Lähtekoht:**  
- *Pseudonüümimine* (pööratav; jääb GDPR alla) versus *anonüümimine* (pöördumatu; GDPR-ist väljas). [3](https://www.europeanlawblog.eu/pub/tfef074h)

**Kasutatav protokoll (lihtne ja auditeeritav):**
1) **Tokenid**: asenda IMD ID/Project/Partner tokenitega; mapping jääb sisevõrku (repo ei sisalda). [3](https://www.europeanlawblog.eu/pub/tfef074h)  
2) **Kuupäevad**: kuva kuutasemel; soovi korral lisa deterministlik päevanihe Δ (nt +29 p).  
3) **Raha/kogused** (kui üldse kuvada): skaleeri konstandiga *k*, säilitades suhted, varjates absoluudid.  
4) **Väike‑N (k‑anonymity idee)**: kombinatsioone, kus **N < 5**, ei avalikustata. [4](https://utrechtuniversity.github.io/dataprivacyhandbook/k-l-t-anonymity.html)  
5) **Anonümiseeri enne importi** (Power Query), et vältida lekkimist .pbix metaandmetest ja tooltipidest. [5](https://community.fabric.microsoft.com/t5/Desktop/How-to-anonymize-mask-part-of-Data/m-p/899308)  
6) **Sensitivity Label** Power BI-s (nt “Internal/Confidential”): label kandub edasi eksportides (Excel/PDF/PPT). [6](https://learn.microsoft.com/en-us/fabric/enterprise/powerbi/service-security-apply-data-sensitivity-labels)

> Soovi korral võib kasutada ka sünteetilist andmestikku (konstrueeritud jaotused/korrelatsioonid; 0 pärisrida), mis tüüpiliselt kvalifitseerub anonüümseks, kui realistlik re‑identifitseerimine puudub. [4](https://utrechtuniversity.github.io/dataprivacyhandbook/k-l-t-anonymity.html)

---

## 6. Dashboardi minimaalne komplekt (lehed & visuaalid)

**Leht “Overview”**
- KPI kaardid: **Avg duration (days)**, **Opened (kuu)**, **Closed (kuu)**, **SLA ≤30 päeva (%)**  
- Joon: **Avg duration by Month**  
- 2 tulpdiagrammi: **Opened by Month** ja **Closed by Month** (või üks virnitud)  
- Pareto: **Total IMDs by BusinessCase**  
- Filtrid: **Month/Quarter**, **IMDType**, **BusinessCase**, **CountryGroup**

**Leht “SLA & Flow” (valikuline)**
- **% Closed ≤30 days by Month** (joon/ala)  
- **WIP by Month** (joon)

---

## 7. Projekti struktuur (repo)

``
