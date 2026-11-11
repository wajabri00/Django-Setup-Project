# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project state

This is an early-stage scaffold for a Django + React LMS. The README describes it as: environment setup with custom apps, Django admin customized with **Jazzmin**, and static/media/template configuration. Only the Django backend exists — there is no React frontend in the repo yet. The README is written in French.

The Django project lives in `LMS/Django React LMS/backend/` (note the spaces in the path — quote it in shell commands). All commands below run from that directory.

## Commands

```bash
python -m venv .venv             # .venv/ is gitignored; .vscode/settings.json points VS Code at it
.venv\Scripts\Activate.ps1       # PowerShell (or call .venv\Scripts\python.exe directly)
pip install -r requirements.txt  # on Python 3.13+, PyYAML may first need: PYYAML_FORCE_LIBYAML=0 pip install PyYAML

python manage.py migrate
python manage.py createsuperuser
python manage.py runserver       # admin at http://127.0.0.1:8000/admin/

python manage.py test                                # all tests
python manage.py test api                            # one app
python manage.py test api.tests.SomeTestCase.test_x  # single test
```

There is no linter or formatter configured.

## Architecture

- `backend/` — the Django project package (settings, urls, wsgi/asgi). `ROOT_URLCONF = backend.urls`, which currently routes only `admin/` plus static/media serving.
- Three custom apps, all empty stubs so far (no models, views, urls, or migrations): `core`, `userauths` (intended for user/auth models), `api` (intended for the REST API).
- `requirements.txt` already pins the intended stack — DRF, simplejwt, drf-yasg, django-cors-headers, django-storages/boto3, anymail, stripe, ckeditor-5, dj-database-url/environs, psycopg2-binary — but **none of these are in `INSTALLED_APPS` or configured yet**. Only `jazzmin` is wired in (it must stay first in `INSTALLED_APPS` so it overrides admin templates).
- Database is SQLite (`db.sqlite3`, committed). `SECRET_KEY` and `DEBUG = True` are hardcoded in `settings.py`; no `.env` loading yet.
- No `AUTH_USER_MODEL` is set. If a custom user model is added in `userauths`, set `AUTH_USER_MODEL` before the first migration is applied (or reset the SQLite DB).

## Settings notes

- `TEMPLATES.DIRS` → `<backend>/templates`; `STATICFILES_DIRS` → `<backend>/static`; `STATIC_ROOT` → `<backend>/staticfiles` (gitignored); `MEDIA_ROOT` → `<backend>/media`, served via `urls.py` in development.
- Most `requirements.txt` pins are old; compiled packages (cffi, cryptography, PyYAML, charset-normalizer) use `>=` and psycopg2 is `psycopg2-binary` so they install on recent Python without MSVC.
- Admin branding ("IFRIQIYA INFORMATIQUE") and theme are in `JAZZMIN_SETTINGS` / `JAZZMIN_UI_TWEAKS` at the bottom of `settings.py`.
- README screenshots live in `docs/screenshots/`.
