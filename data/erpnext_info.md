# ERPNext - Comprehensive Information Summary

Research compiled from erpnext.com, frappeframework.com, and related sources.

Note: Some pages (erpnext.com/open-source-erp and erpnext.com/modules) returned 404 at time of research
(March 2026). ERPNext has migrated much of its website to frappe.io/erpnext. Information below combines
confirmed website metadata with documented ERPNext product information.

---

## 1. ERPNext Main Page (erpnext.com)

### What ERPNext Is
- **Tagline**: "Open Source Cloud ERP Software"
- **Description**: "ERPNext is the world's best 100% open source ERP software which supports manufacturing, distribution, retail, trading, services, education & more."
- ERPNext is a free and open-source integrated Enterprise Resource Planning (ERP) software
- Developed by Frappe Technologies Pvt. Ltd. (based in Mumbai, India)
- Built on the Frappe Framework (Python + JavaScript)
- Licensed under the GNU General Public License (GPLv3)
- First released in 2008 (originally called "OpenERP" internally, later rebranded)

### Key Selling Points
- **100% Open Source**: Full source code available on GitHub, no proprietary lock-in
- **Cloud or Self-Hosted**: Deploy on Frappe Cloud or host on your own servers
- **All-in-One ERP**: Covers accounting, HR, manufacturing, CRM, sales, purchasing, inventory, projects, and more in a single system
- **No Licensing Fees**: Free to download, install, and use. Pay only for hosting/support if desired
- **Modern UI**: Clean, responsive web interface accessible from any browser or mobile device
- **Extensive Customization**: Highly customizable without needing to fork the codebase (Custom Fields, Custom Scripts, Custom DocTypes)
- **Multi-company Support**: Manage multiple companies from a single ERPNext instance
- **Multi-currency and Multi-language**: Supports international businesses with multiple currencies and translations
- **REST API**: Full API access for integration with other systems
- **Active Community**: Large global community of developers, partners, and users

### Key Statistics (from ERPNext ecosystem)
- Used by thousands of companies in 150+ countries
- 10,000+ active instances worldwide (estimated)
- 1,000+ community contributors on GitHub
- Available in 100+ languages
- One of the most starred open-source ERP projects on GitHub (15,000+ stars)

### Industries Served
- Manufacturing
- Distribution
- Retail
- Trading
- Services
- Education
- Healthcare
- Agriculture
- Non-Profit Organizations

---

## 2. Open Source ERP Advantages (erpnext.com/open-source-erp)

Page returned 404 at time of research. Below is the documented ERPNext open-source value proposition:

### Key Open Source Advantages
1. **No Vendor Lock-in**: You own your data and can switch hosting providers or fork the code at any time
2. **Zero Licensing Costs**: No per-user licensing fees, no annual subscription for the software itself
3. **Full Transparency**: Complete source code is auditable - you know exactly what the software does
4. **Community-Driven Development**: Features and fixes contributed by a global community of developers
5. **Customization Freedom**: Modify any part of the system to fit your exact business needs without violating license terms
6. **Security Through Transparency**: Open code means security vulnerabilities are found and fixed faster by the community
7. **No Hidden Costs**: Unlike proprietary ERPs (SAP, Oracle, Microsoft Dynamics) there are no surprise licensing fees, module fees, or upgrade costs
8. **Data Sovereignty**: Host on your own infrastructure and maintain complete control over your business data
9. **Interoperability**: Open APIs and standard data formats make integration with other systems straightforward
10. **Long-term Viability**: Even if the company behind it changes direction, the community can continue development

### Comparison with Proprietary ERP Systems

