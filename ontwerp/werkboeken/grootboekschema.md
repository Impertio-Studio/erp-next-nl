---
titel: Grootboekschema — Nederlandse Inrichting
bedrijf: [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Gevorderd
doelgroep: Boekhouder, IT
---

# Grootboekschema — Nederlandse Inrichting

## Overzicht

Dit werkboek beschrijft het grootboekschema (Chart of Accounts) zoals ingericht voor [BEDRIJFSNAAM]. Het schema is gebaseerd op het ERPNext Standard Template en uitgebreid met Nederlandse rekeningen voor BTW, salarisadministratie en bedrijfsspecifieke kosten.

Het schema bevat **148 rekeningen** verdeeld over 5 hoofdcategorieen.

---

## Structuur

Het grootboekschema is opgebouwd uit vijf root-types:

| Root Type | Beschrijving | Balans/W&V |
|---|---|---|
| **Asset** | Bezittingen (activa) | Balans |
| **Liability** | Schulden (passiva) | Balans |
| **Equity** | Eigen vermogen | Balans |
| **Income** | Opbrengsten | Winst & Verlies |
| **Expense** | Kosten | Winst & Verlies |

---

## 1. Activa (Assets)

### Application of Funds (Assets) — Root

#### Current Assets (Vlottende Activa)

**Bankrekeningen:**
| Rekening | Nummer | Type |
|---|---|---|
| [IBAN HOOFDREKENING] | [IBAN HOOFDREKENING] | Bank (hoofdrekening) |
| [BEDRIJFSNAAM] Savings 1 | [IBAN SPAARREKENING 1] | Bank (spaarrekening) |
| [BEDRIJFSNAAM] Savings 2 | [IBAN SPAARREKENING 2] | Bank (spaarrekening) |
| Cash | — | Cash |
| Subvention | — | Bank |

**Debiteuren:**
| Rekening | Nummer | Type |
|---|---|---|
| Debtors | 12000 | Receivable |
| Domera | — | — |
| Loan receivable - Domera | — | Receivable |
| Vroughindeweij Industries deposit | — | Receivable |

**Overige vlottende activa:**
| Rekening | Gebruik |
|---|---|
| Cross-postings | Kruisposten |
| Payment differences | Betalingsverschillen |
| Stock In Hand | Voorraad |
| Input VAT 21% | Voorbelasting BTW 21% |

**Voorraden (Inventory):**
| Rekening | Gebruik |
|---|---|
| Coin Inventory | Muntenvoorraad |
| Coins | Munten |

#### Fixed Assets (Vaste Activa)

| Rekening | Type |
|---|---|
| Buildings | Gebouwen |
| Capital Equipments | Kapitaalgoederen |
| Electronic Equipments | Elektronica |
| Furnitures and Fixtures | Meubilair |
| Hardware | Hardware |
| Office Equipments | Kantoorapparatuur |
| Plants and Machineries | Machines |
| Softwares | Software |
| Accumulated Depreciation | Cumulatieve afschrijvingen |
| CWIP Account | Onderhanden werk (Capital Work in Progress) |

#### Investments (Beleggingen)

| Rekening | Gebruik |
|---|---|
| Other Company Investments | Deelnemingen in andere bedrijven |

---

## 2. Passiva (Liabilities)

### Source of Funds (Liabilities) — Root

#### Current Liabilities (Kortlopende Schulden)

**Crediteuren:**
| Rekening | Type | Gebruik |
|---|---|---|
| Creditors | Payable | Standaard crediteurenrekening |
| Creditors Abroad $ | Payable | Buitenlandse crediteuren (USD) |
| Payroll Payable | — | Loonverplichtingen |

**BTW-rekeningen (Duties and Taxes):**
| Rekening | Type | Tarief |
|---|---|---|
| VAT 21% | Tax | Standaard BTW |
| VAT 9% | Tax | Laag tarief |
| VAT 6% | Tax | Oud laag tarief (pre-2019) |
| VAT 0% | Tax | Nultarief |
| VAT Abroad 0% | — | Buitenland nultarief |
| VAT 21% Shifted | — | Verlegd |
| Tax Payable | — | Overige belasting |

**Salarisverplichtingen:**
| Rekening | Gebruik |
|---|---|
| Net Salary Payable | Netto salaris te betalen |
| Holiday Allowance Payable | Vakantiegeld te betalen |
| Wage Taxes | Loonbelasting af te dragen |
| Social Security Fund | Sociale premies |
| Tax reserves | Belastingreserves |
| Work Related Costs | Werkkostenregeling (WKR) |

**Creditcard:**
| Rekening | Gebruik |
|---|---|
| Credit Card Payable - [VISA KAARTNUMMER] | Creditcardschuld |
| CreditCard [VISA KAARTNUMMER] | Creditcard (oud) |

**Leningen:**
| Rekening | Gebruik |
|---|---|
| Loan Payable Rabo | Rabobank lening |
| Bank Overdraft Account | Rekening-courant |
| Secured Loans | Gedekte leningen |
| Unsecured Loans | Ongedekte leningen |

---

## 3. Eigen Vermogen (Equity)

| Rekening | Type | Gebruik |
|---|---|---|
| Capital Stock | Equity | Ingebracht kapitaal |
| Opening Balance Equity | Equity | Openingsbalans |
| Retained Earnings | Equity | Ingehouden winsten |
| Dividends Paid | Equity | Uitgekeerd dividend |
| Revaluation Surplus | Equity | Herwaarderingsreserve |

---

## 4. Opbrengsten (Income)

### Direct Income

| Rekening | Gebruik |
|---|---|
| Sales | Omzet uit verkopen |
| Service | Omzet uit dienstverlening |
| 2024 Income | Inkomsten 2024 (historisch) |
| 2025 Income | Inkomsten 2025 |

### Indirect Income

| Rekening | Gebruik |
|---|---|
| Aanmaningskosten opbrengst | Opbrengst uit aanmaningen |
| Profit distribution | Winstverdeling |

---

## 5. Kosten (Expenses)

### Directe Kosten

| Rekening | Gebruik |
|---|---|
| Cost of Goods Sold | Kostprijs omzet |
| Stock Adjustment | Voorraadcorrectie |
| Expenses Included In Valuation | Kosten in voorraadwaardering |
| Expenses Included In Asset Valuation | Kosten in activawaardering |

### Indirecte Kosten — Personeel

| Rekening | Gebruik |
|---|---|
| Salary | Brutoloon |
| Net Salary | Nettoloon |
| Holiday Allowance Expenses | Vakantiegeld (kosten) |
| Insurance Contributions (4515) | Verzekeringspremies |
| Cost Salary Administration | Kosten salarisadministratie |
| Cost Staffing Agency | Uitzendbureau |
| Employee Training Expenses | Opleiding personeel |
| Other Employee Expenses | Overige personeelskosten |

### Indirecte Kosten — Huisvesting

| Rekening | Gebruik |
|---|---|
| Office Rent | Huur kantoor |
| Office Maintenance Expenses | Onderhoud kantoor |
| Gas and electricity expenses | Gas en elektra |
| Building Insurance | Opstalverzekering |

### Indirecte Kosten — Vervoer

| Rekening | Gebruik |
|---|---|
| Car expenses | Autokosten |
| Car Insurance | Autoverzekering |
| Fuel Costs | Brandstof |
| Parking costs | Parkeerkosten |
| Other Costs Transport | Overige vervoerskosten |
| Travel Expenses | Reiskosten |
| Verkeersboetes | Verkeersboetes |

### Indirecte Kosten — Kantoor & IT

| Rekening | Gebruik |
|---|---|
| Software Expenses | Softwarelicenties |
| AI Software Costs | AI-software (bijv. Claude, ChatGPT) |
| Server Hosting Expenses Hetzner | Serverhosting |
| Telephone Expenses | Telefoonkosten |
| Print and Stationery | Drukwerk en kantoorartikelen |
| Postal Expenses | Verzendkosten |

### Indirecte Kosten — Zakelijk

| Rekening | Gebruik |
|---|---|
| Accountant Costs | Accountantskosten |
| Counsel Costs | Advieskosten |
| Legal Expenses | Juridische kosten |
| Notary costs | Notariskosten |
| Permit Costs | Vergunningen |
| Advertising and advertising costs | Reclame |
| Marketing Expenses | Marketing |
| Entertainment Expenses | Representatiekosten |
| Representation Cost | Representatiekosten (75%) |
| Customer gifts | Relatiegeschenken |
| Cooperation costs | Samenwerkingskosten |
| Domera deposit monthly | Domera afdracht |
| Donations | Donaties |

### Indirecte Kosten — Financieel

| Rekening | Gebruik |
|---|---|
| Bank and Transaction Costs | Bank- en transactiekosten |
| Exchange Gain/Loss | Valutaverschillen |
| Interest cost | Rentekosten |
| Late payment fees | Boeterente |
| Financial Lease | Financiele lease |
| Fines and Penalties | Boetes |
| Write Off | Afboekingen |
| Round Off | Afrondingsverschillen |
| Depreciation | Afschrijvingen |

### Overige Kosten

| Rekening | Gebruik |
|---|---|
| Administrative Expenses | Administratiekosten |
| Archive cost | Archiefkosten |
| Miscellaneous Expenses | Diverse kosten |
| Third Party Expenses | Kosten derden |
| Project development | Projectontwikkeling |
| Personal Withdrawal | Prive-opnames |
| Office Food | Kantoorvoorzieningen (eten) |
| Impertire | Impertire |
| Other Expenses | Overige kosten |
| 2024 Expenses / 2025 Expenses | Historische kosten per jaar |
| creditors abroad | Buitenlandse crediteurenkosten |
| Non-Deductible VAT Expense | Niet-aftrekbare BTW |

---

## Aandachtspunten voor Nederlandse Inrichting

### 1. BTW-rekeningen

Zorg dat alle BTW-tarieven als aparte rekeningen onder "Duties and Taxes" staan. Dit is essentieel voor een correcte BTW-aangifte. Zie het werkboek [BTW-inrichting](btw-inrichting.md).

### 2. Salarisrekeningen

Voor de Nederlandse salarisadministratie zijn aparte rekeningen nodig voor:
- Brutoloon vs. nettoloon
- Loonbelasting (Wage Taxes)
- Sociale premies (Social Security Fund)
- Vakantiegeld (Holiday Allowance)
- Werkkostenregeling (Work Related Costs)

### 3. Prive-opnames (VOF)

Bij een V.O.F. zijn prive-opnames van de vennoten geen salaris maar onttrekkingen aan het eigen vermogen. De rekening "Personal Withdrawal" wordt hiervoor gebruikt.

### 4. Representatiekosten

In Nederland is slechts 80% (soms 73.5%) van representatiekosten aftrekbaar voor de inkomstenbelasting. De rekening "Representation Cost" wordt apart bijgehouden voor de fiscale aangifte.

### 5. Account Nummering

Het huidige schema gebruikt voornamelijk namen in plaats van nummers. Voor een volledig RGS-conform schema kunnen rekeningnummers worden toegevoegd. Op dit moment heeft alleen de debiteurenrekening een nummer (12000).

---

## RGS (Referentie Grootboekschema)

Het Referentie Grootboekschema (RGS) is de Nederlandse standaard voor grootboekindelingen. Een toekomstige verbetering is het toekennen van RGS-codes aan alle rekeningen. Dit maakt:

- Automatische rapportage aan de Belastingdienst mogelijk
- Uitwisseling met accountantssoftware eenvoudiger
- Vergelijking met branchegenoten mogelijk

---

*Versie 1.0 — 24 maart 2026*
