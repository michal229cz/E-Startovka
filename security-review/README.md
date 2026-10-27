# E-Startovka – v5.2.8

Stáhněte `estartovka-zabezpeceni-k-testovani.zip` přes **Download raw file** a rozbalte do samostatné složky.

Přihlašovací obrazovka má modré pozadí #2563eb podle barvy loga, bílý formulář zůstal stejný. Nadále platí přihlášení před výběrem letiště, seznam všech letišť se zámky podle oprávnění a podpora PNG log.

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

SHA-256 ZIPu: `44cab3757284910ce18595c6034d66bfba4c2bee474d49841b913f004979b46e`
