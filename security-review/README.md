# Aktuálně: v5.3.3 – explicitní pásmo pro LOCAL

**[Aktualizace stávající instalace](oprava-importu-local-5.3.3.zip)** · **[Postup a nastavení](OPRAVA-IMPORTU-LOCAL.md)** · **[Celý balíček](estartovka-import-5.3.3.zip)**.

Uživatel potvrdil pro časy zdroje LKST český místní čas. Po aktualizaci Functions i Hostingu a obnovení webu nastavte ve správě LKST „Časové pásmo zdroje při označení LOCAL“ na **Europe/Prague** a uložte zdroj. Bez výslovného nastavení se nejednoznačné LOCAL nadále nepovažuje za konkrétní pásmo.

Prošlo 63 jednotkových testů a skutečný prohlížeč s Firebase SDK a emulátory včetně nastavení pásma a importu LOCAL HTML. Úplné aktuální online načítání zatím nebylo ověřené. Živý Firebase nebyl z tohoto chatu měněn.

Níže je historie předchozích oprav.

---

# Aktuální oprava: odstranění /set_timezone (HTTP 404)

**[Malý opravný balíček pro v5.3.2](oprava-importu-404.zip)** · **[Postup](OPRAVA-IMPORTU-404.md)**.

Nahrazuje dva backendové soubory, stačí nasadit Functions. Volání /set_timezone a cookies byla odstraněna; import ověřuje pásmo přímo ve stránce a převádí potvrzené časové údaje do UTC. Prošlo 61 jednotkových testů. Aktuální plný balíček v5.3.2 tuto opravu už obsahuje. Skutečný online přenos po změně zatím není ověřený.

Předchozí oprava časového pásma, která /set_timezone vyžadovala, je překonaná. Níže zůstává historie verzí.

---

# E-Startovka – aktuálně v5.3.2

**[Oprava data pro již nainstalovanou v5.3.1](oprava-importu-datum-5.3.2.zip)** · **[návod k opravě](OPRAVA-IMPORTU-DATUM.md)** · **[celý nový balíček](estartovka-import-5.3.2.zip)**.

Oprava určuje datum uloženého HTML automaticky, přijímá různé zápisy data stránky a při online neshodě ukáže oba dny. Obsahuje předchozí opravu UTC. Vyžaduje nasadit Functions i Hosting. Prošlo 61 jednotkových testů a ověření automatického data ve skutečném prohlížeči s Firebase emulátory. Konkrétní odpověď aktuálního online zdroje zatím nebyla ověřená.

Předchozí verze a návody níže zůstávají pro historii.

---

# E-Startovka – import Ibisek v5.3.1

**Oprava chyby časového pásma:** [malý balíček](oprava-importu-casove-pasmo.zip) a [postup](OPRAVA-IMPORTU-PASMO.md). Stačí nahradit dva backendové soubory a nasadit Functions. Plný balíček níže už opravu obsahuje. Prošlo 58 jednotkových testů; skutečná online odpověď po přepnutí pásma zatím nebyla ověřena.

Nový balíček je **[estartovka-import-5.3.1.zip](estartovka-import-5.3.1.zip)**. Použití, nasazení, ověřené scénáře a omezení najdete v **[IMPORT-LETU.md](IMPORT-LETU.md)**.

Z dodaného HTML LKST bylo ověřeno všech devět letů, převod Europe/Prague do UTC, doplnění posádky a opakovaný import bez duplicit. Online načítání je implementované s volitelným intervalem pěti minut, ale spojení z živého Firebase se zdrojem ještě není ověřené. Nejprve vyzkoušejte uložený HTML soubor, potom „Načíst nyní“. Živý Firebase ani main nebyly změněny.

SHA-256: `27a38fc70d5d545ca2aba636b98cd82cf6c7e5f306d8fb28440c53e30bba7ed9`.

Původní balíček v5.2.9 a pracovní příprava v5.3.0 zůstávají pro historii. Jejich návody níže se vztahují k tehdejším verzím, nikoli k aktualizaci v5.3.1.

---

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
