---
titel: Inrichting van een ERPNext Instance — Compleet Stappenplan
versie: 1.0
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Expert
doelgroep: IT, Ondernemer, Boekhouder
---

# Inrichting van een ERPNext Instance

## Compleet Stappenplan — Van Installatie tot Productie

Dit werkboek beschrijft alle stappen om een ERPNext-instance volledig in te richten voor een Nederlands bedrijf. Van het installeren van apps tot het configureren van e-mailaccounts.

---

## Overzicht van de Stappen

```
 1. Apps Installeren (NL Administratie)
 2. Workflows Configureren
 3. Company-structuur (Onderlinge Verhoudingen)
 4. Bankkoppeling
 5. Users & Employees
 6. Roles & Permissions
 7. Factuur Template (Print Format)
 8. Uren Template (Timesheet Print Format)
 9. Email Templates
10. Email Ondertekening
11. Email Accounts & Authenticatie
12. Emails Linken aan Accounts
```

---

## 1. Apps Installeren — NL Administratie

### Standaard Apps

Bij een nieuwe ERPNext-instance zijn de volgende apps beschikbaar:

| App | Functie | Installatie |
|---|---|---|
| **frappe** | Framework (altijd vereist) | Standaard geinstalleerd |
| **erpnext** | ERP-kernfunctionaliteit | Standaard geinstalleerd |
| **hrms** | HR & Salarismodule | Optioneel |
| **payments** | Betalingsintegraties | Optioneel |
| **lending** | Leningenbeheer | Optioneel |

### Apps Installeren via Bench

```bash
# HR Module (voor salarisadministratie, verlof, etc.)
bench get-app hrms
bench --site [uw-site] install-app hrms

# Payments (voor online betalingen)
bench get-app payments
bench --site [uw-site] install-app payments
```

### NL-specifieke Apps en Configuratie

Er is geen officieel "Netherlands localization" app, maar de volgende instellingen maken ERPNext geschikt voor de Nederlandse administratie:

**Stap 1: Taal instellen**

1. Ga naar **Setup > Settings > System Settings**
2. Stel in:

| Veld | Waarde |
|---|---|
| **Language** | `Nederlands` (of `English` als u de Engelse interface prefereert) |
| **Date Format** | `dd-mm-yyyy` |
| **Time Format** | `HH:mm:ss` |
| **Number Format** | `#.###,##` (punten voor duizendtallen, komma voor decimalen) |
| **Currency** | `EUR` |
| **Country** | `Netherlands` |
| **Time Zone** | `Europe/Amsterdam` |
| **First Day of the Week** | `Monday` |

**Stap 2: Boekjaar**

1. Ga naar **Setup > Fiscal Year > New**
2. Vul in:
   - Year Name: `2026`
   - Year Start Date: `2026-01-01`
   - Year End Date: `2026-12-31`
   - Is Default: Ja

**Stap 3: Valuta**

1. Ga naar **Setup > Currency**
2. Controleer dat EUR (Euro) als standaardvaluta is ingesteld
3. Optioneel: activeer USD, GBP voor buitenlandse transacties

**Stap 4: BTW-instellingen**

Volg het werkboek [BTW-inrichting voor Nederland](btw-inrichting.md) voor het instellen van:
- BTW-rekeningen (21%, 9%, 0%, verlegd)
- Purchase Taxes and Charges Templates
- Sales Taxes and Charges Templates
- Tax Categories

**Stap 5: Grootboekschema**

Volg het werkboek [Grootboekschema](grootboekschema.md) voor het aanpassen van het standaard grootboekschema naar Nederlandse normen.

---

## 2. Workflows Configureren

### Wat zijn Workflows?

Workflows in ERPNext definieren goedkeuringsprocessen. Documenten doorlopen statussen en vereisen goedkeuring van specifieke rollen voordat ze verder kunnen.

### Workflow Aanmaken

1. Ga naar **Setup > Workflow > New**
2. Vul in:

| Veld | Waarde |
|---|---|
| **Workflow Name** | Beschrijvende naam |
| **Document Type** | Het DocType waarop de workflow van toepassing is |
| **Is Active** | Ja |
| **Override Status** | Ja (workflow bepaalt de status) |

### Voorbeeld: Purchase Invoice Goedkeuringsworkflow

```
Draft → Ter Beoordeling → Goedgekeurd → Submitted
                        → Afgekeurd → Draft
```

| Status | Toegestane rollen | Actie | Volgende status |
|---|---|---|---|
| **Draft** | Accounts User | Ter beoordeling indienen | Ter Beoordeling |
| **Ter Beoordeling** | Accounts Manager | Goedkeuren | Goedgekeurd |
| **Ter Beoordeling** | Accounts Manager | Afkeuren | Draft |
| **Goedgekeurd** | Accounts Manager | Submitten | Submitted |

