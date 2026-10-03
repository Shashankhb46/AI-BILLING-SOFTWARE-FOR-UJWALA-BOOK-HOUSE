# Ujwala Book House Billing & Inventory

An integrated React and Flask application for bookstore sales, billing, products, stock, customers, suppliers, purchases, returns, expenses and basic reporting. The supplied Ujwala Book House artwork is included in the interface and generated invoices.

## Requirements

- Node.js 20.19+ or 22.12+
- Python 3.11–3.14 (the checked environment uses Python 3.14)
- Windows PowerShell commands below; use the matching virtual environment paths on other platforms

## Local setup

From this project directory:

```powershell
npm install
Copy-Item backend\.env.example backend\.env
..\.venv\Scripts\python.exe -m pip install -r backend\requirements.txt
```

The repository's virtual environment in the parent directory is optional. If you do not have it, create one with `py -m venv .venv` and replace `..\.venv\Scripts\python.exe` below with `.venv\Scripts\python.exe`.

Initialize or update the database, then load development sample data:

```powershell
Set-Location backend
..\..\.venv\Scripts\python.exe -m flask --app run.py db upgrade
..\..\.venv\Scripts\python.exe seed.py
```

Run the backend in one terminal:

```powershell
..\..\.venv\Scripts\python.exe run.py
```

Run the frontend from the project root in a second terminal:

```powershell
npm run dev
```

Open the URL printed by Vite (normally `http://localhost:5173`). The API is at `http://localhost:5000/api`; `GET /api/health` reports database connectivity.

## Open the development app on a phone

The phone and PC must be on the same Wi-Fi. Find the PC's Wi-Fi IPv4 address with `ipconfig` (for example, `192.168.1.25`). In the project-root `.env`, set the API base URL to that address:

```dotenv
VITE_API_BASE_URL=http://192.168.1.25:5000/api
```

In `backend/.env`, allow the matching frontend origin (replace the example address):

```dotenv
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173,http://192.168.1.25:5173
```

Restart both servers after editing the environment files. Start the backend from `backend` with `..\..\.venv\Scripts\python.exe run.py`. Start Vite from the project root with:

```powershell
npm run dev -- --host 0.0.0.0
```

On the phone, open `http://192.168.1.25:5173` using the same address. If Windows Firewall prompts, allow Python and Node on **Private networks**. The dev server is for your trusted local network; don't expose it to the public internet. Some mobile browsers require HTTPS before allowing camera-based barcode scanning; manual barcode entry is available over local HTTP.

## Development accounts

| Role | Email | Password |
| --- | --- | --- |
| Admin | `admin@ujwala.local` | `admin123` |
| Manager | `manager@ujwala.local` | `manager123` |
| Cashier | `cashier@ujwala.local` | `cashier123` |
| Inventory staff | `inventory@ujwala.local` | `inventory123` |

`seed.py` is intended only for local development. It ensures these accounts exist and resets their passwords when rerun. Do not use seeded credentials or the development secrets for a live store.

## Database and configuration

Flask-Migrate owns schema changes under `backend/migrations`. Use `flask db migrate -m "describe change"` to generate a revision, inspect it, and apply it with `flask db upgrade`. The committed revisions include the current product, sales, inventory, purchases, returns, expenses and administration schema.

Set `SECRET_KEY`, `JWT_SECRET_KEY`, and `DATABASE_URL` in `backend/.env` for the deployment environment. Production app mode checks that both secrets are present. Configure a production CORS policy at the deployment boundary before exposing the API. Uploaded product images are stored under `backend/uploads` by default; back up this directory and the database together.

## Included workflows

- JWT login, role-aware navigation and protected API operations
- Product/category management, image upload, SKU/barcode lookup and stock history/adjustment
- Customer and supplier management
- POS cart, barcode lookup, server-calculated sale totals, stock validation, payment recording and printable A4/thermal invoice PDFs
- Purchases and supplier receipts, validated sales returns/refunds, expense records
- Sales, profit, inventory, purchase and expense summaries with sales CSV export
- Business/receipt settings, user and role administration
- Rule-based assistant queries and a mock image product-finder API

The real AI provider is not configured: image product recognition currently reports demo suggestions and must not be relied on for actual identification. Barcode scanning needs a camera-capable browser and camera permission. Payment modes record the selected method; no payment processor is connected. Confirm the store's legal name, address, tax registration and invoice settings before issuing real invoices.

## Verification

From `backend`:

```powershell
..\..\.venv\Scripts\python.exe -m pytest -q
```

From the project root:

```powershell
npm run build
```
