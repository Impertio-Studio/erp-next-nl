---
titel: ERPNext voor Nederland
type: Webpagina
route: /nederland
versie: 1.0
datum: 2026-03-24
---

# ERPNext voor Nederland

## Uw Bedrijfsadministratie, Volledig in Eigen Hand

ERPNext is een internationaal ERP-systeem dat uitstekend geschikt is voor Nederlandse bedrijven. Op deze pagina leest u hoe ERPNext past binnen de Nederlandse bedrijfsvoering, welke aanpassingen nodig zijn, en waarom steeds meer Nederlandse ondernemers kiezen voor deze open-source oplossing.

---

## Nederlandse Administratie met ERPNext

### Boekhouding

De Nederlandse boekhouding kent specifieke eisen die ERPNext volledig ondersteunt:

**BTW (Omzetbelasting)**
Nederland kent vier BTW-regimes die in ERPNext worden geconfigureerd:

| Regime | Tarief | Toepassing in ERPNext |
|---|---|---|
| Hoog tarief | 21% | Standaard template — automatisch bij NL-leveranciers |
| Laag tarief | 9% | Apart template — voedsel, boeken, cultuur |
| Nultarief | 0% | Export en intracommunautaire leveringen |
| Verlegd | 21% verlegd | Onderaanneming bouw, bepaalde B2B-diensten |

Daarnaast ondersteunt ERPNext de **verleggingsregeling** (reverse charge) voor intracommunautaire verwervingen en de **kleineondernemersregeling (KOR)**.

[Uitgebreide BTW-handleiding →](btw-inrichting.md)

**Factuurvereisten**
Nederlandse facturen moeten voldoen aan wettelijke eisen. ERPNext Print Formats kunnen worden aangepast om te bevatten:

- Naam en adres van leverancier en afnemer
- KvK-nummer
- BTW-identificatienummer
- Factuurnummer (doorlopend)
- Factuurdatum
- Omschrijving van goederen/diensten
- Bedrag exclusief BTW
- Toegepast BTW-tarief en BTW-bedrag
- Totaalbedrag inclusief BTW
- Betalingstermijn
- IBAN-nummer

**Jaarrekening**
ERPNext genereert de financiele overzichten die nodig zijn voor de Nederlandse jaarrekening:
- Balans (activa en passiva)
- Winst- en verliesrekening
- Toelichting op de jaarrekening (handmatig aanvullen)

### Bankieren

**SEPA-betalingen**
ERPNext ondersteunt het genereren van SEPA XML-bestanden (pain.001) voor batchbetalingen. Dit werkt met alle grote Nederlandse banken:

| Bank | Status | Formaat |
|---|---|---|
| Rabobank | Werkend | pain.001.001.09 |
| ABN AMRO | Compatible | pain.001.001.09 |
| ING | Compatible | pain.001.001.09 |
| Bunq | Werkend | pain.001.001.09 |
| Triodos | Compatible | pain.001.001.09 |

[SEPA Workflow handleiding →](payment-order-sepa-workflow.md)

**Bankafschriften**
MT940- en CAMT.053-bestanden van Nederlandse banken kunnen worden geimporteerd voor automatische bankreconciliatie.

**iDEAL / Mollie**
Voor webshops kan ERPNext worden gekoppeld aan Mollie of andere Nederlandse payment providers voor iDEAL, Bancontact en andere lokale betaalmethoden.

### Salarisadministratie

ERPNext heeft een salarismodule die kan worden ingericht voor de Nederlandse situatie:

| Component | ERPNext-ondersteuning |
|---|---|
| Brutoloon | Standaard |
| Loonheffing | Via salary component |
| Sociale premies (WW, WIA, ZVW) | Via salary components |
| Vakantiegeld (8%) | Via salary component + provisie |
| Vakantiedagen | Verlofmodule |
| Pensioenpremie | Via salary component |
| Werkkostenregeling (WKR) | Via aparte kostenrekening |
| Reiskostenvergoeding | Via expense claims |
| Thuiswerkvergoeding | Via salary component |
| 30%-regeling | Via salary component |

**Let op:** Voor de loonaangifte bij de Belastingdienst is doorgaans aanvullende software of een salarisadministrateur nodig. ERPNext vervangt niet een volledig Nederlands loonadministratiesysteem, maar kan wel de basis bieden.

---

## Bedrijfstypen

### Bouw & Techniek

ERPNext is bijzonder geschikt voor bouw- en ingenieursbedrijven:

- **Projectadministratie** — Kosten per project bijhouden
- **Onderaanneming** — BTW-verleggingsregeling automatisch
- **Inkoopbeheer** — Materialen bestellen en ontvangen per project
- **Tijdregistratie** — Uren schrijven op projecten
- **Facturatie** — Op basis van vorderingen of nacalculatie

*Voorbeeld: Een Nederlands ingenieursbureau gebruikt ERPNext voor de volledige administratie, inclusief SEPA-betalingen via hun bank.*

### Handel & Distributie

- **Voorraadbeheer** — Meerdere magazijnen, serienummers, batchnummers
- **Inkoop** — Automatische herbestelpunten, leveranciersvergelijking
- **Verkoop** — Offertes, orders, facturen in een workflow
- **Logistiek** — Pakbonnen, verzendlabels
- **E-commerce** — Ingebouwde webshop of koppeling met WooCommerce/Shopify

### ICT & Dienstverlening

