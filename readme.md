# SQL Zadania Mikołaj Markowicz
# lab 1 zadania 1

## **Zadanie 1**

**Stwórz tabelę `pracownik` z kolumnami:**

```sql
CREATE TABLE pracownik (
    id INT AUTO_INCREMENT PRIMARY KEY,  
    imie VARCHAR(50) NOT NULL,          
    nazwisko VARCHAR(100) NOT NULL,     
    data_urodzenia DATE,                -
    stanowisko ENUM('sprzedawca', 'magazynier', 'księgowa')  
);
```

## **Zadanie 2**

**Wstaw do tabeli `pracownik` trzy rekordy z różnymi wartościami.**

```sql
INSERT INTO pracownik (imie, nazwisko, data_urodzenia, stanowisko) VALUES
('Aleksander', 'Mierzwa', '1988-01-15', 'sprzedawca'),
('Tomasz', 'Wasiak', '1989-11-11', 'magazynier'),
('Anna', 'Taras', '1993-03-03', 'księgowa');
```

## **Zadanie 3**

**Stwórz tabelę `dzial` z kolumnami:**

```sql
CREATE TABLE dzial (
    id INT AUTO_INCREMENT PRIMARY KEY,  
    nazwa VARCHAR(255) NOT NULL         
);
```

## **Zadanie 4**

**Dodaj trzy działy do tabeli `dzial`:**

```sql
INSERT INTO dzial (nazwa) VALUES
('sprzedaż'),
('księgowość'),
('magazyn');
```

## **Zadanie 5**

**Ustaw wartość domyślną kolumny `stanowisko` w tabeli `pracownik` na 'sprzedawca'.**

```sql
ALTER TABLE pracownik 
MODIFY COLUMN stanowisko ENUM('sprzedawca', 'magazynier', 'księgowa') DEFAULT 'sprzedawca';
```

## **Zadanie 6**

**Dodaj do tabeli `pracownik` kolumnę `pensja` jako liczba zmiennoprzecinkowa (float) z 5 miejscami całkowitymi i 2 dziesiętnymi. Uzupełnij kolumnę wartościami.**

```sql
ALTER TABLE pracownik 
ADD COLUMN pensja FLOAT(7,2);

ALTER TABLE pracownik 
MODIFY COLUMN pensja DECIMAL(10,2);

UPDATE pracownik 
SET pensja = CASE 
    WHEN stanowisko = 'sprzedawca' THEN 100000.00
    WHEN stanowisko = 'magazynier' THEN 100000.50
    WHEN stanowisko = 'księgowa' THEN 100000.75
    ELSE 1000.00 
END;
```

## **Zadanie 7**

**Zmień w tabeli `dzial` nazwę kolumny `nazwa` na `nazwa_dzialu`, `id` na `id_dzialu`, w tabeli `pracownik` kolumnę `id` na `id_pracownika`.**

```sql
ALTER TABLE dzial 
CHANGE id id_dzialu INT(11) NOT NULL AUTO_INCREMENT;

ALTER TABLE dzial 
CHANGE nazwa nazwa_dzialu VARCHAR(255);

ALTER TABLE pracownik 
CHANGE id id_pracownika INT NOT NULL AUTO_INCREMENT;
```

## **Zadanie 8**

**Usuń z tabeli `pracownik` pracownika o najwyższej wartości pola `id_pracownika`.**

```sql
SET @max_id = (
    SELECT MAX(id_pracownika) 
    FROM pracownik
);

DELETE FROM pracownik 
WHERE id_pracownika = @max_id;
```


# lab 1 zadania 02

## **Zadanie 1**

**Do tabeli `pracownik` dodaj kolumnę `dzial` i ustaw ją jako klucz obcy do tabeli `dzial`. Usuwanie rekordów potomnych powinno być uniemożliwione. Sprawdź, czy mechanizm spójności działa. Przypisz pracowników do działów.**

```sql
ALTER TABLE pracownik
ADD COLUMN id_dzialu INT,
ADD CONSTRAINT fk_pracownik_dzial
FOREIGN KEY (id_dzialu) REFERENCES dzial(id_dzialu) ON DELETE RESTRICT ON UPDATE CASCADE;

UPDATE pracownik
SET id_dzialu = CASE
    WHEN stanowisko = 'sprzedawca' THEN 1  
    WHEN stanowisko = 'księgowa' THEN 2    
    WHEN stanowisko = 'magazynier' THEN 3  
    ELSE NULL
END;
```

