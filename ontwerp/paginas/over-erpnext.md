---
titel: Wat is ERPNext?
type: Webpagina
route: /over-erpnext
versie: 1.0
datum: 2026-03-24
---

# Wat is ERPNext?

## Een compleet bedrijfssysteem, volledig open source

ERPNext is een Enterprise Resource Planning (ERP) systeem dat alle bedrijfsprocessen integreert in een platform. Het is gebouwd op het Frappe Framework en volledig open source onder de GNU GPLv3 licentie.

---

## Geschiedenis

ERPNext is in 2008 gestart door Rushabh Mehta in Mumbai, India, onder de naam "Web Notes". Het project groeide uit tot een volwaardig ERP-systeem dat inmiddels wordt gebruikt door bedrijven in meer dan 150 landen.

Het bedrijf achter ERPNext, Frappe Technologies, heeft bewust gekozen voor een open-source model. De overtuiging is dat bedrijfssoftware een nutsvoorziening zou moeten zijn — betaalbaar en toegankelijk voor iedereen.

**Mijlpalen:**
- 2008 — Start ontwikkeling als "Web Notes"
- 2011 — Hernoemd naar ERPNext
- 2014 — Lancering Frappe Cloud (hosting platform)
- 2018 — ERPNext v11 met verbeterde manufacturing
- 2022 — ERPNext v14 met modernere UI
- 2024 — ERPNext v15 met verbeterde performance

---

## Het Frappe Framework

ERPNext is gebouwd op het **Frappe Framework**, een full-stack webapplicatieplatform dat speciaal is ontworpen voor het bouwen van bedrijfsapplicaties.

### Technische Stack

| Component | Technologie |
|---|---|
| **Backend** | Python 3 |
| **Frontend** | JavaScript (eigen framework) |
| **Database** | MariaDB / PostgreSQL |
| **Webserver** | Gunicorn + Nginx |
| **Caching** | Redis |
| **Zoekmachine** | Whoosh / Elasticsearch (optioneel) |
| **Realtime** | Socket.io |
| **Taakplanning** | RQ (Redis Queue) |

### Kernconcepten

**DocType** — Het centrale concept in Frappe. Elke entiteit (factuur, klant, product) is een DocType. Door een DocType te definieren, genereert Frappe automatisch:
- Databasetabel
- REST API
- Formulier (UI)
- Lijst-weergave
- Rapportage
- Rechtenstructuur

**Hooks** — Uitbreidingspunten waarmee u code kunt laten uitvoeren bij bepaalde events (bijv. na het opslaan van een factuur).

**Custom Scripts** — Client-side (JavaScript) en server-side (Python) scripts waarmee u gedrag kunt aanpassen zonder de kerncode te wijzigen.

**Workflows** — Visuele workflows met goedkeuringsstappen, acties en condities.

**Print Formats** — Aanpasbare templates voor facturen, offertes en andere documenten, in HTML/Jinja2.

---

## Modules in Detail

### Boekhouding (Accounting)

De boekhoudmodule is het hart van ERPNext. Alle financiele transacties komen hier samen.

**Functies:**
- Dubbel boekhouden (debet/credit)
- Grootboek met onbeperkte diepte
- Meerdere bedrijven in een installatie (multi-company)
- Meerdere valuta's
- BTW-berekening en -rapportage
- Automatische bankreconciliatie
- Betalingsherinneringen
- Budgetbeheer per kostenplaats
- Financiele rapportages (balans, W&V, kasstroomoverzicht)
- Fiscale jaarafsluiting

**Nederlandse context:**
- BTW-tarieven 21%, 9%, 0% en verlegd
- SEPA-betalingen
- Integratie met Nederlandse banken (Rabobank, ABN AMRO, ING)
- KvK-gegevens bij bedrijfsprofiel

### Inkoop (Buying)

**Functies:**
- Leveranciersbeheer
- Offerteaanvragen (RFQ)
- Inkooporders
- Goederenontvangst
- Inkoopfacturen
- Leveranciersbeoordeling
- Automatische herhaalorders

### Verkoop (Selling)

**Functies:**
- Klantenbeheer
- Offertes
- Verkooporders
- Verkoopfacturen
- Prijslijsten en kortingen
- Verkoopanalyse
- Commissieberekening

### Voorraad (Stock / Inventory)

**Functies:**
- Meerdere magazijnen
- Artikelbeheer met varianten
- Serienummers en batchnummers
- Voorraadwaardering (FIFO, gemiddelde prijs)
- Voorraadoverdracht tussen magazijnen
- Inventarisatie
- Automatische herbestelpunten
- Barcode-ondersteuning

### CRM (Customer Relationship Management)

**Functies:**
- Lead-beheer
- Kansen (opportunities)
- Verkooppijplijn
- E-mailintegratie
- Activiteitenlog
- Campagnebeheer
- Klantportaal

### HR (Human Resources)

**Functies:**
- Medewerkersprofielen
- Verlofbeheer
- Aanwezigheidsregistratie
- Salarisadministratie
- Onkostendeclaraties
- Werving en selectie
- Opleidingen
- Beoordelingen

**Nederlandse context:**
- Vakantiegeld (8% regeling)
- Vakantiedagen (wettelijk minimum 20 dagen)
- Loonheffing en sociale premies
- Werkkostenregeling (WKR)

### Projecten

**Functies:**
- Projectbeheer met taken
- Tijdregistratie (timesheets)
- Gantt-diagrammen
- Projectbudgetten
- Facturatie op basis van tijdregistratie
- Projectrapportages

### Productie (Manufacturing)

