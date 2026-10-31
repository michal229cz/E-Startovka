# Oprava importu: /set_timezone vrací 404 (pro v5.3.2)

Uživatel doložil chybu „Nastavení časového pásma zdroje selhalo: HTTP 404“. Předchozí oprava chybně spoléhala na `/set_timezone`, nalezené v dodaném JavaScriptu; existence tohoto kódu neznamenala, že server tuto cestu skutečně poskytuje.

Nová verze tento krok zcela odstraňuje. Načítá pouze pevnou veřejnou adresu `https://logbook.ibisek.com/loc/<ICAO>/<datum>`, bez POST, cookies, přesměrování nebo domnělé timezone relace. Časové pásmo se určí výhradně z výslovných údajů stránky: samostatný banner UTC, nebo uvedená pojmenovaná zóna, například Europe/Prague. LOCAL bez konkrétní zóny se nikdy automaticky nepovažuje za UTC. Pokud zdroj údaj neposkytne, import se zastaví a vypíše rozpoznané označení.

Dnešek a včerejšek se vybírají podle skutečně deklarované zóny zdroje. Nejprve se zkontroluje stránka pro dnešní datum UTC, z jejího pásma se určí správné kalendářní dny a chybějící stránka se případně načte. Stejný den se v jednom běhu neopakuje. Časy se převádějí do UTC podle původní zóny, včetně letního času. Při rozporu mezi pásmy obou stránek se import zastaví.

## Instalace

Pro již nainstalovanou v5.3.2:

1. Stáhněte `oprava-importu-404.zip` a rozbalte přímo do stávající složky `estartovka-secure`.
2. Nahraďte oba soubory `functions/ibisek.js` a `functions/import-service.js`.
3. Spusťte:

```powershell
npx.cmd firebase deploy --project e-startovka-2 --config firebase.production.json --only functions
```

Závislosti ani frontend se nemění, není potřeba npm ci, build nebo nasazení Hostingu. Po dokončení počkejte alespoň pět minut od posledního online pokusu a použijte **Načíst nyní**. Pro tuto backendovou opravu zůstává v hlavičce v5.3.2.

Oprava odstraňuje doloženou příčinu 404; nepotvrzuje úspěch všech následujících kroků na skutečném zdroji. Načítání ze zdejšího prostředí je stále blokováno HTTP 403, proto skutečná odpověď po této změně zatím nebyla ověřena. Při další chybě pošlete celý přesný text. Testy nejsou důkazem dostupnosti nebo formátu aktuální odpovědi serveru.

Prošlo 61 jednotkových testů. Síťový test výslovně kontroluje jediný přímý GET a nepřítomnost cookies, POST a volání `/set_timezone`; další scénáře ověřují UTC, Europe/Prague, chybějící pásmo, převedené časy a výběr dnů kolem půlnoci.

Tento postup nahrazuje starší OPRAVA-IMPORTU-PASMO.md. Z dřívějších oprav zůstává automatické určení data HTML souboru a ochrana proti duplicitám. Živý Firebase nebyl z tohoto chatu změněn.