## **Zadanie 2**

**Utwórz tabelę `stanowisko` z kolumnami `id_stanowiska` (klucz główny) oraz `nazwa_stanowiska`. Dodaj rekordy, które będą zawierały wartości z tabeli `pracownik` i kolumny `stanowisko`.**

```sql
CREATE TABLE stanowisko (
    id_stanowiska INT AUTO_INCREMENT PRIMARY KEY,
    nazwa_stanowiska VARCHAR(100) NOT NULL UNIQUE
);

INSERT INTO stanowisko (nazwa_stanowiska)
SELECT DISTINCT stanowisko FROM pracownik;
```

## **Zadanie 3**

**Zmień definicję kolumny `stanowisko` tak, aby była kluczem obcym do tabeli `stanowisko`. Ustaw pracownikom wartość w tej kolumnie tak, żeby odzwierciedlała wcześniejszą wartość tekstową.**

```sql
ALTER TABLE pracownik
ADD COLUMN id_stanowiska INT,
ADD CONSTRAINT fk_pracownik_stanowisko
FOREIGN KEY (id_stanowiska) REFERENCES stanowisko(id_stanowiska) ON DELETE RESTRICT ON UPDATE CASCADE;

UPDATE pracownik p
JOIN stanowisko s ON p.stanowisko = s.nazwa_stanowiska
SET p.id_stanowiska = s.id_stanowiska;
```

## **Zadanie 4**

**Zmień definicję klucza obcego w tabeli `pracownik` w kolumnie `dzial` tak, aby przy usunięciu działu z tabeli `dzial` wstawiana była wartość `NULL` w kolumnie `dzial` tabeli `pracownik`.**

```sql
ALTER TABLE pracownik
DROP FOREIGN KEY fk_pracownik_dzial;

ALTER TABLE pracownik
ADD CONSTRAINT fk_pracownik_dzial
FOREIGN KEY (id_dzialu) REFERENCES dzial(id_dzialu) ON DELETE SET NULL ON UPDATE CASCADE;
```

# lab 2 zadania część pierwsza

## **Zadanie 1**
```sql
-- Wyświetlenie nazwisk pracowników posortowanych alfabetycznie

use __firma_zti;

SELECT nazwisko
FROM pracownik
ORDER BY nazwisko ASC;
```

## **Zadanie 2**
```sql
-- Wyświetlenie imienia, nazwiska i pensji pracowników urodzonych po 1979 roku
SELECT imie, nazwisko, pensja
FROM pracownik
WHERE data_urodzenia > 1979;
```

## **Zadanie 3**
```sql
-- Wyświetlenie wszystkich informacji dla pracowników z pensją pomiędzy 3500 a 5000
SELECT *
FROM pracownik
WHERE pensja BETWEEN 3500 AND 5000;
```
## **Zadanie 4**
```sql
-- Wyświetlenie towarów, których ilość jest większa niż 10
SELECT *
FROM stan_magazynowy
WHERE ilosc > 10;
```
## **Zadanie 5**
```sql

-- Wyświetlenie wszystkich towarów, których nazwa zaczyna się od A, B lub C
SELECT *
FROM towar
WHERE nazwa_towaru LIKE 'A%'
   OR nazwa_towaru LIKE 'B%'
   OR nazwa_towaru LIKE 'C%';
```
## **Zadanie 6**
```sql
-- Wyświetlenie wszystkich klientów indywidualnych (nie firm)
SELECT *
FROM klient
WHERE czy_firma = 0;
```
## **Zadanie 7**
```sql
-- Wyświetlenie 10 najnowszych zamówień
SELECT *
FROM zamowienie
ORDER BY data_zamowienia DESC
LIMIT 10;
```
## **Zadanie 8**
```sql
-- Wyświetlenie 5 najmniej zarabiających pracowników
SELECT *
FROM pracownik
ORDER BY pensja ASC
LIMIT 5;
```
## **Zadanie 9**
```sql
-- Wyświetlenie 10 najdroższych towarów, których nazwa nie zawiera litery 'a'
SELECT *
FROM towar
WHERE nazwa_towaru NOT LIKE '%a%'
ORDER BY cena_zakupu DESC
LIMIT 10;
```
## **Zadanie 10**
```sql
-- Wyświetlenie towarów z jednostką miary 'szt', posortowanych po nazwie (A-Z), następnie po cenie zakupu malejąco
SELECT t.*
FROM towar t
JOIN stan_magazynowy sm ON t.id_towaru = sm.towar
JOIN jednostka_miary jm ON sm.jm = jm.id_jednostki
WHERE jm.nazwa = 'szt'
ORDER BY t.nazwa_towaru ASC, t.cena_zakupu DESC;
```
## **Zadanie 11**
```sql

-- Stworzenie nowej tabeli towary_powyzej_100 i wstawienie do niej towarów z ceną większą lub równą 100
USE is_markowiczm;

CREATE TABLE towary_powyzej_100 AS 
SELECT * 
FROM __firma_zti.towar 
WHERE cena_zakupu >= 100;
```
## **Zadanie 12**
```sql
-- Stworzenie nowej tabeli pracownik_50_plus i wstawienie do niej pracowników w wieku 50 lat lub więcej
CREATE TABLE is_markowiczm.pracownik_50_plus AS 
SELECT * 
FROM __firma_zti.pracownik 
WHERE TIMESTAMPDIFF(YEAR, data_urodzenia, CURDATE()) >= 50;
```
# lab 2 zadania część druga

