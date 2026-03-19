# Pakendimuudatuste aruanne (Power BI) – Lõputöö

**Autor:** Mariliis Toon  
**Teema:** Pakendimuudatuste (IMD) ülevaade ja analüüs Power BI abil  
**Eesmärk:** luua Power BI aruanne, mis toetab otsustamist: lühendada keskmist läbitöötlusaega ja tõsta SLA ≤30 päeva katvust, hoides avatud vs suletud IMD-de voo tasakaalus.

> Lõputöö ülesehitus järgib Google Data Analytics capstone’i protsessi **Ask – Prepare – Process – Analyze – Share/Act** ning BCS koolituse õpiväljundeid (andmete ettevalmistus, analüüs, visualiseerimine, eetika/andmekaitse, otsustugi). [1](https://github.com/marcoshsq/GoogleDataAnalyticsCapstone)[2](https://www.bcskoolitus.ee/koolitus/andmeanaluutikute-taiendkoolitusprogramm/)

---

## 1. Probleem


**Mõõdikud:**
- Avg duration (days) ↓ (eesmärk nt −10% kvartali jooksul)
- SLA ≤30 päeva (%) ↑ (eesmärk nt +8 p.p.)
- Opened vs Closed tasakaal (WIP ei kasva mitmel järjestikusel kuul)

---

## 2. Metoodika (Ask – Prepare – Process – Analyze – Share/Act)

### ASK
- Äriküsimus + tööhüpoteesid:  
  - H1: teatud BusinessCase’id (nt *Change of packaging material*) annavad ebaproportsionaalse osa mahust ja kestusest.  
  - H2: perioodidel, mil **Opened > Closed**, kasvab **WIP** ja **Avg duration** pikeneb.

### PREPARE (andmed, kvaliteet)
- Allikad: Smartflow Loftware andmebaas 
- Kvaliteet: eraldada IMDd AW proejktidest, eemaldada duplikaadid, puuduvad kuupäevad, kestus ≤ 0, 
- Andmekaitse: anonümiseerida

### PROCESS (Power Query, loogika)


### ANALYZE (visualiseeritavad järeldused)
- Trend: **Avg duration by Month**; hooajalisus/kõikumine.  
- Voo tasakaal: **Opened vs Closed by Month**, **WIP** dünaamika.  
- SLA: **% Closed ≤30 days by Month** ja segmentide kaupa.  
- Pareto: **Total IMDs by BusinessCase** (Top‑N + “Other”).

### SHARE/ACT (otsustugi)

---

## 3. Andmemudel

**IMD**  


**Dimensioonid:**  
- `Date`   
- `IMDType`  
- `BusinessCase`  
- `Country`
  
---

## 4. Mõõdikud (definitsioonid)

- **Avg duration (days)**   
- **Opened (Month)**
- **Closed (Month)** 
- **WIP (MonthEnd)** = eelmise kuu WIP + Opened − Closed.  
- **SLA ≤30 päeva (%)** = suletud ≤30 päeva / kõik suletud (filtri kontekstis).

---

## 5. Andmekaitse ja anonüümistamine

---

## 6. Dashboard

**Leht “Overview”**
- KPI kaardid: **Avg duration (days)**, **Opened (kuu)**, **Closed (kuu)**, **SLA ≤30 päeva (%)**  
- Joon: **Avg duration by Month**  
- 2 tulpdiagrammi: **Opened by Month** ja **Closed by Month**
- Pareto: **Total IMDs by BusinessCase**  

---

