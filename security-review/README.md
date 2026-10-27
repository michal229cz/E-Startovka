# E-Startovka – v5.2.7

Stáhněte `estartovka-zabezpeceni-k-testovani.zip` tlačítkem **Download raw file**. Rozbalte do nové složky. Podrobný návod je v `estartovka-secure/README.md` uvnitř ZIPu.

## Nové změny

- Nejdřív původní přihlašovací formulář se zachovaným vzhledem, potom výběr letiště. Bez přihlášení nelze vstoupit na provozní stránku ani otevřít výběr letišť.
- Po přihlášení jsou vidět všechna letiště, včetně nepřístupných. Nepřístupná letiště jsou zamčená, označená textem Bez oprávnění a nejdou otevřít. Guest je do schválení vidí všechna zamčená. Superadmin má přístup všude.
- Změna letiště zachová přihlášení. Registrace nabízí domovský klub uvnitř stejného formuláře.
- Logo přijímá HTTPS odkaz a PNG data URL, nebo lze použít **Nahrát PNG**. Limit je 256 KB, klient kontroluje PNG a rozměry do 2048 × 2048 px. Pravidla nadále vyžadují superadmina a odmítají SVG/HTML data i příliš velké hodnoty.
- Změna loga nezavře správu ani nevymaže rozepsaný let.

## Ověření

Prošlo 26 jednotkových testů a 18 testů pravidel ve skutečném Database Emulatoru. Prohlížeč se simulovaným Firebase ověřil 10 scénářů. Skutečný prohlížeč s Auth, Functions a Database emulátory ověřil úvodní přihlášení, výběr a zámky letišť, zachování relace, start, přistání, historii, odhlášení a ukládání PNG vložením i souborem. Logo testovacího PNG má více než původně povolených 2048 znaků. Ověřena byla také skutečná registrace a přihlášení nového ověřeného guest účtu do seznamu zamčených letišť. Skutečné doručování e-mailů mimo emulátory nebylo testováno.

## Spuštění nové verze

1. Zastavte staré emulátory (Ctrl+C) a ZIP rozbalte do nové složky.
2. V nové složce spusťte `npm ci`, `npm --prefix functions ci` a `npm run emulators`.
3. V druhém terminálu v téže složce spusťte `npm run seed:emulator`.
4. V prohlížeči použijte Ctrl+F5. V záhlaví musí být **v5.2.7**.

Je nutný celý balíček včetně nových database.rules.json a veřejných souborů. Tato větev aktualizuje pouze ZIP a návod. `main` ani živý Firebase projekt se nemění. Produkční nasazení a kontrola existujících rolí zůstávají samostatným krokem.

SHA-256 ZIPu: `697a412f9e711058ed4ea54c525ca297b64094a452aa6b08352b95a7cf2639ea`