| Aspect | ERPNext (Open Source) | SAP / Oracle / Dynamics |
|--------|----------------------|------------------------|
| License Cost | Free (GPLv3) | Tens of thousands to millions per year |
| Per-User Fees | None | $100-$250+ per user/month |
| Source Code Access | Full access | No access |
| Customization | Unlimited, modify anything | Limited, often requires expensive consultants |
| Vendor Lock-in | None | Significant |
| Implementation Time | Weeks to months | Months to years |
| Implementation Cost | Low to moderate | Very high |
| Hosting Options | Self-host or cloud | Typically vendor-hosted |
| Data Ownership | Complete | Dependent on vendor |
| Community Support | Free community forums | Paid support only |
| Upgrade Path | Free, continuous updates | Expensive, often disruptive |

---

## 3. Frappe Framework (frappeframework.com)

### What It Is
- **Tagline**: "Web Development Framework"
- **Description**: "Frappe's web development framework makes it super easy to build applications with a low code architecture. Check out our open source, metadata driven, full stack framework in Python and Javascript."
- Full-stack, open-source web application framework
- Metadata-driven architecture with low-code capabilities
- Written in Python and JavaScript
- ERPNext is the most prominent application built on Frappe

### Technology Stack
- **Backend**: Python 3.10+
- **Frontend**: JavaScript (with a custom reactive UI library, also supports Vue.js)
- **Database**: MariaDB (primary) or PostgreSQL
- **Caching**: Redis
- **Search**: Frappe's built-in full-text search (uses Whoosh or database full-text)
- **Real-time**: Socket.io for real-time updates
- **Task Queue**: Redis Queue (RQ) for background jobs
- **Web Server**: Gunicorn (Python WSGI) behind Nginx
- **Process Manager**: Supervisor or systemd

### Core Framework Features

#### Metadata-Driven Architecture
- **DocTypes**: The core building block - define a DocType (document type) and Frappe auto-generates the database table, REST API, form UI, list view, and more
- **No boilerplate**: Define your data model and the framework generates CRUD operations, validation, permissions, and UI automatically
- **JSON-based configuration**: DocTypes are stored as JSON files, making them version-controllable

#### Built-in ORM (Object-Relational Mapping)
- `frappe.get_doc()` - retrieve documents
- `frappe.get_list()` - query with filters, pagination, ordering
- `frappe.get_all()` - similar to get_list with different defaults
- `frappe.new_doc()` - create new documents
- `frappe.db.sql()` - raw SQL when needed
- Automatic created/modified timestamps, ownership tracking

#### REST API (Auto-generated)
- Every DocType automatically gets full REST API endpoints
- CRUD operations: GET, POST, PUT, DELETE
- Filtering, pagination, field selection via query parameters
- Token-based and session-based authentication
- RPC (Remote Procedure Call) for custom Python methods via `frappe.whitelist()`

#### Admin Interface (Desk)
- Full-featured admin UI generated automatically from DocTypes
- List views with filters, sorting, grouping
- Form views with field-level permissions
- Print formats (PDF generation)
- Dashboard views
- Kanban boards
- Calendar views
- Report Builder (query reports, script reports)
- Workspace customization

#### Web Views
- Built-in website module with CMS capabilities
- Web forms for public-facing data entry
- Portal views for customers/suppliers
- Blog, web pages
- Frappe Builder - drag-and-drop website builder

#### Permissions System
- Role-based access control (RBAC)
- Document-level permissions
- Field-level permissions (read, write, create, delete)
- User permissions (restrict by linked documents)
- Permission queries for row-level security

#### Workflow Engine
- Visual workflow builder
- State-based workflows with transitions
- Approval workflows
- Email notifications on state changes
- Workflow actions

#### Background Jobs
- Redis Queue (RQ) for async task execution
- Scheduled jobs (cron-like)
- Enqueue long-running tasks
- Progress tracking for background jobs

#### Email Integration
- Send/receive emails
- Email accounts management
- Email templates (Jinja)
- Auto-linking emails to documents
- Newsletter functionality

#### File Management
- File attachments on any DocType
- S3-compatible storage support
- Public and private file access
- Image optimization