## **Zadanie 1**
```sql
-- Wyświetlenie imienia, nazwiska i nazwy działu każdego pracownika
SELECT p.imie, p.nazwisko, d.nazwa
FROM pracownik p
JOIN dzial d ON p.dzial = d.id_dzialu;

```
## **Zadanie 2**
```sql

-- Wyświetlenie nazwy towaru, nazwy kategorii oraz ilości towaru, posortowane po ilości malejąco
SELECT t.nazwa_towaru, k.nazwa_kategori, sm.ilosc
FROM towar t
JOIN kategoria k ON t.kategoria = k.id_kategori
JOIN stan_magazynowy sm ON t.id_towaru = sm.towar
ORDER BY sm.ilosc DESC;
```
## **Zadanie 3**
```sql
-- Wyświetlenie wszystkich anulowanych zamówień
SELECT *
FROM zamowienie
WHERE status_zamowienia = 
                        (SELECT id_statusu_zamowienia 
                        FROM status_zamowienia 
                        WHERE nazwa_statusu_zamowienia = 'Anulowane');

```
## **Zadanie 4**
```sql

-- Wyświetlenie klientów, których adres podstawowy jest w Olsztynie
SELECT k.*
FROM klient k
JOIN adres_klienta a ON k.id_klienta = a.klient
WHERE a.miejscowosc = 'Olsztyn';
```
## **Zadanie 5**
```sql
-- Wyświetlenie nazw jednostek miary, które nie zostały nigdy użyte w stan_magazynowy
SELECT jm.nazwa
FROM jednostka_miary jm
LEFT JOIN stan_magazynowy sm ON jm.id_jednostki = sm.jm
WHERE sm.jm IS NULL;
```
## **Zadanie 6**
```sql
-- Wyświetlenie numeru zamówienia, nazwy towaru, ilości i ceny dla zamówień z 2018 roku
SELECT z.numer_zamowienia, t.nazwa_towaru, pz.ilosc, pz.cena
FROM zamowienie z
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
JOIN towar t ON pz.towar = t.id_towaru
WHERE YEAR(z.data_zamowienia) = 2018;

```
## **Zadanie 7**
```sql
-- Stworzenie tabeli towary_full_info
CREATE TABLE is_markowiczm.towary_full_info AS 
SELECT t.nazwa_towaru, t.cena_zakupu, k.nazwa_kategori, sm.ilosc, jm.nazwa AS jednostka_miary
FROM towar t
JOIN kategoria k ON t.kategoria = k.id_kategori
JOIN stan_magazynowy sm ON t.id_towaru = sm.towar
JOIN jednostka_miary jm ON sm.jm = jm.id_jednostki;
```
## **Zadanie 8**
```sql
-- Wyświetlenie pozycji zamówień dla 5 najstarszych zamówień
SELECT pz.*
FROM pozycja_zamowienia pz
JOIN zamowienie z ON pz.zamowienie = z.id_zamowienia
ORDER BY z.data_zamowienia ASC
LIMIT 5;
```
## **Zadanie 9**
```sql
-- Wyświetlenie wszystkich zamówień, które mają status inny niż zrealizowane
SELECT *
FROM zamowienie
WHERE status_zamowienia != 
                            (SELECT id_statusu_zamowienia 
                            FROM status_zamowienia 
                            WHERE nazwa_statusu_zamowienia = 'Zrealizowane');
```
## **Zadanie 10**
```sql
-- Wyświetlenie adresów z niepoprawnym kodem (niepoprawność można definiować np. jako brakujące cyfry)
SELECT *
FROM adres_klienta
WHERE kod NOT REGEXP '^[0-9]{2}-[0-9]{3}$';
```