### Voorbeeld: Expense Claim Workflow

```
Draft → Ingediend → Goedgekeurd door Manager → Goedgekeurd door Financieel → Betaald
                  → Afgekeurd → Draft
```

### Voorbeeld: Leave Application Workflow

```
Open → Goedgekeurd door Teamleider → Goedgekeurd door HR → Approved
     → Afgekeurd → Rejected
```

### Workflow States Instellen

1. Ga naar **Setup > Workflow State**
2. Maak de benodigde statussen aan:

| Workflow State | Stijl |
|---|---|
| Draft | — |
| Ter Beoordeling | Primary (blauw) |
| Goedgekeurd | Success (groen) |
| Afgekeurd | Danger (rood) |
| Submitted | Success (groen) |

### Workflow Actions

1. Ga naar **Setup > Workflow Action**
2. Maak acties aan:

| Actie | Toelichting |
|---|---|
| Indienen | Van Draft naar Ter Beoordeling |
| Goedkeuren | Akkoord geven |
| Afkeuren | Terug naar Draft |
| Submitten | Definitief maken |

### Tips Workflows

- Begin eenvoudig — voeg complexiteit toe wanneer nodig
- Test workflows grondig voordat u ze activeert
- Documenteer welke rollen welke goedkeuringen doen
- Overweeg e-mailnotificaties bij statuswijzigingen (via Notification)

---

## 3. Company-structuur — Onderlinge Verhoudingen

### Multi-Company Setup

ERPNext ondersteunt meerdere bedrijven in een installatie met onderlinge verhoudingen.

### Structuur Voorbeeld

```
[MOEDERBEDRIJF] (Groep)
├── [BEDRIJF A] V.O.F.       (Dochter)
├── [BEDRIJF B] B.V.         (Dochter)
└── [BEDRIJF C] Cooperatie   (Dochter)
```

### Company Aanmaken

1. Maak eerst het moederbedrijf aan (indien van toepassing):
   - Ga naar **Setup > Company > New**
   - **Is Group**: Ja
   - Vul de overige velden in

2. Maak dochterbedrijven aan:
   - Ga naar **Setup > Company > New**
   - **Parent Company**: Selecteer het moederbedrijf
   - **Is Group**: Nee
   - Elke dochter krijgt een eigen afkorting (bijv. `A`, `B`, `C`)

### Onderlinge Transacties (Inter-Company)

Bij transacties tussen eigen bedrijven:

**Stap 1: Internal Suppliers/Customers instellen**

1. Maak per bedrijf een Supplier aan in het andere bedrijf:
   - Supplier Name: `[BEDRIJF A]`
   - **Is Internal Supplier**: Ja
   - **Represents Company**: `[BEDRIJF A] V.O.F.`
   - **Companies**: voeg de bedrijven toe die mogen inkopen

2. Idem voor Customers:
   - Customer Name: `[BEDRIJF A]`
   - **Is Internal Customer**: Ja
   - **Represents Company**: `[BEDRIJF A] V.O.F.`

**Stap 2: Inter-Company Transacties**

Bij een factuur van Bedrijf A aan Bedrijf B:

1. Maak een Sales Invoice in Bedrijf A (aan interne klant B)
2. ERPNext kan automatisch een Purchase Invoice aanmaken in Bedrijf B
3. Stel dit in via **Setup > Accounts Settings**:
   - **Automatically Create Inter Company Journal Entry**: Ja

### Geconsolideerde Rapportage

Met een groepsstructuur kunt u geconsolideerde rapporten draaien:

- **Accounts > Consolidated Financial Statement** — Gecombineerde balans en W&V
- Intercompany-transacties worden automatisch geelimineerd

### Let Op

- Elk bedrijf heeft een eigen grootboekschema (met eigen afkorting)
- Elk bedrijf kan een eigen bankrekening, BTW-nummer en KvK-nummer hebben
- Medewerkers kunnen aan meerdere bedrijven zijn gekoppeld
- Kostenplaatsen zijn per bedrijf

---

## 4. Bankkoppeling

Volg het werkboek [Bankkoppelingen en Betaalrekeningen](bankkoppelingen.md) voor:

- Bankrekeningen aanmaken en koppelen
- SEPA-betalingen configureren
- Bankreconciliatie instellen

### Samenvatting Stappen

1. **Bank aanmaken** — Setup > Bank (bijv. Rabobank, ABN AMRO, ING)
2. **Grootboekrekening aanmaken** — Chart of Accounts > Bank Accounts > Add Child
3. **Bank Account aanmaken** — Accounts > Bank Account > New (met IBAN, gekoppeld aan grootboek)
4. **Instellen als default** — Company > Default Bank Account
5. **SEPA testen** — Maak een test Payment Order en genereer een XML