- **CRM** — Klantbeheer, leads, kansen
- **Projecten** — Urenregistratie, facturatie op basis van uren
- **Abonnementen** — Terugkerende facturatie
- **SLA-beheer** — Service Level Agreements bijhouden
- **Helpdesk** — Ticketsysteem voor klantvragen

### Productie & Maakindustrie

- **Stuklijsten** — Bill of Materials met sub-assemblages
- **Werkorders** — Productieplanning en -uitvoering
- **Kwaliteitscontrole** — Inspecties bij ontvangst en productie
- **Subcontracting** — Uitbesteding met materiaallevering
- **MRP** — Material Requirements Planning

### Horeca

- **Point of Sale** — Kassasysteem in de browser
- **Voorraadbeheer** — Ingredienten en dranken
- **Recepturen** — Via BOM (stuklijsten)
- **Personeelsplanning** — Via HR-module

### Agrarisch

- **Perceelbeheer** — Via projecten of custom DocTypes
- **Inkoop** — Zaaigoed, voer, bestrijdingsmiddelen
- **Verkoop** — Oogst, veiling, directe verkoop
- **Voorraadbeheer** — Opslag, houdbaarheid (batchnummers)

### Non-profit & Stichtingen

- **Boekhouding** — Gescheiden fondsen via kostenplaatsen
- **Donateurbeheer** — Via CRM
- **Subsidieverantwoording** — Via projectrapportages
- **Vrijwilligersbeheer** — Via HR-module (aangepast)

---

## Compliance & Wetgeving

### AVG / GDPR

ERPNext kan AVG-conform worden ingezet:

- **Data minimalisatie** — Sla alleen noodzakelijke gegevens op
- **Recht op vergetelheid** — Persoonsgegevens kunnen worden geanonimiseerd
- **Data export** — Alle gegevens kunnen worden geexporteerd
- **Toegangscontrole** — Fijnmazige rechtenstructuur per rol
- **Audit trail** — Alle wijzigingen worden gelogd

Bij self-hosting heeft u volledige controle over waar de data wordt opgeslagen (bijv. een Nederlandse of Europese server).

### KvK-registratie

In ERPNext slaat u de KvK-gegevens op in het Company-record:
- KvK-nummer
- Vestigingsnummer
- RSIN
- Rechtsvorm
- SBI-codes

Deze gegevens worden automatisch opgenomen in facturen en correspondentie via Print Formats.

### UBL / e-Facturatie

De Nederlandse overheid stimuleert e-facturatie via het UBL-formaat (Universal Business Language). ERPNext kan worden uitgebreid met een UBL-exportmodule voor:
- Peppol-netwerk
- Overheidsinkoop (via Digipoort)
- B2B e-facturatie

---

## Integraties voor Nederland

| Dienst | Type | Status |
|---|---|---|
| **Rabobank** | SEPA betalingen | Werkend (via custom script) |
| **ABN AMRO** | SEPA betalingen | Compatible |
| **ING** | SEPA betalingen | Compatible |
| **Mollie** | Online betalingen | Via integratie |
| **PostNL** | Verzending | Via API-koppeling |
| **Bol.com** | Marketplace | Via custom integratie |
| **Belastingdienst** | BTW-aangifte | Handmatig (data uit rapporten) |
| **KvK** | Bedrijfsgegevens | Handmatig |
| **Exact Online** | Boekhouding | Via data-export/import |

---

## Veelgestelde Vragen

### Is ERPNext geschikt voor de Nederlandse markt?

Ja. ERPNext ondersteunt meerdere valuta's, belastingregimes en talen. De Nederlandse BTW-structuur, SEPA-betalingen en boekhoudstandaarden kunnen volledig worden geconfigureerd. Het vergt wel een initieel inrichtingstraject — daar helpen onze werkboeken bij.

### Kan ik mijn huidige boekhouding overzetten?

Ja. ERPNext heeft importfuncties voor:
- Grootboekschema (via CSV)
- Openstaande facturen
- Klanten en leveranciers
- Artikelen en voorraad
- Openingsbalans

### Is ERPNext geschikt voor mijn accountant?

Uw accountant kan een eigen login krijgen met alleen leestoegang tot de financiele modules. Rapportages (balans, W&V, BTW-overzicht) zijn direct beschikbaar. Voor de jaarrekening kunt u data exporteren in een formaat dat uw accountant kan verwerken.

### Hoe zit het met support?

- **Community support** — Gratis via het ERPNext-forum en deze website
- **Frappe Cloud** — Inclusief support bij hosting
- **Implementatiepartners** — Diverse partners in Europa
- **Zelf doen** — Met de documentatie op deze site

### Voldoet ERPNext aan de fiscale bewaarplicht?

De Nederlandse fiscale bewaarplicht vereist dat u uw administratie 7 jaar bewaart. Met ERPNext:
- Alle transacties worden permanent opgeslagen
- Er is een audit trail op elke wijziging
- Submitted documenten kunnen niet worden gewijzigd
- Backups zorgen voor aanvullende veiligheid

---

## Aan de Slag met ERPNext in Nederland

1. **Lees de werkboeken** — Begin met [Bedrijfsinrichting](bedrijfsinrichting.md)
2. **Richt BTW in** — Volg het [BTW-werkboek](btw-inrichting.md)
3. **Koppel uw bank** — Zie [Bankkoppelingen](bankkoppelingen.md)
4. **Stel uw grootboek samen** — Gebruik het [Grootboekschema werkboek](grootboekschema.md)
5. **Start met factureren** — Volg de [Payment Order workflow](payment-order-sepa-workflow.md)

---

*Versie 1.0 — 24 maart 2026*
