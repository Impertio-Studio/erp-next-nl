---
titel: "Server Scripts & Client Scripts — Maatwerk voor ERPNext"
versie: "1.0"
datum: 2026-03-24
categorie: Werkboek
moeilijkheidsgraad: Expert
doelgroep: IT, Boekhouder
---

# Server Scripts & Client Scripts — Maatwerk voor ERPNext

## Inleiding

ERPNext (gebouwd op het Frappe-framework) biedt twee krachtige mechanismen om de standaardfunctionaliteit uit te breiden zonder de broncode aan te passen: **Server Scripts** en **Client Scripts**.

**Server Scripts** draaien op de server (Python) en worden uitgevoerd bij DocType-events (bijv. na opslaan), als API-endpoint of als geplande taak (Scheduler Event). Ze hebben volledige toegang tot de Frappe-API, de database en het e-mailsysteem.

**Client Scripts** draaien in de browser (JavaScript) en worden gebruikt om formulieren, lijstweergaven en andere UI-elementen aan te passen. Ze kunnen knoppen toevoegen, velden verbergen, validaties uitvoeren en communiceren met de server via API-calls.

Dit werkboek documenteert alle maatwerk-scripts die actief zijn in de [BEDRIJFSNAAM] ERPNext-omgeving, gegroepeerd per functioneel domein.

---

## Inhoudsopgave

