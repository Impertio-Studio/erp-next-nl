---
titel: ERPNext Implementatie — Stappenplan
type: Webpagina
route: /implementatie
versie: 1.0
datum: 2026-03-24
---

# ERPNext Implementatie

## Een Gestructureerd Stappenplan

Een succesvolle ERPNext-implementatie vraagt om een gestructureerde aanpak. Of u nu zelf aan de slag gaat of een implementatiepartner inschakelt — dit stappenplan helpt u door het proces.

---

## Overzicht van de Fasen

```
Fase 1          Fase 2          Fase 3          Fase 4          Fase 5
Voorbereiding → Inrichting    → Datamigrate  → Testen       → Live Gang
(1-2 weken)     (2-4 weken)     (1-2 weken)    (1-2 weken)    (doorlopend)
```

---

## Fase 1: Voorbereiding

**Doel:** Helder krijgen wat u nodig heeft en hoe u ERPNext wilt inzetten.

### Stap 1.1: Doelstellingen Bepalen

Beantwoord deze vragen:

- Welke bedrijfsprocessen wilt u in ERPNext beheren?
- Welke systemen vervangt ERPNext? (Excel, Exact, los boekhoudpakket?)
- Hoeveel gebruikers gaan met ERPNext werken?
- Heeft u meerdere bedrijfsentiteiten?
- Welke integraties zijn nodig? (bank, webshop, etc.)

### Stap 1.2: Hostingkeuze

| Optie | Voor wie | Kosten | Beheer |
|---|---|---|---|
| **Frappe Cloud** | Snel starten, geen technische kennis | Vanaf ~$50/maand | Volledig beheerd |
| **OpenCompany246** | Europese hosting met persoonlijke service | Op aanvraag | Beheerd |
| **Zelf hosten (VPS)** | Technisch onderlegd, maximale controle | Vanaf ~EUR 5/maand | Zelf |
| **Zelf hosten (Docker)** | Developers, testomgevingen | Variabel | Zelf |

### Stap 1.3: Testomgeving Opzetten

**Start altijd met een testomgeving.** Richt hier alles in voordat u live gaat.

