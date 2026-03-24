---
titel: Verkoopworkflow — Van Offerte tot Factuur
bedrijf: [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Beginner
doelgroep: Ondernemer, Boekhouder, Verkoop
---

# Verkoopworkflow — Van Offerte tot Factuur

## Overzicht

Dit werkboek beschrijft de complete verkoopworkflow in ERPNext: van het eerste klantcontact tot de uiteindelijke factuur. Elke stap bouwt voort op de vorige en ERPNext houdt automatisch de koppeling tussen alle documenten bij.

```
Quotation → Sales Order → Project → Task → Delivery Note → Sales Invoice
(Offerte)    (Order)      (Project)  (Taak)  (Afleverbon)   (Factuur)
```

---

## Stap 1: Quotation (Offerte)

**Doel:** Een prijsopgave maken voor de klant.

### Wanneer?

- Klant vraagt om een offerte voor een dienst of product
- U wilt een formeel aanbod vastleggen
- Bij aanbestedingen of meervoudige offerteaanvragen

### Aanmaken

1. Ga naar **Selling > Quotation > New**
2. Vul in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **Quotation To** | `Customer` | Of `Lead` als de klant nog geen klant is |
| **Party Name** | Selecteer klant | Bijv. `Domera` |
| **Company** | `[BEDRIJFSNAAM]` | — |
| **Transaction Date** | Vandaag | Datum van de offerte |
| **Valid Till** | Bijv. 30 dagen later | Geldigheidsduur |
| **Order Type** | `Sales` of `Maintenance` | Type opdracht |

3. **Items toevoegen** — klik "+ Add Row":

| Veld | Waarde | Voorbeeld |
|---|---|---|
| **Item Code** | Selecteer artikel/dienst | `Bouwadvies per uur` |
| **Qty** | Aantal | `40` (uren) |
| **Rate** | Prijs per eenheid | `95.00` |
| **Amount** | Automatisch berekend | `3.800,00` |

4. **BTW toevoegen** — scroll naar "Taxes and Charges":
   - Selecteer template: `Netherlands VAT 21% - [AFK]`
   - Of handmatig: Charge Type `On Net Total`, Rate `21`

5. **Betalingsvoorwaarden** — worden automatisch overgenomen van de klant of stel in:
   - Template: `21 Dagen`

6. **Save**

### Offerte Versturen

1. Klik **Print** → selecteer het juiste Print Format
2. Controleer:
   - Klantgegevens correct?
   - Items en prijzen correct?
   - BTW correct berekend?
   - KvK- en BTW-nummer vermeld?
   - Betalingsvoorwaarden vermeld?
3. Klik **Email** om de offerte per e-mail te versturen
   - Of **Print > PDF** om als bijlage te versturen

### Status

| Status | Betekenis |
|---|---|
| **Draft** | Concept — nog niet definitief |
| **Submitted** | Definitief — verstuurd naar klant |
| **Ordered** | Klant heeft akkoord gegeven → Sales Order aangemaakt |
| **Lost** | Offerte niet geaccepteerd |
| **Expired** | Geldigheidsduur verstreken |

### Tips

- Gebruik **Item Groups** om diensten en producten te categoriseren
- Sla veelgebruikte items op zodat u ze snel kunt selecteren
- Gebruik het veld **Terms and Conditions** voor algemene voorwaarden
- Bij herhaalopdrachten: kopieer een bestaande offerte via **Menu > Duplicate**

---

## Stap 2: Sales Order (Verkooporder)

**Doel:** De opdracht formeel vastleggen na akkoord van de klant.

### Aanmaken vanuit Quotation

1. Open de geaccepteerde Quotation
2. Klik **Create > Sales Order**
3. ERPNext kopieert automatisch alle gegevens van de offerte

### Of Handmatig Aanmaken

1. Ga naar **Selling > Sales Order > New**
2. Vul in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **Customer** | Selecteer klant | Overgenomen van offerte |
| **Company** | `[BEDRIJFSNAAM]` | — |
| **Delivery Date** | Verwachte opleverdatum | Belangrijk voor planning |
| **PO No** | Inkoopordernummer klant | Als de klant een PO-nummer heeft |
| **PO Date** | Datum PO klant | — |

3. Items worden overgenomen van de offerte. Pas aan indien nodig.
4. **Save & Submit**

### Belangrijk: Submit

De Sales Order moet worden **Submitted** (definitief gemaakt) voordat u:
- Een Project kunt aanmaken
- Een Delivery Note kunt maken
- Een Sales Invoice kunt genereren

### Status

| Status | Betekenis |
|---|---|
| **Draft** | Concept |
| **To Deliver and Bill** | Nog te leveren en te factureren |
| **To Bill** | Geleverd, nog te factureren |
| **To Deliver** | Gefactureerd, nog te leveren |
| **Completed** | Volledig geleverd en gefactureerd |
| **Cancelled** | Geannuleerd |

---

## Stap 3: Project

**Doel:** Het werk organiseren, voortgang bijhouden en kosten per project registreren.

### Aanmaken vanuit Sales Order

1. Open de Sales Order
2. Klik **Create > Project**
3. ERPNext maakt een project aan gekoppeld aan de Sales Order

### Of Handmatig Aanmaken

1. Ga naar **Projects > Project > New**
2. Vul in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **Project Name** | Beschrijvende naam | `Bouwadvies Domera - Nieuwbouw` |
| **Company** | `[BEDRIJFSNAAM]` | — |
| **Status** | `Open` | Wordt automatisch bijgewerkt |
| **Expected Start Date** | Startdatum | — |
| **Expected End Date** | Einddatum | — |
| **Sales Order** | Koppel aan SO | Als het project voortkomt uit een order |
| **Cost Center** | `Main - [AFK]` | Of `Work by third parties - [AFK]` |
| **Project Type** | Selecteer type | Bijv. `External` |

3. **Save**

### Projectbudget

Stel een budget in om kosten te bewaken:

1. Scroll naar de sectie **Costing and Billing**
2. Vul in:
   - **Estimated Cost** — Geschatte kosten
   - **Total Sales Amount** — Omzet (uit Sales Order)

ERPNext houdt automatisch bij:
- Totale kosten (uit Purchase Invoices, Timesheets, Expense Claims)
- Totale omzet (uit Sales Invoices)
- Marge

### Tips

- Gebruik **% Complete Method** = `Task Completion` om voortgang automatisch te berekenen op basis van taken
- Koppel Purchase Invoices aan het project via het veld "Project" op de factuur — zo worden inkoopkosten per project bijgehouden

---

## Stap 4: Task (Taak)

**Doel:** Het werk opdelen in concrete taken en toewijzen aan medewerkers.

### Aanmaken

1. Open het Project
2. Scroll naar de **Tasks** sectie
3. Klik **Add Row** of ga naar **Projects > Task > New**
4. Vul in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **Subject** | Taakomschrijving | `Constructieberekening` |
| **Project** | Automatisch gekoppeld | — |
| **Status** | `Open` | Open / Working / Completed / Cancelled |
| **Priority** | `Medium` | Low / Medium / High / Urgent |
| **Assigned To** | Selecteer medewerker | Wie voert de taak uit |
| **Expected Start Date** | Startdatum | — |
| **Expected End Date** | Einddatum | — |
| **Expected Time** | Geschatte uren | Bijv. `8` uur |

5. **Save**

### Voorbeeld: Taken voor een Bouwadviesproject

| Taak | Toegewezen aan | Geschatte uren | Status |
|---|---|---|---|
| Intake en locatiebezoek | Maarten | 4 | Open |
| Constructieberekening | Maarten | 16 | Open |
| Rapport opstellen | Elizabeth | 8 | Open |
| Review en oplevering | Maarten | 4 | Open |
| Eindbespreking klant | Maarten | 2 | Open |

### Tijdregistratie

Medewerkers registreren hun uren op taken via **Timesheets**:

1. Ga naar **Projects > Timesheet > New**
2. Vul in:
   - **Employee**: selecteer medewerker
   - **Time Logs**: voeg regels toe per taak
     - **Project**: selecteer project
     - **Task**: selecteer taak
     - **Hours**: aantal uren
     - **Activity Type**: bijv. `Consulting`, `Engineering`
     - **Billing Rate**: uurtarief (voor facturatie)
3. **Save & Submit**

De geregistreerde uren worden automatisch:
- Bijgeschreven op de taak (Actual Time)
- Bijgeschreven op het project (Total Billable Amount)
- Beschikbaar voor facturatie

### Tips

- Gebruik **Task Dependencies** om aan te geven welke taken eerst af moeten zijn
- Het **Gantt-diagram** (via Projects > Gantt Chart) geeft visueel overzicht van de planning
- Taken kunnen ook **subtaken** hebben voor complexe werkpakketten

---

## Stap 5: Delivery Note (Afleverbon)

**Doel:** Vastleggen dat goederen of diensten zijn geleverd aan de klant.

### Wanneer Gebruiken?

- Bij fysieke levering van goederen
- Bij oplevering van een dienst of projectresultaat
- Als u de levering en facturatie wilt scheiden (niet altijd nodig bij dienstverlening)

### Aanmaken vanuit Sales Order

1. Open de Sales Order
2. Klik **Create > Delivery Note**
3. ERPNext kopieert de items van de Sales Order

### Velden

| Veld | Waarde | Toelichting |
|---|---|---|
| **Customer** | Automatisch | Van Sales Order |
| **Posting Date** | Leverdatum | Datum van oplevering |
| **Items** | Overgenomen | Pas hoeveelheden aan bij deellevering |
| **Project** | Koppel aan project | Voor kostentoerekening |

### Deelleveringen

U kunt meerdere Delivery Notes aanmaken voor een Sales Order:
- Lever 20 van de 40 uren → eerste Delivery Note
- Lever de resterende 20 uren → tweede Delivery Note
- ERPNext houdt automatisch bij hoeveel er nog open staat

### Submit

Na controle: **Save & Submit**. De Delivery Note is nu definitief en de Sales Order wordt bijgewerkt.

### Tips

- Bij pure dienstverlening (geen fysieke goederen) kunt u de Delivery Note overslaan en direct een Sales Invoice maken vanuit de Sales Order
- Gebruik Delivery Notes als u tussentijdse opleveringen wilt documenteren
- Print de Delivery Note als pakbon bij fysieke leveringen

---

## Stap 6: Sales Invoice (Verkoopfactuur)

**Doel:** De klant factureren voor de geleverde goederen of diensten.

### Aanmaken

Er zijn meerdere routes om een Sales Invoice aan te maken:

| Route | Wanneer |
|---|---|
| **Vanuit Sales Order** | Factureren zonder Delivery Note |
| **Vanuit Delivery Note** | Factureren na levering |
| **Vanuit Timesheet** | Factureren op basis van uren |
| **Handmatig** | Losse factuur zonder voorafgaande documenten |

### Vanuit Sales Order (Meest Gebruikelijk)

1. Open de Sales Order
2. Klik **Create > Sales Invoice**
3. ERPNext kopieert alle gegevens

### Vanuit Timesheet (Urendeclaratie)

1. Open het Timesheet (submitted)
2. Klik **Create > Sales Invoice**
3. De geregistreerde uren worden automatisch als regels opgenomen
4. Uurtarief x uren = factuurbedrag

### Velden Controleren

| Veld | Waarde | Toelichting |
|---|---|---|
| **Customer** | Automatisch | — |
| **Company** | `[BEDRIJFSNAAM]` | — |
| **Posting Date** | Factuurdatum | Datum waarop de factuur wordt uitgegeven |
| **Due Date** | Automatisch | Op basis van Payment Terms (21 dagen) |
| **Debit To** | `12000 - Debtors - [AFK]` | Debiteurenrekening |
| **Cost Center** | `Main - [AFK]` | Kostenplaats |
| **Project** | Selecteer project | Voor projectrapportage |

### Items Controleren

| Veld | Waarde |
|---|---|
| **Item Code** | Overgenomen van Sales Order |
| **Qty** | Hoeveelheid (eventueel aangepast voor deelfactuur) |
| **Rate** | Prijs per eenheid |
| **Income Account** | Automatisch (bijv. `Sales - [AFK]` of `Service - [AFK]`) |

### BTW Controleren

- Template moet correct zijn: `Netherlands VAT 21% - [AFK]` (of ander tarief)
- Controleer het BTW-bedrag
- Controleer het totaalbedrag inclusief BTW

### Wettelijke Vereisten Nederlandse Factuur

Controleer dat de factuur bevat:

- [ ] Volledige naam en adres van uw bedrijf
- [ ] Volledige naam en adres van de klant
- [ ] Factuurnummer (doorlopend)
- [ ] Factuurdatum
- [ ] KvK-nummer
- [ ] BTW-identificatienummer
- [ ] Omschrijving van de dienst/goederen
- [ ] Hoeveelheid en prijs per eenheid
- [ ] Totaalbedrag exclusief BTW
- [ ] BTW-tarief en BTW-bedrag
- [ ] Totaalbedrag inclusief BTW
- [ ] Betalingstermijn en IBAN

### Submit

Na controle: **Save & Submit**

De factuur is nu:
- Definitief (niet meer te wijzigen)
- Geboekt in het grootboek (Debit: Debiteuren, Credit: Omzet + BTW)
- Zichtbaar in rapportages
- Klaar om te versturen naar de klant

### Versturen

1. Klik **Email** → de factuur wordt als PDF bijlage verstuurd
2. Of klik **Print > PDF** en verstuur handmatig

### Deelfacturatie

U kunt een Sales Order in meerdere delen factureren:

1. Maak een Sales Invoice vanuit de Sales Order
2. Pas de hoeveelheden aan (bijv. 50% van de uren)
3. Submit
4. Later: maak opnieuw een Sales Invoice vanuit dezelfde Sales Order
5. ERPNext toont alleen het nog niet gefactureerde deel

---

## Volledige Workflow — Samenvatting

```
┌─────────────┐     Klant akkoord     ┌─────────────┐
│  Quotation  │ ──────────────────── → │ Sales Order  │
│  (Offerte)  │                        │  (Order)     │
└─────────────┘                        └──────┬───┬──┘
                                              │   │
                                    ┌─────────┘   └──────────┐
                                    ▼                        ▼
                             ┌─────────────┐         ┌──────────────┐
                             │   Project   │         │ Delivery Note│
                             │             │         │ (Afleverbon) │
                             └──────┬──────┘         └──────┬───────┘
                                    │                        │
                                    ▼                        │
                             ┌─────────────┐                │
                             │    Tasks    │                 │
                             │  + Timesheet│                 │
                             └──────┬──────┘                │
                                    │                        │
                                    └────────┬───────────────┘
                                             ▼
                                      ┌─────────────┐
                                      │Sales Invoice│
                                      │  (Factuur)  │
                                      └──────┬──────┘
                                             │
                                             ▼
                                      ┌─────────────┐
                                      │  Betaling   │
                                      │  (Payment   │
                                      │   Entry)    │
                                      └─────────────┘
```

### Documentkoppeling

ERPNext houdt de volledige keten bij. In elk document kunt u zien:

| Document | Ziet u ook |
|---|---|
| Quotation | → Sales Order (als aangemaakt) |
| Sales Order | ← Quotation, → Project, → Delivery Note, → Sales Invoice |
| Project | ← Sales Order, → Tasks, → Timesheets |
| Task | ← Project, → Timesheets |
| Delivery Note | ← Sales Order |
| Sales Invoice | ← Sales Order, ← Delivery Note, → Payment Entry |

Dit geeft volledige traceerbaarheid van offerte tot betaling.

---

## Variaties op de Workflow

### Directe Facturatie (Zonder Project)

Voor eenvoudige verkopen zonder projectbeheer:

```
Quotation → Sales Order → Sales Invoice
```

Sla Project, Tasks en Delivery Note over.

### Dienstverlening op Urenbasis

```
Sales Order → Project → Tasks + Timesheets → Sales Invoice (vanuit Timesheet)
```

Facturatie op basis van daadwerkelijk geregistreerde uren.

### Productverkoop met Levering

```
Sales Order → Delivery Note → Sales Invoice
```

Gebruik Delivery Note als pakbon bij fysieke leveringen.

### Terugkerende Facturatie (Abonnementen)

```
Sales Order → Auto-repeat Sales Invoice (maandelijks/kwartaal)
```

Stel een **Auto Repeat** in op de Sales Invoice voor terugkerende facturatie.

### Voorschotfacturatie

```
Sales Order → Sales Invoice (30% voorschot)
              → werk uitvoeren
              → Sales Invoice (70% restant)
```

### Nacalculatie

```
Sales Order → Project → Tasks + Timesheets + Purchase Invoices
              → Projectrapport (kosten vs. budget)
              → Sales Invoice (op basis van werkelijke kosten + marge)
```

---

## Snelle Referentie

### Navigatie

| Document | Pad |
|---|---|
| Quotation | Selling > Quotation |
| Sales Order | Selling > Sales Order |
| Project | Projects > Project |
| Task | Projects > Task |
| Timesheet | Projects > Timesheet |
| Delivery Note | Stock > Delivery Note |
| Sales Invoice | Accounts > Sales Invoice |

### Standaardwaarden [BEDRIJFSNAAM]

| Instelling | Waarde |
|---|---|
| **Company** | `[BEDRIJFSNAAM]` |
| **Debit To** | `12000 - Debtors - [AFK]` |
| **Income Account** | `Sales - [AFK]` of `Service - [AFK]` |
| **Cost Center** | `Main - [AFK]` |
| **BTW Template** | `Netherlands VAT 21% - [AFK]` |
| **Payment Terms** | `21 Dagen` |

### Veelgebruikte Statusovergangen

| Actie | Van | Naar |
|---|---|---|
| Klant accepteert offerte | Quotation → | Sales Order |
| Project starten | Sales Order → | Project |
| Werk opleveren | Tasks (Completed) → | Delivery Note |
| Factureren | Sales Order / DN → | Sales Invoice |
| Betaling ontvangen | Sales Invoice → | Payment Entry |

---

## Tips & Best Practices

### Wel Doen

1. **Altijd beginnen met een Quotation** — ook bij mondeling akkoord, leg het vast
2. **Sales Order submitten** voordat u begint met werken — dit is uw formele opdracht
3. **Project aanmaken** voor werk dat langer duurt dan een dag — dit geeft overzicht
4. **Timesheets bijhouden** — registreer uren op de dag zelf, niet achteraf
5. **Factureer regelmatig** — maandelijks of bij oplevering, niet pas aan het einde
6. **Controleer de factuur** op wettelijke vereisten voordat u verstuurt

### Niet Doen

1. **Geen Sales Invoice zonder Sales Order** — u mist de koppeling en traceerbaarheid
2. **Geen uren registreren zonder Task** — de uren zijn dan niet gekoppeld aan het project
3. **Niet factureren met verkeerde BTW** — controleer altijd het tarief
4. **Geen facturen versturen zonder PDF-controle** — print altijd eerst een voorbeeld

---

*Versie 1.0 — 24 maart 2026*