---

## 5. Users & Employees

### Users (Systeemgebruikers)

Users zijn inlogaccounts voor het ERPNext-systeem.

**Stap 1: User aanmaken**

1. Ga naar **Setup > User > New**
2. Vul in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **Email** | E-mailadres | Dit is ook de gebruikersnaam |
| **First Name** | Voornaam | — |
| **Last Name** | Achternaam | — |
| **Enabled** | Ja | Account actief |
| **Send Welcome Email** | Ja | Gebruiker ontvangt een welkomstmail met wachtwoord-link |

3. **Rollen toewijzen** — zie sectie 6 (Roles & Permissions)
4. **Save**

**Stap 2: Gebruikersinstellingen**

Per gebruiker kunt u instellen:

| Instelling | Gebruik |
|---|---|
| **Language** | Taal van de interface |
| **Time Zone** | Tijdzone (standaard: Europe/Amsterdam) |
| **Default Company** | Standaardbedrijf bij nieuwe documenten |
| **Allowed Companies** | Bij welke bedrijven mag deze gebruiker werken |
| **Home Page** | Startpagina na inloggen |

### Employees (Medewerkers)

Employees zijn medewerkerrecords, gekoppeld aan Users.

**Stap 1: Employee aanmaken**

1. Ga naar **HR > Employee > New**
2. Vul in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **First Name** | Voornaam | — |
| **Last Name** | Achternaam | — |
| **Company** | Selecteer bedrijf | — |
| **Date of Birth** | Geboortedatum | Verplicht |
| **Date of Joining** | Datum in dienst | Verplicht |
| **Gender** | Geslacht | — |
| **User ID** | Koppel aan User account | Optioneel maar aanbevolen |
| **Status** | `Active` | — |

3. **Persoonlijke gegevens** — vul aan:
   - Adres
   - BSN (via custom field, niet standaard)
   - Noodcontact
   - Bankrekening (voor salarisuitbetaling)

4. **Arbeidsvoorwaarden** — vul aan:
   - Department
   - Designation (functie)
   - Reports To (leidinggevende)
   - Leave Policy (verlofbeleid)
   - Holiday List (feestdagen)

5. **Save**

### Koppeling User ↔ Employee

| Situatie | Aanpak |
|---|---|
| Medewerker met systeemtoegang | Maak User aan + Employee met User ID |
| Medewerker zonder systeemtoegang | Maak alleen Employee aan (bijv. productiemedewerker) |
| Externe gebruiker (accountant) | Maak alleen User aan, geen Employee |

---

## 6. Roles & Permissions

### Standaard Rollen

ERPNext heeft een uitgebreid rollensysteem. De belangrijkste rollen:

| Rol | Toegang tot |
|---|---|
| **System Manager** | Alles — alleen voor beheerders |
| **Accounts User** | Boekhouding: facturen, betalingen, journaalposten |
| **Accounts Manager** | Boekhouding + goedkeuringen, rapportages, instellingen |
| **Sales User** | Verkoop: offertes, orders |
| **Sales Manager** | Verkoop + goedkeuringen, rapportages |
| **Purchase User** | Inkoop: inkooporders, leveranciers |
| **Purchase Manager** | Inkoop + goedkeuringen |
| **Stock User** | Voorraad: ontvangsten, leveringen |
| **Stock Manager** | Voorraad + correcties, instellingen |
| **HR User** | HR: medewerkers, verlof |
| **HR Manager** | HR + goedkeuringen, salarissen |
| **Projects User** | Projecten: taken, timesheets |
| **Projects Manager** | Projecten + rapportages |

### Rollen Toewijzen

1. Open de User
2. Scroll naar **Roles**
3. Voeg de relevante rollen toe

### Voorbeeld: Rolmatrix

| Medewerker | Rollen |
|---|---|
| **Eigenaar/Directie** | System Manager, Accounts Manager, Sales Manager, HR Manager |
| **Boekhouder** | Accounts User, Accounts Manager |
| **Verkoopmedewerker** | Sales User, CRM User |
| **Inkoper** | Purchase User, Stock User |
| **Projectleider** | Projects User, Projects Manager |
| **Medewerker (basis)** | Employee Self Service |
| **Accountant (extern)** | Accounts User (alleen lezen) |

### Custom Permissions

Voor fijnmazig beheer:

1. Ga naar **Setup > Role Permission Manager**
2. Selecteer een DocType (bijv. Sales Invoice)
3. Per rol kunt u instellen:

| Recht | Beschrijving |
|---|---|
| **Read** | Document bekijken |
| **Write** | Document bewerken |
| **Create** | Nieuw document aanmaken |
| **Delete** | Document verwijderen |
| **Submit** | Document definitief maken |
| **Cancel** | Definitief document annuleren |
| **Amend** | Geannuleerd document wijzigen |
| **Report** | Rapportages draaien |
| **Export** | Data exporteren |
| **Print** | Document printen |
| **Email** | Document e-mailen |
| **Share** | Document delen met anderen |

### User Permissions (Data-beperkingen)

Naast rolrechten kunt u data-beperkingen instellen:

1. Ga naar **Setup > User Permission > New**
2. Stel in:

| Veld | Waarde |
|---|---|
| **User** | Selecteer gebruiker |
| **Allow** | DocType waartoe toegang wordt verleend |
| **For Value** | Specifieke waarde (bijv. een specifiek bedrijf) |

**Voorbeeld:** Gebruiker Jan mag alleen documenten zien van Bedrijf A:
- Allow: `Company`
- For Value: `[BEDRIJF A] V.O.F.`

### Tips Permissions

- Begin met standaardrollen — pas alleen aan als het echt nodig is
- Gebruik de **Permission Inspector** (Setup > Show Permissions) om te controleren wat een gebruiker kan
- Test permissies door in te loggen als die gebruiker (via **Setup > User > Impersonate**)
- Bij multi-company: gebruik User Permissions om gebruikers te beperken tot hun eigen bedrijf

---

## 7. Factuur Template (Print Format)

### Wat is een Print Format?

Een Print Format bepaalt hoe een document eruitziet wanneer het wordt geprint of als PDF wordt verstuurd. Voor facturen is dit essentieel — de factuur moet voldoen aan Nederlandse wettelijke eisen.

### Factuur Print Format Aanmaken

1. Ga naar **Setup > Print Format > New**
2. Vul in:

| Veld | Waarde |
|---|---|
| **Name** | `[BEDRIJFSNAAM] Factuur` |
| **Doc Type** | `Sales Invoice` |
| **Module** | `Accounts` |
| **Standard** | Nee (custom) |
| **Print Format Type** | `Jinja` (voor volledige controle) |

3. Plak de HTML/Jinja2 template in het **HTML** veld

### Verplichte Elementen (Nederlandse Wet)

De factuur moet bevatten:

| Element | Jinja2 Code |
|---|---|
| **Bedrijfsnaam** | `{{ doc.company }}` |
| **Adres bedrijf** | `{{ company.address }}` |
| **KvK-nummer** | `{{ company.registration_details }}` |
| **BTW-nummer** | `{{ company.tax_id }}` |
| **Klantnaam** | `{{ doc.customer_name }}` |
| **Klantadres** | Via `{% set address = frappe.get_doc("Address", doc.customer_address) %}` |
| **Factuurnummer** | `{{ doc.name }}` |
| **Factuurdatum** | `{{ doc.posting_date }}` |
| **Items** | `{% for item in doc.items %}` |
| **Nettobedrag** | `{{ doc.net_total }}` |
| **BTW-tarief** | Per regel in taxes tabel |
| **BTW-bedrag** | `{{ doc.total_taxes_and_charges }}` |
| **Totaal incl. BTW** | `{{ doc.grand_total }}` |
| **Betalingstermijn** | `{{ doc.payment_terms_template }}` |
| **Vervaldatum** | `{{ doc.due_date }}` |
| **IBAN** | Via company bank account |
| **BIC** | Via company bank account |

### Voorbeeld Basisstructuur

