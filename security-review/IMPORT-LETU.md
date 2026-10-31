# Import letů Ibisek – v5.3.1

Import uloženého HTML je ověřený proti dodané stránce LKST z 17. 10. 2026 a skutečným Firebase emulátorům. **Online načítání je implementované, ale spojení z živého Firebase s Ibisek zatím nebylo ověřeno.** Z tohoto vývojového prostředí web vrací HTTP 403. Do živého Firebase nebylo nic nasazeno.

## Co zdroj skutečně poskytuje

Dodaný `index.js` nepotvrdil veřejné JSON API. Lety jsou už v HTML stránky. V sekci „Lety“ jsou spárované starty a přistání; samostatné seznamy odletů a příletů se nespojují odhadem. CSV odkaz je `/csv/loc/LKST/2026-10-17`, ale obsah exportu nebyl dodán ani ověřen, takže se zatím nepoužívá.

Dodaná stránka obsahuje devět dokončených letů OK-4704. Patička výslovně potvrzuje Europe/Prague; například 10:53 místního letního času se ukládá jako 08:53 UTC. Přesnost zdroje je jedna minuta. Časy se nepovažují automaticky za UTC a při chybějícím pásmu nebo nejednoznačnosti při změně letního času se import zastaví.

## Vyzkoušení po nasazení

1. Přihlaste se jako správce příslušného letiště nebo superadmin a otevřete **Správa letiště → Zdroj letů tohoto letiště**.
2. Vložte `https://logbook.ibisek.com/loc/LKST` a uložte. Lze vložit také původní odkaz s datem nebo CSV odkaz; uloží se pouze lokace. Pro jiné letiště na stejném webu použijte jeho kód. Jiní poskytovatelé zatím podporovaní nejsou.
3. Pro první ověření nechte automatické načítání vypnuté. V části **Import uložené stránky (HTML)** zvolte datum **2026-10-17**, vyberte svůj `OGN Logbook.html` a klikněte **Importovat soubor**. Skripty souboru se nespouštějí; maximální velikost je 512 KB. Soubor se neukládá na Hosting ani do databáze, ukládají se jen vybrané údaje letů.
4. Nahoře bude přehled **K doplnění: 9 letů**, pokud už stejné lety nebyly převzaté nebo odstraněné. Jsou z historického data, takže je také najdete v Historii. Startér nebo správce vyplní pilota, úlohu a způsob startu; druhý pilot je nepovinný. U aerovleku je nutné doplnit vlečné letadlo a vlekaře. Potom se stav změní na **Doplněno**.
5. Pro online ověření použijte **Načíst nyní**. Toto tlačítko načítá dnešek a včerejšek v UTC, nezávisle na datu zvoleném pro import souboru. Při chybě se zobrazí důvod ve stavu zdroje; uložené lety se nemažou.
6. Po úspěšném online ověření lze zaškrtnout **Automaticky načítat dnešek a včerejšek každých 5 minut** a uložit. Každé letiště má vlastní adresu i přepínač. Bez uloženého zapnutí se naplánovaná funkce zdroje nedotazuje. Prázdnou adresou se zdroj odpojí a stávající lety zůstanou zachované.

Online požadavek používá pevnou doménu `logbook.ibisek.com`, zakazuje přesměrování a žádá časové pásmo UTC pomocí preference `browser_timezone=UTC`. Odpověď musí pásmo UTC potvrdit. Pokud server odmítne přístup, vrátí jinou strukturu nebo pásmo nepotvrdí, neobchází se jeho omezení a import skončí chybou. Podle skutečné odpovědi bude případně potřeba upravit adaptér nebo získat podporované API od provozovatele.

## Chování a omezení

