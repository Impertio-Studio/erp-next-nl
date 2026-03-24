---
titel: ERPNext Nederland — Siteontwerp
versie: 0.1
datum: 2026-03-24
platform: Frappe Website Module
toon: zakelijk-professioneel
---

# ERPNext Nederland — Siteontwerp

## 1. Sitestructuur

```
/                           → Homepage
/over-erpnext               → Wat is ERPNext?
/modules                    → Overzicht van modules
/modules/{module}           → Per module (Boekhouding, Inkoop, Verkoop, etc.)
/nederland                  → ERPNext voor Nederland
/nederland/btw              → BTW-inrichting
/nederland/kvk              → KvK-integratie
/nederland/boekhouding      → Nederlandse boekhouding (RGS, grootboekschema)
/nederland/salarisadmin     → Salarisadministratie NL
/werkboeken                 → Overzicht werkboeken
/werkboeken/{slug}          → Individueel werkboek (stap-voor-stap)
/voorbeelden                → Overzicht voorbeeldimplementaties
/voorbeelden/{slug}         → Individueel voorbeeld / case study
/forum                      → Nederlandstalig forum
/forum/{categorie}          → Forumcategorie
/forum/{categorie}/{post}   → Forumbericht met reacties
/blog                       → Nieuws en artikelen
/blog/{slug}                → Blogbericht
/contact                    → Contactpagina
```

## 2. Contentmodel (Custom DocTypes)

### 2.1 Webpagina (bestaand Frappe DocType)
Gebruikt voor: Homepage, Over ERPNext, Contact, statische pagina's.

### 2.2 Module-beschrijving (nieuw)
| Veld              | Type          | Toelichting                        |
|-------------------|---------------|------------------------------------|
| naam              | Data          | Modulenaam (NL)                    |
| slug              | Data          | URL-pad                            |
| samenvatting      | Small Text    | Korte beschrijving                 |
| inhoud            | Text Editor   | Volledige beschrijving             |
| icoon             | Attach Image  | Module-icoon                       |
| volgorde          | Int           | Sortering op overzichtspagina      |
| gerelateerd_aan   | Link → Module | Koppeling naar ERPNext-module      |

### 2.3 Werkboek
| Veld              | Type          | Toelichting                        |
|-------------------|---------------|------------------------------------|
| titel             | Data          | Titel van het werkboek             |
| slug              | Data          | URL-pad                            |
| categorie         | Select        | NL-specifiek / Algemeen / Module   |
| moeilijkheidsgraad| Select        | Beginner / Gevorderd / Expert      |
| samenvatting      | Small Text    | Korte beschrijving                 |
| doelgroep         | MultiSelect   | Ondernemer / Boekhouder / IT       |
| stappen           | Table → Werkboekstap | Geordende stappen           |
| gepubliceerd      | Check         | Zichtbaar op website               |

### 2.4 Werkboekstap (child table)
| Veld              | Type          | Toelichting                        |
|-------------------|---------------|------------------------------------|
| stap_nummer       | Int           | Volgnummer                         |
| titel             | Data          | Staptitel                          |
| instructie        | Text Editor   | Uitleg met screenshots             |
| tip               | Small Text    | Optionele tip of waarschuwing      |

### 2.5 Voorbeeld / Case Study
| Veld              | Type          | Toelichting                        |
|-------------------|---------------|------------------------------------|
| titel             | Data          | Titel                              |
| slug              | Data          | URL-pad                            |
| branche           | Select        | Handel / Dienstverlening / etc.    |
| bedrijfsgrootte   | Select        | ZZP / MKB / Grootbedrijf          |
| samenvatting      | Small Text    | Korte beschrijving                 |
| inhoud            | Text Editor   | Volledige case study               |
| gebruikte_modules | MultiSelect   | Welke modules worden gebruikt      |
| screenshots       | Table → Attach| Afbeeldingen                       |
| gepubliceerd      | Check         | Zichtbaar op website               |

### 2.6 Forumbericht
| Veld              | Type          | Toelichting                        |
|-------------------|---------------|------------------------------------|
| titel             | Data          | Onderwerp                          |
| slug              | Data          | URL-pad                            |
| categorie         | Link → Forumcategorie | Categorie                 |
| auteur            | Link → User   | Geplaatst door                     |
| inhoud            | Text Editor   | Berichtinhoud                      |
| datum             | Datetime      | Plaatsingsdatum                    |
| reacties          | Table → Forumreactie | Reacties                    |
| opgelost          | Check         | Markering voor opgeloste vragen    |
| vastgepind        | Check         | Bovenaan categorie tonen           |

### 2.7 Forumreactie (child table)
| Veld              | Type          | Toelichting                        |
|-------------------|---------------|------------------------------------|
| auteur            | Link → User   | Geplaatst door                     |
| inhoud            | Text Editor   | Reactie-inhoud                     |
| datum             | Datetime      | Plaatsingsdatum                    |
| is_oplossing      | Check         | Gemarkeerd als oplossing           |

### 2.8 Forumcategorie
| Veld              | Type          | Toelichting                        |
|-------------------|---------------|------------------------------------|
| naam              | Data          | Categorienaam                      |
| slug              | Data          | URL-pad                            |
| beschrijving      | Small Text    | Korte toelichting                  |
| volgorde          | Int           | Sortering                          |

## 3. Gebruikersrollen