# lab 3 zadania część pierwsza funkcje

## **Zadanie 1**

```sql
-- imię i nazwisko każdego pracownika i jego rok urodzenia.
use __firma_zti;
SELECT imie, nazwisko, YEAR(data_urodzenia) AS rok_urodzenia
FROM pracownik;


```
## **Zadanie 2**
```sql
-- imię i nazwisko pracowników oraz ich wiek w latach (bez uwzględniania miesiąca i dnia urodzenia).
SELECT imie, nazwisko, TIMESTAMPDIFF(YEAR, data_urodzenia, CURDATE()) AS wiek
FROM pracownik;
```
## **Zadanie 3**
```sql
-- nazwa działu i liczbę pracowników przypisanych do każdego z nich.
SELECT d.nazwa, COUNT(p.id_pracownika) AS liczba_pracownikow
FROM dzial d
LEFT JOIN pracownik p ON d.id_dzialu = p.dzial
GROUP BY d.nazwa;
```
## **Zadanie 4**
```sql
-- nazwa kategorii liczba produktów w każdej z nich.
SELECT k.nazwa_kategori, COUNT(t.id_towaru) AS liczba_produktow
FROM kategoria k
LEFT JOIN towar t ON k.id_kategori = t.kategoria
GROUP BY k.nazwa_kategori;
```
## **Zadanie 5**
```sql
-- Wyświetl nazwę kategorii i w kolejnej kolumnie listę wszystkich produktów należących do każdej z nich.
SELECT k.nazwa_kategori, GROUP_CONCAT(t.nazwa_towaru ORDER BY t.nazwa_towaru SEPARATOR ', ') AS produkty
FROM kategoria k
LEFT JOIN towar t ON k.id_kategori = t.kategoria
GROUP BY k.nazwa_kategori;
```
## **Zadanie 6**
```sql
-- Wyświetl średnie zarobki pracowników za zaokrągleniem do 2 miejsc po przecinku.
SELECT ROUND(AVG(pensja), 2) AS srednia_pensja
FROM pracownik;
```
## **Zadanie 7**
```sql
-- Wyświetl średnie zarobki pracowników, którzy pracują co najmniej od 5 lat.
SELECT ROUND(AVG(pensja), 2) AS srednia_pensja
FROM pracownik
WHERE TIMESTAMPDIFF(YEAR, data_zatrudnienia, CURDATE()) >= 5;
```
## **Zadanie 8**
```sql
-- Wyświetl 10 najczęściej sprzedawanych produktów.
SELECT t.nazwa_towaru, SUM(pz.ilosc) AS ilosc_sprzedana
FROM pozycja_zamowienia pz
JOIN towar t ON pz.towar = t.id_towaru
GROUP BY t.nazwa_towaru
ORDER BY ilosc_sprzedana DESC
LIMIT 10;
```
## **Zadanie 9**
```sql
-- Wyświetl numer zamówienia, jego wartość (suma wartości wszystkich jego pozycji) zarejestrowanych w pierwszym kwartale 2017 roku.
SELECT z.numer_zamowienia, SUM(pz.ilosc * pz.cena) AS wartosc_zamowienia
FROM zamowienie z
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
WHERE YEAR(z.data_zamowienia) = 2017 AND MONTH(z.data_zamowienia) BETWEEN 1 AND 3
GROUP BY z.numer_zamowienia;
```
## **Zadanie 10**
```sql
-- Wyświetl imię, nazwisko i sumę wartości zamówień, które dany pracownik dodał. Posortuj malejąco po sumie.
SELECT p.imie, p.nazwisko, SUM(pz.ilosc * pz.cena) AS suma_wartosci_zamowien
FROM pracownik p
JOIN zamowienie z ON p.id_pracownika = z.pracownik_id_pracownika
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
GROUP BY p.imie, p.nazwisko
ORDER BY suma_wartosci_zamowien DESC;
```
# lab 3 zadania część druga agregacje

## **Zadanie 1**

