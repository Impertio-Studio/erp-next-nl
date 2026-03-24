---
titel: Geavanceerde Bankreconciliatie
bedrijf: [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Expert
doelgroep: Boekhouder
---

# Geavanceerde Bankreconciliatie

## Overzicht

Bankreconciliatie is het proces waarbij u uw bankafschriften afstemt met de boekingen in ERPNext. Dit is een van de belangrijkste controleprocessen in de boekhouding. Dit werkboek behandelt zowel de standaard als de geavanceerde methoden.

---

## Waarom Bankreconciliatie?

- **Fouten opsporen** — Verkeerde bedragen, ontbrekende boekingen
- **Volledigheid** — Alle transacties zijn verwerkt
- **Fraude-detectie** — Ongeautoriseerde transacties herkennen
- **Accountantscontrole** — Bewijs dat de administratie klopt met de bank

---

## Methode 1: Handmatige Reconciliatie

### Stap 1: Download Bankafschrift

Download het bankafschrift bij uw bank:

| Bank | Formaat | Pad |
|---|---|---|
| **Rabobank** | MT940 of CSV | Rabo Online > Afschriften > Download |
| **ABN AMRO** | MT940 of CAMT.053 | Internet Bankieren > Afschriften |
| **ING** | MT940 of CSV | Mijn ING > Afschriften > Downloaden |

**Aanbevolen formaat:** MT940 — dit is het meest gestandaardiseerde formaat en werkt het beste met ERPNext.

### Stap 2: Import in ERPNext

1. Ga naar **Accounts > Bank Statement Import**
2. Selecteer:
   - **Bank Account**: `[BEDRIJFSNAAM] - [BANK]`
   - **Import File**: Upload het MT940/CSV bestand
3. Klik **Import**
4. ERPNext maakt **Bank Transactions** aan voor elke regel op het afschrift

### Stap 3: Matching

1. Ga naar **Accounts > Bank Reconciliation Tool**
2. Selecteer:
   - **Bank Account**: `[BEDRIJFSNAAM] - [BANK]`
   - **From Date** en **To Date**: de periode van het afschrift
3. ERPNext toont:
   - Links: onafgestemde banktransacties
   - Rechts: mogelijke matches uit de boekhouding

4. Per transactie:
   - **Auto-match**: ERPNext probeert automatisch te matchen op bedrag en referentie
   - **Handmatig matchen**: selecteer de juiste boeking
   - **Nieuwe boeking**: als er geen match is, maak een nieuwe Payment Entry of Journal Entry

### Stap 4: Controleer

Na het reconcilieren:
- Het banksaldo in ERPNext moet gelijk zijn aan het saldo op het bankafschrift
- Alle transacties moeten zijn afgestemd of verklaard

---

## Methode 2: Geavanceerde Reconciliatie (Custom Script)

[BEDRIJFSNAAM] gebruikt een custom Server Script ("Reconcile Bank Transaction") voor geavanceerde reconciliatie.

### Hoe Werkt Het?

Het script biedt extra functionaliteit bovenop de standaard reconciliatie:

1. **Slimme matching** — Matcht niet alleen op exact bedrag maar ook op:
   - Gedeeltelijke matches (meerdere boekingen voor een banktransactie)
   - Referentienummer matching
   - Leveranciers-/klantnaam matching
2. **Bulk reconciliatie** — Meerdere transacties in een keer afstemmen
3. **Automatische categorie-toewijzing** — Herkent terugkerende transacties

### Gebruik

1. Ga naar de **Bank Transaction** lijst
2. Selecteer onafgestemde transacties
3. Gebruik de reconciliatie-knop
4. Het script probeert automatisch de beste match te vinden

---

## Veelvoorkomende Scenario's

### Scenario 1: Exacte Match

**Bank:** EUR 1.210,00 ontvangen van Domera
**ERPNext:** Sales Invoice #SI-2026-0042, bedrag EUR 1.210,00

Actie: Automatisch gematcht. Bevestig de match.

### Scenario 2: Gedeeltelijke Betaling

**Bank:** EUR 605,00 ontvangen van Domera
**ERPNext:** Sales Invoice #SI-2026-0042, bedrag EUR 1.210,00

Actie:
1. Maak een Payment Entry voor EUR 605,00
2. De Sales Invoice status wordt `Partly Paid`
3. Het openstaande bedrag wordt EUR 605,00

### Scenario 3: Samengevoegde Betaling

**Bank:** EUR 3.630,00 ontvangen van Domera
**ERPNext:** Drie facturen: EUR 1.210,00 + EUR 1.210,00 + EUR 1.210,00

Actie:
1. Maak een Payment Entry voor EUR 3.630,00
2. Koppel alle drie facturen in de Payment References tabel
3. Alle drie facturen worden `Paid`

### Scenario 4: Bankkosten

**Bank:** EUR -5,50 afgeschreven (bankkosten)
**ERPNext:** Geen boeking

Actie:
1. Maak een Journal Entry:
   - Debit: `Bank and Transaction Costs - [AFK]` (EUR 5,50)
   - Credit: `[IBAN HOOFDREKENING] - [AFK]` (EUR 5,50)
2. Match met de banktransactie

### Scenario 5: Salarisbetaling

**Bank:** EUR -2.800,00 afgeschreven (netto salaris)
**ERPNext:** Salary Slip voor de medewerker

Actie:
1. Als de Salary Slip is submitted, maak een Payment Entry
2. Paid From: `[IBAN HOOFDREKENING] - [AFK]`
3. Paid To: `Net Salary Payable - [AFK]`
4. Match met de banktransactie

### Scenario 6: Overboeking Eigen Rekeningen

**Bank:** EUR -10.000,00 van betaalrekening naar spaarrekening
**Bank:** EUR +10.000,00 op spaarrekening

Actie:
1. Maak een Journal Entry (Bank Entry):
   - Debit: `[IBAN SPAARREKENING 1] - [AFK]` (spaarrekening)
   - Credit: `[IBAN HOOFDREKENING] - [AFK]` (betaalrekening)
2. Match beide banktransacties met deze Journal Entry

### Scenario 7: Creditcard Afschrijving

**Bank:** EUR -1.250,00 afgeschreven (Visa maandafschrijving)
**ERPNext:** Diverse creditcardboekingen op `Credit Card Payable - [VISA KAARTNUMMER] - [AFK]`

Actie:
1. Maak een Journal Entry:
   - Debit: `Credit Card Payable - [VISA KAARTNUMMER] - [AFK]` (EUR 1.250,00)
   - Credit: `[IBAN HOOFDREKENING] - [AFK]` (EUR 1.250,00)
2. Match met de banktransactie
3. Controleer of het saldo op de creditcardrekening klopt met het Visa-overzicht

### Scenario 8: BTW-betaling aan Belastingdienst

**Bank:** EUR -4.250,00 afgeschreven (BTW-aangifte Q1)
**ERPNext:** Geen boeking

Actie:
1. Maak een Journal Entry:
   - Debit: `VAT 21% - [AFK]` (af te dragen BTW, bijv. EUR 5.000,00)
   - Credit: `Input VAT 21% - [AFK]` (te vorderen BTW, bijv. EUR 750,00)
   - Credit: `[IBAN HOOFDREKENING] - [AFK]` (netto te betalen, EUR 4.250,00)
2. Match met de banktransactie

---

## CAMT.053 vs. MT940

### MT940 (Legacy)

- Ouder formaat, breed ondersteund
- Eenvoudige structuur
- Beperkte informatie per transactie
- Wordt langzaam uitgefaseerd

### CAMT.053 (Nieuw)

- XML-gebaseerd, rijkere data
- Meer details per transactie (end-to-end referentie, mandaat-ID)
- Beter geschikt voor automatische matching
- Toekomstbestendig

**Aanbeveling:** Gebruik CAMT.053 als uw bank dit ondersteunt. De rijkere data maakt automatische matching betrouwbaarder.

---

## Frequentie

| Frequentie | Aanbevolen voor | Voordeel |
|---|---|---|
| **Dagelijks** | Bedrijven met veel transacties | Direct inzicht, weinig achterstand |
| **Wekelijks** | MKB met gemiddeld volume | Goede balans tussen inspanning en controle |
| **Maandelijks** | Kleine bedrijven, weinig transacties | Minimale inspanning |
| **Per kwartaal** | Niet aanbevolen | Te grote achterstand, fouten moeilijk te traceren |

**[BEDRIJFSNAAM]:** Wekelijks wordt aanbevolen, minimaal maandelijks.

---

## Reconciliatie Checklist

### Per Sessie

- [ ] Bankafschrift gedownload (MT940 of CAMT.053)
- [ ] Geimporteerd in ERPNext
- [ ] Alle transacties gematcht of nieuwe boekingen aangemaakt
- [ ] Eindsaldo ERPNext = eindsaldo bankafschrift
- [ ] Geen onverklaarde verschillen

### Per Maand

- [ ] Alle bankrekeningen gereconcilieerd (betaalrekening + spaarrekeningen)
- [ ] Creditcardrekening afgestemd met Visa-overzicht
- [ ] Kruisposten (overboekingen tussen eigen rekeningen) correct geboekt
- [ ] BTW-betaling aan Belastingdienst correct geboekt (indien van toepassing)

### Per Kwartaal

- [ ] Banksaldo in ERPNext vergeleken met bankafschrift op kwartaaldatum
- [ ] Openstaande (onafgestemde) banktransacties onderzocht en opgelost
- [ ] Reconciliatie-rapport opgeslagen voor accountant

---

## Problemen Oplossen

### Verschil Tussen ERPNext en Bank

**Mogelijke oorzaken:**
1. **Ontbrekende boeking** — Transactie op bank maar niet in ERPNext
2. **Dubbele boeking** — Transactie twee keer geboekt in ERPNext
3. **Verkeerd bedrag** — Typefout bij handmatige boeking
4. **Timing** — Transactie valt net in de volgende periode

**Oplossing:**
1. Sorteer transacties op datum en vergelijk een voor een
2. Check het General Ledger voor de bankrekening
3. Gebruik het Bank Reconciliation Statement rapport

### Niet-matchende Referenties

Banktransacties bevatten vaak andere omschrijvingen dan in ERPNext. Tips:
- Stel standaard referenties in bij betaalwijzen
- Gebruik het factuurnummer als omschrijving bij betalingen
- Train het reconciliatiescript op veelvoorkomende patronen

---

## Rapporten

### Bank Reconciliation Statement

Ga naar **Accounts > Bank Reconciliation Statement**. Dit rapport toont:
- Banksaldo volgens ERPNext
- Banksaldo volgens bankafschrift
- Verschil en oorzaken (boekingen niet op afschrift, afschrift niet in ERPNext)

### Bank Clearance Summary

Ga naar **Accounts > Bank Clearance Summary**. Overzicht van:
- Gecleared (afgestemde) transacties
- Ongecleared (nog af te stemmen) transacties
- Per periode

---

*Versie 1.0 — 24 maart 2026*
