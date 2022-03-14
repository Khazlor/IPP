# Implementační dokumentace k 1. úloze do IPP 2021/2022
#### Jméno a příjmení: Vladimír Horák
#### Login: xhorak72

Implementaci php skriptu jsem rozdělil do 2 souborů:

`parse.php` – obsahuje kontrolu vstupnich parametru a volani jednotlivých casti analyzatoru.
`analyzator.php` – obsahuje třídu `Analyzator` pod kterou je implementována analýza a generace výstupního XML kódu.

Samotná analýza byla rozdělena do 2 částí

## 1. uložení vstupu do pole
Zde ze standartního vstupu čtu data, která následně dělím po řádcích a po slovech (OPCODE / operand) a ukládám si je do 2D pole `$input`.
Zároveň jsem odstranil komentáře a většinu přebytečných mezer a dále pracoval s touto strukturou.

`$input[i][j] - i jde po řádcích, j jde po slovech na řádku`

## 2. analýza
V této metodě jsem procházel data v poli a prováděl lexikální a syntaktické kontroly.
Kdykoli jsem zde narazil na prázdný prvek pole (bílý znak na začátku, nebo konci řádku), tak jsem ho vyhodil z pole.
První kontrola, kterou jsem prováděl je zda je v prvním prvku hlavička `.IPPcode22` bez ohledu na velikost písmen, a kontrolu zda je za hlavičkou ještě nějaký kód, který tam nemá co dělat.
### Lexikální kontroly
V další části analýzy jsem prováděl primárně lexikální kontroly.
Pomocí dvou for cyklů postupně procházím řádky a jednotlivá slova na nich a testuji zda se:

1. shodují s nějakým OPCODE (kde nezáleží na velikosti písmen) a zda je OPCODE pouze na začátku řádku
2. zda se jedná o `int`/`bool`/`string`/`nil` (jako operandy na velikosti písmen záleží)
3. zda slovo obsahuje @, v jakém případě testuji zda je to konstanta, nebo proměnná a provádím další kontroly, jako jsou povolené znaky u proměnné, či zda prostor před, nebo za @ není prázdný, nebo jinak nesprávný.

V případě že najdu OPCODE za nimž má být `label`, provedu kontrolu povolených znaků v labelu a posunu se na další slovo, abych label nevyhodnotil jako nepovolené slovo.

Pokud vše proběhlo v pořádku, provedu vygenerování XML hlavičky, protože v další sekci postupně generuji výstupní XML kód
### Syntaktické kontroly
V této části postupně procházím řádky a kontroluji zda je na začátku OPCODE. Pokud je, tak podle toho jaké má daný OPCODE mít argumenty kontroluji jestli zadané argumenty sedí, a zda jich tam je správný počet. Pokud ano tak vygeneruji XML kód a jdu na další řádek.
## Generování XML
Pro generování výstupního XML kódu jsem použil `XMLWriter`.
S jeho pomocí jsem si vytvořil 3 pomocné metody:
`xml_generate_header` - vygeneruje hlavičku výstupního kódu.
`xml_generate_instr` - vygeneruje kód pro danou zpracovanou instrukci. Počet generovaných argumentů se odvíjí od počtu argumentů zadaných při volání metody.
`xml_generate_end` - vygeneruje konec xml a provede výpis na standartní výstup.

Po lexikálních kontrolách provedu generování hlavičky.
Po syntaktické kontrole řádku provedu generování instrukce s argumenty z daného řádku.
Na konci programu vygeneruji xml konec.
