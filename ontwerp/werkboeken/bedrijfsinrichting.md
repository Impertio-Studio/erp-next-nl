---
titel: Bedrijfsinrichting — [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Beginner
doelgroep: Ondernemer, Boekhouder, IT
---

# Bedrijfsinrichting — [BEDRIJFSNAAM]

## Overzicht

Dit werkboek beschrijft de volledige inrichting van een Nederlands bedrijf in ERPNext, aan de hand van de configuratie van [BEDRIJFSNAAM]

---

## Bedrijfsgegevens

| Veld | Waarde |
|---|---|
| **Bedrijfsnaam** | [BEDRIJFSNAAM] |
| **Afkorting** | [AFK] |
| **Land** | Netherlands |
| **Valuta** | EUR |
| **KvK-nummer** | [KVK-NUMMER] |
| **BTW-nummer** | [BTW-NUMMER] |
| **Rechtsvorm** | Vennootschap Onder Firma |
| **Datum oprichting** | 22-09-2009 |
| **SBI-codes** | 7112 — Ingenieurs en overig technisch ontwerp en advies |
| | 6201 — Ontwikkelen, produceren en uitgeven van software |
| **Vestigingsadres** | [adres] |
| **Telefoon** | [telefoonnummer] |
| **Website** | [website] |
| **E-mail** | [email]@[bedrijf].nl |
| **Werkzame personen** | 4 |

### Vennoten

| Naam | Geboortedatum | In functie sinds | Bevoegdheid |
|---|---|---|---|
| [VENNOOT], [Voornamen] | 05-05-1986 | 01-01-2022 | Bevoegd tot EUR 10.000 |
| [VENNOOT], [Voornamen] | 04-02-1982 | 01-01-2022 | Bevoegd tot EUR 10.000 |

---

## Inrichting in ERPNext

### Stap 1: Company Aanmaken

Ga naar **Setup > Company > New** en vul de volgende velden in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **Company Name** | `[BEDRIJFSNAAM]` | Volledige handelsnaam |
| **Abbreviation** | `[AFK]` | Wordt gebruikt als suffix bij alle accounts |
| **Default Currency** | `EUR` | Standaard voor Nederland |
| **Country** | `Netherlands` | Bepaalt beschikbare templates |
| **Tax ID** | `[BTW-NUMMER]` | BTW-identificatienummer |
| **Date of Establishment** | `2009-09-22` | Oprichtingsdatum |
| **Registration Details** | KvK-gegevens | Volledige KvK-uitdraai (zie boven) |
| **Chart of Accounts** | `Standard Template` | ERPNext standaard, daarna aanpassen |

### Stap 2: Standaard Accounts Instellen

Na het aanmaken van het bedrijf, configureer de standaard accounts:

| Instelling | Account | Toelichting |
|---|---|---|
| **Default Bank Account** | `[IBAN HOOFDREKENING] - [AFK]` | Hoofdbankrekening Rabobank |
| **Default Cash Account** | `Cash - [AFK]` | Contante betalingen |
| **Default Receivable Account** | `12000 - Debtors - [AFK]` | Debiteuren |
| **Default Payable Account** | `Creditors - [AFK]` | Crediteuren |
| **Default Expense Account** | `Cost of Goods Sold - [AFK]` | Standaard kostenrekening |
| **Default Income Account** | `Sales - [AFK]` | Standaard omzetrekening |
| **Write Off Account** | `Write Off - [AFK]` | Afboekingen |
| **Exchange Gain/Loss** | `Exchange Gain/Loss - [AFK]` | Valutaverschillen |
| **Round Off Account** | `Round Off - [AFK]` | Afrondingsverschillen |

### Stap 3: Betalingsvoorwaarden

| Instelling | Waarde |
|---|---|
| **Payment Terms** | `21 Dagen` |

Dit is de standaard betalingstermijn die automatisch wordt toegepast op nieuwe facturen. De termijn van 21 dagen is gebruikelijk in het Nederlandse MKB.

### Stap 4: Kostenplaatsen

De volgende kostenplaatsen zijn ingericht:

| Kostenplaats | Type | Bovenliggend |
|---|---|---|
| **[bedrijfsnaam] - [AFK]** | Groep (root) | — |
| **Main - [AFK]** | Kostenplaats | [bedrijfsnaam] - [AFK] |
| **Work by third parties - [AFK]** | Kostenplaats | [bedrijfsnaam] - [AFK] |

- **Main - [AFK]** wordt gebruikt als standaard kostenplaats voor alle reguliere boekingen
- **Work by third parties - [AFK]** wordt gebruikt voor kosten van ingehuurde derden

### Stap 5: Vakantiedagen

| Instelling | Waarde |
|---|---|
| **Default Holiday List** | `Vakantiedagen 2026` |

Maak jaarlijks een nieuwe Holiday List aan met de Nederlandse feestdagen:
- Nieuwjaarsdag (1 januari)
- Goede Vrijdag
- Eerste en Tweede Paasdag
- Koningsdag (27 april)
- Bevrijdingsdag (5 mei, eens per 5 jaar vrij)
- Hemelvaartsdag
- Eerste en Tweede Pinksterdag
- Eerste en Tweede Kerstdag (25-26 december)

### Stap 6: Voorraadadministratie

| Instelling | Waarde | Toelichting |
|---|---|---|
| **Enable Perpetual Inventory** | Ja | Automatische voorraadboekingen |
| **Default Inventory Account** | `Stock In Hand - [AFK]` | Voorraadrekening |
| **Stock Adjustment Account** | `Stock Adjustment - [AFK]` | Voorraadcorrecties |
| **Stock Received But Not Billed** | `Stock Received But Not Billed - [AFK]` | Ontvangen, nog niet gefactureerd |

---

## Betaalmethoden

De volgende betaalmethoden zijn beschikbaar:

| Betaalmethode | Type | Gebruik |
|---|---|---|
| **Wire Transfer** | Bank | Standaard voor leveranciersbetalingen (SEPA) |
| **Bank Draft** | Bank | Bankoverschrijvingen |
| **Cash** | Contant | Contante betalingen |
| **Credit Card** | Bank | Creditcardbetalingen (Visa) |
| **Cheque** | Bank | Niet gebruikelijk in NL, maar beschikbaar |

---

## Vaste Waarden (Snelle Referentie)

| Parameter | Waarde |
|---|---|
| **Company** | `[BEDRIJFSNAAM]` |
| **Abbreviation suffix** | `- [AFK]` |
| **Naming Series (Purchase Invoice)** | `.YY.[AFK].-.#####` |
| **Default Cost Center** | `Main - [AFK]` |
| **Default Payment Terms** | `21 Dagen` |
| **IBAN** | `[IBAN HOOFDREKENING]` |
| **BIC** | `RABONL2U` |

---

## Tips voor Nederlandse Inrichting

1. **KvK-gegevens** — Bewaar de volledige KvK-uitdraai in het veld "Registration Details". Dit is handig voor facturen en correspondentie.

2. **BTW-nummer** — Het Tax ID veld moet het volledige BTW-identificatienummer bevatten (NL + 9 cijfers + B + 2 cijfers).

3. **Afkorting** — Kies een korte, unieke afkorting. Bij meerdere bedrijven in dezelfde ERPNext-instance voorkomt dit verwarring (bijv. `[AFK]` voor het ene bedrijf, `[AFK2]` voor het andere).

4. **Betalingstermijnen** — 30 dagen is wettelijk standaard in Nederland, maar in de praktijk hanteren veel MKB-bedrijven 14 of 21 dagen.

5. **Boekjaar** — Het standaard boekjaar in Nederland loopt van 1 januari tot 31 december. Stel dit in via **Setup > Fiscal Year**.

---

*Versie 1.0 — 24 maart 2026*