Via Frappe Cloud:
1. Ga naar [frappecloud.com](https://frappecloud.com)
2. Maak een account aan
3. Kies "New Site" → selecteer ERPNext
4. Uw testomgeving is binnen minuten klaar

### Stap 1.4: Team Samenstellen

| Rol | Verantwoordelijkheid |
|---|---|
| **Projectleider** | Overzicht, planning, beslissingen |
| **Key user Financieel** | Grootboek, BTW, bankzaken |
| **Key user Operationeel** | Inkoop, verkoop, voorraad |
| **IT-contactpersoon** | Technische zaken, integraties |

---

## Fase 2: Inrichting

**Doel:** ERPNext configureren voor uw bedrijfssituatie.

### Stap 2.1: Setup Wizard

Bij de eerste keer inloggen doorloopt u de Setup Wizard:

1. **Taal** → Nederlands
2. **Land** → Nederland
3. **Bedrijfsnaam** → Uw officieel handelsnaam
4. **Afkorting** → Korte code (bijv. "3" of "BV")
5. **Boekjaar** → 1 januari - 31 december
6. **Valuta** → EUR

### Stap 2.2: Bedrijfsgegevens Invullen

- KvK-nummer
- BTW-identificatienummer
- Vestigingsadres
- Contactgegevens
- Registratie-details

[Handleiding: Bedrijfsinrichting →](/werkboeken/bedrijfsinrichting)

### Stap 2.3: Grootboekschema

Het standaard ERPNext-grootboekschema is een goed beginpunt. Pas het aan voor de Nederlandse situatie:

1. Verwijder rekeningen die u niet nodig heeft
2. Voeg Nederlandse rekeningen toe (BTW, vakantiegeld, etc.)
3. Overweeg RGS-nummering voor toekomstige compatibiliteit

[Handleiding: Grootboekschema →](/werkboeken/grootboekschema)

### Stap 2.4: BTW Configureren

Richt de Nederlandse BTW-tarieven in:

1. BTW-rekeningen aanmaken (21%, 9%, 0%, verlegd)
2. Inkoop- en verkoopbelastingsjablonen instellen
3. Tax Categories koppelen
4. Test met een proeffactuur

[Handleiding: BTW-inrichting →](/werkboeken/btw-inrichting)

### Stap 2.5: Bankrekeningen Koppelen

1. Bank aanmaken (Rabobank, ABN AMRO, ING, etc.)
2. Bankrekening(en) configureren met IBAN
3. Koppelen aan grootboekrekening
4. SEPA-export testen

[Handleiding: Bankkoppelingen →](/werkboeken/bankkoppelingen)

### Stap 2.6: Overige Instellingen

- **Betalingstermijnen** → bijv. "21 Dagen", "30 Dagen"
- **Nummerreeksen** → Factuur- en ordernummers
- **Print Formats** → Factuursjablonen met KvK/BTW-nummer
- **E-mail** → SMTP-instellingen voor e-mailnotificaties
- **Gebruikers en rollen** → Accounts aanmaken, rechten toewijzen

---

## Fase 3: Datamigratie

**Doel:** Bestaande data overzetten naar ERPNext.

### Stap 3.1: Stamgegevens Importeren

| Data | Formaat | Import via |
|---|---|---|
| Klanten | CSV | Data Import Tool |
| Leveranciers | CSV | Data Import Tool |
| Artikelen | CSV | Data Import Tool |
| Medewerkers | CSV | Data Import Tool |
| Grootboekrekeningen | CSV | Chart of Accounts Import |

### Stap 3.2: Openingsbalans

1. Bepaal de overgangsdatum (bijv. 1 januari)
2. Stel de openingsbalans op in uw huidige systeem
3. Voer de openingsbalans in via Journal Entry
4. Controleer: activa = passiva + eigen vermogen

### Stap 3.3: Openstaande Posten

- **Openstaande verkoopfacturen** → Als Sales Invoice (met "Is Opening Entry")
- **Openstaande inkoopfacturen** → Als Purchase Invoice (met "Is Opening Entry")
- **Voorraad** → Via Stock Reconciliation

### Stap 3.4: Historische Data (Optioneel)

Overweeg of u historische data wilt importeren. Vaak is het beter om:
- Met een schone lei te beginnen op de overgangsdatum
- Historische data te bewaren in het oude systeem (7 jaar bewaarplicht)
- Alleen samenvattingen (openingsbalans) over te nemen

---

## Fase 4: Testen

**Doel:** Controleren of alles correct werkt voordat u live gaat.

### Stap 4.1: Functionele Tests

Test elk proces van begin tot eind:

| Test | Beschrijving | Status |
|---|---|---|
| Verkooporder → Factuur → Betaling | Complete verkoopworkflow | |
| Inkooporder → Ontvangst → Factuur → Betaling | Complete inkoopworkflow | |
| BTW-berekening | Correct tarief op facturen | |
| SEPA-export | XML genereren en controleren | |
| Bankreconciliatie | Afschrift importeren en matchen | |
| Print Format | Factuur voldoet aan NL-eisen | |
| Rapportages | Balans, W&V, BTW-overzicht | |

### Stap 4.2: Gebruikerstraining

Train de key users op hun specifieke werkgebied:

- Boekhouder → Facturen, betalingen, bankreconciliatie, rapportages
- Inkoper → Leveranciers, inkooporders, goederenontvangst
- Verkoper → Offertes, verkooporders, CRM
- Manager → Dashboards, rapportages, goedkeuringen

### Stap 4.3: Parallelle Run (Aanbevolen)

Draai ERPNext 2-4 weken parallel naast uw huidige systeem:

1. Voer alle transacties in beide systemen in
2. Vergelijk de resultaten aan het einde van de periode
3. Los verschillen op
4. Pas aan waar nodig

---

## Fase 5: Live Gang

### Stap 5.1: Go-Live Checklist

- [ ] Alle stamgegevens zijn gecontroleerd
- [ ] Openingsbalans is correct ingevoerd
- [ ] BTW-berekeningen zijn getest en correct
- [ ] SEPA-export werkt met uw bank
- [ ] Print Formats zijn goedgekeurd
- [ ] Gebruikers zijn getraind
- [ ] Backups zijn geconfigureerd
- [ ] E-mailnotificaties werken

### Stap 5.2: Overstap

1. Sluit de boekhouding in het oude systeem af
2. Maak een laatste export/backup van het oude systeem
3. Voer de definitieve openingsbalans in
4. Start met live boeken in ERPNext
5. Communiceer de overstap naar het team

### Stap 5.3: Nazorg

De eerste weken na go-live:

- Dagelijkse controle op boekingen
- Snelle respons op vragen van gebruikers
- Wekelijkse evaluatie met key users
- Aanpassingen doorvoeren waar nodig

---

## Implementeren met AI-ondersteuning (Claude)

Een uniek voordeel van ERPNext is dat u **Claude** (AI-assistent) kunt inzetten bij de implementatie en het dagelijks gebruik:

### Tijdens de Implementatie

- **Configuratie-advies** — Vraag Claude welke instellingen u nodig heeft
- **Datamigratie** — Claude kan CSV-bestanden voorbereiden en valideren
- **Script-ontwikkeling** — Custom scripts laten schrijven door Claude
- **Print Formats** — Factuursjablonen laten ontwerpen
- **Probleemoplossing** — Foutmeldingen analyseren en oplossen

### In het Dagelijks Gebruik

- **Factuurverwerking** — Upload factuur-PDFs naar Claude, die maakt automatisch Purchase Invoices aan in ERPNext
- **Rapportages** — Vraag Claude om specifieke analyses te draaien
- **Configuratie-aanpassingen** — Nieuwe BTW-templates, workflows of DocTypes laten aanmaken
- **Troubleshooting** — Bij problemen kan Claude direct de ERPNext API raadplegen

### Hoe Werkt Dat?

Claude kan via de ERPNext API direct communiceren met uw ERPNext-instance:

1. **MCP-server** — Koppel Claude aan uw ERPNext via een MCP-server (Model Context Protocol)
2. **API-key** — Genereer een API-key in ERPNext voor Claude
3. **Conversatie** — Geef Claude instructies in gewoon Nederlands

**Voorbeeld:**
> "Claude, maak een inkoopfactuur aan voor leverancier PCheck B.V., factuurnummer 2026-0042, bedrag EUR 1.500 exclusief 21% BTW, voor softwarelicenties."

Claude maakt dan automatisch de Purchase Invoice aan in ERPNext, kiest de juiste BTW-template, en hangt eventueel de PDF bij.

---

## Tijdlijn

### Klein Bedrijf (1-5 gebruikers)

| Fase | Duur | Toelichting |
|---|---|---|
| Voorbereiding | 1 week | Doelstellingen, hosting kiezen |
| Inrichting | 1-2 weken | Basisconfiguratie |
| Datamigratie | 1 week | Stamgegevens, openingsbalans |
| Testen | 1 week | Functionele tests |
| **Totaal** | **4-5 weken** | |

### Middelgroot Bedrijf (5-50 gebruikers)

| Fase | Duur | Toelichting |
|---|---|---|
| Voorbereiding | 2 weken | Uitgebreide analyse |
| Inrichting | 3-4 weken | Configuratie + maatwerk |
| Datamigratie | 2 weken | Stamgegevens + historische data |
| Testen | 2 weken | Functioneel + parallelle run |
| **Totaal** | **9-10 weken** | |

---

## Hulp Nodig?

| Optie | Voor wie | Contact |
|---|---|---|
| **Zelf doen** | Technisch onderlegde gebruikers | Gebruik deze werkboeken |
| **PRILK** | Implementatiebegeleiding NL-administratie | [Contact →](/partners) |
| **Impertio Studio** | Custom development en integraties | [Contact →](/partners) |
| **OpenCompany246** | Beheerde hosting | [Contact →](/partners) |
| **Community** | Vragen en tips | [Forum →](/forum) |

---

*Versie 1.0 — 24 maart 2026*