1. [SEPA / Betalingen](#1-sepa--betalingen)
2. [E-mail / Notificaties](#2-e-mail--notificaties)
3. [Offerte / Verkoop](#3-offerte--verkoop)
4. [Timesheet / Project](#4-timesheet--project)
5. [Bank / Reconciliatie](#5-bank--reconciliatie)
6. [Inkoop / Purchase Invoice](#6-inkoop--purchase-invoice)
7. [Overige](#7-overige)
8. [Zelf scripts aanmaken in ERPNext](#8-zelf-scripts-aanmaken-in-erpnext)
9. [Tips voor Nederlandse gebruikers](#9-tips-voor-nederlandse-gebruikers)

---

## 1. SEPA / Betalingen

### 1.1 Custom SEPA Export (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Custom SEPA Export |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** Genereert een SEPA XML-bestand (pain.001.001.09) voor een Payment Order. Het script haalt bedrijfs- en bankgegevens op, bouwt de volledige XML-structuur inclusief Group Header, Payment Info en individuele Credit Transfer transacties, en slaat het resultaat op als downloadbaar bestand. De IBAN van leveranciers wordt opgezocht via hun Bank Account. Specifiek gebouwd voor de hoofdentiteit met Bunq-bankgegevens (RABONL2U BIC).

```python
# SEPA Export pain.001.001.09 - EXACT BUNQ WERKEND VOORBEELD
# 1-op-1 kopie van werkend e-Boekhouden bestand

payment_order = frappe.form_dict.payment_order

doc = frappe.get_doc("Payment Order", payment_order)
company = frappe.get_doc("Company", doc.company)
bank_account = frappe.get_doc("Bank Account", doc.company_bank_account)

total_amount = sum([ref.amount for ref in doc.references])
num_transactions = len(doc.references)

# Timestamp
timestamp = frappe.utils.now()
if '.' in timestamp:
    timestamp = timestamp.split('.')[0]
timestamp = timestamp.replace(' ', 'T')

# Message ID
msg_id = doc.name.replace(' ', '').replace('-', '')
creation_ts = frappe.utils.now().replace('-','').replace(':','').replace(' ','').replace('.','')[:14]
msg_id = msg_id + creation_ts

# XML - EXACT BUNQ STRUCTUUR
xml = '<?xml version="1.0" encoding="UTF-8"?>'
xml += '<Document xmlns="urn:iso:std:iso:20022:tech:xsd:pain.001.001.09" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">\n'
xml += '  <CstmrCdtTrfInitn>\n'

# Group Header
xml += '    <GrpHdr>\n'
xml += '      <MsgId>' + msg_id + '</MsgId>\n'
xml += '      <CreDtTm>' + timestamp + '</CreDtTm>\n'
xml += '      <NbOfTxs>' + str(num_transactions) + '</NbOfTxs>\n'
xml += '      <CtrlSum>' + str(round(total_amount, 2)) + '</CtrlSum>\n'
xml += '      <InitgPty>\n'
xml += '        <Nm>' + company.company_name + '</Nm>\n'
xml += '      </InitgPty>\n'
xml += '    </GrpHdr>\n'

# Payment Info
xml += '    <PmtInf>\n'
xml += '      <PmtInfId>' + msg_id + '</PmtInfId>\n'
xml += '      <PmtMtd>TRF</PmtMtd>\n'
xml += '      <BtchBookg>false</BtchBookg>\n'
xml += '      <NbOfTxs>' + str(num_transactions) + '</NbOfTxs>\n'
xml += '      <CtrlSum>' + str(round(total_amount, 2)) + '</CtrlSum>\n'
xml += '      <PmtTpInf>\n'
xml += '        <SvcLvl>\n'
xml += '          <Cd>SEPA</Cd>\n'
xml += '        </SvcLvl>\n'
xml += '      </PmtTpInf>\n'
xml += '      <ReqdExctnDt>\n'
xml += '        <Dt>' + str(doc.posting_date) + '</Dt>\n'
xml += '      </ReqdExctnDt>\n'
xml += '      <Dbtr>\n'
xml += '        <Nm>' + company.company_name + '</Nm>\n'
xml += '      </Dbtr>\n'
xml += '      <DbtrAcct>\n'
xml += '        <Id>\n'
xml += '          <IBAN>' + bank_account.iban + '</IBAN>\n'
xml += '        </Id>\n'
xml += '      </DbtrAcct>\n'
xml += '      <DbtrAgt>\n'
xml += '        <FinInstnId>\n'
xml += '          <BICFI>RABONL2U</BICFI>\n'
xml += '        </FinInstnId>\n'
xml += '      </DbtrAgt>\n'
xml += '      <ChrgBr>SLEV</ChrgBr>\n'

# Transactions
idx = 0
for ref in doc.references:
    xml += '      <CdtTrfTxInf>\n'
    xml += '        <PmtId>\n'
    end_to_end_id = msg_id + 'V' + str(idx)
    if len(end_to_end_id) > 35:
        end_to_end_id = end_to_end_id[:35]
    xml += '          <EndToEndId>' + end_to_end_id + '</EndToEndId>\n'
    xml += '        </PmtId>\n'
    xml += '        <Amt>\n'
    xml += '          <InstdAmt Ccy="EUR">' + str(round(ref.amount, 2)) + '</InstdAmt>\n'
    xml += '        </Amt>\n'
    xml += '        <Cdtr>\n'
    xml += '          <Nm>' + ref.supplier + '</Nm>\n'
    xml += '        </Cdtr>\n'

    supplier_bank = frappe.db.get_value("Bank Account",
        {"party_type": "Supplier", "party": ref.supplier},
        ["iban", "bank"], as_dict=True)

    xml += '        <CdtrAcct>\n'
    xml += '          <Id>\n'
    if supplier_bank and supplier_bank.iban:
        xml += '            <IBAN>' + supplier_bank.iban + '</IBAN>\n'
    xml += '          </Id>\n'
    xml += '        </CdtrAcct>\n'
    xml += '        <RmtInf>\n'
    ustrd = ref.reference_name or 'Payment'
    if len(ustrd) > 140:
        ustrd = ustrd[:140]
    xml += '          <Ustrd>' + ustrd + '</Ustrd>\n'
    xml += '        </RmtInf>\n'
    xml += '      </CdtTrfTxInf>\n'
    idx = idx + 1

xml += '    </PmtInf>\n'
xml += '  </CstmrCdtTrfInitn>\n'
xml += '</Document>'

# Save
filename = "SEPA_" + doc.name + ".xml"
file_doc = frappe.get_doc({
    "doctype": "File",
    "file_name": filename,
    "content": xml,
    "is_private": 0
})
file_doc.save()

frappe.response.message = file_doc.file_url
```

---

### 1.2 Custom SEPA Export Cooperatie (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Custom SEPA Export Cooperatie |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** Variant van de SEPA-export specifiek voor de entiteit "[BEDRIJFSNAAM] Cooperatie U.A." met ABN AMRO-bankgegevens (IBAN: NL34ABNA0497468425, BIC: ABNANL2A). Bevat een companycheck zodat het script niet per ongeluk voor een andere entiteit wordt gebruikt. Bouwt dezelfde pain.001.001.09 XML-structuur op en slaat het bestand op als File-document.

```python
import frappe
from datetime import datetime

# Company-specific settings
COMPANY_NAME = "[BEDRIJFSNAAM] Cooperatie U.A."
COMPANY_IBAN = "NL34ABNA0497468425"
COMPANY_BIC = "ABNANL2A"

doc_name = frappe.form_dict.get("docname")
if not doc_name:
    frappe.response.message = {"error": "No Payment Order specified"}
else:
    doc = frappe.get_doc("Payment Order", doc_name)

    # Check company
    if doc.company != COMPANY_NAME:
        frappe.response.message = {
            "error": "Dit script is alleen voor " + COMPANY_NAME,
            "company_found": doc.company
        }
    else:
        # Generate timestamps
        now = datetime.now()
        creation_ts = now.strftime("%Y%m%d%H%M%S")
        creation_datetime = now.strftime("%Y-%m-%dT%H:%M:%S")
        exec_date = doc.posting_date if hasattr(doc, "posting_date") else now.strftime("%Y-%m-%d")

        # Message ID (alleen letters en cijfers)
        msg_id = doc.name.replace("-", "").replace(" ", "") + creation_ts

        # Count transactions and calculate total
        num_txs = len(doc.references)
        ctrl_sum = sum(float(ref.amount) for ref in doc.references)

        # Build XML
        xml_lines = []
        xml_lines.append('<?xml version="1.0" encoding="UTF-8"?>')
        xml_lines.append('<Document xmlns="urn:iso:std:iso:20022:tech:xsd:pain.001.001.09" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">')
        xml_lines.append("  <CstmrCdtTrfInitn>")

        # Group Header
        xml_lines.append("    <GrpHdr>")
        xml_lines.append("      <MsgId>" + msg_id + "</MsgId>")
        xml_lines.append("      <CreDtTm>" + creation_datetime + "</CreDtTm>")
        xml_lines.append("      <NbOfTxs>" + str(num_txs) + "</NbOfTxs>")
        xml_lines.append("      <CtrlSum>" + str(ctrl_sum) + "</CtrlSum>")
        xml_lines.append("      <InitgPty>")
        xml_lines.append("        <Nm>" + COMPANY_NAME + "</Nm>")
        xml_lines.append("      </InitgPty>")
        xml_lines.append("    </GrpHdr>")

        # Payment Info
        xml_lines.append("    <PmtInf>")
        xml_lines.append("      <PmtInfId>" + msg_id + "</PmtInfId>")
        xml_lines.append("      <PmtMtd>TRF</PmtMtd>")
        xml_lines.append("      <BtchBookg>false</BtchBookg>")
        xml_lines.append("      <NbOfTxs>" + str(num_txs) + "</NbOfTxs>")
        xml_lines.append("      <CtrlSum>" + str(ctrl_sum) + "</CtrlSum>")
        xml_lines.append("      <PmtTpInf>")
        xml_lines.append("        <SvcLvl>")
        xml_lines.append("          <Cd>SEPA</Cd>")
        xml_lines.append("        </SvcLvl>")
        xml_lines.append("      </PmtTpInf>")
        xml_lines.append("      <ReqdExctnDt>")
        xml_lines.append("        <Dt>" + exec_date + "</Dt>")
        xml_lines.append("      </ReqdExctnDt>")

        # Debtor (our company)
        xml_lines.append("      <Dbtr>")
        xml_lines.append("        <Nm>" + COMPANY_NAME + "</Nm>")
        xml_lines.append("      </Dbtr>")
        xml_lines.append("      <DbtrAcct>")
        xml_lines.append("        <Id>")
        xml_lines.append("          <IBAN>" + COMPANY_IBAN + "</IBAN>")
        xml_lines.append("        </Id>")
        xml_lines.append("      </DbtrAcct>")
        xml_lines.append("      <DbtrAgt>")
        xml_lines.append("        <FinInstnId>")
        xml_lines.append("          <BICFI>" + COMPANY_BIC + "</BICFI>")
        xml_lines.append("        </FinInstnId>")
        xml_lines.append("      </DbtrAgt>")
        xml_lines.append("      <ChrgBr>SLEV</ChrgBr>")

        # Credit Transfer Transactions
        for idx, ref in enumerate(doc.references):
            xml_lines.append("      <CdtTrfTxInf>")
            xml_lines.append("        <PmtId>")
            xml_lines.append("          <EndToEndId>" + msg_id + "V" + str(idx) + "</EndToEndId>")
            xml_lines.append("        </PmtId>")
            xml_lines.append("        <Amt>")
            xml_lines.append('          <InstdAmt Ccy="EUR">' + str(float(ref.amount)) + "</InstdAmt>")
            xml_lines.append("        </Amt>")
            xml_lines.append("        <Cdtr>")
            xml_lines.append("          <Nm>" + (ref.supplier or "") + "</Nm>")
            xml_lines.append("        </Cdtr>")
            xml_lines.append("        <CdtrAcct>")
            xml_lines.append("          <Id>")

            # Get IBAN from bank account
            iban = ""
            if ref.bank_account:
                ba = frappe.get_doc("Bank Account", ref.bank_account)
                iban = ba.iban or ""

            xml_lines.append("            <IBAN>" + iban + "</IBAN>")
            xml_lines.append("          </Id>")
            xml_lines.append("        </CdtrAcct>")
            xml_lines.append("        <RmtInf>")
            xml_lines.append("          <Ustrd>" + (ref.reference_name or "") + "</Ustrd>")
            xml_lines.append("        </RmtInf>")
            xml_lines.append("      </CdtTrfTxInf>")

        xml_lines.append("    </PmtInf>")
        xml_lines.append("  </CstmrCdtTrfInitn>")
        xml_lines.append("</Document>")

        xml_content = "\n".join(xml_lines)

        # Create File
        file_name = "SEPA_" + doc.name.replace(" ", "") + ".xml"
        file_doc = frappe.get_doc({
            "doctype": "File",
            "file_name": file_name,
            "content": xml_content,
            "is_private": 0
        })
        file_doc.insert(ignore_permissions=True)

        frappe.response.message = {
            "success": True,
            "file_url": file_doc.file_url,
            "file_name": file_name,
            "company": COMPANY_NAME,
            "num_transactions": num_txs,
            "total_amount": ctrl_sum
        }
```

---

### 1.3 Custom SEPA Export Engineering (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Custom SEPA Export Engineering |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** Variant van de SEPA-export specifiek voor de entiteit "[BEDRIJFSNAAM] Engineering" met ING-bankgegevens (IBAN: NL54INGB0006629483, BIC: INGBNL2A). Identieke structuur als de Cooperatie-variant, maar met afwijkende company- en bankgegevens.

```python
import frappe
from datetime import datetime

# Company-specific settings
COMPANY_NAME = "[BEDRIJFSNAAM] Engineering"
COMPANY_IBAN = "NL54INGB0006629483"  # ING Bank
COMPANY_BIC = "INGBNL2A"  # ING Bank BIC

doc_name = frappe.form_dict.get("docname")
if not doc_name:
    frappe.response.message = {"error": "No Payment Order specified"}
else:
    doc = frappe.get_doc("Payment Order", doc_name)

    # Check company
    if doc.company != COMPANY_NAME:
        frappe.response.message = {
            "error": "Dit script is alleen voor " + COMPANY_NAME,
            "company_found": doc.company
        }
    else:
        # Generate timestamps
        now = datetime.now()
        creation_ts = now.strftime("%Y%m%d%H%M%S")
        creation_datetime = now.strftime("%Y-%m-%dT%H:%M:%S")
        exec_date = doc.posting_date if hasattr(doc, "posting_date") else now.strftime("%Y-%m-%d")

        # Message ID (alleen letters en cijfers)
        msg_id = doc.name.replace("-", "").replace(" ", "") + creation_ts

        # Count transactions and calculate total
        num_txs = len(doc.references)
        ctrl_sum = sum(float(ref.amount) for ref in doc.references)

        # Build XML
        xml_lines = []
        xml_lines.append('<?xml version="1.0" encoding="UTF-8"?>')
        xml_lines.append('<Document xmlns="urn:iso:std:iso:20022:tech:xsd:pain.001.001.09" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">')
        xml_lines.append("  <CstmrCdtTrfInitn>")

        # Group Header
        xml_lines.append("    <GrpHdr>")
        xml_lines.append("      <MsgId>" + msg_id + "</MsgId>")
        xml_lines.append("      <CreDtTm>" + creation_datetime + "</CreDtTm>")
        xml_lines.append("      <NbOfTxs>" + str(num_txs) + "</NbOfTxs>")
        xml_lines.append("      <CtrlSum>" + str(ctrl_sum) + "</CtrlSum>")
        xml_lines.append("      <InitgPty>")
        xml_lines.append("        <Nm>" + COMPANY_NAME + "</Nm>")
        xml_lines.append("      </InitgPty>")
        xml_lines.append("    </GrpHdr>")

        # Payment Info
        xml_lines.append("    <PmtInf>")
        xml_lines.append("      <PmtInfId>" + msg_id + "</PmtInfId>")
        xml_lines.append("      <PmtMtd>TRF</PmtMtd>")
        xml_lines.append("      <BtchBookg>false</BtchBookg>")
        xml_lines.append("      <NbOfTxs>" + str(num_txs) + "</NbOfTxs>")
        xml_lines.append("      <CtrlSum>" + str(ctrl_sum) + "</CtrlSum>")
        xml_lines.append("      <PmtTpInf>")
        xml_lines.append("        <SvcLvl>")
        xml_lines.append("          <Cd>SEPA</Cd>")
        xml_lines.append("        </SvcLvl>")
        xml_lines.append("      </PmtTpInf>")
        xml_lines.append("      <ReqdExctnDt>")
        xml_lines.append("        <Dt>" + exec_date + "</Dt>")
        xml_lines.append("      </ReqdExctnDt>")

        # Debtor (our company)
        xml_lines.append("      <Dbtr>")
        xml_lines.append("        <Nm>" + COMPANY_NAME + "</Nm>")
        xml_lines.append("      </Dbtr>")
        xml_lines.append("      <DbtrAcct>")
        xml_lines.append("        <Id>")
        xml_lines.append("          <IBAN>" + COMPANY_IBAN + "</IBAN>")
        xml_lines.append("        </Id>")
        xml_lines.append("      </DbtrAcct>")
        xml_lines.append("      <DbtrAgt>")
        xml_lines.append("        <FinInstnId>")
        xml_lines.append("          <BICFI>" + COMPANY_BIC + "</BICFI>")
        xml_lines.append("        </FinInstnId>")
        xml_lines.append("      </DbtrAgt>")
        xml_lines.append("      <ChrgBr>SLEV</ChrgBr>")

        # Credit Transfer Transactions
        for idx, ref in enumerate(doc.references):
            xml_lines.append("      <CdtTrfTxInf>")
            xml_lines.append("        <PmtId>")
            xml_lines.append("          <EndToEndId>" + msg_id + "V" + str(idx) + "</EndToEndId>")
            xml_lines.append("        </PmtId>")
            xml_lines.append("        <Amt>")
            xml_lines.append('          <InstdAmt Ccy="EUR">' + str(float(ref.amount)) + "</InstdAmt>")
            xml_lines.append("        </Amt>")
            xml_lines.append("        <Cdtr>")
            xml_lines.append("          <Nm>" + (ref.supplier or "") + "</Nm>")
            xml_lines.append("        </Cdtr>")
            xml_lines.append("        <CdtrAcct>")
            xml_lines.append("          <Id>")

            # Get IBAN from bank account
            iban = ""
            if ref.bank_account:
                ba = frappe.get_doc("Bank Account", ref.bank_account)
                iban = ba.iban or ""

            xml_lines.append("            <IBAN>" + iban + "</IBAN>")
            xml_lines.append("          </Id>")
            xml_lines.append("        </CdtrAcct>")
            xml_lines.append("        <RmtInf>")
            xml_lines.append("          <Ustrd>" + (ref.reference_name or "") + "</Ustrd>")
            xml_lines.append("        </RmtInf>")
            xml_lines.append("      </CdtTrfTxInf>")

        xml_lines.append("    </PmtInf>")
        xml_lines.append("  </CstmrCdtTrfInitn>")
        xml_lines.append("</Document>")

        xml_content = "\n".join(xml_lines)

        # Create File
        file_name = "SEPA_" + doc.name.replace(" ", "") + ".xml"
        file_doc = frappe.get_doc({
            "doctype": "File",
            "file_name": file_name,
            "content": xml_content,
            "is_private": 0
        })
        file_doc.insert(ignore_permissions=True)

        frappe.response.message = {
            "success": True,
            "file_url": file_doc.file_url,
            "file_name": file_name,
            "company": COMPANY_NAME,
            "num_transactions": num_txs,
            "total_amount": ctrl_sum
        }
```

---

### 1.4 Payment Order SEPA Button (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Payment Order SEPA Button |
| **DocType** | Payment Order |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Voegt een "SEPA Export (Correct)"-knop toe aan het Payment Order-formulier (alleen zichtbaar bij submitted documents). Bij klikken wordt het server-side `custom_sepa_export`-API-endpoint aangeroepen en het resulterende XML-bestand automatisch gedownload in een nieuw tabblad.

```javascript
frappe.ui.form.on('Payment Order', {
    refresh: function(frm) {
        if (frm.doc.docstatus === 1) {
            frm.add_custom_button(__('SEPA Export (Correct)'), function() {
                frappe.show_alert({
                    message: __('SEPA XML wordt gegenereerd...'),
                    indicator: 'blue'
                }, 3);

                frappe.call({
                    method: 'custom_sepa_export',
                    args: {
                        payment_order: frm.doc.name
                    },
                    callback: function(r) {
                        if (r.message) {
                            // Open file URL = download!
                            window.open(r.message, '_blank');

                            frappe.show_alert({
                                message: __('SEPA XML download gestart!'),
                                indicator: 'green'
                            }, 5);
                        }
                    },
                    error: function(r) {
                        frappe.msgprint({
                            title: __('Error'),
                            indicator: 'red',
                            message: __('SEPA export failed: ') + (r.message || 'Unknown error')
                        });
                    }
                });
            }, __('Create'));
        }
    }
});
```

---

## 2. E-mail / Notificaties

### 2.1 Dunning [BEDRIJFSNAAM] (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Dunning [BEDRIJFSNAAM] |
| **Type** | Scheduler Event |
| **Reference DocType** | — |

**Beschrijving:** Automatische aanmaak van betalingsherinneringen (Dunning) voor de entiteit "[BEDRIJFSNAAM] V.O.F.". Draait als geplande taak en doorloopt alle onbetaalde, verstuurde facturen. Na 3 dagen wordt een "Eerste Herinnering" aangemaakt, na 24 dagen een "Aanmaning". Intercompany-klanten worden overgeslagen. In TEST_MODE worden alleen concepten aangemaakt; in LIVE-modus worden dunnings submitted en e-mails verstuurd.

```python
TEST_MODE = True
COMPANY = "[BEDRIJFSNAAM] V.O.F."
EERSTE_HERINNERING_DAGEN = 3
AANMANING_DAGEN = 24
DUNNING_TYPE_HERINNERING = "Eerste Herinnering - 3"
DUNNING_TYPE_AANMANING = "Aanmaning - 3"
EMAIL_ACCOUNT = "[email]@[bedrijf].nl"
SKIP_CUSTOMERS = ["[BEDRIJFSNAAM] Cooperatie U.A.", "[BEDRIJFSNAAM] Bouwkunde", "[BEDRIJFSNAAM] Engineering", "[BEDRIJFSNAAM] V.O.F."]

log_messages = []

def log(msg):
    log_messages.append(msg)

try:
    current_date = frappe.utils.getdate(frappe.utils.today())
    log("=== Auto Dunning [BEDRIJFSNAAM] gestart op " + str(current_date) + " | TEST_MODE=" + str(TEST_MODE) + " ===")

    overdue_invoices = frappe.get_all("Sales Invoice",
        filters={
            "company": COMPANY,
            "docstatus": 1,
            "status": ["in", ["Unpaid", "Overdue"]],
            "outstanding_amount": [">", 0],
            "custom_email_status": ["in", ["Verstuurd", "Herinnering 1"]]
        },
        fields=["name", "customer", "customer_name", "posting_date", "due_date",
                "grand_total", "outstanding_amount", "custom_email_status", "currency", "conversion_rate"]
    )

    log("Gevonden: " + str(len(overdue_invoices)) + " onbetaalde verstuurde facturen")

    created_count = 0
    skipped_intercompany = 0
    skipped_not_due = 0
    skipped_exists = 0
    error_count = 0

    for inv in overdue_invoices:
        try:
            if inv.customer in SKIP_CUSTOMERS or inv.customer_name in SKIP_CUSTOMERS:
                skipped_intercompany += 1
                continue

            days_overdue = frappe.utils.date_diff(current_date, frappe.utils.getdate(inv.due_date))

            if days_overdue < EERSTE_HERINNERING_DAGEN:
                skipped_not_due += 1
                continue

            target_type = None
            new_email_status = None

            if days_overdue >= AANMANING_DAGEN and inv.custom_email_status == "Herinnering 1":
                target_type = DUNNING_TYPE_AANMANING
                new_email_status = "Aanmaning"
            elif days_overdue >= EERSTE_HERINNERING_DAGEN and inv.custom_email_status == "Verstuurd":
                target_type = DUNNING_TYPE_HERINNERING
                new_email_status = "Herinnering 1"

            if not target_type:
                skipped_exists += 1
                continue

            existing_dunnings = frappe.get_all("Overdue Payment",
                filters={"sales_invoice": inv.name, "docstatus": ["in", [0, 1]]},
                fields=["parent"]
            )
            existing_types = []
            for ed in existing_dunnings:
                dt = frappe.db.get_value("Dunning", ed.parent, "dunning_type")
                if dt:
                    existing_types.append(dt)

            if target_type in existing_types:
                skipped_exists += 1
                continue

            payment_schedules = frappe.get_all("Payment Schedule",
                filters={"parent": inv.name, "outstanding": [">", 0]},
                fields=["name", "payment_term", "due_date", "invoice_portion",
                        "payment_amount", "outstanding", "paid_amount", "discounted_amount"]
            )

            if not payment_schedules:
                log("  SKIP " + str(inv.name) + ": geen payment schedule")
                continue

            dunning_type_doc = frappe.get_doc("Dunning Type", target_type)

            dunning = frappe.new_doc("Dunning")
            dunning.company = COMPANY
            dunning.customer = inv.customer
            dunning.dunning_type = target_type
            dunning.posting_date = frappe.utils.today()
            dunning.dunning_fee = dunning_type_doc.dunning_fee or 0
            dunning.rate_of_interest = dunning_type_doc.rate_of_interest or 0
            dunning.income_account = dunning_type_doc.income_account
            dunning.cost_center = dunning_type_doc.cost_center or "Main - 3"
            dunning.currency = inv.currency or "EUR"
            dunning.conversion_rate = inv.conversion_rate or 1
            dunning.dunning_amount = dunning_type_doc.dunning_fee or 0
            dunning.base_dunning_amount = dunning_type_doc.dunning_fee or 0
            dunning.grand_total = (dunning_type_doc.dunning_fee or 0) + (inv.outstanding_amount or 0)
            dunning.base_grand_total = (dunning_type_doc.dunning_fee or 0) + (inv.outstanding_amount or 0)
            dunning.sales_invoice = inv.name

            for ps in payment_schedules:
                dunning.append("overdue_payments", {
                    "sales_invoice": inv.name,
                    "payment_schedule": ps.name,
                    "payment_term": ps.payment_term or "",
                    "due_date": ps.due_date,
                    "invoice_portion": ps.invoice_portion or 100,
                    "payment_amount": ps.payment_amount or 0,
                    "outstanding": ps.outstanding or 0,
                    "paid_amount": ps.paid_amount or 0,
                    "discounted_amount": ps.discounted_amount or 0,
                    "interest": 0
                })

            dunning.insert(ignore_permissions=True)

            frappe.db.set_value("Sales Invoice", inv.name, "custom_email_status", new_email_status, update_modified=False)

            log("  AANGEMAAKT: " + str(dunning.name) + " | " + str(inv.customer_name) + " | " + str(target_type) + " | EUR " + str(inv.outstanding_amount) + " | " + str(days_overdue) + " dagen | " + str(inv.custom_email_status) + " -> " + str(new_email_status))

            if not TEST_MODE:
                dunning.submit()

                customer_email = frappe.db.get_value("Customer", inv.customer, "email_id")
                if not customer_email:
                    contacts = frappe.get_all("Dynamic Link",
                        filters={"link_doctype": "Customer", "link_name": inv.customer, "parenttype": "Contact"},
                        fields=["parent"]
                    )
                    for c in contacts:
                        email = frappe.db.get_value("Contact", c.parent, "email_id")
                        if email:
                            customer_email = email
                            break

                if customer_email:
                    frappe.sendmail(
                        recipients=[customer_email],
                        sender=EMAIL_ACCOUNT,
                        subject="Betalingsherinnering - " + str(inv.name),
                        message=dunning_type_doc.dunning_letter_text[0].body_text if dunning_type_doc.dunning_letter_text else "Betalingsherinnering",
                        reference_doctype="Dunning",
                        reference_name=dunning.name
                    )
                    log("    E-MAIL verstuurd naar " + str(customer_email))
                else:
                    log("    GEEN E-MAIL: geen adres voor " + str(inv.customer_name))

            created_count += 1

        except Exception as inv_error:
            error_count += 1
            log("  FOUT bij " + str(inv.name) + " (" + str(inv.customer_name) + "): " + str(inv_error))
            continue

    log("")
    log("=== SAMENVATTING ===")
    log("Aangemaakt: " + str(created_count))
    log("Fouten: " + str(error_count))
    log("Overgeslagen intercompany: " + str(skipped_intercompany))
    log("Overgeslagen niet verlopen: " + str(skipped_not_due))
    log("Overgeslagen dunning bestaat/status: " + str(skipped_exists))
    log("Mode: " + ("TEST (drafts)" if TEST_MODE else "LIVE (submit+email)"))

    frappe.log_error(title="Auto Dunning [BEDRIJFSNAAM]", message="\n".join(log_messages))
    frappe.db.commit()

except Exception as e:
    log("FOUT: " + str(e))
    frappe.log_error(title="Auto Dunning [BEDRIJFSNAAM] FOUT", message="\n".join(log_messages) + "\n\n" + str(e))
```

---

### 2.2 Dunning Cooperatie (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Dunning Cooperatie |
| **Type** | Scheduler Event |
| **Reference DocType** | — |

**Beschrijving:** Identieke dunning-automatisering als hierboven, maar voor de entiteit "[BEDRIJFSNAAM] Cooperatie U.A." met eigen Dunning Types ("Eerste Herinnering Cooperatie - 7" en "Aanmaning Cooperatie - 7") en eigen e-mailaccount ([user]@[bedrijf].nl). Dezelfde logica: na 3 dagen eerste herinnering, na 24 dagen aanmaning.

```python
TEST_MODE = True
COMPANY = "[BEDRIJFSNAAM] Cooperatie U.A."
EERSTE_HERINNERING_DAGEN = 3
AANMANING_DAGEN = 24
DUNNING_TYPE_HERINNERING = "Eerste Herinnering Cooperatie - 7"
DUNNING_TYPE_AANMANING = "Aanmaning Cooperatie - 7"
EMAIL_ACCOUNT = "[user]@[bedrijf].nl"
SKIP_CUSTOMERS = ["[BEDRIJFSNAAM] Cooperatie U.A.", "[BEDRIJFSNAAM] Bouwkunde", "[BEDRIJFSNAAM] Engineering", "[BEDRIJFSNAAM] V.O.F."]

log_messages = []

def log(msg):
    log_messages.append(msg)

try:
    current_date = frappe.utils.getdate(frappe.utils.today())
    log("=== Auto Dunning Cooperatie gestart op " + str(current_date) + " | TEST_MODE=" + str(TEST_MODE) + " ===")

    overdue_invoices = frappe.get_all("Sales Invoice",
        filters={
            "company": COMPANY,
            "docstatus": 1,
            "status": ["in", ["Unpaid", "Overdue"]],
            "outstanding_amount": [">", 0],
            "custom_email_status": ["in", ["Verstuurd", "Herinnering 1"]]
        },
        fields=["name", "customer", "customer_name", "posting_date", "due_date",
                "grand_total", "outstanding_amount", "custom_email_status", "currency", "conversion_rate"]
    )

    log("Gevonden: " + str(len(overdue_invoices)) + " onbetaalde verstuurde facturen")

    created_count = 0
    skipped_intercompany = 0
    skipped_not_due = 0
    skipped_exists = 0
    error_count = 0

    for inv in overdue_invoices:
        try:
            if inv.customer in SKIP_CUSTOMERS or inv.customer_name in SKIP_CUSTOMERS:
                skipped_intercompany += 1
                continue

            days_overdue = frappe.utils.date_diff(current_date, frappe.utils.getdate(inv.due_date))

            if days_overdue < EERSTE_HERINNERING_DAGEN:
                skipped_not_due += 1
                continue

            target_type = None
            new_email_status = None

            if days_overdue >= AANMANING_DAGEN and inv.custom_email_status == "Herinnering 1":
                target_type = DUNNING_TYPE_AANMANING
                new_email_status = "Aanmaning"
            elif days_overdue >= EERSTE_HERINNERING_DAGEN and inv.custom_email_status == "Verstuurd":
                target_type = DUNNING_TYPE_HERINNERING
                new_email_status = "Herinnering 1"

            if not target_type:
                skipped_exists += 1
                continue

            existing_dunnings = frappe.get_all("Overdue Payment",
                filters={"sales_invoice": inv.name, "docstatus": ["in", [0, 1]]},
                fields=["parent"]
            )
            existing_types = []
            for ed in existing_dunnings:
                dt = frappe.db.get_value("Dunning", ed.parent, "dunning_type")
                if dt:
                    existing_types.append(dt)

            if target_type in existing_types:
                skipped_exists += 1
                continue

            payment_schedules = frappe.get_all("Payment Schedule",
                filters={"parent": inv.name, "outstanding": [">", 0]},
                fields=["name", "payment_term", "due_date", "invoice_portion",
                        "payment_amount", "outstanding", "paid_amount", "discounted_amount"]
            )

            if not payment_schedules:
                log("  SKIP " + str(inv.name) + ": geen payment schedule")
                continue

            dunning_type_doc = frappe.get_doc("Dunning Type", target_type)

            dunning = frappe.new_doc("Dunning")
            dunning.company = COMPANY
            dunning.customer = inv.customer
            dunning.dunning_type = target_type
            dunning.posting_date = frappe.utils.today()
            dunning.dunning_fee = dunning_type_doc.dunning_fee or 0
            dunning.rate_of_interest = dunning_type_doc.rate_of_interest or 0
            dunning.income_account = dunning_type_doc.income_account
            dunning.cost_center = dunning_type_doc.cost_center or "Main - 7"
            dunning.currency = inv.currency or "EUR"
            dunning.conversion_rate = inv.conversion_rate or 1
            dunning.dunning_amount = dunning_type_doc.dunning_fee or 0
            dunning.base_dunning_amount = dunning_type_doc.dunning_fee or 0
            dunning.grand_total = (dunning_type_doc.dunning_fee or 0) + (inv.outstanding_amount or 0)
            dunning.base_grand_total = (dunning_type_doc.dunning_fee or 0) + (inv.outstanding_amount or 0)
            dunning.sales_invoice = inv.name

            for ps in payment_schedules:
                dunning.append("overdue_payments", {
                    "sales_invoice": inv.name,
                    "payment_schedule": ps.name,
                    "payment_term": ps.payment_term or "",
                    "due_date": ps.due_date,
                    "invoice_portion": ps.invoice_portion or 100,
                    "payment_amount": ps.payment_amount or 0,
                    "outstanding": ps.outstanding or 0,
                    "paid_amount": ps.paid_amount or 0,
                    "discounted_amount": ps.discounted_amount or 0,
                    "interest": 0
                })

            dunning.insert(ignore_permissions=True)

            frappe.db.set_value("Sales Invoice", inv.name, "custom_email_status", new_email_status, update_modified=False)

            log("  AANGEMAAKT: " + str(dunning.name) + " | " + str(inv.customer_name) + " | " + str(target_type) + " | EUR " + str(inv.outstanding_amount) + " | " + str(days_overdue) + " dagen | " + str(inv.custom_email_status) + " -> " + str(new_email_status))

            if not TEST_MODE:
                dunning.submit()

                customer_email = frappe.db.get_value("Customer", inv.customer, "email_id")
                if not customer_email:
                    contacts = frappe.get_all("Dynamic Link",
                        filters={"link_doctype": "Customer", "link_name": inv.customer, "parenttype": "Contact"},
                        fields=["parent"]
                    )
                    for c in contacts:
                        email = frappe.db.get_value("Contact", c.parent, "email_id")
                        if email:
                            customer_email = email
                            break

                if customer_email:
                    frappe.sendmail(
                        recipients=[customer_email],
                        sender=EMAIL_ACCOUNT,
                        subject="Betalingsherinnering - " + str(inv.name),
                        message=dunning_type_doc.dunning_letter_text[0].body_text if dunning_type_doc.dunning_letter_text else "Betalingsherinnering",
                        reference_doctype="Dunning",
                        reference_name=dunning.name
                    )
                    log("    E-MAIL verstuurd naar " + str(customer_email))
                else:
                    log("    GEEN E-MAIL: geen adres voor " + str(inv.customer_name))

            created_count += 1

        except Exception as inv_error:
            error_count += 1
            log("  FOUT bij " + str(inv.name) + " (" + str(inv.customer_name) + "): " + str(inv_error))
            continue

    log("")
    log("=== SAMENVATTING ===")
    log("Aangemaakt: " + str(created_count))
    log("Fouten: " + str(error_count))
    log("Overgeslagen intercompany: " + str(skipped_intercompany))
    log("Overgeslagen niet verlopen: " + str(skipped_not_due))
    log("Overgeslagen dunning bestaat/status: " + str(skipped_exists))
    log("Mode: " + ("TEST (drafts)" if TEST_MODE else "LIVE (submit+email)"))

    frappe.log_error(title="Auto Dunning Cooperatie", message="\n".join(log_messages))
    frappe.db.commit()

except Exception as e:
    log("FOUT: " + str(e))
    frappe.log_error(title="Auto Dunning Cooperatie FOUT", message="\n".join(log_messages) + "\n\n" + str(e))
```

---

### 2.3 Mark Sales Invoice Email Sent (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Mark Sales Invoice Email Sent |
| **Type** | DocType Event |
| **Reference DocType** | Communication |

**Beschrijving:** Wordt getriggerd bij het aanmaken van een Communication-document (e-mail). Wanneer een verzonden e-mail gekoppeld is aan een Sales Invoice, wordt het veld `custom_email_status` automatisch bijgewerkt. Bij een betalingsherinnering wordt de status "Herinnering 1"; bij een reguliere e-mail wordt de status "Verstuurd" (alleen als er nog geen status was).

```python
# Stel Email Status in op Sales Invoice bij verzonden Communication
if doc.reference_doctype == 'Sales Invoice' and doc.reference_name and doc.communication_type == 'Communication' and doc.sent_or_received == 'Sent':
    if doc.subject and 'betalingsherinnering' in doc.subject.lower():
        frappe.db.set_value('Sales Invoice', doc.reference_name, 'custom_email_status', 'Herinnering 1', update_modified=False)
    else:
        current = frappe.db.get_value('Sales Invoice', doc.reference_name, 'custom_email_status')
        if not current:
            frappe.db.set_value('Sales Invoice', doc.reference_name, 'custom_email_status', 'Verstuurd', update_modified=False)
```

---

### 2.4 Notify Timesheet Creation Failure (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Notify Timesheet Creation Failure |
| **Type** | Scheduler Event |
| **Reference DocType** | — |

**Beschrijving:** Dagelijkse controle of de automatische timesheet-aanmaak (scheduled job `api.create_weekly_timesheet`) recent is mislukt. Als er een fout is gevonden in de Scheduled Job Log van de afgelopen dag, wordt er een waarschuwingsmail gestuurd met de foutdetails en een link naar de log.

```python
# Check of de timesheet scheduled job recent gefaald is
# Draait dagelijks en stuurt een e-mail als er een fout was

from datetime import datetime, timedelta

yesterday = frappe.utils.add_days(frappe.utils.today(), -1)

failed_logs = frappe.get_all(
    "Scheduled Job Log",
    filters={
        "scheduled_job_type": "api.create_weekly_timesheet",
        "status": "Failed",
        "creation": [">", yesterday]
    },
    fields=["name", "creation", "details"],
    order_by="creation desc",
    limit=1
)

if failed_logs:
    log = failed_logs[0]
    error_details = log.get("details", "Geen details beschikbaar")

    frappe.sendmail(
        recipients=["[email]@[bedrijf].nl"],
        subject="[ERPNext] Automatische timesheets NIET aangemaakt!",
        message=f"""
        <h3>Automatische timesheet-aanmaak gefaald</h3>
        <p>De scheduled job <strong>create_weekly_timesheet</strong> is gefaald op {log.get('creation')}.</p>
        <h4>Foutmelding:</h4>
        <pre style="background:#f5f5f5;padding:10px;border-radius:4px;overflow-x:auto;">{error_details}</pre>
        <p>Ga naar <a href="[ERPNext URL]/app/scheduled-job-log/{log.get('name')}">de log</a> voor meer details.</p>
        <p>Na het oplossen van het probleem kan de job handmatig gedraaid worden via Scheduled Job Type &gt; api.create_weekly_timesheet &gt; Run Now.</p>
        """,
        now=True
    )
    frappe.log_error("Timesheet creation failure notification sent to [email]@[bedrijf].nl", "Timesheet Alert")
```

---

### 2.5 Bulk Email Sales Invoices (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Bulk Email Sales Invoices |
| **DocType** | Sales Invoice |
| **View** | List |
| **Enabled** | Ja |

**Beschrijving:** Uitgebreid lijstweergave-script voor Sales Invoices met meerdere functionaliteiten:

- **Kleurindicatoren:** Kolommen `custom_days_open` en `custom_email_status` krijgen gekleurde pills (groen/blauw/oranje/rood) op basis van betalingsstatus en doorlooptijd.
- **Bulk Email Facturen:** Selecteer meerdere facturen en verstuur ze in bulk. Auto-detecteert het type (Credit/Vaste prijs/Uren) en kiest het juiste print format. Toont een review-dialoog met bewerkbare e-mailadressen en print formats.
- **Bulk Email Herinneringen:** Selecteer verlopen facturen en verstuur betalingsherinneringen. Detecteert deelbetalingen en past de berichttekst automatisch aan. Bevat een preview-functie per factuur.
- **E-mail lookup:** Zoekt e-mailadressen op via primaire contactpersoon, klant-e-mail of fallback naar gekoppelde contacten.

```javascript
// Bulk Email Facturen v3 - Sales Invoice List View
// Auto-detectie: Credit / Vaste prijs (Aangenomen werk) / Uren
var _si_settings = frappe.listview_settings['Sales Invoice'] || {};
frappe.listview_settings['Sales Invoice'] = _si_settings;
var _si_orig_onload = _si_settings.onload;

// =====================================================================
// Extra velden laden voor lijstweergave
// =====================================================================
_si_settings.add_fields = ['posting_date', 'outstanding_amount', 'due_date', 'custom_date_paid'];

// =====================================================================
// Kleur-indicators voor kolommen
// =====================================================================
_si_settings.formatters = Object.assign({}, _si_settings.formatters || {}, {
    custom_days_open: function(value, df, doc) {
        if (!doc || !doc.posting_date || doc.docstatus !== 1) return '';
        // Betaalde factuur: toon dagen tot betaling in groen
        if (doc.outstanding_amount <= 0 && doc.custom_date_paid) {
            var days_to_pay = frappe.datetime.get_diff(doc.custom_date_paid, doc.posting_date);
            if (days_to_pay < 0) days_to_pay = 0;
            return '<span class="indicator-pill green">' + days_to_pay + 'd</span>';
        }
        if (doc.outstanding_amount <= 0) return '';
        var today = frappe.datetime.get_today();
        var days = frappe.datetime.get_diff(today, doc.posting_date);
        if (days <= 0) return '';
        var due_days = doc.due_date ? frappe.datetime.get_diff(today, doc.due_date) : 0;
        if (due_days > 30) return '<span class="indicator-pill red">' + days + 'd</span>';
        if (due_days > 0) return '<span class="indicator-pill orange">' + days + 'd</span>';
        return '<span class="indicator-pill blue">' + days + 'd</span>';
    },
    custom_email_status: function(value) {
        if (value === 'Verstuurd') {
            return '<span class="indicator-pill green">Verstuurd</span>';
        } else if (value === 'Herinnering 1') {
            return '<span class="indicator-pill orange">Herinnering 1</span>';
        }
        return '';
    }
});

// =====================================================================
// Onload: bulk email knoppen + dagen open kolom
// =====================================================================
_si_settings.onload = function(listview) {
    if (_si_orig_onload) _si_orig_onload.call(this, listview);
    if (!listview._bulk_email_added) {
        listview._bulk_email_added = true;
        listview.page.add_action_item(__('Bulk Email Facturen'), function() {
            _be_start(listview);
        });
        listview.page.add_action_item(__('Bulk Email Herinneringen'), function() {
            _bh_start(listview);
        });
    }

    // Dagen Open kolom: bereken na elke render
    if (!listview._days_open_hooked) {
        listview._days_open_hooked = true;
        var orig_render = listview.render.bind(listview);
        listview.render = function() {
            orig_render();
            _si_update_days_open(listview);
        };
        // Eerste keer ook uitvoeren
        setTimeout(function() { _si_update_days_open(listview); }, 300);
    }
};

// (Volledige code omvat 500+ regels met _be_start, _be_review_dialog,
//  _be_send, _be_show_results, _be_lookup_email, _bh_get_payment_info,
//  _bh_partial_payment_msg, _bh_start, _bh_review_dialog, _bh_send functies)
// Zie het originele script in data/client_scripts.json voor de volledige broncode.
```

> **Let op:** Het volledige script is zeer uitgebreid (500+ regels). De kernfuncties zijn hierboven beschreven. Het complete script is beschikbaar in `data/client_scripts.json` onder de naam "Bulk Email Sales Invoices".

---

### 2.6 Email Sales Invoice (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Email Sales Invoice |
| **DocType** | Sales Invoice |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Voegt meerdere e-mailknoppen toe aan het Sales Invoice-formulier onder de groep "Communication":

- **Email Invoice:** Verstuurt de factuur met print format "[BEDRIJFSNAAM] Factuur Nieuw" en template "Factuur template".
- **Email Invoice With Timesheet:** Verstuurt de factuur met print format "[BEDRIJFSNAAM] Factuur+Urenstaat Nieuw".
- **Email Credit Note:** Alleen zichtbaar bij creditnota's, met template "Credit-template".
- **Betalingsherinnering 1:** Alleen zichtbaar bij verlopen facturen met openstaand bedrag. Geel gemarkeerde knop.

Het script personaliseert de aanhef automatisch (vervangt "Geachte heer/mevrouw" door de voornaam), voegt de gebruikershandtekening toe, en stelt het juiste print format in.

```javascript
frappe.ui.form.on('Sales Invoice', {
    refresh: function(frm) {
        if (frm.doc.docstatus === 2) return; // Alleen op Submitted facturen

        // ===================================================================
        // Hulp functie: beste e-mailadres van de klant ophalen
        // ===================================================================
        function get_customer_email(frm, callback) {
            if (frm.doc.contact_email) {
                return callback(frm.doc.contact_email);
            }
            if (!frm.doc.customer) {
                return callback('');
            }

            frappe.call({
                method: 'frappe.client.get_value',
                args: {
                    doctype: 'Customer',
                    filters: { name: frm.doc.customer },
                    fieldname: ['customer_primary_contact', 'email_id']
                },
                callback: function(r) {
                    if (!r.message) {
                        fallback_to_any_contact_email();
                        return;
                    }
                    const cust = r.message;

                    if (cust.customer_primary_contact) {
                        frappe.call({
                            method: 'frappe.client.get_value',
                            args: {
                                doctype: 'Contact',
                                filters: { name: cust.customer_primary_contact },
                                fieldname: 'email_id'
                            },
                            callback: function(c) {
                                if (c.message && c.message.email_id) {
                                    callback(c.message.email_id);
                                } else if (cust.email_id) {
                                    callback(cust.email_id);
                                } else {
                                    fallback_to_any_contact_email();
                                }
                            }
                        });
                    } else if (cust.email_id) {
                        callback(cust.email_id);
                    } else {
                        fallback_to_any_contact_email();
                    }
                }
            });

            function fallback_to_any_contact_email() {
                frappe.call({
                    method: 'frappe.client.get_list',
                    args: {
                        doctype: 'Dynamic Link',
                        filters: {
                            parenttype: 'Contact',
                            link_doctype: 'Customer',
                            link_name: frm.doc.customer
                        },
                        fields: ['parent'],
                        limit_page_length: 1,
                        order_by: 'creation asc'
                    },
                    callback: function(r) {
                        if (!r.message || r.message.length === 0) {
                            callback('');
                            return;
                        }
                        frappe.call({
                            method: 'frappe.client.get_value',
                            args: {
                                doctype: 'Contact',
                                filters: { name: r.message[0].parent },
                                fieldname: 'email_id'
                            },
                            callback: function(c) {
                                callback(c.message && c.message.email_id ? c.message.email_id : '');
                            }
                        });
                    }
                });
            }
        }

        // ===================================================================
        // Hulp functie: Email Account van huidige gebruiker ophalen
        // ===================================================================
        function get_email_account(callback) {
            frappe.call({
                method: 'frappe.client.get_value',
                args: {
                    doctype: 'Email Account',
                    filters: { email_id: frappe.session.user_email },
                    fieldname: 'name'
                },
                callback: function(r) {
                    callback(r.message ? r.message.name : null);
                }
            });
        }

        // ===================================================================
        // Algemene functie: open Communication Composer met template + bijlage
        // ===================================================================
        function open_email_composer(template_name, print_format, subject_prefix) {
            get_email_account(function(email_account_name) {
                if (!email_account_name) {
                    frappe.msgprint(__('Geen Email Account gevonden voor ') + frappe.session.user_email);
                    return;
                }

                get_customer_email(frm, function(recipient) {
                    if (!recipient) {
                        frappe.msgprint(__('Geen e-mailadres gevonden voor deze klant.'));
                        return;
                    }

                    frappe.call({
                        method: 'frappe.email.doctype.email_template.email_template.get_email_template',
                        args: {
                            template_name: template_name,
                            doc: frm.doc
                        },
                        freeze: true,
                        freeze_message: __('Template laden...'),
                        callback: function(r) {
                            if (!r.message) {
                                frappe.msgprint(__('Fout bij laden van e-mailtemplate.'));
                                return;
                            }

                            // FIX 1: Aanhef personaliseren
                            let message = r.message.message;
                            if (frm.doc.contact_display) {
                                const firstName = frm.doc.contact_display.split(' ')[0];
                                message = message.replace('Geachte heer/mevrouw,', `Geachte ${firstName},`);
                            }

                            // FIX 2: signature: false zodat oude handtekening NIET wordt toegevoegd
                            const composer = new frappe.views.CommunicationComposer({
                                doc: frm.doc,
                                frm: frm,
                                subject: r.message.subject || __(subject_prefix) + frm.doc.name,
                                recipients: recipient,
                                message: message,
                                attach_document_print: true,
                                print_format: print_format,
                                print_letterhead: true,
                                email_template: template_name,
                                sender: frappe.session.user_email,
                                email_account: email_account_name,
                                signature: false,  // GEEN automatische signature!
                                send_read_receipt: false
                            });

                            // FIX 3: Handmatig print format instellen na dialog setup
                            setTimeout(() => {
                                if (composer.dialog && composer.dialog.fields_dict.select_print_format) {
                                    composer.dialog.set_value('select_print_format', print_format);
                                }
                            }, 200);

                            // FIX 4: Automatisch "Add Signature" klikken
                            setTimeout(() => {
                                const $addSigBtn = composer.dialog.$wrapper.find('button:contains("Add Signature")');
                                if ($addSigBtn.length) {
                                    $addSigBtn.click();
                                }
                            }, 300);
                        }
                    });
                });
            });
        }

        // ===================================================================
        // Knoppen
        // ===================================================================
        frm.add_custom_button(__('Email Invoice'), function() {
            open_email_composer('Factuur template', '[BEDRIJFSNAAM] Factuur Nieuw', 'Factuur: ');
        }, __('Communication'));

        frm.add_custom_button(__('Email Invoice With Timesheet'), function() {
            open_email_composer('Factuur template', '[BEDRIJFSNAAM] Factuur+Urenstaat Nieuw', 'Factuur: ');
        }, __('Communication'));

        if (frm.doc.is_return || frm.doc.return_against) {
            frm.add_custom_button(__('Email Credit Note'), function() {
                open_email_composer('Credit-template', '[BEDRIJFSNAAM] Credit Nieuw', 'Creditnota: ');
            }, __('Communication'));
        }

        // ===================================================================
        // Betalingsherinnering (alleen bij vervallen + openstaand bedrag)
        // ===================================================================
        if (
            !frm.doc.is_return &&
            frm.doc.outstanding_amount > 0 &&
            frm.doc.due_date &&
            frappe.datetime.str_to_obj(frm.doc.due_date) < frappe.datetime.get_today()
        ) {
            frm.add_custom_button(__('Betalingsherinnering 1'), function() {
                open_email_composer(
                    'Betalingsherinnering 1',
                    '[BEDRIJFSNAAM] Factuur Nieuw',
                    '1e betalingsherinnering: '
                );
            }, __('Communication'));

            setTimeout(() => {
                frm.page.btn_secondary
                    .find('button:contains("Betalingsherinnering 1")')
                    .css({
                        'background-color': '#fff3cd',
                        'border-color': '#ffeaa7',
                        'color': '#856404'
                    });
            }, 300);
        }
    }
});
```

---

## 3. Offerte / Verkoop

### 3.1 Accept Quotation (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Accept Quotation |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** Publiek API-endpoint dat klanten in staat stelt een offerte te accepteren of te bekijken via een beveiligde link. Werkt met een MD5-token voor authenticatie. Bij acceptatie wordt de offertestatus gewijzigd naar "Ordered" en ontvangt de beheerder een notificatie per e-mail. De klant krijgt een bevestigingspagina te zien met bedrijfsbranding. Bij het bekijken van de offerte wordt een overzichtspagina getoond met alle items, prijzen en een acceptatieknop.

```python
def generate_token(qname):
    result = frappe.db.sql("SELECT MD5(CONCAT(%s, '[secret_salt]'))", qname)
    return result[0][0][:32] if result else ''

quotation = frappe.form_dict.get('quotation')
token = frappe.form_dict.get('token')
action = frappe.form_dict.get('action')

if not quotation or not token:
    frappe.respond_as_web_page('Ongeldige link', '<div style="text-align:center;max-width:600px;margin:40px auto;font-family:sans-serif;"><h2>Ongeldige link</h2><p>Deze link is ongeldig of onvolledig.</p></div>')
elif token != generate_token(quotation):
    frappe.respond_as_web_page('Ongeldige link', '<div style="text-align:center;max-width:600px;margin:40px auto;font-family:sans-serif;"><h2>Ongeldige of verlopen link</h2><p>Deze link is niet meer geldig.</p></div>')
else:
    doc = frappe.get_doc('Quotation', quotation)

    if action == 'accept':
        if doc.docstatus == 1 and doc.status not in ['Ordered', 'Lost']:
            doc.db_set('status', 'Ordered')
            frappe.db.commit()

            frappe.sendmail(
                recipients=['[email]@[bedrijf].nl'],
                subject='Offerte ' + str(quotation) + ' geaccepteerd door ' + str(doc.party_name),
                message='<p>De klant <b>' + str(doc.party_name) + '</b> heeft offerte <b>' + str(quotation) + '</b> (' + str(doc.custom_description or '') + ') geaccepteerd via de acceptatielink.</p><p>De offertestatus is automatisch gewijzigd naar "Ordered".</p>',
                now=True
            )

            description = str(doc.custom_description or quotation)
            html = '<div style="text-align:center;max-width:600px;margin:40px auto;font-family:sans-serif;color:#350E35;">'
            html = html + '<img src="[ERPNext URL]/files/[BEDRIJFSNAAM] New Logo.png" alt="[BEDRIJFSNAAM]" style="height:50px;">'
            html = html + '<div style="background:#f0faf9;border:2px solid #00A19A;border-radius:12px;padding:30px;margin:20px 0;">'
            html = html + '<h2 style="color:#00A19A;">Offerte geaccepteerd!</h2>'
            html = html + '<p>Bedankt voor uw bevestiging.</p>'
            html = html + '<p>Wij gaan voor u aan de slag met <b>' + description + '</b>.</p>'
            html = html + '<p>U ontvangt binnenkort een opdrachtbevestiging van ons.</p>'
            html = html + '</div>'
            html = html + '<p style="font-size:13px;color:#888;margin-top:30px;">Ingenieursbureau [BEDRIJFSNAAM] &middot; [adres] &middot; [postcode] [plaatsnaam] &middot; [telefoonnummer]</p>'
            html = html + '</div>'
            frappe.respond_as_web_page('Offerte Geaccepteerd', html)

        else:
            html = '<div style="text-align:center;max-width:600px;margin:40px auto;font-family:sans-serif;color:#350E35;">'
            html = html + '<img src="[ERPNext URL]/files/[BEDRIJFSNAAM] New Logo.png" alt="[BEDRIJFSNAAM]" style="height:50px;">'
            html = html + '<h2>Deze offerte is al verwerkt</h2>'
            html = html + '<p>Deze offerte is al eerder geaccepteerd of is niet meer beschikbaar.</p>'
            html = html + '</div>'
            frappe.respond_as_web_page('Offerte Verwerkt', html)

    else:
        currency = 'EUR'
        try:
            curr_name = frappe.db.get_value('Company', doc.company, 'default_currency')
            currency = str(frappe.db.get_value('Currency', curr_name, 'symbol') or 'EUR')
        except Exception:
            pass

        items_html = ''
        idx = 0
        for item in doc.items:
            idx = idx + 1
            desc_short = str(item.description or '').replace('\n', '<br>')[:200]
            if item.uom == 'Hour':
                items_html = items_html + '<tr><td style="padding:10px 8px;border-bottom:1px solid #eee"><b>Deel ' + str(idx) + ': ' + str(item.item_name) + '</b><br><span style="font-size:13px;color:#666">' + desc_short + '</span></td><td style="padding:10px 8px;border-bottom:1px solid #eee;text-align:right;white-space:nowrap">' + currency + ' ' + str(int(item.rate)) + ',- /uur</td></tr>'
            else:
                items_html = items_html + '<tr><td style="padding:10px 8px;border-bottom:1px solid #eee"><b>Deel ' + str(idx) + ': ' + str(item.item_name) + '</b><br><span style="font-size:13px;color:#666">' + desc_short + '</span></td><td style="padding:10px 8px;border-bottom:1px solid #eee;text-align:right;white-space:nowrap">' + currency + ' ' + str(int(item.amount)) + ',-</td></tr>'

        accept_url = '/api/method/accept_quotation?quotation=' + str(quotation) + '&token=' + str(token) + '&action=accept'
        description = str(doc.custom_description or quotation)
        total_str = str(int(doc.total))
        grand_total_str = str(int(doc.grand_total))
        date_str = str(doc.transaction_date)
        valid_str = str(doc.valid_till)

        html = '<div style="max-width:700px;margin:40px auto;font-family:sans-serif;color:#350E35;">'
        html = html + '<img src="[ERPNext URL]/files/[BEDRIJFSNAAM] New Logo.png" alt="[BEDRIJFSNAAM]" style="height:50px;margin-bottom:20px;">'
        html = html + '<h1 style="color:#00A19A;font-size:22px;">' + description + '</h1>'
        html = html + '<p>Offerte: <b>' + str(quotation) + '</b><br>Datum: ' + date_str + '<br>Geldig tot: ' + valid_str + '</p>'
        html = html + '<div style="background:#fafafa;border:1px solid #ddd;border-radius:12px;padding:25px;margin:20px 0;">'
        html = html + '<table style="width:100%;border-collapse:collapse;">'
        html = html + items_html
        html = html + '<tr><td style="padding:12px 8px;"><b>Totaal excl. BTW</b></td><td style="padding:12px 8px;text-align:right;font-weight:bold;font-size:18px;color:#00A19A;">' + currency + ' ' + total_str + ',-</td></tr>'
        html = html + '<tr><td style="padding:8px;"><b>Totaal incl. BTW</b></td><td style="padding:8px;text-align:right;">' + currency + ' ' + grand_total_str + ',-</td></tr>'
        html = html + '</table></div>'
        html = html + '<div style="text-align:center;margin:30px 0;">'
        html = html + '<p>Gaat u akkoord met deze offerte?</p>'
        html = html + '<a href="' + accept_url + '" style="display:inline-block;background:#00A19A;color:white;padding:14px 40px;border-radius:8px;text-decoration:none;font-size:16px;font-weight:bold;margin:10px;">Offerte Accepteren</a>'
        html = html + '</div>'
        html = html + '<p style="font-size:13px;color:#888;margin-top:30px;text-align:center;">Ingenieursbureau [BEDRIJFSNAAM] &middot; [adres] &middot; [postcode] [plaatsnaam] &middot; [telefoonnummer]</p>'
        html = html + '</div>'
        frappe.respond_as_web_page('Offerte ' + str(quotation), html)
```

---

### 3.2 get_portal_quotations (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | get_portal_quotations |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** Portal-API die offertes retourneert voor de ingelogde portal-gebruiker. Zoekt de gekoppelde klant op via User Permission en haalt vervolgens alle submitted Quotations op die gefilterd zijn op `custom_customer`. Retourneert maximaal 100 offertes gesorteerd op datum (nieuwste eerst).

```python
# Portal API: Returns quotations for the current logged-in portal user
# Looks up the Customer linked to the user via User Permission,
# then returns submitted Quotations filtered by custom_customer.

user = frappe.session.user

if user == 'Guest':
	frappe.response['message'] = []
else:
	# Get customer linked to this portal user
	customer = frappe.db.get_value('User Permission',
		{'user': user, 'allow': 'Customer'}, 'for_value')

	if not customer:
		frappe.response['message'] = []
	else:
		quotations = frappe.get_all('Quotation',
			filters={'custom_customer': customer, 'docstatus': 1},
			fields=['name', 'party_name', 'custom_description', 'grand_total',
				'transaction_date', 'status', 'valid_till'],
			order_by='transaction_date desc',
			limit_page_length=100,
			ignore_permissions=True
		)
		frappe.response['message'] = quotations
```

---

### 3.3 Email Quotation (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Email Quotation |
| **DocType** | Quotation |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Voegt een "E-mail opstellen"-knop toe aan het Quotation-formulier. Gebruikt het e-mailtemplate "Offerte 1", personaliseert de aanhef en voegt de gebruikershandtekening toe. Synchroniseert ook automatisch het `custom_customer`-veld met `party_name` wanneer het offerteype "Customer" is.

```javascript
frappe.ui.form.on('Quotation', {
    refresh: function(frm) {
        if (frm.doc.quotation_to === 'Customer' && frm.doc.party_name && frm.doc.custom_customer !== frm.doc.party_name) {
            frm.set_value('custom_customer', frm.doc.party_name);
        }

        frm.add_custom_button(__('E-mail opstellen'), function() {
            frappe.call({
                method: 'frappe.client.get_list',
                args: {
                    doctype: 'Email Account',
                    filters: {
                        email_id: frappe.session.user,
                        enable_outgoing: 1
                    },
                    fields: ['name', 'signature'],
                    limit_page_length: 1
                },
                callback: function(account_response) {
                    var user_signature = '';
                    if (account_response.message && account_response.message.length > 0) {
                        user_signature = account_response.message[0].signature || '';
                    }

                    frappe.call({
                        method: 'frappe.email.doctype.email_template.email_template.get_email_template',
                        args: {
                            template_name: 'Offerte 1',
                            doc: frm.doc
                        },
                        callback: function(r) {
                            if (!r.exc && r.message) {
                                var message = r.message.message || '';
                                if (frm.doc.contact_display) {
                                    message = message.replace('Geachte heer/mevrouw', 'Beste ' + frm.doc.contact_display);
                                }

                                // Voeg de gebruiker-specifieke ondertekening toe
                                if (user_signature) {
                                    message += '<br>' + user_signature;
                                }

                                new frappe.views.CommunicationComposer({
                                    doc: frm.doc,
                                    frm: frm,
                                    subject: r.message.subject || __('Offerte: ') + frm.doc.name,
                                    recipients: frm.doc.contact_email || '',
                                    message: message,
                                    attach_document_print: false,
                                    email_template: 'Offerte 1',
                                    signature: false
                                });
                            } else {
                                frappe.msgprint(__('Fout bij laden van template.'));
                            }
                        }
                    });
                }
            });
        }, __('Communicatie'));
    },
    party_name: function(frm) {
        if (frm.doc.quotation_to === 'Customer' && frm.doc.party_name) {
            frm.set_value('custom_customer', frm.doc.party_name);
        } else {
            frm.set_value('custom_customer', '');
        }
    },
    quotation_to: function(frm) {
        if (frm.doc.quotation_to === 'Customer' && frm.doc.party_name) {
            frm.set_value('custom_customer', frm.doc.party_name);
        } else {
            frm.set_value('custom_customer', '');
        }
    }
});
```

---

### 3.4 Sales Invoice Preview (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Sales Invoice Preview |
| **DocType** | Sales Invoice |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Toont een inline PDF-preview van de verkoopfactuur direct onder de itemstabel. De gebruiker kan kiezen uit meerdere print formats (Factuur, Factuur + Urenstaat, Credit Nota, Factuur met Timesheet, Winstuitkering). Bevat knoppen om de preview te verversen, de PDF in een nieuw tabblad te openen en te downloaden.

```javascript
frappe.ui.form.on('Sales Invoice', {
    refresh: function(frm) {
        if (!frm.is_new()) {
            $('.sales-invoice-preview').remove();

            // Beschikbare print formats
            let print_formats = [
                { name: '[BEDRIJFSNAAM] Factuur Nieuw', label: 'Factuur' },
                { name: '[BEDRIJFSNAAM] Factuur+Urenstaat Nieuw', label: 'Factuur + Urenstaat' },
                { name: '[BEDRIJFSNAAM] Credit Nieuw', label: 'Credit Nota' },
                { name: '[BEDRIJFSNAAM] factuur met timesheet', label: 'Factuur met Timesheet' },
                { name: '[BEDRIJFSNAAM] Winstuitkering', label: 'Winstuitkering' }
            ];

            // Bepaal standaard format op basis van docstatus en is_return
            let default_format = frm.doc.is_return ? '[BEDRIJFSNAAM] Credit Nieuw' : '[BEDRIJFSNAAM] Factuur Nieuw';

            // Bouw dropdown
            let options = print_formats.map(pf =>
                `<option value="${pf.name}" ${pf.name === default_format ? 'selected' : ''}>${pf.label}</option>`
            ).join('');

            let pdf_url = `/api/method/frappe.utils.print_format.download_pdf?doctype=Sales%20Invoice&name=${encodeURIComponent(frm.doc.name)}&format=${encodeURIComponent(default_format)}&no_letterhead=0`;

            let preview_html = `
                <div class="sales-invoice-preview" style="margin: 15px 0;">
                    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; flex-wrap: wrap; gap: 10px;">
                        <div style="display: flex; align-items: center; gap: 10px;">
                            <h4 style="margin: 0;"><i class="fa fa-file-text-o"></i> Factuur Preview</h4>
                            <select id="print-format-selector" class="form-control" style="width: auto; display: inline-block; min-width: 200px;">
                                ${options}
                            </select>
                        </div>
                        <div style="display: flex; gap: 5px;">
                            <button id="refresh-preview-btn" class="btn btn-xs btn-default">
                                <i class="fa fa-refresh"></i> Ververs
                            </button>
                            <button id="open-pdf-btn" class="btn btn-xs btn-default" data-url="${pdf_url}">
                                <i class="fa fa-external-link"></i> Open PDF
                            </button>
                            <button id="download-pdf-btn" class="btn btn-xs btn-primary">
                                <i class="fa fa-download"></i> Download
                            </button>
                        </div>
                    </div>
                    <div id="preview-container" style="border: 1px solid #d1d8dd; border-radius: 4px; background: #f5f5f5;">
                        <iframe
                            id="invoice-preview-frame"
                            src="${pdf_url}"
                            style="width: 100%; height: 800px; border: none;"
                            frameborder="0">
                        </iframe>
                    </div>
                </div>
            `;

            $(frm.fields_dict.items.wrapper).after(preview_html);

            // Store doc name for URL building
            window._invoice_name = frm.doc.name;

            // Event handlers
            $('#print-format-selector').on('change', function() {
                let format = $(this).val();
                let new_url = `/api/method/frappe.utils.print_format.download_pdf?doctype=Sales%20Invoice&name=${encodeURIComponent(window._invoice_name)}&format=${encodeURIComponent(format)}&no_letterhead=0`;
                $('#invoice-preview-frame').attr('src', new_url);
                $('#open-pdf-btn').data('url', new_url);
            });

            $('#refresh-preview-btn').on('click', function() {
                let frame = $('#invoice-preview-frame')[0];
                frame.src = frame.src;
            });

            $('#open-pdf-btn').on('click', function() {
                window.open($(this).data('url'), '_blank');
            });

            $('#download-pdf-btn').on('click', function() {
                let format = $('#print-format-selector').val();
                let url = `/api/method/frappe.utils.print_format.download_pdf?doctype=Sales%20Invoice&name=${encodeURIComponent(window._invoice_name)}&format=${encodeURIComponent(format)}&no_letterhead=0`;
                // Trigger download
                let a = document.createElement('a');
                a.href = url;
                a.download = `${window._invoice_name}.pdf`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
            });
        }
    }
});
```

---

## 4. Timesheet / Project

### 4.1 BWK_timesheet_jochem_test (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | BWK_timesheet_jochem_test |
| **Type** | Scheduler Event |
| **Reference DocType** | — |

**Beschrijving:** Experimenteel script voor het automatisch genereren van een timesheet-rapport als PDF of HTML. Berekent de vorige maand, haalt rapportdata op via `frappe.desk.query_report.run()`, bouwt een HTML-tabel met totalen, en probeert deze via pdfkit naar PDF te converteren. Als dat mislukt, wordt het HTML-bestand opgeslagen in de bestandenbibliotheek.

```python
# Automatisch report printen en opslaan

# Bepaal vorige maand
today = frappe.utils.now_datetime()
last_month = frappe.utils.add_months(today, -1)

# Bepaal de periode naam
month_names = {
    1: 'Januari', 2: 'Februari', 3: 'Maart', 4: 'April',
    5: 'Mei', 6: 'Juni', 7: 'Juli', 8: 'Augustus',
    9: 'September', 10: 'Oktober', 11: 'November', 12: 'December'
}
period_name = month_names.get(last_month.month, 'Vorige maand')

# Filters voor het report
filters = {
    "Period": period_name,
    "Project_Company": ""  # Of vul een specifieke company in
}

# Haal report data op
from frappe.desk.query_report import run
report_data = run("test", filters=frappe.as_json(filters))

# Maak HTML van de data
columns = report_data.get('columns', [])
data = report_data.get('result', [])

html = f"""
<html>
<head>
    <style>
        table {{ border-collapse: collapse; width: 100%; font-size: 11px; }}
        th, td {{ border: 1px solid #ddd; padding: 8px; text-align: left; }}
        th {{ background-color: #4CAF50; color: white; }}
        h2 {{ color: #333; }}
    </style>
</head>
<body>
    <h2>Timesheet Report - {period_name} {last_month.year}</h2>
    <table>
        <thead><tr>
"""

# Headers
for col in columns:
    if isinstance(col, dict):
        html += f"<th>{col.get('label', '')}</th>"
    else:
        html += f"<th>{col}</th>"

html += "</tr></thead><tbody>"

# Data rows
total_hours = 0
for row in data:
    html += "<tr>"
    for i, cell in enumerate(row):
        html += f"<td>{cell if cell is not None else ''}</td>"
        # Tel hours op (veronderstel dat hours op positie 8 staat)
        if i == 8 and cell:
            try:
                total_hours += float(cell)
            except:
                pass
    html += "</tr>"

# Totaal rij
html += f"""
        <tr style="font-weight: bold; background-color: #f2f2f2;">
            <td colspan="8">TOTAAL</td>
            <td>{total_hours:.2f}</td>
            <td></td>
        </tr>
    </tbody></table>
</body>
</html>
"""

# Probeer PDF te maken met verschillende methodes
pdf_content = None
error_msg = ""

# Methode 1: Probeer pdfkit
try:
    import pdfkit
    pdf_content = pdfkit.from_string(html, False)
except Exception as e:
    error_msg += f"pdfkit failed: {str(e)}; "

# Methode 2: Als pdfkit niet werkt, sla HTML op
if not pdf_content:
    file_name = f"timesheet_report_{last_month.year}_{last_month.month:02d}.html"
    file_doc = frappe.get_doc({
        "doctype": "File",
        "file_name": file_name,
        "content": html.encode('utf-8'),
        "is_private": 0,
        "folder": "Home"
    })
    file_doc.save()
    frappe.db.commit()
    frappe.logger().info(f"HTML report saved: {file_name} (PDF conversion failed: {error_msg})")
else:
    # Sla PDF op
    file_name = f"timesheet_report_{last_month.year}_{last_month.month:02d}.pdf"
    file_doc = frappe.get_doc({
        "doctype": "File",
        "file_name": file_name,
        "content": pdf_content,
        "is_private": 0,
        "folder": "Home"
    })
    file_doc.save()
    frappe.db.commit()
    frappe.logger().info(f"PDF report saved: {file_name}")
```

---

### 4.2 Create Tasks From Sales Order (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Create Tasks From Sales Order |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** API-endpoint dat taken aanmaakt in een project op basis van de items uit een gekoppelde Sales Order. Elk Sales Order-item wordt een taak met het itemnaam als onderwerp, de beschrijving en het verwacht aantal uren (qty). Wordt aangeroepen vanuit het client script "Create Task from Sales Order".

```python
def create_tasks_from_sales_order(project_name, sales_order_name):
    if not sales_order_name:
        frappe.throw("Geen Sales Order gelinkt aan dit project.")

    # Haal de Sales Order op met items
    sales_order = frappe.get_doc("Sales Order", sales_order_name)

    created_tasks = []
    for item in sales_order.items:
        # Bepaal expected_time: qty van het item (aantal uren)
        expected_hours = item.qty or 0

        # Maak nieuwe Task
        new_task = frappe.get_doc({
            "doctype": "Task",
            "subject": item.item_name or "Onbekend item",
            "description": item.description or "",
            "project": project_name,
            "expected_time": expected_hours,  # Aantal uren uit Sales Order Item
            "company": sales_order.company
        })
        new_task.insert(ignore_permissions=True)
        created_tasks.append(new_task.name)

    return created_tasks
```

---

### 4.3 Create Task from Sales Order (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Create Task from Sales Order |
| **DocType** | Project |
| **View** | Form |
| **Enabled** | Nee (uitgeschakeld) |

**Beschrijving:** Voegt een knop "Maak Taken van Sales Order" toe aan het Project-formulier (alleen wanneer een Sales Order gekoppeld is). Na bevestiging wordt het bijbehorende server script aangeroepen om taken aan te maken op basis van de Sales Order-items.

```javascript
frappe.ui.form.on('Project', {
    refresh: function(frm) {
        if (frm.doc.sales_order) {  // Toon knop alleen als Sales Order gelinkt is
            frm.add_custom_button(__('Maak Taken van Sales Order'), function() {
                frappe.confirm(
                    __('Wil je taken aanmaken gebaseerd op de items uit de gekoppelde Sales Order?'),
                    function() {
                        // Roep de server-methode aan
                        frappe.call({
                            method: 'create_tasks_from_sales_order',
                            args: {
                                project_name: frm.doc.name,
                                sales_order_name: frm.doc.sales_order
                            },
                            callback: function(r) {
                                if (!r.exc && r.message) {
                                    frappe.msgprint(__('Taken succesvol aangemaakt: ') + r.message.join(', '));
                                    frm.refresh();
                                } else {
                                    frappe.msgprint(__('Fout bij aanmaken van taken: ') + (r.exc || __('Onbekende fout.')));
                                }
                            }
                        });
                    }
                );
            }, __('Taken'));
        }
    }
});
```

---

### 4.4 Fetch Timesheets from last month (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Fetch Timesheets from last month |
| **DocType** | Sales Invoice |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Voegt een "Fetch Timesheets Last Month"-knop toe aan het Sales Invoice-formulier. Haalt alle unieke projecten op uit de factuuritems, zoekt de bijbehorende timesheets van de vorige maand op en toont ze in een overzichtstabel met medewerker, uren, factureerbare uren en status.

```javascript
frappe.ui.form.on('Sales Invoice', {
    refresh: function(frm) {
        update_timesheet_button(frm);
    },

    items_add: function(frm) {
        update_timesheet_button(frm);
    },
    items_remove: function(frm) {
        update_timesheet_button(frm);
    },
    items_move: function(frm) {
        update_timesheet_button(frm);
    },

    onload_post: function(frm) {
        frm.fields_dict.items.grid.grid_rows.forEach(row => {
            row.on('project', () => update_timesheet_button(frm));
        });
    }
});

// Centrale functie - voorkomt code-duplicatie
function update_timesheet_button(frm) {
    frm.remove_custom_button(__('Fetch Timesheets Last Month'));

    if (!frm.doc.items || frm.doc.items.length === 0) return;

    const projects = [...new Set(
        frm.doc.items
            .filter(item => item.project)
            .map(item => item.project)
    )];

    if (projects.length === 0) return;

    frm.add_custom_button(__('Fetch Timesheets Last Month'), function() {
        fetch_and_show_timesheets(frm, projects);
    }, __('Timesheets'));

    frm.change_custom_button_type(__('Fetch Timesheets Last Month'), __('Timesheets'), 'primary');
}

function fetch_and_show_timesheets(frm, projects) {
    const project_to_use = projects.length === 1 ? projects[0] : projects[0];

    const today = frappe.datetime.str_to_obj(frappe.datetime.get_today());
    const firstDayLastMonth = new Date(today.getFullYear(), today.getMonth() - 1, 1);
    const lastDayLastMonth  = new Date(today.getFullYear(), today.getMonth(), 0);

    const from_date = frappe.datetime.obj_to_str(firstDayLastMonth).substr(0, 10);
    const to_date   = frappe.datetime.obj_to_str(lastDayLastMonth).substr(0, 10);
    const start_dt  = `${from_date} 00:00:00`;
    const end_dt    = `${to_date} 23:59:59`;

    frappe.call({
        method: "frappe.client.get_list",
        args: {
            doctype: "Timesheet Detail",
            fields: ["parent"],
            filters: {
                project: project_to_use,
                from_time: [">=", start_dt],
                to_time: ["<=", end_dt]
            },
            limit_page_length: 1000,
            distinct: true
        },
        freeze: true,
        freeze_message: __('Timesheets vorige maand ophalen...')
    }).then(r => {
        if (!r.message || r.message.length === 0) {
            frappe.msgprint({ message: __('Geen timesheets gevonden voor vorige maand.'), indicator: 'orange' });
            return;
        }

        const timesheet_names = r.message.map(d => d.parent);

        return frappe.call({
            method: "frappe.client.get_list",
            args: {
                doctype: "Timesheet",
                fields: ["name", "employee", "employee_name", "total_hours", "total_billable_hours", "status", "start_date", "end_date"],
                filters: { name: ["in", timesheet_names] },
                order_by: "start_date desc",
                limit_page_length: 500
            }
        });
    }).then(r => {
        if (!r || !r.message || r.message.length === 0) return;

        const timesheets = r.message;

        let rows = timesheets.map(ts => `
            <tr>
                <td>${frappe.utils.escape_html(ts.name)}</td>
                <td>${frappe.utils.escape_html(ts.employee_name || ts.employee || '')}</td>
                <td class="text-right">${frappe.format(ts.total_hours, {fieldtype: 'Float', precision: 2})}</td>
                <td class="text-right">${frappe.format(ts.total_billable_hours || 0, {fieldtype: 'Float', precision: 2})}</td>
                <td>${frappe.utils.get_indicator_html(ts.status)}</td>
                <td>${frappe.datetime.str_to_user(ts.start_date)}</td>
                <td>${frappe.datetime.str_to_user(ts.end_date)}</td>
            </tr>`).join('');

        let html = `
            <div class="mb-3">
                <strong>Project:</strong> ${frappe.utils.escape_html(project_to_use)}
                ${projects.length > 1 ? ' <small class="text-muted">(meerdere projecten, alleen eerste getoond)</small>' : ''}
                <br><strong>Periode:</strong> ${frappe.datetime.str_to_user(from_date)} t/m ${frappe.datetime.str_to_user(to_date)}
            </div>
            <table class="table table-bordered table-sm">
                <thead class="thead-light">
                    <tr>
                        <th>Timesheet</th><th>Medewerker</th><th>Totaal uren</th><th>Factureerbaar</th>
                        <th>Status</th><th>Van</th><th>Tot</th>
                    </tr>
                </thead>
                <tbody>${rows}</tbody>
            </table>`;

        frappe.msgprint({
            title: __('Timesheets vorige maand'),
            message: html,
            wide: true
        });
    }).catch(err => {
        frappe.show_alert({
            message: __('Fout bij ophalen timesheets: ') + (err.message || ''),
            indicator: 'red'
        });
    });
}
```

---

### 4.5 Timesheet Default Activity Type (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Timesheet Default Activity Type |
| **DocType** | Timesheet |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Stelt automatisch het standaard Activity Type in per medewerker bij het toevoegen van nieuwe timesheet-regels. Bevat een mapping van employee-codes naar activiteitstypes (bijv. "ENG_Senior Bouwkundig Ingenieur" voor Piet Mol). Wanneer een medewerker wordt geselecteerd of een nieuwe regel wordt toegevoegd, wordt het activity type automatisch ingevuld.

```javascript
// Standaard Activity Type per Employee
// Deployed via deploy_client_script.py

const EMPLOYEE_ACTIVITY_MAP = {
    "HR-EMP-00004": "ENG_Senior Bouwkundig Ingenieur", // Piet Mol
    // HR-EMP-00017: Hester Mol - geen default
    "HR-EMP-00027": "ENG_Medior Bouwkundig tekenaar",  // Joran
    "HR-EMP-00040": "ENG_Medior Bouwkundig tekenaar",  // Sam
    "HR-EMP-00047": "ENG_Medior Bouwkundig tekenaar",  // Maryam
};

frappe.ui.form.on("Timesheet Detail", {
    time_logs_add(frm, cdt, cdn) {
        const employee = frm.doc.employee;
        if (employee && EMPLOYEE_ACTIVITY_MAP[employee]) {
            frappe.model.set_value(cdt, cdn, "activity_type", EMPLOYEE_ACTIVITY_MAP[employee]);
        }
    }
});

frappe.ui.form.on("Timesheet", {
    employee(frm) {
        const activity = EMPLOYEE_ACTIVITY_MAP[frm.doc.employee];
        if (activity && frm.doc.time_logs) {
            frm.doc.time_logs.forEach(row => {
                if (!row.activity_type) {
                    frappe.model.set_value(row.doctype, row.name, "activity_type", activity);
                }
            });
            frm.refresh_field("time_logs");
        }
    }
});
```

---

### 4.6 Niet eigen project (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Niet eigen project |
| **DocType** | Project |
| **View** | Form |
| **Enabled** | Nee (uitgeschakeld) |

**Beschrijving:** Waarschuwt medewerkers wanneer zij een timesheet-regel aanmaken voor een project dat bij een andere company hoort dan hun eigen standaard-company. Vergelijkt de project-company met de user-company en toont een rode melding bij een mismatch.

```javascript
frappe.ui.form.on('Timesheet', {
    refresh(frm) {
        frm.fields_dict["time_logs"].grid.wrapper.on("change", "input, select", async function (e) {
            let fieldname = $(e.target).attr("data-fieldname");

            if (fieldname !== "project") return;

            let row = frm.fields_dict["time_logs"].grid.get_selected_children();
            if (!row || row.length === 0) return;

            row = row[0];

            if (!row.project) return;

            // Project company ophalen
            let project_data = await frappe.db.get_value("Project", row.project, "company");
            if (!project_data) return;

            let project_company = project_data.company;

            // User company ophalen
            let user_company = frappe.defaults.get_user_default("Company");

            // fallback
            if (!user_company) {
                let user = await frappe.db.get_value("User", frappe.session.user, "default_company");
                user_company = user ? user.default_company : null;
            }

            if (!user_company) return;

            // Vergelijking -> melding
            if (project_company !== user_company) {
                frappe.msgprint({
                    title: __("Project Company Mismatch"),
                    message: __(
                        `Dit project hoort bij <b>${project_company}</b>, maar jouw company is <b>${user_company}</b>.`
                    ),
                    indicator: "red"
                });
            }
        });
    }
});
```

---

### 4.7 Show timesheet details (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Show timesheet details |
| **DocType** | Project |
| **View** | Form |
| **Enabled** | Nee (uitgeschakeld) |

**Beschrijving:** Voegt een "Timesheet"-knop toe aan het Project-formulier die de gebruiker doorverwijst naar het "Timesheet Tracker"-rapport, gefilterd op het huidige project.

```javascript
frappe.ui.form.on('Project', {
refresh: function(frm) {
frm.add_custom_button(__('Timesheet'), function() {
frappe.route_options = {
"project": frm.doc.name
};
frappe.set_route("query-report", "Timesheet%Tracker");
});
}
});
```

---

### 4.8 Open projectfolder (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Open projectfolder |
| **DocType** | Project |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Voegt een "Open Projectfolder"-knop toe aan het Project-formulier. Bouwt het pad op naar de lokale Windows-projectmap op basis van de company (met mapping naar mapnamen) en het projectnummer/naam. Probeert de map te openen via `file:///` URI en toont het pad als fallback.

```javascript
frappe.ui.form.on('Project', {
    refresh: function(frm) {
        // Voeg een custom knop toe aan het formulier (zonder groep)
        frm.add_custom_button(__('Open Projectfolder'), function() {
            // Mapping voor company naar code
            var company_map = {
                '[BEDRIJFSNAAM] Cooperatie': '1_[bedrijfsnaam]_cooperatie',
                '[BEDRIJFSNAAM] V.O.F.': '3_[bedrijfsnaam]_bouwtechniek',
                '[BEDRIJFSNAAM] Engineering': '5_[bedrijfsnaam]_engineering',
                '[BEDRIJFSNAAM] Bouwkunde': '7_[bedrijfsnaam]_bouwkunde'
            };

            // Haal de company code op (fallback naar 'unknown' als niet gevonden)
            var company_code = company_map[frm.doc.company] || 'unknown';

            // Haal projectnummer (ID) en naam op
            var project_number = frm.doc.name || 'unknown';
            var project_name = frm.doc.project_name || 'unknown';

            // Bouw de mapnaam op (gebruik forward slashes voor file URI)
            var path = 'C:/[BEDRIJFSNAAM]/50_projecten/' + company_code + '/' + project_number + ' ' + project_name;

            // Probeer de map te openen in Windows Verkenner via file URI
            window.open('file:///' + path);

            // Alternatief: Toon de path in een bericht voor kopieren
            frappe.msgprint(__('Open deze map in Verkenner: ') + path);
        });
    }
});
```

---

### 4.9 Project - Opdrachtbevestiging Knop (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Project - Opdrachtbevestiging Knop |
| **DocType** | Project |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Voegt een primaire "Opdrachtbevestiging"-knop toe aan het Project-formulier (alleen bij bestaande projecten met een klant). Zoekt het e-mailadres van de klant op via contacten, rendert het e-mailtemplate "Opdrachtbevestiging Project" en opent de Communication Composer.

```javascript
frappe.ui.form.on('Project', {
    refresh: function(frm) {
        if (!frm.is_new() && frm.doc.customer) {
            frm.add_custom_button(__('Opdrachtbevestiging'), function() {
                // Get contact email for customer
                frappe.call({
                    method: 'frappe.client.get_list',
                    args: {
                        doctype: 'Contact',
                        filters: [
                            ['Dynamic Link', 'link_doctype', '=', 'Customer'],
                            ['Dynamic Link', 'link_name', '=', frm.doc.customer]
                        ],
                        fields: ['email_id'],
                        limit_page_length: 1
                    },
                    callback: function(contact_r) {
                        let recipient = '';
                        if (contact_r.message && contact_r.message.length > 0) {
                            recipient = contact_r.message[0].email_id || '';
                        }

                        // Get rendered template
                        frappe.call({
                            method: 'frappe.email.doctype.email_template.email_template.get_email_template',
                            args: {
                                template_name: 'Opdrachtbevestiging Project',
                                doc: frm.doc
                            },
                            callback: function(template_r) {
                                if (template_r.message) {
                                    new frappe.views.CommunicationComposer({
                                        doc: frm.doc,
                                        frm: frm,
                                        subject: template_r.message.subject,
                                        recipients: recipient,
                                        message: template_r.message.message,
                                        attach_document_print: false
                                    });
                                }
                            }
                        });
                    }
                });
            }).addClass('btn-primary');
        }
    }
});
```

---

### 4.10 Project Company Default (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Project Company Default |
| **DocType** | Project |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Stelt automatisch de standaard-company in bij het aanmaken van een nieuw project, op basis van de gebruikersinstelling `default_company`.

```javascript
frappe.ui.form.on('Project', {
    onload: function(frm) {
        if (frm.is_new() && !frm.doc.company) {
            let default_company = frappe.defaults.get_user_default("company");
            if (default_company) {
                frm.set_value('company', default_company);
            }
        }
    }
});
```

---

### 4.11 Download Info (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Download Info |
| **DocType** | Project |
| **View** | List |
| **Enabled** | Nee (uitgeschakeld) |

**Beschrijving:** Voegt een "Download JSON"-knop toe aan het Project-formulier. Exporteert projectgegevens (naam, status, klant, datums, voortgang, locatie, taken) als JSON-bestand voor lokale download.

```javascript
frappe.ui.form.on('Project', {
    refresh: function(frm) {
        frm.add_custom_button(__('Download JSON'), function() {
            // Haal eerst het gekoppelde adres op via server call
            frappe.call({
                method: 'frappe.client.get_list',
                args: {
                    doctype: 'Address',
                    filters: [
                        ['Dynamic Link', 'link_doctype', '=', 'Project'],
                        ['Dynamic Link', 'link_name', '=', frm.doc.name]
                    ],
                    fields: [
                        'name', 'address_title', 'address_line1', 'address_line2',
                        'city', 'pincode', 'state', 'country'
                    ]
                },
                async: false,
                callback: function(r) {
                    let address_data = null;

                    if (r.message && r.message.length > 0) {
                        let addr = r.message[0];
                        address_data = {
                            address_name: addr.name,
                            address_title: addr.address_title,
                            address_line1: addr.address_line1,
                            address_line2: addr.address_line2,
                            city: addr.city,
                            pincode: addr.pincode,
                            state: addr.state,
                            country: addr.country
                        };
                    }

                    // Verzamel project data
                    let project_data = {
                        project_name: frm.doc.project_name,
                        project_number: frm.doc.name,
                        status: frm.doc.status,
                        company: frm.doc.company,
                        customer: frm.doc.customer,
                        expected_start_date: frm.doc.expected_start_date,
                        expected_end_date: frm.doc.expected_end_date,
                        actual_start_date: frm.doc.actual_start_date,
                        actual_end_date: frm.doc.actual_end_date,
                        description: frm.doc.notes,
                        percent_complete: frm.doc.percent_complete,
                        project_location: address_data,
                        tasks: frm.doc.tasks || []
                    };

                    // Maak JSON download
                    let dataStr = JSON.stringify(project_data, null, 2);
                    let dataBlob = new Blob([dataStr], {type: 'application/json'});
                    let url = URL.createObjectURL(dataBlob);

                    let a = document.createElement('a');
                    a.href = url;
                    a.download = frm.doc.name + '.json';
                    a.click();
                    URL.revokeObjectURL(url);

                    frappe.show_alert({
                        message: __('Project JSON gedownload'),
                        indicator: 'green'
                    });
                }
            });
        });
    }
});
```

---

## 5. Bank / Reconciliatie

### 5.1 Reconcile Bank Transaction (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Reconcile Bank Transaction |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** API-endpoint voor het koppelen (reconcilieren) van banktransacties aan Payment Entries, Journal Entries, Sales Invoices of Purchase Invoices. Ondersteunt ook het submitten van conceptdocumenten (draait server-side om timestamp-problemen te voorkomen). Het gekoppelde document wordt als payment_entry aan de banktransactie toegevoegd met het volledig bedrag.

```python
# Haal parameters op
bank_transaction_name = frappe.form_dict.get("bank_transaction_name")
payment_entry = frappe.form_dict.get("payment_entry")
journal_entry = frappe.form_dict.get("journal_entry")
sales_invoice = frappe.form_dict.get("sales_invoice")
purchase_invoice = frappe.form_dict.get("purchase_invoice")
submit_doc = frappe.form_dict.get("submit_doc")
submit_doctype = frappe.form_dict.get("submit_doctype")

result = {}

# Submit a draft document if requested (runs server-side, no timestamp issues)
if submit_doc and submit_doctype:
    doc = frappe.get_doc(submit_doctype, submit_doc)
    if doc.docstatus == 0:
        doc.submit()
        result["submitted"] = submit_doc
        result["docstatus"] = doc.docstatus
        if submit_doctype == "Journal Entry":
            journal_entry = submit_doc
        elif submit_doctype == "Payment Entry":
            payment_entry = submit_doc

if not bank_transaction_name:
    if submit_doc:
        frappe.response["message"] = result
    else:
        frappe.throw("bank_transaction_name is verplicht")

if bank_transaction_name:
    bank_txn = frappe.get_doc("Bank Transaction", bank_transaction_name)

    doc_type = None
    doc_name = None

    if payment_entry:
        doc_type = "Payment Entry"
        doc_name = payment_entry
    elif journal_entry:
        doc_type = "Journal Entry"
        doc_name = journal_entry
    elif sales_invoice:
        doc_type = "Sales Invoice"
        doc_name = sales_invoice
    elif purchase_invoice:
        doc_type = "Purchase Invoice"
        doc_name = purchase_invoice

    if doc_type and doc_name:
        bank_txn.append("payment_entries", {
            "payment_document": doc_type,
            "payment_entry": doc_name,
            "allocated_amount": bank_txn.deposit or bank_txn.withdrawal
        })
        bank_txn.save(ignore_permissions=True)

    result["bank_transaction"] = bank_transaction_name
    result["status"] = bank_txn.status
    result["success"] = True
    frappe.response["message"] = result
```

---

## 6. Inkoop / Purchase Invoice

### 6.1 Purchase Invoice Preview (Client Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Purchase Invoice Preview |
| **DocType** | Purchase Invoice |
| **View** | Form |
| **Enabled** | Ja |

**Beschrijving:** Toont automatisch een preview van bijlagen (PDF, afbeeldingen, e-mails) direct onder de itemstabel van een inkoopfactuur. Bij meerdere bijlagen kan de gebruiker wisselen via een dropdown. Ondersteunt PDF (iframe), afbeeldingen (img-tag) en e-mailbestanden (.eml/.msg). Bevat een "Open in nieuw tabblad"-knop.

```javascript
frappe.ui.form.on('Purchase Invoice', {
    refresh: function(frm) {
        if (!frm.is_new()) {
            $('.supplier-invoice-preview').remove();

            frappe.call({
                method: 'frappe.client.get_list',
                args: {
                    doctype: 'File',
                    filters: {
                        attached_to_doctype: 'Purchase Invoice',
                        attached_to_name: frm.doc.name
                    },
                    fields: ['name', 'file_name', 'file_url', 'is_private'],
                    order_by: 'creation desc'
                },
                async: false,
                callback: function(r) {
                    let files = r.message || [];

                    // Categoriseer bestanden
                    let viewable_files = files.filter(f => {
                        if (!f.file_name) return false;
                        let ext = f.file_name.toLowerCase();
                        return ext.endsWith('.pdf') ||
                               ext.endsWith('.png') || ext.endsWith('.jpg') || ext.endsWith('.jpeg') ||
                               ext.endsWith('.gif') || ext.endsWith('.webp') || ext.endsWith('.bmp') ||
                               ext.endsWith('.eml') || ext.endsWith('.msg');
                    });

                    // Voeg type toe aan elk bestand
                    viewable_files.forEach(f => {
                        let ext = f.file_name.toLowerCase();
                        if (ext.endsWith('.pdf')) f.type = 'pdf';
                        else if (ext.endsWith('.eml') || ext.endsWith('.msg')) f.type = 'email';
                        else f.type = 'image';
                    });

                    if (viewable_files.length > 0) {
                        // Bouw dropdown
                        let options = viewable_files.map((f, idx) => {
                            let icon = f.type === 'pdf' ? '\ud83d\udcc4' : f.type === 'email' ? '\ud83d\udce7' : '\ud83d\uddbc\ufe0f';
                            return `<option value="${idx}" ${idx === 0 ? 'selected' : ''}>${icon} ${f.file_name}</option>`;
                        }).join('');

                        let selector_html = viewable_files.length > 1 ? `
                            <select id="file-selector" class="form-control" style="width: auto; display: inline-block; min-width: 250px;">
                                ${options}
                            </select>
                        ` : `<span style="color: #6c7680;">${viewable_files[0].file_name}</span>`;

                        let first_file = viewable_files[0];
                        let preview_content = getPreviewContent(first_file);

                        let preview_html = `
                            <div class="supplier-invoice-preview" style="margin: 15px 0;">
                                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; flex-wrap: wrap; gap: 10px;">
                                    <div style="display: flex; align-items: center; gap: 10px;">
                                        <h4 style="margin: 0;"><i class="fa fa-paperclip"></i> Bijlagen</h4>
                                        ${selector_html}
                                    </div>
                                    <button id="open-file-btn" class="btn btn-xs btn-default" data-url="${first_file.file_url}">
                                        <i class="fa fa-external-link"></i> Open in nieuw tabblad
                                    </button>
                                </div>
                                <div id="preview-container" style="border: 1px solid #d1d8dd; border-radius: 4px; background: #f5f5f5; min-height: 400px;">
                                    ${preview_content}
                                </div>
                            </div>
                        `;

                        $(frm.fields_dict.items.wrapper).after(preview_html);

                        // Store files data
                        window._preview_files = viewable_files;

                        // Event handlers
                        $('#file-selector').on('change', function() {
                            let idx = parseInt($(this).val());
                            let file = window._preview_files[idx];
                            $('#preview-container').html(getPreviewContent(file));
                            $('#open-file-btn').data('url', file.file_url);
                        });

                        $('#open-file-btn').on('click', function() {
                            window.open($(this).data('url'), '_blank');
                        });

                    } else {
                        let preview_html = `
                            <div class="supplier-invoice-preview" style="margin: 15px 0; padding: 20px; background: #f8f8f8; border: 1px dashed #d1d8dd; border-radius: 4px; text-align: center;">
                                <i class="fa fa-paperclip" style="font-size: 48px; color: #d1d8dd;"></i>
                                <p style="margin-top: 10px; color: #8d99a6;">Geen bijlagen gevonden (PDF, afbeelding of e-mail).</p>
                            </div>
                        `;
                        $(frm.fields_dict.items.wrapper).after(preview_html);
                    }
                }
            });
        }
    }
});

function getPreviewContent(file) {
    if (file.type === 'pdf') {
        return `<iframe src="${file.file_url}" style="width: 100%; height: 700px; border: none;" frameborder="0"></iframe>`;
    } else if (file.type === 'image') {
        return `<div style="padding: 20px; text-align: center; max-height: 700px; overflow: auto;">
            <img src="${file.file_url}" style="max-width: 100%; height: auto; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
        </div>`;
    } else if (file.type === 'email') {
        return `<iframe src="${file.file_url}" style="width: 100%; height: 700px; border: none; background: white;" frameborder="0"></iframe>`;
    }
    return '<p>Kan dit bestandstype niet weergeven.</p>';
}
```

---

## 7. Overige

### 7.1 Remove User Role (Server Script)

| Eigenschap | Waarde |
|---|---|
| **Naam** | Remove User Role |
| **Type** | API |
| **Reference DocType** | — |

**Beschrijving:** Beheertool die de rol "Accounts User" verwijdert uit het Role Profile "Werknemer" en vervolgens alle gebruikers met dit profiel bijwerkt. Retourneert een overzicht van de resterende rollen en bijgewerkte gebruikers.

```python
# Remove Accounts User from Werknemer Role Profile
rp = frappe.get_doc('Role Profile', 'Werknemer')
rp.roles = [r for r in rp.roles if r.role != 'Accounts User']
rp.save()
frappe.db.commit()

# Now update all users with this profile
users = frappe.get_all('User', filters={'role_profile_name': 'Werknemer', 'enabled': 1}, pluck='name')
results = []
for user_email in users:
    user_doc = frappe.get_doc('User', user_email)
    # remove_roles handles the actual removal
    user_doc.remove_roles('Accounts User')
    roles_left = [r.role for r in user_doc.roles]
    results.append({'user': user_email, 'roles': roles_left})

frappe.response['message'] = {
    'profile_roles': [r.role for r in rp.roles],
    'users_updated': results
}
```

---

## 8. Zelf scripts aanmaken in ERPNext

### Server Scripts aanmaken

1. Ga naar **Setup > Server Script** (of typ `/app/server-script` in de URL).
2. Klik op **+ Add Server Script**.
3. Vul de volgende velden in:
   - **Name:** Een duidelijke naam (bijv. "Update Invoice Status").
   - **Script Type:** Kies uit:
     - **DocType Event:** Wordt uitgevoerd bij events op een specifiek DocType (Before Save, After Insert, Before Submit, etc.).
     - **API:** Maakt een REST API-endpoint aan, bereikbaar via `/api/method/<scriptnaam>`.
     - **Scheduler Event:** Draait op een schema (dagelijks, wekelijks, etc.).
   - **Reference DocType:** (alleen bij DocType Event) Het DocType waarop het script reageert.
   - **Script:** De Python-code. Je hebt toegang tot:
     - `frappe` — het volledige Frappe-framework
     - `doc` — het huidige document (bij DocType Events)
     - `frappe.form_dict` — de request-parameters (bij API)

4. Klik op **Save**.

**Belangrijk:** Server Scripts draaien in een sandbox. Niet alle Python-modules zijn beschikbaar. `import os`, `import subprocess` en dergelijke zijn geblokkeerd.

### Client Scripts aanmaken

1. Ga naar **Setup > Client Script** (of typ `/app/client-script` in de URL).
2. Klik op **+ Add Client Script**.
3. Vul de volgende velden in:
   - **Name:** Een beschrijvende naam.
   - **DocType:** Het DocType waarop het script van toepassing is.
   - **View:** Kies uit Form of List.
   - **Enabled:** Vink aan om het script te activeren.
   - **Script:** De JavaScript-code. Gebruik:
     - `frappe.ui.form.on('DocType', { ... })` voor formulierevents.
     - `frappe.listview_settings['DocType']` voor lijstweergave-aanpassingen.

4. Klik op **Save**.

### Best practices

- **Naamgeving:** Gebruik duidelijke, beschrijvende namen die aangeven wat het script doet.
- **Foutafhandeling:** Gebruik altijd `try/except` (Python) of `try/catch` (JavaScript) om fouten op te vangen.
- **Logging:** Gebruik `frappe.log_error()` in server scripts voor foutregistratie.
- **Testen:** Test altijd eerst in een ontwikkel- of testomgeving. Gebruik `TEST_MODE = True` voor scripts die gegevens wijzigen.
- **Documentatie:** Voeg commentaar toe aan je scripts, vooral bij complexe logica.
- **Prestaties:** Vermijd zware database-queries in DocType Events die bij elke save draaien.

---

## 9. Tips voor Nederlandse gebruikers

### Taal en lokalisatie

- ERPNext ondersteunt Nederlandstalige vertalingen via het Translation-systeem. Gebruik `__('tekst')` in client scripts voor vertaalbare strings.
- E-mailtemplates kunnen in het Nederlands worden geschreven (zie de templates "Factuur template", "Betalingsherinnering 1", "Offerte 1").
- Gebruik de aanhefconventie "Geachte heer/mevrouw" als standaard en personaliseer automatisch met de voornaam uit `contact_display`.

### SEPA en bankieren

- SEPA-exports gebruiken het standaard formaat **pain.001.001.09** dat door alle Nederlandse banken wordt geaccepteerd.
- Zorg dat de **IBAN** en **BIC** correct zijn ingevuld bij zowel uw eigen Bank Account als die van leveranciers.
- Bij meerdere BV's of entiteiten (zoals in deze omgeving) is het raadzaam per entiteit een apart SEPA-exportscript te maken met vastgelegde bankgegevens.

### Facturering

- Het `custom_email_status`-veld op Sales Invoice is een custom field dat de e-mailstatus bijhoudt (Verstuurd / Herinnering 1 / Aanmaning).
- Het `custom_days_open`-veld wordt door het client script berekend en visueel weergegeven met kleurcodes.
- Het `custom_date_paid`-veld houdt de betaaldatum bij voor rapportage.

### Dunning / Betalingsherinneringen

- De automatische dunning-scripts werken met twee niveaus: Eerste Herinnering (na 3 dagen) en Aanmaning (na 24 dagen).
- Intercompany-klanten worden automatisch overgeslagen om interne facturen niet te herinneren.
- Gebruik `TEST_MODE = True` bij het eerste gebruik om te controleren of de juiste facturen worden geselecteerd.

### Multi-company omgeving

- Bij een multi-company structuur (zoals cooperatie, BV's, VOF) is het belangrijk om per entiteit de juiste cost centers, income accounts en bankgegevens te configureren.
- Het "Project Company Default"-script helpt om automatisch de juiste company in te stellen bij nieuwe projecten.
- Het "Niet eigen project"-script (momenteel uitgeschakeld) kan worden geactiveerd om medewerkers te waarschuwen bij cross-company timesheetregistratie.

---

## Overzichtstabel

### Server Scripts

| Naam | Type | DocType | Categorie |
|---|---|---|---|
| Accept Quotation | API | — | Offerte/Verkoop |
| BWK_timesheet_jochem_test | Scheduler Event | — | Timesheet/Project |
| Create Tasks From Sales Order | API | — | Timesheet/Project |
| Custom SEPA Export | API | — | SEPA/Betalingen |
| Custom SEPA Export Cooperatie | API | — | SEPA/Betalingen |
| Custom SEPA Export Engineering | API | — | SEPA/Betalingen |
| Dunning [BEDRIJFSNAAM] | Scheduler Event | — | E-mail/Notificaties |
| Dunning Cooperatie | Scheduler Event | — | E-mail/Notificaties |
| get_portal_quotations | API | — | Offerte/Verkoop |
| Mark Sales Invoice Email Sent | DocType Event | Communication | E-mail/Notificaties |
| Notify Timesheet Creation Failure | Scheduler Event | — | E-mail/Notificaties |
| Reconcile Bank Transaction | API | — | Bank/Reconciliatie |
| Remove User Role | API | — | Overige |

### Client Scripts

| Naam | DocType | View | Enabled | Categorie |
|---|---|---|---|---|
| Bulk Email Sales Invoices | Sales Invoice | List | Ja | E-mail/Notificaties |
| Create Task from Sales Order | Project | Form | Nee | Timesheet/Project |
| Download Info | Project | List | Nee | Timesheet/Project |
| Email Quotation | Quotation | Form | Ja | Offerte/Verkoop |
| Email Sales Invoice | Sales Invoice | Form | Ja | E-mail/Notificaties |
| Fetch Timesheets from last month | Sales Invoice | Form | Ja | Timesheet/Project |
| Niet eigen project | Project | Form | Nee | Timesheet/Project |
| Open projectfolder | Project | Form | Ja | Timesheet/Project |
| Payment Order SEPA Button | Payment Order | Form | Ja | SEPA/Betalingen |
| Project - Opdrachtbevestiging Knop | Project | Form | Ja | Offerte/Verkoop |
| Project Company Default | Project | Form | Ja | Timesheet/Project |
| Purchase Invoice Preview | Purchase Invoice | Form | Ja | Inkoop |
| Sales Invoice Preview | Sales Invoice | Form | Ja | Offerte/Verkoop |
| Show timesheet details | Project | Form | Nee | Timesheet/Project |
| Timesheet Default Activity Type | Timesheet | Form | Ja | Timesheet/Project |
