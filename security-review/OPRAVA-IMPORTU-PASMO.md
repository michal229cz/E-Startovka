# Oprava importu: časové pásmo (pro v5.3.1)

Původní parser hledal pouze českou větu s konkrétním pásmem. Patička ale může zobrazovat jen `ALL TIMES ARE YOURS LOCAL`. Samotné LOCAL neříká, které pásmo server použil.

Dodaný zdrojový JavaScript při změně pásma volá `POST /set_timezone` a až potom stránku obnovuje. Oprava napodobuje tento postup: nejprve odešle UTC, vyžaduje úspěšnou odpověď se serverovou cookie `session` a pak touto relací načte lety. LOCAL se pro online import považuje za UTC pouze po tomto serverovém nastavení. Výslovně uvedené jiné pásmo zůstává chybou. U ručně nahraného HTML bez informace o pásmu se UTC nikdy nepředpokládá.

Přibyla také podpora výslovného UTC banneru bez české věty a diagnostika rozpoznaného označení při další chybě. Přístup k živému `/set_timezone` z vývojového prostředí nebyl možný; oprava je ověřená řízenými odpověďmi a 58 jednotkovými testy, nikoli skutečnou online synchronizací.

## Instalace

Tato malá oprava je určená do již nainstalované verze v5.3.1.

1. Stáhněte `oprava-importu-casove-pasmo.zip` přes GitHub a rozbalte jej do své existující složky `estartovka-secure`.
2. Nahraďte oba soubory: `functions/ibisek.js` a `functions/import-service.js`. Před nahrazením si můžete zachovat jejich kopie.
3. V PowerShellu z této složky spusťte:

```powershell
npx.cmd firebase deploy --project e-startovka-2 --config firebase.production.json --only functions
```

Pro tuto opravu nejsou potřeba nové závislosti ani build frontendu. Neinstalujte ji do starších verzí, které ještě nemají kompletní import v5.3.1.

Po úspěšném nasazení počkejte aspoň pět minut od posledního online pokusu a ve správě letiště použijte **Načíst nyní**. Případnou novou chybovou zprávu pošlete celou, včetně části „Označení ve zdroji“, pokud se zobrazí. Živé Firebase prostředí nebylo z tohoto chatu měněno.
