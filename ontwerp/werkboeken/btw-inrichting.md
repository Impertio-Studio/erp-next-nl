---
titel: BTW-inrichting voor Nederland
bedrijf: [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Gevorderd
doelgroep: Boekhouder, Ondernemer
---

# BTW-inrichting voor Nederland

## Overzicht

Dit werkboek beschrijft hoe de Nederlandse BTW-structuur is ingericht in ERPNext voor [BEDRIJFSNAAM] Nederland kent meerdere BTW-tarieven en speciale regelingen die correct moeten worden geconfigureerd.

---

## Nederlandse BTW-tarieven

| Tarief | Percentage | Toepassing |
|---|---|---|
| **Hoog tarief** | 21% | Standaard voor de meeste goederen en diensten |
| **Laag tarief** | 9% | Voedingsmiddelen, water, medicijnen, boeken, cultuur |
| **Nultarief** | 0% | Intracommunautaire leveringen, export buiten EU |
| **Vrijgesteld** | — | Medische diensten, onderwijs, financiele diensten |
| **Verlegd** | 21% (verlegd) | Onderaanneming in de bouw, bepaalde B2B-diensten |

---

## BTW-rekeningen in het Grootboek

De volgende grootboekrekeningen zijn ingericht onder **Duties and Taxes - [AFK]**:

| Rekening | Type | Gebruik |
|---|---|---|
| **VAT 21% - 3** | Liability / Tax | Standaard BTW 21% — af te dragen |
| **VAT 9% - 3** | Liability / Tax | Laag BTW-tarief 9% |
| **VAT 6% - 3** | Liability / Tax | Oud laag tarief (historisch, voor 2019) |
| **VAT 0% - 3** | Liability / Tax | Nultarief |
| **VAT Abroad 0% - 3** | Liability | Buitenlandse leveringen 0% |
| **VAT 21% Shifted - 3** | Liability | BTW verlegd 21% |
| **Input VAT 21% - 3** | Asset (Tax Assets) | Voorbelasting 21% — te vorderen |
| **Non-Deductible VAT Expense - 3** | Expense | Niet-aftrekbare BTW |
| **Tax Payable - 3** | Liability | Verschuldigde belasting (overig) |

---

## Inkoopbelasting-templates (Purchase Taxes)

De volgende templates zijn beschikbaar voor inkoopfacturen:

### 1. Netherlands VAT 21% (added) — Standaard

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Netherlands VAT 21% - [AFK]` |
| **Titel** | Netherlands VAT 21% added |
| **Standaard** | Ja (is_default) |
| **Tax Category** | `EU NL Full tax` |
| **Toepassing** | Alle standaard Nederlandse inkopen met 21% BTW |

**Wanneer gebruiken:** Voor de meeste binnenlandse facturen van Nederlandse leveranciers.

### 2. Netherlands VAT 21% (included)

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Netherlands VAT 21% included - [AFK]` |
| **Standaard** | Nee |
| **Toepassing** | Facturen waar BTW al in het bedrag is inbegrepen |

**Wanneer gebruiken:** Wanneer een leverancier bedragen inclusief BTW factureert (bijv. bonnetjes, kassabonnen).

### 3. Netherlands VAT 21% shifted (Verlegd)

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Netherlands VAT shifted 21% - [AFK]` |
| **Standaard** | Nee |
| **Toepassing** | BTW-verleggingsregeling |

**Wanneer gebruiken:** Bij onderaanneming in de bouw of andere diensten waarbij de BTW is verlegd naar de afnemer. De factuur van de leverancier vermeldt "BTW verlegd".

### 4. Netherlands VAT 9%

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Netherlands VAT 9% - [AFK]` |
| **Standaard** | Nee |
| **Toepassing** | Laag BTW-tarief |

**Wanneer gebruiken:** Voor inkopen die onder het lage tarief vallen (voedingsmiddelen, boeken, etc.).

### 5. Netherlands VAT 0%

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Netherlands VAT 0% - [AFK]` |
| **Standaard** | Nee |
| **Toepassing** | Nultarief binnenland |

**Wanneer gebruiken:** Voor BTW-vrijgestelde inkopen binnen Nederland.

### 6. Netherlands VAT Abroad 0%

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Netherlands VAT Abroad 0% - [AFK]` |
| **Standaard** | Nee |
| **Toepassing** | Buitenlandse leveranciers |

**Wanneer gebruiken:** Bij inkopen van leveranciers buiten Nederland waar geen Nederlandse BTW van toepassing is.

### 7. Abroad VAT 0%

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Abroad VAT - 0% - [AFK]` |
| **Tax Category** | `Abroad taxes 0%` |
| **Toepassing** | Buitenlandse leveringen buiten EU |

**Wanneer gebruiken:** Bij inkopen van buiten de EU.

### 8. NL Reverse Charge 21% VAT

| Eigenschap | Waarde |
|---|---|
| **Naam** | `NL Reverse Charge 21%VAT - [AFK]` |
| **Standaard** | Nee |
| **Toepassing** | Verleggingsregeling met tegenrekening |

**Wanneer gebruiken:** Bij intracommunautaire verwervingen (ICL) of diensten van EU-leveranciers. Hierbij wordt 21% BTW verlegd en tegelijk als voorbelasting opgevoerd.

### 9. Netherlands VAT 21% (deducted) — Uitgeschakeld

| Eigenschap | Waarde |
|---|---|
| **Naam** | `Netherlands VAT 21% deducted - [AFK]` |
| **Status** | Uitgeschakeld (disabled) |

Dit template is niet meer in gebruik.

---

## Tax Categories

Tax Categories worden gebruikt om automatisch het juiste BTW-template te selecteren op basis van de leverancier:

| Tax Category | Toepassing | Koppeling |
|---|---|---|
| **EU NL Full tax** | Standaard Nederlandse leveranciers | Netherlands VAT 21% (added) |
| **Abroad taxes 0%** | Leveranciers buiten EU | Abroad VAT - 0% |

### Instellen bij Supplier

Bij het aanmaken van een leverancier, stel de Tax Category in:

- **Nederlandse leverancier** → `EU NL Full tax`
- **EU-leverancier (diensten)** → gebruik Reverse Charge template
- **Leverancier buiten EU** → `Abroad taxes 0%`

---

## BTW-aangifte

### Kwartaalaangifte

De meeste Nederlandse MKB-bedrijven doen per kwartaal BTW-aangifte bij de Belastingdienst. De aangifte bevat:

| Rubriek | Bron in ERPNext |
|---|---|
| **1a. Leveringen/diensten belast met hoog tarief** | Sales Invoices met 21% BTW |
| **1b. Leveringen/diensten belast met laag tarief** | Sales Invoices met 9% BTW |
| **1e. Leveringen/diensten belast met 0% of niet bij u belast** | Sales Invoices met 0% |
| **4a. Leveringen/diensten uit landen binnen de EU** | Purchase Invoices met Reverse Charge |
| **4b. Leveringen/diensten uit landen buiten de EU** | Purchase Invoices met Abroad 0% |
| **5b. Voorbelasting** | Alle inkoop-BTW (Input VAT) |

### Rapportage in ERPNext

Gebruik de volgende rapporten voor de BTW-aangifte:

1. **Accounts > Tax Detail** — Overzicht van alle BTW-boekingen per periode
2. **Accounts > General Ledger** — Filter op BTW-rekeningen voor detail
3. **Accounts > Trial Balance** — Saldo van BTW-rekeningen per periode

---

## Veelvoorkomende Scenario's

### Scenario 1: Standaard Nederlandse Factuur (21% BTW)

1. Maak Purchase Invoice aan
2. Template: `Netherlands VAT 21% - [AFK]` (wordt automatisch geselecteerd via Tax Category)
3. BTW wordt berekend op het nettobedrag

### Scenario 2: Factuur met BTW Inbegrepen

1. Maak Purchase Invoice aan
2. Template: `Netherlands VAT 21% included - [AFK]`
3. ERPNext berekent het nettobedrag terug uit het brutobedrag

### Scenario 3: Onderaannemer (BTW Verlegd)

1. Maak Purchase Invoice aan
2. Template: `Netherlands VAT shifted 21% - [AFK]`
3. Geen BTW wordt betaald aan de leverancier
4. BTW wordt verlegd naar de eigen aangifte

### Scenario 4: EU-leverancier (Intracommunautair)

1. Maak Purchase Invoice aan
2. Template: `NL Reverse Charge 21%VAT - [AFK]`
3. 21% BTW wordt tegelijk als schuld en als voorbelasting geboekt
4. Per saldo geen effect op de kas, wel op de aangifte (rubriek 4a)

### Scenario 5: Leverancier buiten EU

1. Maak Purchase Invoice aan
2. Template: `Abroad VAT - 0% - [AFK]`
3. Geen BTW van toepassing

---

## Tips

1. **Controleer altijd de Tax Category** van een leverancier voordat je een factuur boekt. Een verkeerde category leidt tot een verkeerd BTW-template.

2. **BTW-verlegd facturen** moeten de vermelding "BTW verlegd" bevatten op de factuur van de leverancier. Controleer dit.

3. **Kwartaalcontrole** — Vergelijk elk kwartaal de BTW-rekeningen in ERPNext met de ingediende BTW-aangifte.

4. **Laag tarief** — Let op dat het lage tarief per 1 januari 2019 is gewijzigd van 6% naar 9%. Voor historische boekingen is de `VAT 6% - [AFK]` rekening beschikbaar.

---

*Versie 1.0 — 24 maart 2026*
