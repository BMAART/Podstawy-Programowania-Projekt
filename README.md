# Podstawy-Programowania-Projekt
Celem projektu było wykonanie zestawu zadań praktycznych obejmujących pracę z plikami tekstowymi, narzędziami systemowymi, przetwarzaniem danych, generowaniem skryptów SQL, konwersją plików graficznych, tworzeniem galerii internetowej oraz automatyzacją operacji na plikach.

# Podstawy Programowania – Projekt  
**Autorzy:** Bartosz Materek, Mateusz Świetlik  

---

## Zadanie 3 – Przekształcenie danych do formatu kolumnowego

### Opis  
Celem zadania było przekształcenie danych z pliku tekstowego do formatu trzech kolumn, gotowego do importu.

### Polecenia

`cd /d/ID_101/` \
`tr -d '\r' < dane.txt > dane1.txt` \
`echo -e "x\ty\tz" > danekol.txt && paste - - - < dane1.txt >> danekol.txt` \
`sed -i '1s/z/\t\tz/' danekol.txt` \
`sed -i '2s/0$/\t\t0/' danekol.txt` 

### Wynik: Plik _danekol.txt_ zawierający dane w trzech kolumnach.

## Zadanie 4 – Tworzenie i nakładanie poprawek typu patch

### Opis  
Celem zadania było porównanie dwóch plików tekstowych, wygenerowanie pliku różnic w formacie patch oraz nałożenie tych poprawek na plik źródłowy. Dodatkowo sprawdzono zgodność plików poprzez porównanie sum kontrolnych.

### Polecenia
`cd /d/ID_101/` \
`diff -u lista.txt lista-pop.txt > lista.patch` \
`patch lista.txt < lista.patch` \
`dos2unix lista-pop.txt` \
`md5sum lista.txt lista-pop.txt` 

### Wynik: Sumy kontrolne plików są identyczne.

## Zadanie 5 – Konwersja CSV → SQL oraz SQL → CSV

### Opis  
Celem zadania było przekształcenie danych CSV do formatu SQL oraz wykonanie operacji odwrotnej — konwersji SQL do CSV z dodatkową obróbką danych.

### Polecenia
CSV → SQL \
`awk -F";" 'NR>1 { printf "INSERT INTO stepsData (time, intensity, steps) VALUES (%s, %s, %s);\n", $1, $2, $3 }' steps-2sql.csv > steps-2sql.sql` \
SQL → CSV \
`sed -E 's/INSERT INTO stepsData \(dateTime, steps, synced\) VALUES \(([^,]+), ([^,]+), ([^)]+)\);/\1;\2;\3/' steps-2csv.sql \
| awk -F";" 'BEGIN { print "dateTime;steps;synced" } { dt=$1/1000; printf "%d;%s;%s\n", dt, $2, $3 }' > steps-2csv.csv`

### Wynik: Pliki _steps-2sql.sql_ oraz _steps-2csv.csv_ gotowe do użycia.

## Zadanie 6 – Przygotowanie plików JSON5 do tłumaczenia

### Opis  
Celem zadania było przygotowanie plików językowych JSON5 do tłumaczenia poprzez duplikowanie wpisów klucz–wartość oraz dodanie wersji zakomentowanej. Dodatkowo wyodrębniono nowe wpisy z nowszej wersji pliku.