```html
<div class="factuur">
  <div class="header">
    <img src="{{ company.company_logo }}" style="height: 60px;">
    <div class="bedrijfsgegevens">
      <strong>{{ doc.company }}</strong><br>
      {{ frappe.get_value("Address", company.registered_office, "address_line1") }}<br>
      KvK: {{ company.registration_details[:20] }}<br>
      BTW: {{ company.tax_id }}
    </div>
  </div>

  <div class="klantgegevens">
    <strong>{{ doc.customer_name }}</strong><br>
    {% if doc.customer_address %}
      {{ frappe.get_doc("Address", doc.customer_address).address_line1 }}<br>
      {{ frappe.get_doc("Address", doc.customer_address).pincode }}
      {{ frappe.get_doc("Address", doc.customer_address).city }}
    {% endif %}
  </div>

  <div class="factuurdetails">
    <table>
      <tr><td>Factuurnummer:</td><td>{{ doc.name }}</td></tr>
      <tr><td>Factuurdatum:</td><td>{{ doc.posting_date }}</td></tr>
      <tr><td>Vervaldatum:</td><td>{{ doc.due_date }}</td></tr>
      {% if doc.po_no %}
      <tr><td>Uw referentie:</td><td>{{ doc.po_no }}</td></tr>
      {% endif %}
    </table>
  </div>

  <table class="items">
    <thead>
      <tr>
        <th>Omschrijving</th>
        <th>Aantal</th>
        <th>Prijs</th>
        <th>Bedrag</th>
      </tr>
    </thead>
    <tbody>
      {% for item in doc.items %}
      <tr>
        <td>{{ item.item_name }}<br><small>{{ item.description }}</small></td>
        <td>{{ item.qty }}</td>
        <td>{{ frappe.format_value(item.rate, {"fieldtype": "Currency"}) }}</td>
        <td>{{ frappe.format_value(item.amount, {"fieldtype": "Currency"}) }}</td>
      </tr>
      {% endfor %}
    </tbody>
  </table>

  <div class="totalen">
    <table>
      <tr><td>Subtotaal:</td><td>{{ frappe.format_value(doc.net_total, {"fieldtype": "Currency"}) }}</td></tr>
      {% for tax in doc.taxes %}
      <tr><td>{{ tax.description }}:</td><td>{{ frappe.format_value(tax.tax_amount, {"fieldtype": "Currency"}) }}</td></tr>
      {% endfor %}
      <tr class="totaal"><td><strong>Totaal:</strong></td><td><strong>{{ frappe.format_value(doc.grand_total, {"fieldtype": "Currency"}) }}</strong></td></tr>
    </table>
  </div>

  <div class="betaalgegevens">
    <p>Gelieve het bedrag binnen {{ doc.payment_terms_template or "21 dagen" }} over te maken op:</p>
    <p>
      IBAN: [IBAN]<br>
      BIC: [BIC]<br>
      t.n.v. {{ doc.company }}<br>
      o.v.v. {{ doc.name }}
    </p>
  </div>

  <div class="footer">
    <small>{{ doc.company }} | KvK: [KVK] | BTW: {{ company.tax_id }}</small>
  </div>
</div>
```

### Meerdere Print Formats

U kunt meerdere factuursjablonen aanmaken voor verschillende situaties:

| Print Format | Gebruik |
|---|---|
| `[BEDRIJFSNAAM] Factuur` | Standaard verkoopfactuur |
| `[BEDRIJFSNAAM] Factuur + Urenstaat` | Factuur met bijgevoegde urenspecificatie |
| `[BEDRIJFSNAAM] Credit Nota` | Creditfactuur |
| `[BEDRIJFSNAAM] Pro Forma` | Pro forma factuur (offerte-achtig) |

### Print Format Instellen als Default

1. Ga naar **Setup > Print Settings**
2. Of stel per DocType in:
   - **Customize Form > Sales Invoice**
   - Veld "Default Print Format" → selecteer uw template

---

## 8. Uren Template (Timesheet Print Format)

### Timesheet Print Format Aanmaken

1. Ga naar **Setup > Print Format > New**
2. Vul in:

| Veld | Waarde |
|---|---|
| **Name** | `[BEDRIJFSNAAM] Urenstaat` |
| **Doc Type** | `Timesheet` |
| **Print Format Type** | `Jinja` |

### Voorbeeld Urenstaat Template

```html
<div class="urenstaat">
  <div class="header">
    <h2>Urenstaat</h2>
    <p><strong>Medewerker:</strong> {{ doc.employee_name }}</p>
    <p><strong>Periode:</strong> {{ doc.start_date }} t/m {{ doc.end_date }}</p>
    {% if doc.project %}
    <p><strong>Project:</strong> {{ doc.project }}</p>
    {% endif %}
  </div>

  <table class="uren">
    <thead>
      <tr>
        <th>Datum</th>
        <th>Project</th>
        <th>Taak</th>
        <th>Activiteit</th>
        <th>Uren</th>
        <th>Declarabel</th>
      </tr>
    </thead>
    <tbody>
      {% for row in doc.time_logs %}
      <tr>
        <td>{{ row.from_time.strftime('%d-%m-%Y') if row.from_time else '' }}</td>
        <td>{{ row.project or '' }}</td>
        <td>{{ row.task or '' }}</td>
        <td>{{ row.activity_type or '' }}</td>
        <td>{{ row.hours }}</td>
        <td>{{ "Ja" if row.is_billable else "Nee" }}</td>
      </tr>
      {% endfor %}
    </tbody>
    <tfoot>
      <tr>
        <td colspan="4"><strong>Totaal</strong></td>
        <td><strong>{{ doc.total_hours }}</strong></td>
        <td></td>
      </tr>
      {% if doc.total_billable_hours %}
      <tr>
        <td colspan="4"><strong>Totaal declarabel</strong></td>
        <td><strong>{{ doc.total_billable_hours }}</strong></td>
        <td></td>
      </tr>
      {% endif %}
    </tfoot>
  </table>
</div>
```

