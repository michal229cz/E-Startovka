# Příprava importu letů – v5.3.0

**Toto je rozpracovaná verze. Automatické načítání z Ibisek zatím nefunguje.**
Přístup na https://logbook.ibisek.com/loc/LKST z vývojového prostředí vrací CONNECT 403 i po udělení síťového přístupu. Nebyl ověřen endpoint, formát odpovědi, stabilní identifikátory letů, význam časů ani podmínky provozovatele. Proto není implementováno ani nasazeno periodické volání neznámého API. Samotné uložení odkazu žádný import nespustí. Ruční starty zůstávají funkční.

## Připravené chování

- Ve **Správě letiště → Zdroj letů tohoto letiště** uloží správce vlastního letiště nebo superadmin odkaz ve tvaru `https://logbook.ibisek.com/loc/LKST`. Každé letiště má vlastní konfiguraci. Jiný kód lokace na stejném webu je podporovaný; další poskytovatelé vyžadují vlastní ověřený adaptér. Prázdný odkaz zdroj odpojí, již uložené lety nemaže.
- Stav výslovně říká „Automatické načítání čeká na ověření API Ibisek“. Konfigurace je soukromá provozní informace letiště, není v katalogu pro nepřihlášené návštěvníky. Neukládají se API klíče ani tokeny.
- Převzatý let bude označen **K doplnění**. Startér nebo správce doplní pilota, úlohu a způsob startu; druhý pilot je volitelný. U aerovleku je nutné doplnit také vlečné letadlo a vlekaře. Server potom nastaví **Doplněno** a zapíše auditní záznam.
- Nad obsahem je rozbalovací seznam všech nedoplněných letů s počtem, včetně starších dní. Stav a tlačítko doplnění jsou i u jednotlivých letů v provozu a historii. Ručně zadané lety tuto povinnost nově nemají.
- Posádku a úlohu lze doplnit za letu i po přistání. Doplnění nemění časy ani letadlo. Časy importovaného letu spravuje zdroj a nelze je přepsat běžnou editací či ručním přistáním. Dokončení zdrojového letu nezávisí na doplnění posádky.
- Interní serverový modul umí bezpečně sloučit již převedenou dávku zdrojových letů: stabilní ID zabraňují duplicitám, opravy zachovají lidské údaje a chybějící položky v částečné odpovědi neznamenají smazání ani přistání. Smazání importovaného letu správcem vytváří tombstone, který zabrání jeho opětovnému vytvoření při dalším importu stejného zdrojového ID.
- Lety vlečné a větroně se zatím nijak automaticky nepárují. Jejich skutečný vztah lze určit teprve podle informací poskytovaných API.

## Co je potřeba k dokončení

Dokumentace nebo URL požadavku s letovými daty a anonymizovaná ukázka odpovědi. V prohlížeči otevřete stránku LKST, F12 → Network / Síť → Fetch/XHR, obnovte stránku a najděte odpověď s lety. Nesdílejte hesla, cookies, přístupové tokeny ani osobní údaje. Provozovatel může místo toho dodat dokumentaci API a pravidla jeho používání.

Potom je potřeba vytvořit adaptér skutečné odpovědi do interního formátu v `functions/imports.js`, ověřit ID, časové pásmo, stránkování a opravy, přidat omezené serverové načítání se zabezpečením proti SSRF, timeoutem a limitem velikosti, zámek proti souběžným běhům, poslední úspěšnou synchronizaci, chyby a naplánování podle dovoleného intervalu. Nejdříve se musí ověřit proti zaznamenané anonymizované odpovědi a testovacímu letišti. Interní formát v testech není odhad schématu API Ibisek.

## Ověření této přípravy

- 39 jednotkových testů: validace/autorizace, importní slučování, zachování lidských údajů, opravy, duplicity, tombstones a stávající bezpečnostní kontroly.
- 19 testů pravidel ve skutečném Database Emulatoru: včetně zákazu přímých klientských změn zdrojové konfigurace a tombstones.
- 10 stávajících prohlížečových scénářů v Chromium s náhradou Firebase.
- Nové prohlížečové scénáře v Chromium s náhradou Firebase: fronta aktivních i starších ukončených letů, povinná pole, aerovlek, bezpečné vykreslení, volání serveru, konfigurace zdroje a odstranění soukromých údajů při odhlášení.
- Sestavení a dvě kontroly oddělení živé a místní Hosting konfigurace.
- Skutečný Firebase SDK potvrzuje u všech sedmi exportovaných funkcí callable Gen 2. Přibyly `saveFlightSource` a `completeFlightDetails`; plánovač se neexportuje.

Nové callables byly testovány s paměťovou náhradou databáze; nebyly ověřeny proti živému Firebase projektu. Přenos z externího logbooku nebyl proveden. Nejsou k dispozici skutečná importovaná data ani ověření skutečného API.

Příkazy pro testování:

```sh
npm ci
npm --prefix functions ci
npm test
npm run test:rules
npm run test:browser
npm run test:imports:browser
npm run test:hosting
```

Plnou novou verzi přenášejte jako celek. Dřívější hotfix s jediným `functions/index.js` k této verzi nepatří: backend nově potřebuje také `functions/imports.js` a frontend nové formuláře i bundle. Balíček nemění existující živé soubory, dokud jej sami nenasadíte; změny v tomto chatu jsou pouze ve vývojovém prostoru a samostatné GitHub větvi.