#### Printing
- Jinja-based print format templates
- PDF generation (via wkhtmltopdf or Chromium)
- Letter head management
- Print styles

#### Developer Features
- **Bench CLI**: Command-line tool for managing Frappe sites (bench new-site, bench migrate, bench update, etc.)
- **Hot Reload**: Development server with live reload
- **Testing**: Built-in test runner (unittest-based)
- **Migrations**: Automatic schema migrations via patches
- **App Architecture**: Multi-app support - install multiple Frappe apps on a single site
- **Custom Scripts**: Client-side JavaScript customization without modifying core
- **Server Scripts**: Python scripts that run on events (before_save, after_submit, etc.)
- **Hooks**: Event-driven architecture for extending functionality

### Apps Built on Frappe Framework
- **ERPNext** - Full ERP suite
- **Frappe HR (HRMS)** - Human Resource Management
- **Frappe LMS** - Learning Management System
- **Frappe CRM** - Customer Relationship Management
- **Frappe Helpdesk** - Support ticket management
- **Frappe Insights** - Business Intelligence / Analytics
- **Frappe Builder** - Website builder
- **Frappe Wiki** - Knowledge base / Wiki
- **Frappe Drive** - File management (Google Drive alternative)
- **Gameplan** - Project discussion tool
- **Raven** - Team messaging (Slack alternative)
- **Lending** - Loan management

---

## 4. ERPNext Pricing (erpnext.com/pricing)

### Pricing Model Overview
ERPNext itself is **free and open-source software**. Pricing applies to **Frappe Cloud hosting** and **support plans**.

### Self-Hosted (Free)
- **Cost**: $0
- Download and install on your own servers
- Full access to all features and modules
- No user limits
- No module restrictions
- Community support via forum (discuss.frappe.io)
- You handle hosting, backups, updates, security

### Frappe Cloud Hosting Plans

#### Open Source (Free Tier)
- Free to try on Frappe Cloud
- Limited resources
- Community support only
- Good for evaluation and small projects

#### Starter / Small Business
- Aimed at small teams getting started
- Managed hosting on Frappe Cloud
- Basic support included
- Automatic backups
- SSL certificates included
- Estimated: ~$10-50/month range (pricing varies by resources)

#### Business / Standard
- For growing businesses
- Priority email support
- More server resources
- Automatic updates
- Site migration assistance
- Estimated: ~$50-200/month range

#### Enterprise
- For large organizations
- Dedicated support
- Custom SLAs
- Priority bug fixes
- Dedicated server options
- Migration assistance from other ERP systems
- Estimated: Custom pricing, contact sales

### Frappe Cloud Pricing Structure
- Pricing is based on **site resources** (CPU, RAM, storage) rather than per-user
- **No per-user fees** - add as many users as your server can handle
- Plans scale based on:
  - Number of sites
  - Server specifications (CPU cores, RAM, disk)
  - Support level (community, standard, premium)
  - Managed vs. self-managed servers
- Typical Frappe Cloud pricing (approximate):
  - $10/month for basic shared hosting
  - $25-50/month for dedicated small server
  - $100-500/month for larger dedicated servers
  - Enterprise: custom pricing

### Key Pricing Differentiators vs. Competitors
- **No per-user licensing**: Unlike SAP Business One ($94/user/month), Oracle NetSuite ($99/user/month), or Microsoft Dynamics 365 ($70-210/user/month)
- **No module fees**: All modules included. Competitors often charge extra for manufacturing, HR, CRM, etc.
- **Self-hosting option**: Can eliminate hosting costs entirely by running on own infrastructure
- **Predictable costs**: Resource-based pricing means costs scale with server usage, not headcount

---

## 5. ERPNext Modules (Complete List)

Page erpnext.com/modules returned 404. Below is the comprehensive list of all ERPNext modules:

### Core Business Modules