---

## 9. Email Templates

### Email Templates Aanmaken

Ga naar **Setup > Email Template > New** voor elk type bericht.

### Factuur E-mail

| Veld | Waarde |
|---|---|
| **Name** | `Factuur Verzenden` |
| **Subject** | `Factuur {{ doc.name }} — {{ doc.company }}` |
| **DocType** | `Sales Invoice` |

**Body:**
```
Geachte {{ doc.customer_name }},

Hierbij ontvangt u factuur {{ doc.name }} d.d. {{ frappe.format_date(doc.posting_date) }}
ten bedrage van {{ doc.currency }} {{ frappe.format_value(doc.grand_total, {"fieldtype": "Currency"}) }}.

De vervaldatum is {{ frappe.format_date(doc.due_date) }}.

Wij verzoeken u het bedrag over te maken op:
IBAN: [IBAN HOOFDREKENING]
t.n.v. [BEDRIJFSNAAM]
o.v.v. {{ doc.name }}

De factuur vindt u als bijlage bij deze e-mail.

Met vriendelijke groet,
{{ frappe.session.user_fullname }}
[BEDRIJFSNAAM]
```

### Offerte E-mail

| Veld | Waarde |
|---|---|
| **Name** | `Offerte Verzenden` |
| **Subject** | `Offerte {{ doc.name }} — {{ doc.company }}` |
| **DocType** | `Quotation` |

**Body:**
```
Geachte {{ doc.party_name }},

Naar aanleiding van uw verzoek sturen wij u hierbij onze offerte {{ doc.name }}.

Het totaalbedrag bedraagt {{ doc.currency }} {{ frappe.format_value(doc.grand_total, {"fieldtype": "Currency"}) }} inclusief BTW.

Deze offerte is geldig tot {{ frappe.format_date(doc.valid_till) }}.

De offerte vindt u als bijlage bij deze e-mail. Mocht u vragen hebben,
neem dan gerust contact met ons op.

Met vriendelijke groet,
{{ frappe.session.user_fullname }}
[BEDRIJFSNAAM]
```

### Betalingsherinnering E-mail

| Veld | Waarde |
|---|---|
| **Name** | `Betalingsherinnering` |
| **Subject** | `Betalingsherinnering — {{ doc.name }}` |
| **DocType** | `Sales Invoice` |

**Body:**
```
Geachte {{ doc.customer_name }},

Volgens onze administratie is de betaling van onderstaande factuur nog niet ontvangen:

Factuurnummer: {{ doc.name }}
Factuurdatum: {{ frappe.format_date(doc.posting_date) }}
Vervaldatum: {{ frappe.format_date(doc.due_date) }}
Openstaand bedrag: {{ doc.currency }} {{ frappe.format_value(doc.outstanding_amount, {"fieldtype": "Currency"}) }}

Wij verzoeken u het bedrag zo spoedig mogelijk over te maken op:
IBAN: [IBAN HOOFDREKENING]
t.n.v. [BEDRIJFSNAAM]
o.v.v. {{ doc.name }}

Indien u de betaling reeds heeft verricht, kunt u deze herinnering als niet verzonden beschouwen.

Met vriendelijke groet,
{{ frappe.session.user_fullname }}
[BEDRIJFSNAAM]
```

### Inkooporder E-mail

| Veld | Waarde |
|---|---|
| **Name** | `Inkooporder Verzenden` |
| **Subject** | `Inkooporder {{ doc.name }} — {{ doc.company }}` |
| **DocType** | `Purchase Order` |

**Body:**
```
Geachte heer/mevrouw,

Hierbij ontvangt u onze inkooporder {{ doc.name }}.

Gelieve de goederen/diensten te leveren conform de specificaties in de bijlage.
De gevraagde leverdatum is {{ frappe.format_date(doc.schedule_date) }}.

De inkooporder vindt u als bijlage bij deze e-mail.

Met vriendelijke groet,
{{ frappe.session.user_fullname }}
[BEDRIJFSNAAM]
```

---

## 10. Email Ondertekening

### Email Signature Instellen

Elke gebruiker kan een eigen e-mailondertekening instellen:

1. Ga naar **Uw Profielfoto > My Settings**
2. Scroll naar **Email Signature**
3. Vul de HTML-ondertekening in:

```html
<div style="font-family: Arial, sans-serif; font-size: 13px; color: #333;">
  <p style="margin: 0;">
    <strong>{{ user_fullname }}</strong><br>
    {{ designation }}<br>
    <br>
    <strong>[BEDRIJFSNAAM]</strong><br>
    [adres]<br>
    [postcode] [plaatsnaam]<br>
    T: [telefoonnummer]<br>
    E: [email]<br>
    W: [website]
  </p>
  <p style="margin-top: 10px;">
    <img src="[logo-url]" alt="[BEDRIJFSNAAM]" style="height: 40px;">
  </p>
  <p style="font-size: 11px; color: #999; margin-top: 10px;">
    KvK: [KVK-NUMMER] | BTW: [BTW-NUMMER]
  </p>
</div>
```

### Bedrijfsbrede Signature (via Footer)

Voor een standaard footer op alle uitgaande e-mails:

1. Ga naar **Setup > Email Account**
2. Open het standaard uitgaande account
3. Vul het veld **Footer** in met de bedrijfsgegevens

---

## 11. Email Accounts & Authenticatie

### Email Account Configureren

ERPNext vereist minimaal een uitgaand e-mailaccount voor het versturen van facturen, offertes en notificaties.

**Stap 1: Uitgaand Account**

1. Ga naar **Setup > Email Account > New**
2. Vul in:

| Veld | Waarde | Toelichting |
|---|---|---|
| **Email Address** | `[email]@[bedrijf].nl` | Hoofde-mailadres |
| **Email Account Name** | `Uitgaande mail` | Beschrijvende naam |
| **Service** | Selecteer provider | Zie tabel hieronder |
| **Password / App Password** | Wachtwoord | Zie authenticatie hieronder |
| **Default Outgoing** | Ja | Standaard voor uitgaande mails |
| **Always use Account Email Address** | Optioneel | Alle mails komen van dit adres |

**Stap 2: Inkomend Account (Optioneel maar Aanbevolen)**

| Veld | Waarde |
|---|---|
| **Enable Incoming** | Ja |
| **IMAP Server** | Zie tabel hieronder |
| **IMAP Port** | 993 (SSL) |
| **Use IMAP** | Ja |
| **Use SSL** | Ja |
| **Append To** | `Communication` (standaard) |

### SMTP/IMAP Instellingen per Provider

| Provider | SMTP Server | SMTP Port | IMAP Server | IMAP Port |
|---|---|---|---|---|
| **Microsoft 365** | smtp.office365.com | 587 (TLS) | outlook.office365.com | 993 |
| **Google Workspace** | smtp.gmail.com | 587 (TLS) | imap.gmail.com | 993 |
| **TransIP** | smtp.transip.email | 465 (SSL) | imap.transip.email | 993 |
| **Eigen server** | mail.[domein].nl | 587/465 | mail.[domein].nl | 993 |

### Authenticatie

#### Microsoft 365 (OAuth)

Microsoft vereist OAuth2 voor SMTP/IMAP:

1. Registreer een app in **Azure AD > App Registrations**
2. Voeg de volgende API-permissies toe:
   - `SMTP.Send`
   - `IMAP.AccessAsUser.All`
3. Genereer een **Client Secret**
4. Vul in ERPNext in:
   - **Auth Method**: `OAuth`
   - **Client ID**: van Azure AD
   - **Client Secret**: van Azure AD
   - **Authorization URL**: `https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize`
   - **Token URL**: `https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token`

#### Google Workspace (App Password)

1. Ga naar **Google Account > Security > 2-Step Verification**
2. Genereer een **App Password** voor "Mail"
3. Gebruik dit wachtwoord in ERPNext (niet uw gewone wachtwoord)

#### Reguliere SMTP (Wachtwoord)

Voor andere providers:
1. Gebruik uw e-mailwachtwoord direct
2. Zorg dat **SMTP-toegang** is ingeschakeld bij uw provider

### Meerdere Email Accounts

U kunt meerdere e-mailaccounts configureren:

| Account | Gebruik | Default |
|---|---|---|
| `info@[bedrijf].nl` | Algemene correspondentie | Outgoing |
| `facturen@[bedrijf].nl` | Factuurverzending | — |
| `hr@[bedrijf].nl` | HR-communicatie | — |
| `support@[bedrijf].nl` | Klantvragen (→ Issue) | Incoming |

---

## 12. Emails Linken aan Accounts

### Wat Betekent "Emails Linken"?

In ERPNext kunnen inkomende en uitgaande e-mails automatisch worden gekoppeld aan de juiste documenten en klanten. Dit geeft een compleet communicatie-overzicht per klant, project of factuur.

### Automatisch Linken

ERPNext linkt e-mails automatisch op basis van:

1. **E-mailadres** — E-mails van/aan een klant of leverancier worden gekoppeld aan dat contact
2. **Referentie in onderwerp** — Als het onderwerp een documentnaam bevat (bijv. "SI-2026-0001") wordt de e-mail aan dat document gekoppeld
3. **Reply-to header** — Antwoorden op eerder verstuurde mails worden gekoppeld aan hetzelfde document

### Email Account → DocType Koppeling

U kunt instellen dat inkomende e-mails automatisch documenten aanmaken:

1. Open het Email Account
2. Stel **Append To** in:

| Append To | Effect |
|---|---|
| `Communication` | E-mail wordt opgeslagen als communicatie (standaard) |
| `Issue` | E-mail wordt een supportticket |
| `Lead` | E-mail wordt een nieuwe lead |
| `Opportunity` | E-mail wordt een nieuwe kans |

### Voorbeeld: Support-inbox

1. Maak een Email Account `support@[bedrijf].nl`
2. Stel in:
   - **Enable Incoming**: Ja
   - **Append To**: `Issue`
3. Elke inkomende e-mail op dit adres maakt automatisch een Issue aan
4. Antwoorden op de Issue worden via dit e-mailadres verstuurd

### Voorbeeld: Sales-inbox

1. Maak een Email Account `sales@[bedrijf].nl`
2. Stel in:
   - **Enable Incoming**: Ja
   - **Append To**: `Lead`
3. Elke inkomende e-mail van onbekende afzenders maakt een Lead aan
4. E-mails van bestaande klanten worden gekoppeld aan het klantrecord

### Communicatie Bekijken

De communicatiegeschiedenis is zichtbaar op meerdere plekken:

| Plek | Wat u ziet |
|---|---|
| **Klant/Leverancier > Activity** | Alle e-mails met deze partij |
| **Document > Comments/Activity** | E-mails gerelateerd aan dit document |
| **Email Inbox** (in ERPNext) | Alle inkomende e-mails |
| **Communication** lijst | Alle communicatie, filterbaar |

### Email Regels (Email Rules)

Voor geavanceerde routering:

1. Ga naar **Setup > Email Rule > New**
2. Stel regels in op basis van:
   - Afzender (e-mailadres of domein)
   - Onderwerp (bevat bepaalde tekst)
   - E-mailaccount (welke inbox)
3. Actie: toewijzen aan specifieke gebruiker, afdeling of DocType

### Tips

- **Reply-to adres** — Stel per DocType een reply-to adres in zodat antwoorden op de juiste plek binnenkomen
- **Email threading** — ERPNext groepeert gerelateerde e-mails automatisch (op basis van Message-ID en In-Reply-To headers)
- **Bijlagen** — Bijlagen bij inkomende e-mails worden automatisch opgeslagen bij het gekoppelde document
- **BCC** — Stel een BCC-adres in om een kopie van alle uitgaande mails te bewaren
- **Spam** — Configureer filters om spam te voorkomen (vooral bij openbare support-inboxen)

---

## Samenvatting Checklist

### Initieel Setup

- [ ] Apps geinstalleerd (erpnext, hrms, payments)
- [ ] Systeeminstellingen correct (taal, datum, valuta, tijdzone)
- [ ] Boekjaar aangemaakt
- [ ] Grootboekschema ingericht
- [ ] BTW-templates geconfigureerd

### Bedrijfsstructuur

- [ ] Company('s) aangemaakt
- [ ] Onderlinge verhoudingen (parent/child) ingesteld
- [ ] Inter-company suppliers/customers geconfigureerd
- [ ] Bankrekeningen gekoppeld

### Gebruikers

- [ ] User accounts aangemaakt
- [ ] Employees aangemaakt en gekoppeld aan Users
- [ ] Rollen toegewezen
- [ ] User Permissions ingesteld (bij multi-company)

### Templates

- [ ] Factuur Print Format aangemaakt en getest
- [ ] Urenstaat Print Format aangemaakt
- [ ] Email Templates aangemaakt (factuur, offerte, herinnering)
- [ ] Email Signatures ingesteld per gebruiker

### E-mail

- [ ] Uitgaand e-mailaccount geconfigureerd en getest
- [ ] Inkomend e-mailaccount geconfigureerd (optioneel)
- [ ] OAuth/App Password correct ingesteld
- [ ] Emails worden correct gekoppeld aan documenten
- [ ] Test-e-mail succesvol verstuurd

### Workflows

- [ ] Goedkeuringsworkflows gedefinieerd
- [ ] Workflow States aangemaakt
- [ ] Workflow Actions geconfigureerd
- [ ] Getest met testdocumenten

---

*Versie 1.0 — 24 maart 2026*
