# Oprava konfliktu GCFv2 / GCFv1

Tato oprava je určená pro dříve stažený balíček E-Startovky v5.2.8. Odstraňuje z functions/index.js nepoužívaný export onUserCreatedRole a import Firebase Functions v1. Registrace vytváří guest profil pod ochranou pravidel; backend exportuje pouze pět potřebných callable funkcí Gen 2. Tento postup byl ověřen 27 testy, kontrolou metadat Firebase SDK a skutečnou registrací v emulátorech.

## Postup v PowerShellu

1. Uschovejte původní functions/index.js. Z opravy přeneste nový soubor functions/index.js do stejné složky projektu a nahraďte jím původní soubor. Žádné nové závislosti ani nový build frontendu nejsou pro tuto malou opravu potřeba.
2. V hlavní složce projektu (kde leží firebase.production.json) odstraňte přesně starou funkci z projektu e-startovka-2:

```powershell
npx.cmd firebase functions:delete default:onUserCreatedRole --region europe-west1 --project e-startovka-2
```

Při potvrzení v CLI ověřte, že jde pouze o onUserCreatedRole v europe-west1. Tento příkaz odstraňuje starou Cloud Function, nikoli uživatelské účty nebo záznamy databáze. Firebase CLI spravuje i odregistrování přidruženého spouštěče. Opravený zdrojový kód tuto funkci znovu nevytváří.

3. Znovu spusťte nasazení:

```powershell
npx.cmd firebase deploy --project e-startovka-2 --config firebase.production.json --only "database,functions,hosting"
```

Cloudové kroky spouštíte vy ve svém prostředí s vaším Firebase přihlášením. Z tohoto chatu nebyla žádná cloudová funkce smazána a web nebyl nasazen. Pokud CLI ukáže jinou chybu, zachovejte její celý výpis bez hesel a tokenů pro další diagnostiku.