**Functies:**
- Stuklijsten (Bill of Materials)
- Werkorders
- Productieplanning
- Subcontracting
- Kwaliteitscontrole
- Onderhoudsbeheer

### Assets (Vaste Activa)

**Functies:**
- Activaregistratie
- Automatische afschrijvingen (lineair, degressief)
- Activaverplaatsing
- Activaverkoop/-afvoer
- Afschrijvingsrapportage

### Website / CMS

**Functies:**
- Webpagina's met WYSIWYG-editor
- Blog
- Webformulieren
- Webshop (e-commerce)
- Klantportaal
- SEO-instellingen

---

## Vergelijking met Andere Systemen

### ERPNext vs. SAP Business One

| Aspect | ERPNext | SAP Business One |
|---|---|---|
| **Licentie** | Open source (gratis) | Proprietary (duur) |
| **Implementatie** | Weken | Maanden |
| **Complexiteit** | Gemiddeld | Hoog |
| **Aanpasbaarheid** | Zeer hoog (open code) | Beperkt |
| **Community** | Actief, open | Gesloten, via partners |
| **Geschikt voor** | MKB, startups | Middelgroot tot groot |

### ERPNext vs. Exact Online

| Aspect | ERPNext | Exact Online |
|---|---|---|
| **Licentie** | Open source | Per gebruiker/maand |
| **Modules** | Alles inbegrepen | Per module betalen |
| **Hosting** | Zelf of cloud | Alleen cloud |
| **Aanpasbaarheid** | Volledig | Beperkt (API) |
| **Data-export** | Volledig | Beperkt |
| **Nederlandse markt** | Groeiend | Marktleider |

### ERPNext vs. Odoo

| Aspect | ERPNext | Odoo |
|---|---|---|
| **Licentie** | GNU GPLv3 (echt open) | LGPL + proprietary (Enterprise) |
| **Kernmodules** | Alles gratis | Community: beperkt, Enterprise: betaald |
| **Framework** | Frappe (Python) | Odoo (Python) |
| **Complexiteit** | Eenvoudiger | Complexer |
| **Community** | Kleiner, hechter | Groter, gefragmenteerd |
| **Aanpak** | Alles-in-een | Modulair |

### ERPNext vs. AFAS

| Aspect | ERPNext | AFAS |
|---|---|---|
| **Licentie** | Open source | Proprietary |
| **Focus** | Internationaal, breed | Nederland, HR-focus |
| **Hosting** | Zelf of cloud | Alleen cloud |
| **Prijs** | Gratis + hosting | Hoog |
| **NL-specifiek** | Handmatig inrichten | Kant-en-klaar |
| **Flexibiliteit** | Zeer hoog | Gemiddeld |

---

## Voordelen op een Rij

### Voor de Ondernemer
- Geen licentiekosten — investeer in implementatie, niet in software
- Alle bedrijfsdata op een plek — geen dubbele invoer
- Realtime inzicht in financien, voorraad en verkoop
- Schaalbaar — groeit mee met uw bedrijf

### Voor de Boekhouder
- Dubbel boekhouden met automatische tegenboekingen
- BTW-rapportage per periode
- Bankreconciliatie
- Multi-company boekhouding
- Audit trail op elke transactie

### Voor IT
- Moderne webapplicatie — geen dikke clients
- REST API voor integraties
- Docker-support voor eenvoudige deployment
- Actieve ontwikkelgemeenschap
- Python en JavaScript — gangbare technologieen

### Voor HR
- Complete personeelsadministratie
- Verlof- en aanwezigheidsbeheer
- Salarisberekening
- Onkostendeclaraties met goedkeuringsworkflow
- Medewerkersportaal

---

## Technische Vereisten

### Self-hosted (Minimaal)

| Component | Vereiste |
|---|---|
| **OS** | Ubuntu 22.04+ / Debian 12+ |
| **CPU** | 2 cores |
| **RAM** | 4 GB |
| **Opslag** | 40 GB SSD |
| **Python** | 3.10+ |
| **Node.js** | 18+ |
| **Database** | MariaDB 10.6+ |

### Aanbevolen voor Productie

| Component | Vereiste |
|---|---|
| **CPU** | 4+ cores |
| **RAM** | 8+ GB |
| **Opslag** | 100+ GB SSD |
| **Backup** | Dagelijkse automatische backups |
| **SSL** | Let's Encrypt of eigen certificaat |

### Cloud Opties

- **Frappe Cloud** — Officieel hostingplatform van Frappe Technologies
- **Zelf hosten** — Op een VPS bij bijv. Hetzner, DigitalOcean, of TransIP
- **Docker** — Officieel Docker-image beschikbaar

---

## Aan de Slag

### Optie 1: Frappe Cloud (Eenvoudigst)

1. Ga naar [frappecloud.com](https://frappecloud.com)
2. Maak een account aan
3. Kies een plan
4. Uw ERPNext-omgeving is binnen minuten klaar

### Optie 2: Zelf Installeren

```bash
# Installeer Bench (het CLI-beheergereedschap)
pip install frappe-bench

# Maak een nieuwe bench aan
bench init erpnext-bench
cd erpnext-bench

# Installeer ERPNext
bench get-app erpnext
bench new-site mijnbedrijf.nl
bench --site mijnbedrijf.nl install-app erpnext

# Start de ontwikkelserver
bench start
```

### Optie 3: Docker

```bash
git clone https://github.com/frappe/frappe_docker
cd frappe_docker
docker compose -f pwd.yml up -d
```

---

*Versie 1.0 — 24 maart 2026*