| Rol               | Rechten                                          |
|-------------------|--------------------------------------------------|
| Bezoeker (gast)   | Alle pagina's, werkboeken, voorbeelden lezen     |
|                   | Forum lezen                                      |
| Lid               | Forum: berichten plaatsen en reageren             |
|                   | Eigen berichten bewerken                         |
| Redacteur         | Werkboeken, voorbeelden en pagina's beheren      |
|                   | Blog schrijven                                   |
|                   | Forumberichten modereren                         |
| Beheerder         | Alles + site-instellingen en gebruikersbeheer    |

## 4. Paginaindelingen (wireframes)

### 4.1 Homepage
```
┌─────────────────────────────────────────────────┐
│  Logo    Navigatie: Modules | NL | Werkboeken   │
│          | Voorbeelden | Forum | Blog | Contact  │
├─────────────────────────────────────────────────┤
│                                                  │
│   ERPNext Nederland                              │
│   Dé Nederlandstalige bron voor ERPNext          │
│                                                  │
│   [Aan de slag]        [Werkboeken bekijken]     │
│                                                  │
├──────────┬──────────┬───────────────────────────┤
│ Modules  │ NL-setup │ Laatste werkboeken         │
│ ○ Boekh. │ ○ BTW    │ ┌───────────────────┐     │
│ ○ Inkoop │ ○ KvK    │ │ Werkboek 1        │     │
│ ○ Verk.  │ ○ RGS    │ │ Werkboek 2        │     │
│ ○ Voorr. │ ○ Salar. │ │ Werkboek 3        │     │
│          │          │ └───────────────────┘     │
├──────────┴──────────┴───────────────────────────┤
│ Laatste forumberichten          Laatste blog     │
│ ┌─────────────────────┐  ┌──────────────────┐   │
│ │ Vraag 1             │  │ Artikel 1        │   │
│ │ Vraag 2             │  │ Artikel 2        │   │
│ └─────────────────────┘  └──────────────────┘   │
├─────────────────────────────────────────────────┤
│  Footer: Links | © ERPNext Nederland            │
└─────────────────────────────────────────────────┘
```

### 4.2 Werkboekpagina
```
┌─────────────────────────────────────────────────┐
│  Navigatie                                       │
├─────────────┬───────────────────────────────────┤
│ Inhoudsopg. │  Werkboektitel                     │
│             │  Categorie | Niveau | Doelgroep    │
│ 1. Stap 1   │                                    │
│ 2. Stap 2   │  Samenvatting                      │
│ 3. Stap 3   │                                    │
│ 4. Stap 4   │  ─────────────────────────────     │
│             │                                    │
│             │  Stap 1: Titel                     │
│             │  Instructie met screenshots         │
│             │  💡 Tip: ...                        │
│             │                                    │
│             │  Stap 2: Titel                     │
│             │  ...                               │
├─────────────┴───────────────────────────────────┤
│  ← Vorige werkboek    Volgende werkboek →        │
└─────────────────────────────────────────────────┘
```

### 4.3 Forumoverzicht
```
┌─────────────────────────────────────────────────┐
│  Navigatie                                       │
├─────────────────────────────────────────────────┤
│  Forum                          [Nieuw bericht]  │
├──────────────┬──────────────────────────────────┤
│ Categorieën  │  Recente berichten                │
│              │                                   │
│ ● Algemeen   │  Titel                Auteur  Dat │
│ ● Boekhouding│  ────────────────────────────── │
│ ● Inkoop     │  Hoe stel ik BTW in?  Jan   3/20 │
│ ● Technisch  │  RGS-schema import    Piet  3/19 │
│ ● Overig     │  Salarisstroken NL    Kees  3/18 │
│              │                                   │
├──────────────┴──────────────────────────────────┤
│  Footer                                          │
└─────────────────────────────────────────────────┘
```

## 5. Nederland-specifieke inhoud (eerste opzet)

### Werkboeken (prioriteit)
1. **BTW-inrichting** — Hoog/laag/nul-tarief, aangifte, ICP
2. **Grootboekschema (RGS)** — Referentie Grootboekschema importeren
3. **KvK-gegevens** — Bedrijfsgegevens koppelen
4. **Facturatie** — Nederlandse factuurvereisten (KvK-nr, BTW-nr)
5. **Salarisadministratie** — Loonheffing, pensioenen, cao-instellingen
6. **SEPA-betalingen** — Bankintegratie voor NL-banken
7. **Jaarrekening** — Balans en W&V conform Nederlandse normen

### Forumcategorieën
- Algemeen
- Boekhouding & Financieel
- Inkoop & Voorraad
- Verkoop & CRM
- HR & Salarisadministratie
- Technisch / Installatie
- Verzoeken & Ideeën

## 6. Technische aanpak

### Frappe Website Module
- Webpagina's via `Website Page` DocType
- Blog via `Blog Post` DocType
- Custom DocTypes voor werkboeken, forum, voorbeelden
- Jinja2-templates voor de weergave
- Portal-pagina's voor forumfunctionaliteit

### Stijl
- Frappe's standaard CSS-framework als basis
- Zakelijke kleurstelling (blauw/grijs/wit)
- Consistent met ERPNext-branding
- Responsive design

### Taal
- Alle interface-elementen in het Nederlands
- URL-paden in het Nederlands
- Frappe-vertalingen activeren voor NL