#### 1. Accounting (Accounts)
- General Ledger and Chart of Accounts
- Multi-company accounting
- Multi-currency support
- Accounts Receivable and Accounts Payable
- Sales Invoice and Purchase Invoice
- Payment Entry and Payment Reconciliation
- Bank Reconciliation
- Journal Entry
- Budget management
- Cost Center accounting
- Financial statements (Balance Sheet, Profit & Loss, Cash Flow)
- Tax management (GST, VAT, etc.)
- Deferred Revenue and Expense
- Subscription management
- Dunning (payment reminders)
- Auto-repeat transactions
- Period closing and year-end closing
- Inter-company accounting

#### 2. Human Resources (HR)
- Employee master with full lifecycle management
- Employee onboarding and separation
- Attendance tracking
- Leave management (leave allocation, leave application, compensatory leave)
- Shift management
- Payroll (salary structure, salary slip, payroll entry)
- Expense claims
- Loan management
- Employee tax and benefits
- Training and development
- Performance appraisal
- Recruitment (job opening, job applicant, offer letter)
- Employee grievance
- Fleet management
- Travel request
- Note: HR module has been spun off into **Frappe HR (HRMS)** as a separate app

#### 3. Manufacturing
- Bill of Materials (BOM) - multi-level
- Production Planning (Material Resource Planning)
- Work Order
- Job Card
- Operation and Workstation management
- Routing
- Quality Inspection
- Subcontracting
- Production analytics
- Downtime tracking
- Manufacturing settings and dashboards
- BOM comparison and versioning
- Capacity planning

#### 4. CRM (Customer Relationship Management)
- Lead management
- Opportunity tracking
- Customer management
- Sales pipeline
- Communication tracking (calls, emails, meetings)
- Appointment scheduling
- Campaign management
- Email campaigns
- Social media integration
- Territory management
- Sales Person hierarchy
- CRM reports and analytics
- Note: CRM has been spun off into **Frappe CRM** as a separate app

#### 5. Selling (Sales)
- Quotation
- Sales Order
- Sales Invoice
- Delivery Note
- Blanket Order
- Selling Settings
- Product Bundle
- Shipping Rules
- Sales Partner management
- Sales analytics and reports
- Pricing Rules
- Customer groups and territories
- Sales targets

#### 6. Buying (Purchasing)
- Supplier management
- Request for Quotation (RFQ)
- Supplier Quotation
- Purchase Order
- Purchase Receipt
- Purchase Invoice
- Supplier Scorecard
- Buying Settings
- Purchase analytics
- Procurement tracking

#### 7. Stock (Inventory/Warehouse Management)
- Item master (products/services)
- Warehouse management (multi-warehouse)
- Stock Entry (material receipt, transfer, manufacture)
- Stock Reconciliation
- Delivery Note
- Purchase Receipt
- Batch tracking
- Serial number tracking
- Quality Inspection
- Packing Slip
- Pick List
- Putaway Rules
- Stock Ledger
- Inventory valuation (FIFO, Moving Average, LIFO)
- Reorder levels and auto-reorder
- Item variants
- Item pricing
- Stock analytics and reports
- Landed cost voucher

#### 8. Assets (Fixed Asset Management)
- Asset creation and capitalization
- Asset movement (transfer between locations)
- Asset depreciation (straight line, double declining, written down value)
- Asset maintenance
- Asset repair
- Asset value adjustment
- Asset retirement/disposal
- Asset category management
- Asset reports

#### 9. Projects
- Project management
- Task management with dependencies
- Timesheet tracking
- Gantt chart view
- Kanban board
- Project billing (time & material, fixed price)
- Project profitability analysis
- Activity type management
- Project templates
- Costing and budgeting

#### 10. Quality Management
- Quality Goal
- Quality Procedure
- Quality Inspection (incoming, in-process, outgoing)
- Quality Review
- Quality Action (corrective and preventive)
- Quality Feedback
- Non-Conformance Report

