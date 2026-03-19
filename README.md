# Pakendimuudatuste aruanne (Power BI) – Lõputöö

**Autor:** Mariliis Toon  
**Teema:** Pakendimuudatuste (IMD) ülevaade ja analüüs Power BI abil  
**Eesmärk:** luua Power BI aruanne, mis toetab otsustamist: lühendada keskmist läbitöötlusaega ja tõsta SLA ≤30 päeva katvust, hoides avatud vs suletud IMD-de voo tasakaalus.

> Lõputöö ülesehitus järgib Google Data Analytics protsessi **Ask – Prepare – Process – Analyze – Share/Act** ning BCS koolituse õpiväljundeid.

---

## 1. Probleem

Pakendimuudatuste (IMD) protsessi ajad kõiguvad ning perioodidel, mil avamisi on rohkem kui sulgemisi, kasvab WIP, mis omakorda venitab keskmist kestust. Vajame lihtsat Power BI ülevaadet, mis kuude lõikes näitab **Avg duration**, **Opened/Closed**, **SLA ≤30 päeva** ja võimaldab tuvastada **pudelikaelu** (nt BusinessCase’i või IMD tüübi lõikes). 

**Mõõdikud:**
- Avg duration (days) ↓ 
- SLA ≤30 päeva (%) ↑ (eesmärk nt +8 p.p.)
- Opened vs Closed tasakaal (WIP ei kasva mitmel järjestikusel kuul)

---

## 2. Metoodika (Ask – Prepare – Process – Analyze – Share/Act)

### ASK
- Äriküsimus + tööhüpoteesid:  
  - teatud BusinessCase’id (nt *Change of packaging material*) annavad ebaproportsionaalse osa mahust ja kestusest.  
  - perioodidel, mil **Opened > Closed**, kasvab **WIP** ja **Avg duration** pikeneb.

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
  Keskmine päevade arv IMD avamisest sulgemiseni valitud filtrikontekstis.  
  **Loogika:** arvuta vaid suletud IMD-del `CloseDate − OpenDate` (päevades) ja võta nende väärtuste keskmine.  
  **Märkus:** avamata read ei kuulu arvestusse.

- **SLA ≤30 päeva (%)**  
  Osakaal suletud IMD-dest, mis suleti **30 päeva või kiiremini**.  
  **Loogika:** `(# suletud IMD, kus DurationDays ≤ 30) / (# kõik suletud IMD)` × 100%.

- **Opened (Month)**  
  Valitud kuus **avamiste arv**: IMD, mille `OpenDate` kuulub valitud kuusse (`OpenMonth`).

- **Closed (Month)**  
  Valitud kuus **sulgemiste arv**: IMD, mille `CloseDate` kuulub valitud kuusse (`CloseMonth`).

- **WIP (MonthEnd)**  
  Kuu lõpu seisuga **pooleliolevad** IMD-d.  
  **Loogika:** loenda IMD, millel `OpenDate ≤ kuu_lõpp` **ja** (`CloseDate` on tühi **või** `CloseDate > kuu_lõpp`).  
  **Taust:** WIP = “parasjagu süsteemis olev, kuid lõpetamata töö/üksused”

- **Opened vs Closed tasakaal**  
  Voo tasakaal on **OK**, kui valitud perioodil `Opened ≈ Closed`. Kui korduvalt `Opened > Closed`, **WIP kasvab** ja keskmine kestus kipub pikenema (intuitsioon Little’i seadusest: **WIP = läbilaskevõime × tsükli aeg** stabiilses olekus).

## 5. Andmekaitse ja anonüümistamine

---

## 6. Dashboard

**Leht “Overview”**
- KPI kaardid: **Avg duration (days)**, **Opened (kuu)**, **Closed (kuu)**, **SLA ≤30 päeva (%)**  
- Joon: **Avg duration by Month**  
- 2 tulpdiagrammi: **Opened by Month** ja **Closed by Month**
- Pareto: **Total IMDs by BusinessCase**  

---

## 7.Järeldus

Kokkuvõtvalt võimaldab dashboard tuvastada perioodid ja segmendid, kus IMD protsess venib (Avg duration)
---

