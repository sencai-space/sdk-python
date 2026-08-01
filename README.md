# sencai-sdk (Python)

Python klient pro Sencai Platform API (`/api/v1/*`), generovaný z veřejného OpenAPI specu
([`sencai.space/openapi/sencai-platform.public.v1.yaml`](../sencai.space/openapi/sencai-platform.public.v1.yaml),
výstup F4.DEVPORTAL.01).

> **Balíček zatím není publikovaný na PyPI.** Veřejná distribuce je obchodní/release rozhodnutí
> mimo scope F4.DEVPORTAL (viz `PHASE-4-PLAN.md`, modul přehled DEVPORTAL). Struktura je
> PyPI-ready (`pyproject.toml` + `setup.cfg`/`setup.py`), ale do doby publikace se instaluje jen
> z monorepo cesty nebo přímo z gitu.

## Co je (ne)generované

Tento adresář je z většiny **generovaný** (`sencai_sdk/`, `setup.py`, `pyproject.toml`,
`requirements.txt`, …) přes [`scripts/sdk-generate.sh`](../scripts/sdk-generate.sh) —
`openapi-generator-cli` (`python` generátor). Ručně psané a **chráněné** přes
`.openapi-generator-ignore` (regenerace je nikdy nepřepíše): tento `README.md`,
`CHANGELOG.md`, `.env.example`.

> **⚠️ Regenerace je dnes nefunkční.** `scripts/sdk-generate.sh` čte spec z
> `sencai.space/openapi/sencai-platform.public.v1.yaml` — `sencai.space` (Strapi) byl ale
> **smazán 2026-07-30**, adresář v monorepu vůbec neexistuje. Skript proto skončí hned na
> úvodní kontrole existence souboru. Committed generovaný kód (`sencai_sdk/`) zůstává
> instalovatelný (viz Smoke test níže) — je jen zamrzlý na posledním stavu specu, dokud
> nevznikne nový zdroj (typicky vygenerovaný z `sencai-backend`, Go náhrady Strapi).

Regenerace po změně specu (až bude mít skript kde číst):

```bash
cd sencai.space && npm run openapi:generate   # F4.DEVPORTAL.01, pokud se spec změnil
cd .. && ./scripts/sdk-generate.sh
```

## Instalace (zatím jen z monorepo cesty)

```bash
python3 -m venv .venv
.venv/bin/pip install -e .
```

## Použití

```python
from sencai_sdk import ApiClient, Configuration, CloudInstanceApi

config = Configuration(
    host="http://api.sencai.localhost/api/v1",  # nebo https://api.sencai.space/api/v1 v produkci
    access_token="<keycloak-jwt>",               # Bearer JWT — nebo budoucí API-key header,
                                                   # viz `security` sekce specu
)

with ApiClient(config) as client:
    cloud_instances = CloudInstanceApi(client)
    result = cloud_instances.find_cloud_instance()
```

`Configuration.host` i autentizace jsou vždy konfigurovatelné — balíček neobsahuje žádnou
hardcoded produkční URL.

## Smoke test

```bash
python3 -m venv .venv
.venv/bin/pip install -e .
.venv/bin/python -c "from sencai_sdk import CloudInstanceApi; print(CloudInstanceApi)"
```

## Verzování

Verze balíčku (`pyproject.toml`/`setup.py` → `version`) je odvozená z `info.version` veřejného
OpenAPI specu při každé regeneraci. Breaking change v API → zdokumentovat v `CHANGELOG.md`
s odkazem na `doc.sencai.space/dev/api-versioning` (`SUNSET_DATE` konvenci).
