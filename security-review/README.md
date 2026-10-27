# E-Startovka – opravená revize v5.2.6

Stáhněte `estartovka-zabezpeceni-k-testovani.zip` tlačítkem **Download raw file**. Rozbalte do nové složky. Návod je uvnitř v `estartovka-secure/README.md`.

## Oprava chyby 403 při startu

Původní balíček používal ve frontendu a seedu jiný namespace demo databáze než Functions Emulator. Server proto neviděl profil demo-superadmina. Nová revize sjednocuje namespace pouze v demo emulátorech a zachovává kontroly oprávnění. Zároveň opravuje falešné chyby „Let neexistuje“ při studené cache transakcí a správné vrácení validačních chyb bez timeoutu.

## Aktualizace

1. Zastavte staré emulátory (Ctrl+C).
2. Nový ZIP rozbalte do nové složky.
3. V nové složce spusťte `npm ci`, `npm --prefix functions ci` a `npm run emulators`.
4. V druhém terminálu v téže složce spusťte `npm run seed:emulator`.
5. Obnovte lokální stránku pomocí Ctrl+F5. V záhlaví musí být **v5.2.6**. Přihlaste se demo účtem a vyzkoušejte start.

Při přenášení jen serverové opravy jsou potřeba **všechny tři soubory**: functions/index.js, functions/emulator-config.js a functions/existing-transaction.js. Doporučené je rozbalení kompletní nové verze.

## Ověření

- 25 jednotkových testů prošlo.
- 15 testů pravidel prošlo proti skutečnému Firebase Database Emulatoru, včetně odmítnutí povýšení role a přístupu k cizímu letišti.
- Skutečná integrace Auth/Functions/RTDB prošla: start superadmina a startera, odmítnutí guest/cizího letiště, admin-only mazání, současná přistání vlečné/větroně, editace, audit a zákaz smazání letiště s členy.
- Skutečný prohlížeč s Firebase SDK a běžícími emulátory prošel: přihlášení, start bez 403, přistání, historie a odhlášení s vymazáním dat. Žádný požadavek na produkční služby.

Balíček obsahuje celý upravený frontend, Functions, rules a testy. Tato větev mění pouze ZIP a tento návod. `main` ani živý Firebase projekt se nemění. Produkční nasazení a kontrola existujících rolí nadále vyžadují samostatný krok; skutečné doručování ověřovacích e-mailů nebylo testováno.

SHA-256 ZIPu: `105d72fa7c6928d40282ad0dc99eb8fbef577a658debce137cd27f15974d05c8`
