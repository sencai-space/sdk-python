# Changelog

Formát dle [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), verzování dle
[Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added

- Počáteční generování Python klienta (`sencai_sdk`) z konsolidovaného veřejného OpenAPI specu
  (`python` generátor, `scripts/sdk-generate.sh`) — F4.DEVPORTAL.02.
- Getting Started dokumentace (`doc.sencai.space/dev/sdk/python`, F4.DEVPORTAL.04) — instalace,
  inicializace klienta, kompletní `find_organisation` + `organisation_invite_member` příklad
  ověřený proti živému lokálnímu stacku, konvence zpracování chyb (`ApiException`).

### Fixed

- Regenerováno po opravě `sencai.space/scripts/openapi-merge.ts` (F4.DEVPORTAL.04 — viz jeho
  CHANGELOG): auto-discovered CRUD modely už nejsou zabalené v neexistujícím `attributes`
  wrapperu (Strapi v5 Document Service API je flat) — bez této opravy `pydantic` model
  reálná data prostě zahazoval (např. `CloudInstanceApi.find_cloud_instance()` vracelo objekty
  bez `name`/`status`/…). `json`-typová pole (např. `Organisation.ip_allowlist`, ve skutečnosti
  JSON pole, ne objekt) už nejsou vynuceně typována jako `Dict`, ale jako `Any` — bez toho
  `pydantic` odmítal každou reálnou odpověď obsahující tohle pole s `dict_type` chybou. Žádná
  ruční změna v tomto adresáři, jen dopad regenerace ze specu.
