# Oprava kontroly data – v5.3.2

Vlastní automatické načítání z Ibisek zatím stále nebylo možné ověřit z tohoto vývojového prostředí. Oprava řeší příliš doslovnou kontrolu data a ruční zadávání data souboru; pokud server skutečně vrací jiný den, online import se nadále bezpečně zastaví a nová zpráva uvede požadované i vrácené datum.

## Instalace do stávající v5.3.1

1. Stáhněte `oprava-importu-datum-5.3.2.zip` a rozbalte přímo do stávající složky `estartovka-secure`. Nahraďte soubory ve functions, src, public a hosting-release. Zachovejte případné vlastní změny jako zálohu.
2. Balíček obsahuje sestavený frontend. Závislosti se nemění; build a nová instalace nejsou potřeba. V této složce spusťte:

```powershell
npx.cmd firebase deploy --project e-startovka-2 --config firebase.production.json --only "functions,hosting"
```

3. Po dokončení obnovte web přes Ctrl+F5. V hlavičce bude v5.3.2. Tato oprava mění také frontend, samotné nasazení Functions nestačí.

## Co je opravené

- Import uloženého HTML získá datum z obsahu stránky. Už se nespoléhá na ručně zadané datum a nesprávné datum ze staršího klienta ignoruje. Nový klient po výběru souboru datum zobrazí jako údaj jen pro čtení. Server jej nezávisle ověří a vrátí skutečné importované datum v odpovědi.
- Datum v poli stránky přijímá místní formát s běžnými mezerami, s jedno- i dvoucifernými čísly, nebo ISO. Když pole chybí či má jiný formát, slouží jako podklad jednoznačný odkaz na CSV export konkrétního letiště. Podporované údaje si nesmějí odporovat. Původní parser porovnával pouze přesný text DD.MM.YYYY.
- Online import nadále ověřuje přesnou shodu s požadovaným dnem. Nenačte omylem jiný den. Nová chyba řekne například „požadováno 2026-10-31, stránka 2026-10-17“ místo obecného sdělení o souboru. To rozliší formátovací problém od stránky, která skutečně vrací jiná data.
- Neočekávané chyby načítání nově obsahují konkrétní důvod přímo v hlášce, aby nebylo nutné obnovovat web pro přečtení uloženého stavu.
- Oprava nastavení UTC z předchozího balíčku je zahrnutá. Není potřeba postupně instalovat oba staré hotfixy.

## Vyzkoušení

Ve správě letiště vyberte původní OGN Logbook.html. Datum se samo ukáže jako 2026-10-17. Klikněte **Importovat soubor**. Stejné již importované záznamy se znovu nevytvoří a doplněná posádka zůstane zachovaná. Pro HTML není potřeba čekat pět minut.

Poté použijte **Načíst nyní**, nejdříve pět minut od předchozího online pokusu. Při další chybě pošlete celý nový text včetně požadovaného data, data stránky a hodnoty pole. Skutečnou odpověď serveru ze živého projektu nelze rekonstruovat pouze z původní obecné chybové zprávy.

## Ověření

Prošlo 61 jednotkových testů, nové prohlížečové scénáře s náhradou Firebase, dvě kontroly Hosting konfigurace a skutečný Chromium s Firebase SDK a místními emulátory: automatické určení data, import devíti letů, doplnění posádky, historie a opakovaný import bez duplicit. Živé Firebase nebylo z tohoto chatu měněno.
