---
titel: Bulk E-mail en Betalingsherinneringen
bedrijf: [BEDRIJFSNAAM]
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Gevorderd
doelgroep: Boekhouder, Ondernemer
---

# Bulk E-mail en Betalingsherinneringen

## Overzicht

Dit werkboek beschrijft hoe u in ERPNext facturen in bulk per e-mail kunt versturen en automatische betalingsherinneringen (dunning) kunt instellen. Dit is essentieel voor een efficiente debiteurenbeheer.

---

## Deel 1: Bulk E-mail van Verkoopfacturen

### Waarom Bulk E-mail?

Na het aanmaken van meerdere facturen (bijv. maandfacturatie) wilt u niet elke factuur handmatig per e-mail versturen. Met de bulk e-mailfunctie stuurt u alle openstaande facturen in een keer.

### Methode 1: Via de Lijstweergave (Client Script)

[BEDRIJFSNAAM] gebruikt een custom Client Script ("Bulk Email Sales Invoices") dat een knop toevoegt aan de Sales Invoice lijstweergave.

**Stappen:**

1. Ga naar **Accounts > Sales Invoice** (lijstweergave)
2. Filter op de facturen die u wilt versturen:
   - Status: `Unpaid` of `Overdue`
   - Posting Date: de relevante periode
3. Selecteer de facturen (vinkjes aan de linkerkant)
4. Klik op de knop **"Bulk Email"** (toegevoegd via Client Script)
5. Bevestig het versturen

Elke klant ontvangt:
- Een e-mail met de factuur als PDF-bijlage
- Het onderwerp bevat het factuurnummer
- De berichttekst bevat het factuurbedrag en de vervaldatum

### Methode 2: Via Auto Email Report

ERPNext kan automatisch rapporten per e-mail versturen:

1. Ga naar **Setup > Auto Email Report > New**
2. Vul in:

| Veld | Waarde |
|---|---|
| **Report** | `Accounts Receivable` |
| **User** | Uw e-mailadres |
| **Frequency** | Weekly / Monthly |
| **Filters** | Company = `[BEDRIJFSNAAM]` |

Dit stuurt u regelmatig een overzicht van openstaande facturen.

### Methode 3: Via Print Format en Email

Voor individuele facturen:

1. Open de Sales Invoice
2. Klik **Menu > Email**
3. Vul het e-mailadres in (wordt automatisch ingevuld vanuit klantrecord)
4. Kies het Print Format
5. De factuur wordt als PDF bijgevoegd
6. Pas eventueel de berichttekst aan
7. Klik **Send**

### E-mail Sjablonen

Maak e-mailsjablonen aan voor consistent communicatie:

1. Ga naar **Setup > Email Template > New**
2. Voorbeeld voor factuure-mail:

**Onderwerp:** `Factuur {{ doc.name }} — {{ doc.company }}`

**Bericht:**
```
Geachte {{ doc.customer_name }},

Hierbij ontvangt u factuur {{ doc.name }} d.d. {{ doc.posting_date }}
ten bedrage van {{ doc.currency }} {{ doc.grand_total }}.

De betalingstermijn is {{ doc.payment_terms_template or "21 dagen" }}.
De vervaldatum is {{ doc.due_date }}.

Wij verzoeken u het bedrag over te maken op:
IBAN: [IBAN HOOFDREKENING]
t.n.v. [BEDRIJFSNAAM]
o.v.v. {{ doc.name }}

Met vriendelijke groet,
[BEDRIJFSNAAM]
```

3. Save

### Tips Bulk E-mail

- **Controleer e-mailadressen** — Zorg dat bij elke klant een e-mailadres is ingevuld
- **Test eerst** — Stuur eerst een testfactuur naar uzelf
- **Tijdstip** — Verstuur zakelijke e-mails op werkdagen, bij voorkeur 's ochtends
- **Rate limiting** — ERPNext heeft standaard een limiet op het aantal e-mails per uur. Pas dit aan via Email Account settings als u grote batches verstuurt.

---

## Deel 2: Betalingsherinneringen (Dunning)

### Automatische Herinneringen

[BEDRIJFSNAAM] gebruikt geautomatiseerde betalingsherinneringen via Server Scripts (Scheduler Events). Er zijn aparte scripts per bedrijfsentiteit:

- **Dunning [BEDRIJFSNAAM]** — Voor [BEDRIJFSNAAM]
- **Dunning Cooperatie** — Voor de Cooperatie

### Hoe Werkt Het?

Het dunning-script draait automatisch (als Scheduler Event) en:

1. Zoekt alle facturen die over de vervaldatum zijn
2. Groepeert ze per klant
3. Stuurt per klant een herinneringsmail met:
   - Lijst van openstaande facturen
   - Factuurnummers en bedragen
   - Totaal openstaand bedrag
   - Betalingsinstructies (IBAN)
4. Logt de verstuurde herinnering

### Herinneringsschema

Een typisch schema voor betalingsherinneringen:

| Termijn | Actie | Toon |
|---|---|---|
| **Vervaldatum + 7 dagen** | Eerste herinnering | Vriendelijk |
| **Vervaldatum + 21 dagen** | Tweede herinnering | Zakelijk |
| **Vervaldatum + 35 dagen** | Derde herinnering (aanmaning) | Formeel |
| **Vervaldatum + 49 dagen** | Ingebrekestelling | Juridisch |

### Handmatige Dunning via ERPNext

ERPNext heeft ook een ingebouwde Dunning-module:

1. Ga naar **Accounts > Dunning > New**
2. Selecteer de klant
3. ERPNext toont automatisch de openstaande facturen
4. Kies het **Dunning Type** (eerste herinnering, aanmaning, etc.)
5. Pas het bedrag aan (eventueel met rente of aanmaningskosten)
6. Submit
7. Verstuur per e-mail

### Dunning Types Instellen

1. Ga naar **Accounts > Dunning Type > New**
2. Maak de volgende types aan:

| Dunning Type | Dagen na vervaldatum | Kosten | Rente |
|---|---|---|---|
| **Eerste herinnering** | 7 | EUR 0 | 0% |
| **Tweede herinnering** | 21 | EUR 0 | 0% |
| **Aanmaning** | 35 | EUR 15 | 0% |
| **Ingebrekestelling** | 49 | EUR 40 | Wettelijke rente |

### Wettelijke Rente (Nederland)

De wettelijke handelsrente in Nederland wordt halfjaarlijks vastgesteld:
- Check de actuele rente op de website van de Rijksoverheid
- Administratiekosten mogen in rekening worden gebracht (max EUR 40 bij handelstransacties)

### Aanmaningskosten Boeken

Aanmaningskosten worden geboekt op de rekening `Aanmaningskosten opbrengst - [AFK]` (Indirect Income).

---

## Deel 3: Accounts Receivable Beheer

### Debiteurenoverzicht

Gebruik het rapport **Accounts Receivable** voor een overzicht van alle openstaande facturen:

1. Ga naar **Accounts > Accounts Receivable**
2. Filter op:
   - Company: `[BEDRIJFSNAAM]`
   - Ageing Based On: `Due Date`
3. Het rapport toont:
   - Per klant: openstaande facturen
   - Ouderdomsanalyse: 0-30 dagen, 30-60 dagen, 60-90 dagen, 90+ dagen
   - Totalen

### Acties op Basis van het Overzicht

| Ouderdom | Actie |
|---|---|
| **0-30 dagen** | Geen actie (binnen betalingstermijn) |
| **30-60 dagen** | Eerste herinnering versturen |
| **60-90 dagen** | Tweede herinnering + telefonisch contact |
| **90+ dagen** | Aanmaning + overweeg incasso |

### Tips Debiteurenbeheer

1. **Wekelijks controleren** — Bekijk het Accounts Receivable rapport elke week
2. **Snel handelen** — Hoe langer u wacht, hoe kleiner de kans op betaling
3. **Persoonlijk contact** — Bij grote bedragen: bel de klant
4. **Betalingsregeling** — Bied bij financiele problemen een regeling aan
5. **Documenteer alles** — Log communicatie in ERPNext (via Comments)
6. **Afboeken** — Facturen die definitief oninbaar zijn: boek af via Journal Entry op `Write Off - [AFK]`

---

## Instellingen

### E-mail Account Configureren

Zorg dat uw e-mail correct is geconfigureerd:

1. Ga naar **Setup > Email Account**
2. Configureer een uitgaand e-mailaccount:

| Veld | Waarde |
|---|---|
| **Email Address** | bijv. `[email]` |
| **SMTP Server** | Afhankelijk van provider |
| **Port** | 587 (TLS) of 465 (SSL) |
| **Use TLS** | Ja |
| **Default Outgoing** | Ja (voor uitgaande mails) |

### E-mail Queue

Bekijk de status van verstuurde en wachtende e-mails:

- **Setup > Email Queue** — Overzicht van alle e-mails in de wachtrij
- Status: Sent, Not Sent, Error
- Bij fouten: controleer de foutmelding en pas e-mailinstellingen aan

---

*Versie 1.0 — 24 maart 2026*
