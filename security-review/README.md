# E-Startovka – balíček bezpečnostních oprav k testování

Stáhněte soubor `estartovka-zabezpeceni-k-testovani.zip` pomocí tlačítka **Download raw file** u souboru na GitHubu a rozbalte jej do samostatné složky. Podrobný návod je uvnitř v `estartovka-secure/README.md`.

Balíček obsahuje opravený frontend, Firebase Functions, databázová pravidla a testy. Nenahrazuje automaticky současný web a není nasazen do Firebase. Tato větev přidává pouze ZIP a tento návod; původní webové soubory nemění.

**Stav ověření:** prošlo 18 jednotkových testů, 9 scénářů prohlížeče se simulovaným Firebase a načtení skutečného SDK pod CSP. Kontrola produkčních závislostí nehlásila známé zranitelnosti. Integrační testy skutečných Firebase pravidel a Functions zatím blokuje stažení emulátoru. Před nasazením je nutné je dokončit a zkontrolovat existující role uživatelů.

Výchozí konfigurace balíčku používá pouze lokální demo emulátory, nikoli živý projekt. Do produkce zatím nenasazovat.

SHA-256 ZIPu: `8bd98d47b7f96e2e76cefe52e1e0a096c3502171c0f812bc3ea444168500f3f1`
