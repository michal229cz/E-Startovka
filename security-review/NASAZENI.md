# Nasazení na Firebase Hosting – e-startovka-2

Balíček v5.2.9 má modré přihlašovací pozadí a přípravu samostatné verze pro Hosting. Níže uvedené příkazy spouštíte vy ve svém terminálu v rozbalené složce `estartovka-secure`. V tomto chatu nebyl web nasazen.

## 1. Připravte skutečný projekt

V konzoli Firebase vyberte **e-startovka-2**:

- Pro nasazení Cloud Functions musí mít projekt tarif **Blaze** s připojenou fakturací. Hosting sám o sobě nevyžaduje všechny možnosti tohoto tarifu, ale tento web používá také Functions.
- V Authentication → Sign-in method povolte **Email/Password**. V Settings → Authorized domains zkontrolujte doménu výsledného Hostingu; vlastní doménu přidejte, pokud ji použijete.
- Projekt musí mít Realtime Database odpovídající veřejné konfiguraci: `e-startovka-2-default-rtdb.europe-west1.firebasedatabase.app`.
- Před změnou živé verze zachovejte zálohu databáze, původních pravidel a původní verze webu. U existujících účtů ověřte, kdo smí mít admin/superadmin; stará pravidla umožňovala uživateli vlastní povýšení a nová pravidla již přidělené role sama neopraví.

Pokud je projekt prázdný, založte přes důvěryhodnou Firebase Console první záznam `airports/<id>` s poli `name`, `icao`, `order` (číslo) a `logoUrl` (prázdný řetězec nebo podporované logo). Příklad ID je `lkst`; zadejte skutečné údaje svého letiště. První správce se pak na nasazeném webu zaregistruje a ověří e-mail. V Authentication najdete jeho UID a v RTDB u `users/<UID>` mu jako vlastník projektu nastavíte `role: "superadmin"` a správný `airportId`. Při registraci vzniká bezpečně pouze guest. Oprávnění není potřeba otevírat v databázových pravidlech.

## 2. Připravte soubory

Je potřeba Node.js 24. V rozbalené složce spusťte:

```sh
npm ci
npm --prefix functions ci
npm run prepare:hosting
```

Přípravný příkaz provede build a vytvoří:

- `hosting-release/` – kompletní veřejné soubory s konfigurací vašeho projektu a vypnutými emulátory;
- `firebase.production.json` – konfiguraci nasazení pravidel, Functions i Hostingu. Hosting ukazuje na `hosting-release/` a produkční CSP již neobsahuje adresy lokálních emulátorů.

Použije se vámi dodaná veřejná konfigurace v `firebase-config.live.example.js`. Skript ověřuje projekt e-startovka-2 a jeho databázi. Soubor neobsahuje servisní klíč a takový klíč do něj nikdy nevkládejte. Změny aplikace dělejte v `src/`, poté znovu spusťte přípravu. Generovanou složku ručně neupravujte.

Místní `public/firebase-config.js` a výchozí demo alias v `.firebaserc` zůstávají zachované, takže běžné místní testy nadále používají emulátory.

## 3. Přihlaste Firebase CLI

```sh
npx firebase login
```

V otevřeném prohlížeči vyberte Google účet s oprávněním spravovat projekt e-startovka-2. Hesla, OAuth tokeny ani servisní klíče se neposílají do chatu.

## Jednorázová oprava staré funkce onUserCreatedRole

Pokud CLI hlásí `Functions cannot be downgraded from GCFv2 to GCFv1` pro onUserCreatedRole, používejte opravený functions/index.js z této verze. Export onUserCreatedRole je odstraněný: registrace nyní vytváří pouze guest profil přímo pod kontrolou databázových pravidel a server čte role z chráněných záznamů. Nové vydání nasazuje pět callable funkcí Gen 2.

Starou již nasazenou funkci odstraňte jednorázově přes Firebase CLI:

```sh
npx firebase functions:delete default:onUserCreatedRole --region europe-west1 --project e-startovka-2
```

Před potvrzením v CLI ověřte, že seznam obsahuje pouze `onUserCreatedRole` v europe-west1. Příkaz odstraňuje tuto starou Cloud Function; neodstraňuje účty ani databázové záznamy. Použijte Firebase CLI, které při mazání spravuje také přidružené spouštěče. Funkce se z opraveného zdrojového kódu znovu nevytvoří.

Na Windows v PowerShellu lze použít `npm.cmd` a `npx.cmd`, pokud systém blokuje jejich .ps1 variantu.

## 4. Nasaďte všechny části

Následující příkaz **už mění skutečný projekt e-startovka-2**:

```sh
npx firebase deploy --project e-startovka-2 --config firebase.production.json --only "database,functions,hosting"
```

Nasadí pravidla Realtime Database, Functions i kompletní veřejné soubory. Aktualizace těchto tří služeb není atomická; počítejte s krátkým přerušením provozu během přechodu. Samotný Hosting pro tento web nestačí, protože starty, přistání a editace používají Functions a omezení přístupu vyžadují nová pravidla.

Použití explicitního `--project` a `--config` je důležité: výchozí lokální konfigurace zůstává demo. Nepoužívejte zde příkaz `npm run seed:emulator` k přípravě živých účtů; ten připravuje pouze izolovaná demo data a emulátory.

## 5. Ověřte nasazený web

CLI po úspěšném nasazení vypíše Hosting URL. Otevřete ji a ověřte přihlášení, vlastní letiště, zámky cizích letišť, start a přistání zkušebního letu, historii, logo a odhlášení. Ověřte také registraci a doručení skutečného ověřovacího e-mailu – emulátory reálné doručování neposkytují.

Základní workflow, registrace, pravidla a PNG loga prošly testy proti emulátorům. Živá konfigurace vašeho projektu, jeho stará data a skutečné doručování e-mailů v tomto chatu ověřeny nebyly.
