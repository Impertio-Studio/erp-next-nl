---
titel: Bankkoppelingen en Betaalrekeningen
bedrijf: [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Gevorderd
doelgroep: Boekhouder, Ondernemer
---

# Bankkoppelingen en Betaalrekeningen

## Overzicht

Dit werkboek beschrijft de configuratie van bankrekeningen in ERPNext voor een Nederlands bedrijf. Alle bankrekeningen moeten correct zijn ingericht voor:

- Dagelijkse boekhouding (ontvangsten en betalingen)
- SEPA-betalingen via Payment Orders
- Bankafstemming (Bank Reconciliation)
- Creditcardadministratie

---

## Bankrekeningen — [BEDRIJFSNAAM]

### 1. Hoofdrekening — Rabobank

| Veld | Waarde |
|---|---|
| **Account Name** | [BEDRIJFSNAAM] [BANK] |
| **Bank** | Rabobank Netherlands |
| **IBAN** | [IBAN HOOFDREKENING] |
| **BIC** | RABONL2U |
| **Is Default** | Ja |
| **Is Company Account** | Ja |
| **Gekoppeld Grootboek** | `[IBAN HOOFDREKENING] - [IBAN HOOFDREKENING] - [AFK]` |
| **Type** | Betaalrekening |

Dit is de primaire betaalrekening. Alle dagelijkse transacties, SEPA-betalingen en inkomende betalingen lopen via deze rekening.

### 2. Spaarrekening 1

| Veld | Waarde |
|---|---|
| **Account Name** | [BEDRIJFSNAAM] Savings 1 |
| **Bank** | Rabobank Netherlands |
| **IBAN** | [IBAN SPAARREKENING 1] |
| **Type** | Spaarrekening (Saving Account) |
| **Is Company Account** | Ja |
| **Gekoppeld Grootboek** | `[IBAN SPAARREKENING 1] - [BEDRIJFSNAAM] Savings 1 - [AFK]` |

### 3. Spaarrekening 2

| Veld | Waarde |
|---|---|
| **Account Name** | [BEDRIJFSNAAM] Savings 2 |
| **Bank** | Rabobank Netherlands |
| **IBAN** | [IBAN SPAARREKENING 2] |
| **Type** | Spaarrekening (Saving Account) |
| **Is Company Account** | Ja |
| **Gekoppeld Grootboek** | `[IBAN SPAARREKENING 2] - [BEDRIJFSNAAM] Savings 2 - [AFK]` |

### 4. Creditcard — Visa

| Veld | Waarde |
|---|---|
| **Account Name** | [BEDRIJFSNAAM] Credit Card VISA |
| **Bank** | Rabobank Netherlands |
| **Type** | Credit Account |
| **Is Company Account** | Ja |
| **Gekoppeld Grootboek** | `Credit Card Payable - [VISA KAARTNUMMER] - [AFK]` |

De creditcard is gekoppeld aan een aparte liability-rekening. Creditcardbetalingen worden eerst geboekt op deze rekening en bij de maandelijkse afschrijving afgestemd met de hoofdrekening.

---

## Grootboekrekeningen voor Bank

De bankrekeningen zijn gekoppeld aan de volgende grootboekrekeningen:

| Grootboekrekening | Root Type | Account Type | Parent |
|---|---|---|---|
| `[IBAN HOOFDREKENING] - [AFK]` | Asset | Bank | Bank Accounts - [AFK] |
| `[IBAN SPAARREKENING 1] - [AFK]` | Asset | Bank | Current Assets - [AFK] |
| `[IBAN SPAARREKENING 2] - [AFK]` | Asset | Bank | Current Assets - [AFK] |
| `Credit Card Payable - [VISA KAARTNUMMER] - [AFK]` | Liability | Bank | Current Liabilities - [AFK] |
| `Cash - [AFK]` | Asset | Cash | Cash In Hand - [AFK] |

---

## Bankrekening Aanmaken — Stap voor Stap

### Voor een bedrijfsbankrekening

1. **Maak eerst een grootboekrekening aan:**
   - Ga naar **Accounts > Chart of Accounts**
   - Navigeer naar **Application of Funds > Current Assets > Bank Accounts**
   - Klik **Add Child**
   - Account Name: het IBAN-nummer
   - Account Type: `Bank`
   - Save

2. **Maak dan de Bank Account aan:**
   - Ga naar **Accounts > Bank Account > New**
   - Vul in:

| Veld | Waarde | Verplicht |
|---|---|---|
| **Account Name** | Beschrijvende naam | Ja |
| **Bank** | Selecteer bank | Ja |
| **Account No / IBAN** | IBAN-nummer | Ja |
| **Is Company Account** | Aanvinken | Ja |
| **Company** | `[BEDRIJFSNAAM]` | Ja |
| **Account** | Selecteer de grootboekrekening | Ja |
| **Is Default** | Aanvinken voor hoofdrekening | — |

3. **Stel de bankrekening in als standaard bij het bedrijf:**
   - Ga naar **Setup > Company > [BEDRIJFSNAAM]**
   - Veld "Default Bank Account" → selecteer de nieuwe bankrekening

### Voor een leveranciersbankrekening

Zie het werkboek [Payment Order & SEPA Export Workflow](payment-order-sepa-workflow.md), sectie "Voorbereiding: Supplier Setup".

---

## Nederlandse Banken in ERPNext

De volgende Nederlandse banken worden veel gebruikt. Zorg dat deze zijn aangemaakt in **Accounts > Bank**:

| Bank | BIC |
|---|---|
| Rabobank Netherlands | RABONL2U |
| ABN AMRO Netherlands | ABNANL2A |
| ING Netherlands | INGBNL2A |
| SNS Bank | SNSBNL2A |
| Triodos Bank | TRIONL2U |
| Bunq | BUNQNL2A |
| Knab | KNABNL2H |
| ASN Bank | ASNBNL21 |
| RegioBank | RBRBNL21 |

---

## Bankafstemming (Bank Reconciliation)

### Werkwijze

1. **Download bankafschrift** van Rabobank (MT940 of CSV formaat)
2. Ga naar **Accounts > Bank Reconciliation**
3. Selecteer **Bank Account**: `[BEDRIJFSNAAM] - [BANK]`
4. Upload het bankafschrift
5. ERPNext matcht automatisch transacties met bestaande boekingen
6. Controleer en bevestig de matches
7. Maak handmatig boekingen aan voor onbekende transacties

### Tips voor Bankafstemming

1. **Regelmatig afstemmen** — Bij voorkeur wekelijks, minimaal maandelijks
2. **Rabobank formaat** — Gebruik het MT940-formaat voor de beste compatibiliteit
3. **Naamgeving** — Zorg dat leveranciers- en klantnamen in ERPNext overeenkomen met de namen op bankafschriften
4. **Spaarrekeningen** — Stem ook spaarrekeningen af, vooral bij overboekingen tussen rekeningen

---

## Overboekingen Tussen Rekeningen

Bij overboekingen tussen eigen rekeningen (bijv. van betaalrekening naar spaarrekening):

1. Ga naar **Accounts > Journal Entry > New**
2. Vul in:

| Veld | Waarde |
|---|---|
| **Entry Type** | Bank Entry |
| **Posting Date** | Datum van overboeking |

3. Voeg twee regels toe:

| Account | Debit | Credit |
|---|---|---|
| Spaarrekening (bijv. `[IBAN SPAARREKENING 1]... - [AFK]`) | Bedrag | — |
| Hoofdrekening (`[IBAN HOOFDREKENING] - [AFK]`) | — | Bedrag |

4. Save en Submit

---

## Creditcard Workflow

### Boeken van creditcarduitgaven

1. **Bij aankoop:** Boek de uitgave op de creditcardrekening
   - Maak een Payment Entry of Purchase Invoice
   - Mode of Payment: `Credit Card`
   - Paid From: `Credit Card Payable - [VISA KAARTNUMMER] - [AFK]`

2. **Bij maandelijkse afschrijving:** Boek de overboeking
   - Journal Entry: Bank Entry
   - Debit: `Credit Card Payable - [VISA KAARTNUMMER] - [AFK]` (verlaagt de schuld)
   - Credit: `[IBAN HOOFDREKENING] - [AFK]` (bedrag gaat van bankrekening)

### Tips Creditcard

- Bewaar altijd de bonnetjes/facturen bij creditcarduitgaven
- Stem maandelijks het creditcard-saldo af met het Visa-overzicht
- De creditcardrekening staat onder Liabilities omdat het een schuld is aan de kaartuitgever

---

## Veelgestelde Vragen

### Waarom staat de creditcard onder Liabilities?

Een creditcard is in feite een kortlopende lening. Het saldo vertegenwoordigt een schuld aan de kaartuitgever (Rabobank/Visa). Bij betaling van het maandoverzicht wordt deze schuld afgelost.

### Kan ik meerdere betaalrekeningen gebruiken?

Ja. Je kunt meerdere bankrekeningen aanmaken, elk met een eigen grootboekrekening. Stel de meest gebruikte in als default. Bij Payment Orders selecteer je expliciet welke rekening je wilt gebruiken.

### Hoe verwerk ik buitenlandse bankkosten?

Boek bankkosten op de rekening `Bank and Transaction Costs - [AFK]`. Bij valutaverschillen gebruik je `Exchange Gain/Loss - [AFK]`.

---

*Versie 1.0 — 24 maart 2026*