```sql
-- Wyświetl nazwę działu i minimalną, maksymalną i średnią wartość pensji w każdym dziale.
SELECT d.nazwa, MIN(p.pensja) AS min_pensja, MAX(p.pensja) AS max_pensja, ROUND(AVG(p.pensja), 2) AS srednia_pensja
FROM dzial d
JOIN pracownik p ON d.id_dzialu = p.dzial
GROUP BY d.nazwa;
```
## **Zadanie 2**
```sql
-- Wyświetl pełną nazwę klienta i wartość zamówienia dla 10 najwyższych wartości zamówienia.
use __firma_zti;
SELECT k.pelna_nazwa, SUM(pz.ilosc * pz.cena) AS wartosc_zamowienia
FROM zamowienie z
JOIN klient k ON z.klient = k.id_klienta
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
GROUP BY k.pelna_nazwa
ORDER BY wartosc_zamowienia DESC
LIMIT 10;
```
## **Zadanie 3**
```sql
-- Wyświetl wartość przychodu dla każdego roku. Dane posortuj malejąco według sumy wartości zamówień.
use __firma_zti;
SELECT YEAR(z.data_zamowienia) AS rok, SUM(pz.ilosc * pz.cena) AS przychod
FROM zamowienie z
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
GROUP BY rok
ORDER BY przychod DESC;

```
## **Zadanie 4**
```sql
-- Wyświetl sumę wartości wszystkich anulowanych zamówień.
use __firma_zti;
SELECT SUM(pz.ilosc * pz.cena) AS suma_anulowanych
FROM zamowienie z
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
WHERE z.status_zamowienia = 
                            (SELECT id_statusu_zamowienia 
                            FROM status_zamowienia 
                            WHERE nazwa_statusu_zamowienia = 'Anulowane');
```
## **Zadanie 5**
```sql
-- Wyświetl liczbę zamówień i sumę zamówień dla każdego miasta z podstawowego adresu klientów.
use __firma_zti;
SELECT a.miejscowosc, COUNT(z.id_zamowienia) AS liczba_zamowien, SUM(pz.ilosc * pz.cena) AS suma_zamowien
FROM zamowienie z
JOIN klient k ON z.klient = k.id_klienta
JOIN adres_klienta a ON k.id_klienta = a.klient
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
GROUP BY a.miejscowosc;
```
## **Zadanie 6**
```sql
-- Wyświetl dotychczasowy dochód firmy biorąc pod uwagę tylko zamówienia zrealizowane.
use __firma_zti;
SELECT SUM(pz.ilosc * pz.cena) AS dochod
FROM zamowienie z
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
WHERE z.status_zamowienia = 
                            (SELECT id_statusu_zamowienia 
                            FROM status_zamowienia 
                            WHERE nazwa_statusu_zamowienia = 'Zrealizowane');
```
## **Zadanie 7**
```sql
-- Policz i wyświetl dochód (przychód z zamówień - cena zakupu towaru) w każdym roku działalności firmy.
use __firma_zti;
SELECT YEAR(z.data_zamowienia) AS rok, SUM((pz.ilosc * pz.cena) - (pz.ilosc * t.cena_zakupu)) AS dochod
FROM zamowienie z
JOIN pozycja_zamowienia pz ON z.id_zamowienia = pz.zamowienie
JOIN towar t ON pz.towar = t.id_towaru
GROUP BY rok
ORDER BY rok;
```
## **Zadanie 8**
```sql
-- Wyświetl wartość aktualnego stanu magazynowego z podziałem na kategorię produktów.
use __firma_zti;
SELECT k.nazwa_kategori, SUM(sm.ilosc * t.cena_zakupu) AS wartosc_magazynowa
FROM towar t
JOIN kategoria k ON t.kategoria = k.id_kategori
JOIN stan_magazynowy sm ON t.id_towaru = sm.towar
GROUP BY k.nazwa_kategori;
```
## **Zadanie 9**
```sql
-- Policz ilu pracowników urodziło się w danym miesiącu, posortowane chronologicznie.
use __firma_zti;
SELECT MONTHNAME(data_urodzenia) AS miesiac, 
       MONTH(data_urodzenia) AS numer_miesiaca, 
       COUNT(*) AS liczba_pracownikow
FROM pracownik
GROUP BY miesiac, numer_miesiaca
ORDER BY numer_miesiaca;
```
## **Zadanie 10**
```sql

-- Wyświetl imię i nazwisko pracownika i koszt jaki poniósł pracodawca od momentu jego zatrudnienia.
use __firma_zti;
SELECT p.imie, p.nazwisko, SUM(DATEDIFF(CURDATE(), p.data_zatrudnienia) * (p.pensja / 30)) AS koszt_pracownika
FROM pracownik p
GROUP BY p.imie, p.nazwisko;

```

