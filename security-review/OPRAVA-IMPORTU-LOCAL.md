# Výslovné nastavení pásma LOCAL – v5.3.3

Živá odpověď Ibisek byla načtena, ale deklaruje jen `ALL TIMES ARE YOURS LOCAL`, bez konkrétního pásma. Uživatel potvrdil, že časy jeho zdroje LKST odpovídají českému místnímu času včetně letního času. Použije se proto výslovně nastavené `Europe/Prague`, nikoli časové pásmo počítače nebo pevný hodinový posun.

## Aktualizace stávající verze

1. Stáhněte `oprava-importu-local-5.3.3.zip` a rozbalte jej přímo do existující složky `estartovka-secure`. Nahraďte soubory ve functions, src, public a hosting-release. Případné vlastní úpravy zachovejte jako zálohu.
2. Balíček obsahuje sestavený frontend a nemění závislosti. Není potřeba npm ci ani build. Spusťte:

```powershell
npx.cmd firebase deploy --project e-startovka-2 --config firebase.production.json --only "functions,hosting"
```

3. Obnovte web přes Ctrl+F5 a ověřte v hlavičce **v5.3.3**.
4. Otevřete **Správa letiště LKST → Zdroj letů → Časové pásmo zdroje při označení LOCAL**.
5. Vyplňte přesně **Europe/Prague** a klikněte **Uložit zdroj**. Po uložení můžete použít **Načíst nyní**. Pro další letiště zvolte jejich skutečné ověřené pásmo zdroje; toto nastavení není globální.

Nastavení zůstává prázdné, dokud jej správce výslovně nezadá. Pro LKST vychází volba Europe/Prague z potvrzení uživatele, nikoli jen z polohy letiště. Živé nastavení nebylo z tohoto chatu změněno.

## Pravidla použití pásma

- Nastavení smí uložit pouze ověřený správce vlastního letiště nebo superadmin. Klient nemůže libovolně ovlivnit pásmo jednotlivého importu; backend čte chráněnou konfiguraci letiště.
- Použije se pouze při rozpoznaném LOCAL banneru bez konkrétního pásma. Výslovně uvedené pásmo nebo UTC přímo ve stránce má přednost.
- Prázdná hodnota vyžaduje údaj ze stránky a jinak import blokuje. Povoleny jsou ověřené IANA zóny nebo UTC; neznámé hodnoty či pevné číselné posuny jsou odmítnuty.
- Pásmo se používá pro online přenos i uložené HTML. Správce musí nastavit pásmo, kterému skutečné údaje zdroje odpovídají. Při importu souboru zobrazeného v jiném pásmu má přednost jeho výslovný údaj; pokud jej soubor neobsahuje, musí nastavení odpovídat i tomuto souboru.
- Europe/Prague zohledňuje letní i zimní čas. Časy se ukládají v UTC, denní dotazy se vybírají podle pásma zdroje. Dvojznačný čas při změně letního času se neodhaduje.
- Stav po importu ukazuje použité pásmo a zda pochází z nastavení letiště. Nevolá se `/set_timezone` a neposílají se timezone ani session cookies.
- Zůstává automatické určení data souboru, ochrana proti duplicitám, uchování posádky i kontrola skutečně jiného data.

## Ověření a limity

Prošlo 63 jednotkových testů, prohlížečové scénáře s náhradou Firebase, skutečný Chromium s Firebase SDK a emulátory (nastavení Europe/Prague, import devíti LOCAL letů, doplnění posádky a opakovaný import) a dvě kontroly Hosting konfigurace. Oprávnění k nastavení, odmítnutí neplatných zón, převod časů a uložení použitého pásma jsou pokryté testy.

Celý aktuální přenos ze skutečného Ibisek zatím nebyl ověřen z tohoto prostředí, kde je přístup blokován. Potvrzení pásma poskytl uživatel. Pokud po nastavení import skončí jinou chybou, pošlete její celý text; nelze předem potvrdit ještě nezpracované varianty aktuálních tabulek nebo dostupnost zdroje.
