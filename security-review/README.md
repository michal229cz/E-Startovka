# E-Startovka – v5.2.9

Stáhněte `estartovka-zabezpeceni-k-testovani.zip` přes **Download raw file** a rozbalte do samostatné složky.

Přihlašovací obrazovka má modré pozadí #2563eb podle barvy loga, bílý formulář zůstal stejný. Nadále platí přihlášení před výběrem letiště, seznam všech letišť se zámky podle oprávnění a podpora PNG log.

## Oprava již rozběhnutého nasazení

Při chybě `onUserCreatedRole: Functions cannot be downgraded from GCFv2 to GCFv1` stačí malý balíček **oprava-nasazeni-functions.zip** a [postup v OPRAVA-NASAZENI.md](OPRAVA-NASAZENI.md). Nahradí functions/index.js, takže backend exportuje pouze pět callable funkcí Gen 2. Starou cloudovou funkci odstraňte jednorázově přes explicitní Firebase CLI příkaz v návodu a potom nasazení opakujte. Není potřeba měnit vzhled nebo znovu instalovat závislosti této malé opravy.

Prošlo 27 jednotkových testů, metadata skutečného Firebase SDK potvrzují u všech pěti exportů platformu gcfv2 a registrace v emulátoru uspěla i bez registračního triggeru. Žádná cloudová funkce nebyla z tohoto chatu odstraněna.

## Nasazení na Firebase Hosting

Podrobný postup je v [NASAZENI.md](NASAZENI.md), také uvnitř ZIPu. Projekt je e-startovka-2 a Functions vyžadují tarif Blaze. Před nasazením zkontrolujte skutečné role uživatelů a zachovejte zálohu.

V rozbalené složce spusťte:

```sh
npm ci
npm --prefix functions ci
npm run prepare:hosting
npx firebase login
```

Příprava vytvoří samostatnou složku hosting-release a firebase.production.json s vypnutými emulátory pro skutečný projekt. Místní demo konfigurace zůstává zachovaná. Build a příprava byly ověřeny včetně opakovaného spuštění a dvou kontrol oddělení konfigurací.

Následující příkaz již aktualizuje skutečný projekt:

```sh
npx firebase deploy --project e-startovka-2 --config firebase.production.json --only "database,functions,hosting"
```

Nasazuje Hosting, Functions i pravidla. V tomto chatu nebylo toto nasazení spuštěno. Tato větev mění pouze doručovací balíček a návody; main i živý web zůstávají beze změn.

SHA-256 úplného ZIPu: `142e5d861dfe428fc2d4b11ac58735d666f566686d617ecad1a89503173a07c9`
