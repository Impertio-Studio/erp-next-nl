---
titel: Payment Order & SEPA Export Workflow
bedrijf: [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Gevorderd
doelgroep: Boekhouder, Ondernemer
---

# Payment Order & SEPA Export Workflow — [BEDRIJFSNAAM]

## Inhoudsopgave

1. [Overzicht](#overzicht)
2. [Voorbereiding: Supplier Setup](#voorbereiding-supplier-setup)
3. [Stap 1: Purchase Invoices Aanmaken](#stap-1-purchase-invoices-aanmaken)
4. [Stap 2: Payment Order Aanmaken](#stap-2-payment-order-aanmaken)
5. [Stap 3: SEPA XML Export](#stap-3-sepa-xml-export)
6. [Stap 4: Upload naar Rabobank](#stap-4-upload-naar-rabobank)
7. [Troubleshooting](#troubleshooting)
8. [Checklist](#checklist)

---

## Overzicht

**Doel:** Betalingen aan leveranciers verwerken via ERPNext en exporteren als SEPA XML voor Rabobank.

**Betrokken systemen:**

- ERPNext ([ERPNext URL])
- Rabobank Online Banking

**Tijdsduur:** ca. 15-30 minuten voor 10-20 facturen

---

## Voorbereiding: Supplier Setup

**Dit moet eenmalig goed gebeuren per leverancier.**

### Stap 1: Check of Supplier bestaat

1. Ga naar: **Buy > Supplier**
2. Zoek de leverancier op naam
3. Als de leverancier bestaat: ga naar Stap 3 (Bank Account)
4. Als de leverancier niet bestaat: ga naar Stap 2

### Stap 2: Nieuwe Supplier Aanmaken

**Wanneer?** Als je een factuur krijgt van een nieuwe leverancier.

1. Ga naar: **Buy > Supplier > New**
2. Vul in:

| Veld | Waarde | Voorbeeld |
|---|---|---|
| **Supplier Name** | Exacte naam zoals op factuur | `PCheck B.V.` |
| **Supplier Group** | Kies relevant | `Local` of `All Supplier Groups` |
| **Country** | Nederland (meestal) | `Netherlands` |
| **Payment Terms** | Betalingstermijn | `21 Dagen` |
| **Tax Category** | BTW regeling | `EU NL Full tax` (standaard NL suppliers) |
| **Is Internal Supplier** | Alleen voor eigen entiteiten | Aanvinken voor Cooperatie/Bouwkunde |
| **Companies** | Welke companies mogen kopen | `[BEDRIJFSNAAM]` (minimaal) |

3. Klik **Save**

### Stap 3: Bank Account Aanmaken

**Zonder Bank Account werkt de SEPA export niet!**

1. Ga naar de Supplier die je net hebt aangemaakt (of een bestaande)
2. Scroll naar beneden naar de "Bank Accounts" sectie
3. Klik "+ Add Row" of ga direct naar: **Accounts > Bank Account > New**
4. Vul in:

| Veld | Waarde | Voorbeeld | Verplicht |
|---|---|---|---|
| **Account Name** | `[Supplier] - [Bank]` | `PCheck B.V. - ABN AMRO` | Ja |
| **Bank** | Kies uit lijst | `ABN AMRO Netherlands` | Ja |
| **Account No / IBAN** | Van factuur | `NL79ABNA0507559010` | Ja |
| **Party Type** | Type | `Supplier` | Ja |
| **Party** | Selecteer supplier | `PCheck B.V.` | Ja |
| **BIC / Swift Code** | Optioneel | `ABNANL2A` | Nee |
| **Is Default** | Standaard account? | Aanvinken | Ja |
| **Is Company Account** | Is dit een bedrijfsrekening? | Niet aanvinken | Ja |

5. Klik **Save**

**Check:** Het IBAN moet zichtbaar zijn onder de supplier.

---

## Stap 1: Purchase Invoices Aanmaken

**Doel:** Facturen registreren in ERPNext voordat je ze kunt betalen.

### Optie A: Via Claude (Geautomatiseerd)

Voor grote batches (10+ facturen):

1. Verzamel alle factuur-PDFs in een map
2. Upload naar Claude chat
3. Claude maakt Purchase Invoices aan volgens protocol
4. Claude hangt PDFs bij

**Voordelen:** Sneller voor grote batches, minder foutgevoelig, PDFs worden automatisch bijgehangen.

### Optie B: Handmatig in ERPNext

Voor enkele facturen:

1. Ga naar: **Buy > Purchase Invoice > New**
2. Vul in:

| Veld | Waarde | Waar te vinden | Verplicht |
|---|---|---|---|
| **Naming Series** | `.YY.3.-.#####` | Auto | Ja |
| **Supplier** | Selecteer leverancier | Van factuur | Ja |
| **Company** | `[BEDRIJFSNAAM]` | - | Ja |
| **Posting Date** | Factuurdatum | Van factuur | Ja |
| **Bill No** | Factuurnummer | Van factuur | Ja |
| **Bill Date** | Factuurdatum | Van factuur | Ja |
| **Due Date** | Betaal uiterlijk | Auto (via payment terms) | Ja |
| **Credit To** | `Creditors - [AFK]` | Auto | Ja |
| **Cost Center** | `Main - [AFK]` | Auto | Ja |

3. Items toevoegen — klik "+ Add Row" onder Items:

| Veld | Waarde | Voorbeeld |
|---|---|---|
| **Item Code** | Selecteer passend item | `Inkoop Softwarelicenties Abonnementen` |
| **Qty** | Aantal | `1` |
| **Rate** | Bedrag excl. BTW | `100.00` |
| **Expense Account** | Auto-ingevuld door item | `Software Expenses - [AFK]` |

**Items selectie tips:**

| Type Factuur | Item Code |
|---|---|
| Software/SaaS | `Inkoop Softwarelicenties Abonnementen` |
| Brandstof | `Overige kosten vervoersmiddelen` |
| Kantoorspullen | `Kantoorbenodigdheden` |
| Hardware | `Hardware kantoor` |
| Telefoon | `Inkoop Telefoonkosten` |
| Horeca | `Represent.kn.(75%)` |

4. BTW toevoegen — scroll naar "Taxes and Charges":
   - Klik "+ Add Row"
   - **Charge Type:** `On Net Total`
   - **Account Head:** `Netherlands VAT 21% - [AFK]`
   - **Rate:** `21`
   - Of selecteer BTW template: `Netherlands VAT 21% - [AFK]`

5. PDF bijvoegen — scroll naar **Attachments > Attach File** > Upload factuur PDF

6. **Save as Draft** — niet submitten zonder check!

7. Controleer:
   - Totaalbedrag klopt met factuur?
   - BTW bedrag klopt?
   - PDF bijgevoegd?
   - Supplier correct?
   - Bill No ingevuld?

8. **Submit** (= definitief maken)

Herhaal voor alle facturen die je wilt betalen.

---

## Stap 2: Payment Order Aanmaken

**Doel:** Meerdere facturen groeperen voor een SEPA bestand.

### Nieuwe Payment Order

1. Ga naar: **Accounts > Payment Order > New**
2. Vul in:

| Veld | Waarde |
|---|---|
| **Company** | `[BEDRIJFSNAAM]` |
| **Company Bank Account** | `[BEDRIJFSNAAM] - [BANK]` |
| **Payment Order Type** | `Payment Entry` |

3. Klik **Save**

### Purchase Invoices Toevoegen

1. Klik op **"Get from" > "Purchase Invoices"**
2. Filters instellen:

| Filter | Waarde | Toelichting |
|---|---|---|
| **Supplier** | Leeg laten | Haalt alle suppliers op |
| **From Posting Date** | Bijv. `2026-01-01` | Vanaf datum |
| **To Posting Date** | Bijv. `2026-03-20` | Tot datum |
| **Cost Center** | `Main - [AFK]` | Alleen [BEDRIJFSNAAM] |

3. Klik **"Get Purchase Invoices"**
4. Selecteer facturen:
   - Vink aan welke facturen je nu wilt betalen
   - Vink uit wat nog even kan wachten
5. Klik **"Get Items"**

**Check:** Alle geselecteerde facturen staan nu in de tabel met supplier naam, factuurnummer, bedrag en Bank Account (IBAN).

### Controleer Bank Accounts

**Alle regels moeten een Bank Account hebben!**

Controleer de kolom "Bank Account":

- **Goed:** `Bank Account: NL79ABNA0507559010 (PCheck B.V. - ABN AMRO)`
- **Fout:** `Bank Account: [leeg]`

Als een regel geen Bank Account heeft:

1. Noteer de Supplier naam
2. Ga naar **Buy > Supplier > [die supplier]**
3. Voeg Bank Account toe (zie Voorbereiding)
4. Kom terug naar Payment Order
5. Verwijder die regel
6. Klik opnieuw "Get from > Purchase Invoices"
7. Selecteer die factuur opnieuw

### Submit Payment Order

1. Controleer totaalbedrag bovenaan
2. Klik **Save**
3. Klik **Submit**

---

## Stap 3: SEPA XML Export

**Doel:** SEPA XML bestand genereren voor Rabobank upload.

### SEPA Export Knop

In de Payment Order (na Submit):

1. Klik op de **"Create"** knop (rechtsboven)
2. Selecteer: **"SEPA Export (Correct)"**

**Gebruik niet de standaard "SEPA XML File" optie!**

Reden: "SEPA Export (Correct)" is een custom script met pain.001.001.09 format. De standaard ERPNext export werkt niet altijd bij Rabobank.

### Download XML

Na klikken verschijnt een download link:

```
SEPA bestand aangemaakt!
Download: SEPA_PMO-00016a1b2c3d.xml
```

Klik op de download link — het bestand wordt gedownload.

### Controleer XML (Aanbevolen)

Open het XML bestand in Notepad of een browser en check:

1. **Header:**
```xml
<MsgId>PMO0001620260320124521</MsgId>
<NbOfTxs>3</NbOfTxs>
<CtrlSum>10114.72</CtrlSum>
```
Aantal transacties = aantal facturen? CtrlSum = totaalbedrag?

2. **Debtor (jullie rekening):**
```xml
<Dbtr>
  <Nm>[BEDRIJFSNAAM]</Nm>
</Dbtr>
<DbtrAcct>
  <Id>
    <IBAN>[IBAN HOOFDREKENING]</IBAN>
  </Id>
</DbtrAcct>
```
Juiste IBAN?

3. **Elke transactie:**
```xml
<Cdtr>
  <Nm>PCheck B.V.</Nm>
</Cdtr>
<CdtrAcct>
  <Id>
    <IBAN>NL79ABNA0507559010</IBAN>
  </Id>
</CdtrAcct>
<Amt>
  <InstdAmt Ccy="EUR">1500.00</InstdAmt>
</Amt>
<RmtInf>
  <Ustrd>263-04666</Ustrd>
</RmtInf>
```
IBAN is ingevuld (niet leeg)? Bedrag klopt? Factuurnummer in reference?

---

## Stap 4: Upload naar Rabobank

### Inloggen Rabobank

1. Ga naar: **https://bankieren.rabobank.nl**
2. Log in met je credentials
3. Ga naar: **Betalen > Betaalbestand uploaden**

### Upload SEPA XML

1. Klik **"Bestand kiezen"**
2. Selecteer het gedownloade XML bestand
3. Klik **"Uploaden"**

### Controleer Preview

Rabobank toont een preview. Check:
- Aantal transacties klopt?
- Totaalbedrag klopt?
- Alle leveranciers staan erbij?
- Uitvoeringsdatum correct?

### Accorderen

1. Als alles klopt: klik **"Accorderen"**
2. Voer tweede handtekening in (indien nodig)
3. Bevestig met 2FA (Rabo Scanner / App)

Betalingen worden uitgevoerd op de uitvoeringsdatum.

---

## Troubleshooting

### Leeg IBAN in SEPA XML

**Symptoom:** Leeg `<CdtrAcct><Id></Id></CdtrAcct>` element in de XML.

**Oorzaak:** Supplier heeft geen Bank Account in ERPNext.

**Oplossing:**
1. Check welke supplier: kijk naar de `<Nm>` tag erboven
2. Ga naar: **Buy > Supplier > [die supplier]**
3. Voeg Bank Account toe (zie Voorbereiding)
4. Ga terug naar Payment Order
5. Verwijder de regel met die supplier
6. Klik "Get from > Purchase Invoices" opnieuw
7. Selecteer de factuur opnieuw
8. Save & Submit
9. Genereer nieuwe SEPA XML

### SEPA Export Knop Niet Zichtbaar

**Symptoom:** "SEPA Export (Correct)" knop bestaat niet in het "Create" menu.

**Oorzaak:** Client Script is niet geinstalleerd of uitgeschakeld.

**Oplossing:**
1. Ga naar: **Setup > Customization > Client Script**
2. Zoek: "SEPA Export Button"
3. Check:
   - "Enabled" is aangevinkt
   - "View" bevat "Form"
   - Script code is aanwezig

Als Client Script niet bestaat:
1. Ga naar: **Setup > Customization > Client Script > New**
2. Vul in:
   - Name: `SEPA Export Button`
   - DocType: `Payment Order`
   - View: `Form`
   - Enabled: aanvinken
3. Plak de SEPA export button code
4. Save
5. Refresh de Payment Order pagina

### Rabobank Weigert SEPA Bestand

**Symptoom:** "Fout gevonden op regel X"

**Mogelijke oorzaken:**
1. **Verkeerd XML format** — Check of je "SEPA Export (Correct)" hebt gebruikt
2. **Leeg IBAN** — Zie sectie hierboven
3. **Ongeldige BIC** — Rabobank verwacht `<BICFI>` tag, niet `<BIC>`
4. **Verkeerde datum format** — Server Script gebruikt correct format

**Oplossing:**
1. Check het XML bestand visueel
2. Als alles correct lijkt: noteer exacte foutmelding
3. Vergelijk met een eerder werkend SEPA export bestand

### Server Script Error

**Symptoom:** "Internal Server Error" bij SEPA export.

**Oplossing:**
1. Check ERPNext Error Log: **Setup > Error Log**
2. Zoek naar recente errors met method `custom_sepa_export`
3. Check Server Script: **Setup > Automation > Server Script** > zoek "Custom SEPA Export"
4. Controleer of de code correct is en herstel indien nodig

---

## Checklist

### Voordat je Begint (Eenmalig per Leverancier)

- [ ] Supplier bestaat in ERPNext
- [ ] Supplier heeft Bank Account met IBAN
- [ ] Bank Account is ingesteld als "Is Default"
- [ ] IBAN is correct (check factuur)

### Per Payment Order

- [ ] Alle Purchase Invoices zijn Submitted
- [ ] PDFs zijn bijgevoegd aan Purchase Invoices
- [ ] Payment Order aangemaakt voor juiste Company
- [ ] Juiste Company Bank Account geselecteerd
- [ ] Purchase Invoices toegevoegd via "Get from"
- [ ] Alle regels hebben een Bank Account (IBAN zichtbaar)
- [ ] Totaalbedrag gecontroleerd
- [ ] Payment Order Submitted

### SEPA Export

- [ ] "SEPA Export (Correct)" knop gebruikt (niet standaard!)
- [ ] XML bestand gedownload
- [ ] XML gecontroleerd op lege IBANs
- [ ] Aantal transacties en totaalbedrag klopt

### Rabobank Upload

- [ ] Ingelogd op Rabobank
- [ ] XML bestand geupload
- [ ] Preview gecontroleerd
- [ ] Geaccordeerd met 2FA

---

## Snelle Referentie

### Vaste Waarden [BEDRIJFSNAAM]

| Veld | Waarde |
|---|---|
| **Company** | `[BEDRIJFSNAAM]` |
| **Naming Series** | `.YY.3.-.#####` |
| **Credit To** | `Creditors - [AFK]` |
| **Cost Center** | `Main - [AFK]` |
| **Bank Account** | `[BEDRIJFSNAAM] - [BANK]` |
| **IBAN** | `[IBAN HOOFDREKENING]` |
| **BIC** | `RABONL2U` |
| **BTW Template** | `Netherlands VAT 21% - [AFK]` |
| **Payment Terms** | `21 Dagen` |

### Veelgebruikte Items

| Type Factuur | Item Code |
|---|---|
| Software/SaaS | `Inkoop Softwarelicenties Abonnementen` |
| Brandstof | `Overige kosten vervoersmiddelen` |
| Auto onderhoud | `Autokosten` |
| Parkeren | `Parkeerkosten` |
| Kantoorspullen | `Kantoorbenodigdheden` |
| Hardware | `Hardware kantoor` |
| Telefoon | `Inkoop Telefoonkosten` |
| Horeca | `Represent.kn.(75%)` |

---

## Tips & Best Practices

### Wel doen

1. Check altijd of supplier een Bank Account heeft voordat je de Purchase Invoice submit
2. Hang PDFs bij aan Purchase Invoices (helpt bij controle achteraf)
3. Gebruik "SEPA Export (Correct)" knop (niet standaard ERPNext export)
4. Controleer XML bestand visueel voordat je upload (vooral IBANs)
5. Maak Payment Orders per week/maand (niet te grote batches)
6. Gebruik duidelijke Bill No (factuurnummers) voor traceerbaarheid

### Niet doen

1. Niet de standaard ERPNext SEPA export gebruiken (werkt niet bij Rabobank)
2. Geen Purchase Invoices submitten zonder PDF
3. Geen Payment Order submitten zonder Bank Accounts te checken
4. Niet vergeten om is_paid aan te vinken als factuur al betaald is
5. Geen suppliers aanmaken zonder Tax Category

### Onderhoud

**Maandelijks:**
- Check of alle suppliers nog correcte Bank Accounts hebben
- Update IBAN als leverancier van bank wisselt
- Clean up oude Draft Purchase Invoices

**Per kwartaal:**
- Check of Server Script nog up-to-date is
- Test SEPA export met klein bedrag

---

*Versie 1.0 — 24 maart 2026*