### Polecenia
`cd /d/ID_101/` \
`sed -E 's/^([[:space:]]*"[^"]+"[[:space:]]*:[[:space:]]*"[^"]+",?)/\/\/ \1\n\1/' en-7.2.json5 > pl-7.2.json5` \
`grep -Fvxf en-7.2.json5 en-7.4.json5` \
`| sed -E 's/^([[:space:]]*"[^"]+"[[:space:]]*:[[:space:]]*"[^"]+",?)/\/\/ \1\n\1/' \` \
`> pl-7.4.json5`

### Wynik: Pliki _pl‑7.2.json5_ oraz _pl‑7.4.json5_ gotowe do tłumaczenia.

## Zadanie 7 – Konwersja i przetwarzanie zdjęć

### Opis  
Celem zadania było rozpakowanie archiwów ZIP, zebranie wszystkich zdjęć, konwersja ich do formatu JPG o wysokości 720 px i DPI 96×96, a następnie przygotowanie archiwum wynikowego.

### Polecenia
`cd /d/ID_101/` \
`unzip kopie-1.zip -d kopie` \
`unzip kopie-2.zip -d kopie` \
`mkdir -p wynik` \
`mkdir -p wszystkie`

`for z in kopie/*.zip; do` \
    `unzip "$z" -d wszystkie` \
`done`

`cd /d/ID_101/wszystkie`\
`for f in *; do`\
    `base=$(basename "$f")`\
    `name="${base%.*}"`\
   ` magick "$f" -resize x720 -density 96x96 "../wynik/${name}.jpg"`\
`done`

`cd /d/ID_101`\
`zip -r fotografie-gotowe.zip wynik`

### Wynik: Archiwum _fotografie-gotowe.zip_ z przekonwertowanymi zdjęciami.

## Zadanie 8 – Generowanie stron PDF z miniaturami

### Opis  
Celem zadania było przygotowanie stron JPG zawierających miniatury zdjęć ułożonych w siatce 2×4, a następnie połączenie ich w jeden dokument PDF.

### Polecenia
`cd /d/ID_101/`\
`mkdir -p pdf_strony`

`montage wynik/*.jpg \`\
   `-tile 2x4 \`\
   `-geometry 380x260+10+10 \`\
  `-gravity center \`\
   `-pointsize 18 \`\
   `-label '%f' \`\
   `pdf_strony/strona-%03d.jpg`

`magick pdf_strony/strona-*.jpg portfolio.pdf`\
`zip -r fotografie-gotowe.zip wynik portfolio.pdf`

### Wynik: Plik _portfolio.pdf_ zawierający strony z miniaturami zdjęć.

## Zadanie 9 – Automatyczne porządkowanie plików według dat

### Opis  
Celem zadania było uporządkowanie plików ZIP według roku i miesiąca, wyodrębnionych z ich nazw.

### Polecenia
`mkdir -p kopie uporzadkowane`\
`unzip -o -j kopie-1.zip -d kopie`\
`unzip -o -j kopie-2.zip -d kopie`

`for plik in kopie/*.zip; do`\
    `nazwa=$(basename -- "$plik")`\
   `rok=${nazwa:0:4}`\
    `miesiac=${nazwa:5:2}`\
    `mkdir -p "zarchiwizowane/$rok/$miesiac"`\
    `cp "$plik" "zarchiwizowane/$rok/$miesiac/"`\
`done`

### Wynik: Pliki uporządkowane w strukturze ROK/MIESIĄC.

## Zadanie 10 – Automatyczne generowanie galerii internetowej

### Opis  
Celem zadania było przygotowanie kompletnej galerii internetowej na podstawie gotowych zdjęć, poprzez automatyczne wygenerowanie bloków HTML i wstawienie ich do szablonu.

### Polecenia
`cd /d/ID_101`\
`unzip galeria.zip -d galeria`\
`unzip fotografie-gotowe.zip -d fotografie`

`cd /d/ID_101/fotografie`\
`mv wynik/* .`\
`rmdir wynik`

`cd /d/ID_101`\
`cp fotografie/* galeria/`\
`cd /d/ID_101/galeria`

Generowanie bloków HTML: \
`for plik in *.{jpg,jpeg,png,JPG,JPEG,PNG}; do`\
    `[ -e "$plik" ] || continue`\
    `echo '<div class="responsive">' >> lista-galeria.html`\
    `echo ' <div class="gallery">' >> lista-galeria.html`\
    `echo "   <a target=\"_blank\" href=\"$plik\">" >> lista-galeria.html`\
    `echo "     <img src=\"$plik\">" >> lista-galeria.html`\
    `echo "   </a>" >> lista-galeria.html`\
    `echo "   <div class=\"desc\">$plik</div>" >> lista-galeria.html`\
    `echo ' </div>' >> lista-galeria.html`\
    `echo '</div>' >> lista-galeria.html`\
    `echo >> lista-galeria.html`\
`done`

Wstawienie do szablonu: \
`sed -i '/plik 1/,/plik 4/d' index.html`\
`sed -i '/<!-- kolejne pliki galerii ... -->/r lista-galeria.html' index.html`

### Wynik: Gotowa galeria internetowa.





