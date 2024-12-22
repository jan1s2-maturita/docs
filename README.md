# Dokumentace
Tento ročníkový projekt si klade za úkol umožnit studentům zapínat kyberbezpečnostní úkoly formátu CTF na školní infrastruktuře pomocí systému Kubernetes.

Projekt je inspirován projekty typu TryHackMe, HackTheBox, CTFd, Haxagon, a dalšími.

Dále si tento projekt klade za cíl být v potenciálně vytěžované části (tj. část uživatelská) co nejvíce rozdělený na mikroslužby, aby bylo možné jednotlivé části projektu snadno rozšiřovat, upravovat a škálovat dle potřeby.

Tento projekt jsem si vybral, abych se lépe naučil pracovat se systémem rozděleným na mikroslužby a abych si vyzkoušel práci s Kubernetes API.

## Použité technologie
V tomto projektu byly použity následující technologie:
- Kubernetes
- Redis
- PostgreSQL
- FastAPI
- Vite
- SQLalchemy
- vcluster
- JWT

## Struktura projektu
Projekt je rozdělen do několika částí:
- `frontend` - webová aplikace pro uživatele
- `admin` - část backendu a frontendu pro správce (ještě není implementováno)
- `backend api` - REST API pro komunikaci mezi frontendem a systémem

Backend API je rozdělen do několika mikroslužeb:
- `router` - hlavní router pro FastAPI, který požadavky směruje na jednotlivé mikroslužby
- `auth` - mikroslužba pro autentizaci uživatelů
- `lister` - mikroslužba pro získávání informací o aktuálně zapnutých úkolech
- `deleter` - mikroslužba pro mazání úkolů
- `deployer` - mikroslužba pro zapínání úkolů
- `flag-submit` - mikroslužba pro odesílání řešení úkolů

Všechny mikroslužby jsou napsány v Pythonu s využitím FastAPI a mají endpoint /health, který vrací status k dané mikroslužbě.

## Mikroslužby

### Router
Tato mikroslužba je hlavní router pro FastAPI, který směruje požadavky na jednotlivé mikroslužby.

### Auth
Tato mikroslužba zajišťuje přihlašování a registraci a jakožto jediná má přístup k privátnímu klíči pro podepisování JWT tokenů pomocí algoritmu RS256.

Tato služba má endpointy:
- POST /login (email, password) - přihlášení, vrací JWT token
- POST /register (email, username, password) - registrace, vrací status
- GET /health

Tato služba potřebuje přístup k PostgreSQL databázi.

### Lister
Tato mikroslužba zajišťuje získávání informací o úkolech dostupných pro uživatele.

Tato služba má endpointy:
- GET / - získání všech úkolů
- GET /running/ - získání všech úkolů, které jsou aktuálně zapnuté
- GET /running/{id} - získání informací o úkolu s daným ID
- GET /health

Tato služba potřebuje přístup k PostgreSQL a Redis databázím.

### Deleter
Tato mikroslužba zajišťuje mazání úkolů.

Tato služba má endpointy:
- DELETE /{id} - smazání úkolu s daným ID
- GET /health

Tato služba potřebuje přístup k Redis databázi a ke Kubernetes API.

### Deployer
Tato mikroslužba zajišťuje zapínání úkolů.

Tato služba má endpointy:
- POST / (challenge_id) - zapnutí úkolu s daným ID

Tato služba potřebuje přístup k PostgreSQL a Redis databázím a ke Kubernetes API.

### Flag-submit
Tato mikroslužba zajišťuje odesílání řešení úkolů.

Tato služba má endpointy:
- POST /{id} (flag) - odeslání řešení úkolu s daným ID

Tato služba potřebuje přístup k PostgreSQL databázi.