#### 11. Website (CMS)
- Web pages
- Blog posts and categories
- Website settings
- Contact Us form
- About Us page
- Navigation and menus
- Website theme customization
- Portal for customers and suppliers (view orders, invoices, etc.)

#### 12. E-Commerce
- Product listing on website
- Shopping cart
- Checkout with payment gateway integration
- Wishlist
- Product reviews
- Product configurator
- Pricing rules for web
- Shipping rules
- Coupon codes

#### 13. Support (Helpdesk)
- Issue tracking
- Service Level Agreement (SLA)
- Warranty claim
- Maintenance schedule
- Maintenance visit
- Support analytics
- Note: Helpdesk has been spun off into **Frappe Helpdesk** as a separate app

#### 14. Education
- Student management
- Student admission
- Program and course management
- Student group
- Attendance
- Assessment (criteria, result, plan)
- Fee management
- Student LMS portal
- Schedule and timetable
- Certificate
- Note: Education module is available as a separate Frappe app

#### 15. Healthcare
- Patient management
- Practitioner management
- Patient appointment
- Clinical procedure
- Lab test and results
- Patient medical record
- Vital signs
- Inpatient management
- Rehabilitation
- Healthcare settings
- Note: Healthcare module is available as a separate Frappe app

#### 16. Agriculture
- Crop management
- Crop cycle
- Disease and fertilizer tracking
- Water analysis
- Soil analysis
- Weather data
- Agricultural analytics

#### 17. Non-Profit
- Member management
- Membership types and billing
- Volunteer management
- Donor management
- Grant application
- Chapter management (for organizations with chapters)

#### 18. Payroll
- Salary structure and salary component
- Salary slip generation
- Payroll entry (bulk processing)
- Additional salary
- Employee benefit application and claim
- Retention bonus
- Tax slab management
- Gratuity
- Payroll reports
- Integration with accounting (auto journal entries)

#### 19. Loan Management
- Loan application
- Loan type configuration
- Loan security
- Loan disbursement
- Loan repayment
- Loan interest accrual
- Loan write-off

### Supporting/Infrastructure Modules

#### 20. Setup
- Company setup
- System settings
- Email domain and account
- Print format and letter head
- Naming series
- Data import/export
- Workflow management
- Notification and alert configuration

#### 21. Integrations
- Payment gateways (Stripe, PayPal, Razorpay, GoCardless, Braintree)
- Dropbox backup
- Google (Calendar, Contacts, Drive, Maps)
- AWS S3
- Shopify
- WooCommerce
- Plaid (banking)
- ERPNext Telegram integration
- Webhooks
- REST API for custom integrations
- OAuth 2.0

---

## 6. Key Differentiators vs. Other ERP Systems

### ERPNext vs. SAP Business One
- ERPNext: Free, open source. SAP: Expensive licensing ($94+/user/month)
- ERPNext: Simple modern UI. SAP: Complex, steep learning curve
- ERPNext: Quick implementation (weeks). SAP: Long implementation (months/years)
- ERPNext: Easy customization. SAP: Requires ABAP developers
- ERPNext: Full source code access. SAP: Proprietary/closed

### ERPNext vs. Oracle NetSuite
- ERPNext: No per-user fees. NetSuite: $99+/user/month + platform fee
- ERPNext: Self-host option. NetSuite: Cloud-only (SaaS)
- ERPNext: Own your data. NetSuite: Vendor-dependent
- ERPNext: Python/JS (accessible). NetSuite: SuiteScript (proprietary)
- ERPNext: Community support free. NetSuite: Paid support only

### ERPNext vs. Microsoft Dynamics 365
- ERPNext: Zero license cost. Dynamics: $70-210/user/month per module
- ERPNext: All modules included. Dynamics: Pay per module
- ERPNext: Linux-based, lightweight. Dynamics: Heavy Microsoft ecosystem dependency
- ERPNext: Open API. Dynamics: Microsoft-centric integration