- Převzaté lety nevyžadují ruční start ani přistání. Odlet bez spárovaného přistání je aktivní, později se přidá zdrojové přistání. Povinnost doplnit posádku tomu nebrání. Přílet z jiného letiště se nevytváří jako nový místní start.
- Posádka a úloha se zachovají při dalších importech i změně času přistání. Časy a registraci spravuje zdroj; ruční přistání a běžná editace časů jsou u převzatých letů blokované. U zdrojových letů se doba letu dopočítává ze zdrojových časů.
- HTML neobsahuje neměnné ID letu. Identifikátor se proto odvozuje z lokace, registrace a času startu v UTC. Opakovaný stejný záznam se nevytvoří znovu. Pokud se změní čas startu nebo tabulky nesouhlasí, import se při zjištěné nejednoznačnosti zastaví a vyžádá kontrolu; nepřepisuje lidské údaje odhadem. Automatické opravy změněných startů nejsou podporované.
- Smazaný převzatý let má serverový tombstone a další import stejného zdrojového ID jej neobnoví. Chybějící řádek v částečné odpovědi let nemaže a zmizení z párované tabulky samo o sobě nezruší dříve zjištěné přistání.
- Při kolizi s ručně zapsaným letem stejné registrace ve stejné minutě se import zastaví pro kontrolu, místo aby vytvořil duplicitu. U téhož letu používejte jeden způsob zápisu.
- Vlečná a větroň se nepárují automaticky. U aerovleku doplňujete údaje o vlečné ručně; zdrojová detekce samostatného letu vlečné není důkazem konkrétního vleku.
- Zdroj zaznamená pouze lety, které zachytí OGN. Výpadek ani absence záznamu se nepovažují za potvrzené přistání. Ruční zápis zůstává dostupný pro nezachycené lety.
- Nové požadavky mají 15sekundový timeout a limit 512 KB, odpověď se pouze parsuje. Žádné skripty, odkazy, obrázky ani jiné zdroje z HTML se nenačítají. Přístupy do letiště a serverové zápisy zůstávají chráněné rolemi a databázovými pravidly.
- Zámek a kontrola revize konfigurace brání souběžnému importu nebo uložení odpovědi po změně/odpojení zdroje. Online pokusy mají minimální rozestup 5 minut; stejné zdroje se v jednom běhu plánovače načítají jen jednou.

## Nasazení aktualizace

Použijte celý balíček `estartovka-import-5.3.1.zip` rozbalený do nové složky. Zachovejte si předchozí lokální verzi a zálohu živé databáze. Starý hotfix obsahující pouze `functions/index.js` k této verzi nepatří.

V PowerShellu ve složce `estartovka-secure`:

```powershell
npm.cmd ci
npm.cmd --prefix functions ci
npm.cmd run prepare:hosting
npx.cmd firebase login
npx.cmd firebase deploy --project e-startovka-2 --config firebase.production.json --only "database,functions,hosting"
```

Příprava zůstává oddělená: místní `public` používá demo emulátory, produkční `hosting-release` skutečný projekt. Nasazuje se devět callable funkcí Gen 2 a jedna plánovaná Gen 2 funkce `syncAirportFlights`. První nasazení plánovače může vyžadovat povolení Cloud Scheduler API. Platí Firebase tarif Blaze a účtování příslušných služeb. V emulátorech je online synchronizace blokovaná; používá se import uloženého HTML.

## Ověření

- 53 jednotkových testů včetně autorizace, převodu dodaného HTML, časových pásem, limitů síťových odpovědí, duplicit, kolize s ručním záznamem, zachování posádky, změny konfigurace během běhu a rozestupu online pokusů.
- 19 testů databázových pravidel ve skutečném Database Emulatoru.
- Stávající a nové prohlížečové scénáře s náhradou Firebase.
- Skutečné Auth/Functions/Database emulátory: import devíti záznamů, ověření oprávnění, doplnění startérem, opakovaný import a smazání s tombstonem.
- Skutečný Chromium + Firebase SDK + emulátory: uložení zdroje, výběr HTML i data, načtení devíti letů, doplnění pilota a úlohy, historie a opakovaný import bez duplicit.
- Build a kontroly oddělení produkční a místní Hosting konfigurace.

Živý projekt, skutečný běh Cloud Scheduleru a dostupnost Ibisek z infrastruktury Google nebyly ověřené. Test síťového transportu používá řízené odpovědi; není důkazem dostupnosti webu. `npm audit --omit=dev` ve functions uvádí 6 středně závažných nálezů v tranzitivním řetězci Google SDK (`gaxios`/`rimraf`/`glob`/`minimatch`); běžné `npm audit fix` je neodstranilo. Neprováděl se neověřený vynucený upgrade hlavních verzí.

Pro místní kontrolu spusťte `npm test`, `npm run test:rules`, `npm run test:browser`, `npm run test:imports:browser`. Pro skutečné emulátory nejdříve spusťte `npm run emulators`, poté `npm run seed:emulator` a `python tests/imports_emulator.py`. Pro prohlížečový test proti emulátorům použijte po novém seedu `python tests/imports_browser_emulator.py` (Chromium/Playwright). Seed je pouze pro demo projekt a nikdy se nesmí použít na živá data.