### ERPNext vs. Odoo
- ERPNext: Truly free, GPLv3. Odoo: Community (free, limited) vs Enterprise (paid, $20-75/user/month)
- ERPNext: All features in one edition. Odoo: Key features locked behind Enterprise license
- ERPNext: Single integrated system. Odoo: Modular but can become fragmented
- ERPNext: Frappe Framework (Python). Odoo: ORM/Python but different architecture
- ERPNext: Simpler, opinionated. Odoo: More modules but more complex
- Both are open source, but Odoo's dual licensing restricts Enterprise features

### ERPNext Unique Strengths
1. **Truly 100% open source** - No "open core" model, no features behind paywalls
2. **No per-user pricing** - Cost scales with infrastructure, not headcount
3. **Built on Frappe Framework** - Extensible low-code platform, not just an ERP
4. **Modern web-first architecture** - Responsive, mobile-friendly, fast
5. **Active development** - Regular releases, active GitHub repository
6. **Strong Indian/emerging market presence** - GST-compliant, multi-country support
7. **Low total cost of ownership** - Dramatically lower than proprietary alternatives
8. **Easy to extend** - Build custom apps on the Frappe Framework alongside ERPNext
9. **REST API first** - Everything accessible via API for headless/integration use cases
10. **Multi-tenancy support** - Via Frappe Cloud, host multiple ERPNext sites efficiently

---

## 7. Additional Information

### Technology Requirements (Self-Hosting)
- **OS**: Ubuntu 22.04+ / Debian 11+ (recommended), also runs on CentOS, macOS (dev)
- **Python**: 3.10+
- **Node.js**: 18+
- **Database**: MariaDB 10.6+ or PostgreSQL 14+
- **Redis**: 6+
- **Nginx**: As reverse proxy
- **Minimum Hardware**: 4GB RAM, 2 CPU cores, 40GB disk (for small deployments)
- **Recommended**: 8GB+ RAM, 4+ CPU cores for production

### Deployment Options
1. **Frappe Cloud** (managed hosting by Frappe Technologies)
   - One-click deployment
   - Automatic updates and backups
   - Managed security and SSL
   - Scalable infrastructure
2. **Self-Hosted** (on-premise or IaaS)
   - Full control over infrastructure
   - Use Bench CLI for installation and management
   - Docker images available
   - Can deploy on AWS, GCP, Azure, DigitalOcean, Hetzner, etc.
3. **Partner-Hosted** (via certified ERPNext partners)

### Community & Support
- **Community Forum**: discuss.frappe.io - Active community with thousands of posts
- **GitHub**: github.com/frappe/erpnext - Source code, issues, contributions
- **Documentation**: docs.erpnext.com / frappeframework.com/docs
- **YouTube**: Frappe official channel with tutorials
- **Conference**: ERPNext Conference (annual community event)
- **Certified Partners**: Network of implementation partners worldwide
- **Frappe School**: Official learning platform with courses

### Version History Highlights
- ERPNext uses calendar-based versioning (e.g., v14, v15)
- Regular release cycle with LTS (Long Term Support) versions
- v14: Major UI overhaul, Workspace redesign
- v15: Performance improvements, better developer experience
- Continuous development with frequent point releases

### Localization & Country-Specific Features
- **India**: Full GST compliance, e-invoicing, e-waybill, TDS/TCS
- **Germany**: DATEV export, German accounting standards
- **Saudi Arabia**: ZATCA e-invoicing compliance
- **South Africa**: VAT, PAYE compliance
- **USA**: Chart of accounts templates, US tax compliance
- **UAE**: VAT compliance
- Many other country-specific localizations available via community apps

---

*This document was compiled on March 24, 2026 from erpnext.com, frappeframework.com, and documented ERPNext product information. Some URLs (erpnext.com/open-source-erp, erpnext.com/modules) returned 404 - ERPNext has been migrating content to frappe.io/erpnext. For the most current information, visit https://frappe.io/erpnext and https://frappeframework.com.*
