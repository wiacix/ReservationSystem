# Historyjki użytkownika — Wypożyczalnia gier i konsol

Historyjki wywodzą się z [wymagań systemu](wymagania.md). Każda ma identyfikator,
priorytet MoSCoW i co najmniej jedno kryterium akceptacji w formacie
Zakładając / Gdy / Wtedy. Historyjki **Must** mają dodatkowo kryterium dla
sytuacji błędnej.

Podsumowanie: **14 historyjek**, w tym **5 Must** (R-01, R-02, R-03, R-04, R-05).

---

## R-01 Przeszukiwanie katalogu gier (Must)

Jako klient chcę filtrować katalog po platformie, gatunku i dostępności,
aby szybko znaleźć grę możliwą do wypożyczenia teraz.

- **Zakładając**, że w katalogu jest 5 gier na PS5, z czego 2 są dostępne
- **Gdy** klient ustawia filtr „platforma = PS5” i „tylko dostępne”
- **Wtedy** system pokazuje dokładnie te 2 dostępne pozycje

Sytuacja błędna:

- **Zakładając**, że żadna gra nie spełnia filtrów
- **Gdy** klient zatwierdza wyszukiwanie
- **Wtedy** system pokazuje komunikat „Brak pozycji” i proponuje zapis do kolejki

INVEST: niezależna od modułu płatności; testowalna przez porównanie wyników z filtrem.

---

## R-02 Limit 3 aktywnych wypożyczeń (Must)

Jako pracownik wypożyczalni chcę, aby system blokował czwarte aktywne
wypożyczenie klienta, aby pilnować limitu leasingu.

- **Zakładając**, że klient ma 3 aktywne wypożyczenia
- **Gdy** próbuje wypożyczyć czwartą grę
- **Wtedy** system odrzuca operację z informacją o limicie

Sytuacja błędna:

- **Zakładając**, że klient ma 3 aktywne wypożyczenia, a jedno właśnie wróciło na magazyn
- **Gdy** klient ponawia wypożyczenie czwartej gry
- **Wtedy** system akceptuje je, bo liczba aktywnych spadła do 2

INVEST: mała, dotyczy jednej reguły; mierzalna liczbą aktywnych pozycji.

---

## R-03 Jedno konto na osobę (Must)

Jako administrator chcę, aby rejestracja weryfikowała tożsamość przez kartę
płatniczą, aby nie dało się założyć wielu kont na te same dane.

- **Zakładając**, że na kartę X istnieje już konto
- **Gdy** ktoś rejestruje nowe konto z tą samą kartą
- **Wtedy** system odrzuca rejestrację i informuje o istniejącym koncie

Sytuacja błędna:

- **Zakładając**, że operator płatności jest niedostępny
- **Gdy** klient próbuje się zarejestrować
- **Wtedy** system wstrzymuje rejestrację i prosi o ponowienie później (nie zakłada konta bez weryfikacji)

INVEST: wartościowa (ochrona przed obchodzeniem limitów); testowalna duplikatem karty.

---

## R-04 Kolejkowanie niedostępnego egzemplarza (Must)

Jako klient chcę zapisać się do kolejki po niedostępną grę, aby dostać ją,
gdy tylko wróci na magazyn.

- **Zakładając**, że gra jest wypożyczona i ma pustą kolejkę
- **Gdy** klient zapisuje się do kolejki, a egzemplarz wraca i przechodzi weryfikację
- **Wtedy** system rezerwuje go dla tego klienta i wysyła powiadomienie z czasem 24h na opłatę

Sytuacja błędna:

- **Zakładając**, że klient dostał rezerwację z kolejki
- **Gdy** nie opłaci jej w ciągu 24h
- **Wtedy** system zwalnia egzemplarz i przekazuje go następnej osobie w kolejce

Sytuacje brzegowe (z ćwiczenia „Łowca luk”, zatwierdzone do kryteriów):

- **Zakładając**, że klient jest już w kolejce na ten tytuł
- **Gdy** zapisuje się ponownie
- **Wtedy** system odrzuca drugi wpis i informuje, że klient już na liście jest

- **Zakładając**, że egzemplarz wraca, ale weryfikacja kończy się wynikiem „uszkodzony”
- **Gdy** system ma przypisać egzemplarz z kolejki
- **Wtedy** egzemplarz nie trafia do rezerwacji; kolejka czeka na kolejny zwrot, a uszkodzenie obsługuje R-10

- **Zakładając**, że pierwsza osoba w kolejce ma już 3 aktywne wypożyczenia (R-02)
- **Gdy** egzemplarz jest gotowy do rezerwacji
- **Wtedy** system pomija tę osobę i rezerwuje egzemplarz dla następnej, z limitem

- **Zakładając**, że pierwsza osoba w kolejce usunęła lub zablokowała konto
- **Gdy** egzemplarz jest gotowy do rezerwacji
- **Wtedy** jej wpis znika z kolejki, a egzemplarz trafia do następnej osoby

- **Zakładając**, że klient ma zarezerwowany egzemplarz z kolejki
- **Gdy** opłaca go dokładnie po upływie 24h
- **Wtedy** płatność jest odrzucona, a rezerwacja już nie istnieje (zasada z pierwszej sytuacji błędnej)

- **Zakładając**, że egzemplarz w kolejce zostaje wycofany (status „Wycofany”)
- **Gdy** system przetwarza zmianę statusu
- **Wtedy** kolejka na ten egzemplarz zostaje zamknięta, a klienci dostają informację

INVEST: testowalna przez symulację zwrotu i upływu czasu; negocjowalna (czas 24h).

---

## R-05 Naliczanie opłat za opóźniony zwrot (Must)

Jako kierownik wypożyczalni chcę, aby system automatycznie naliczał odsetki
za każdy dzień opóźnienia, aby ograniczyć przetrzymywanie sprzętu.

- **Zakładając**, że termin zwrotu minął 3 dni temu, a dzienna stawka kary to 5 zł
- **Gdy** system przelicza zaległości
- **Wtedy** naliczona kara wynosi 15 zł, a egzemplarz ma status „Opóźniony”

Sytuacja błędna:

- **Zakładając**, że klient nadał przesyłkę zwrotną przed terminem
- **Gdy** system przelicza zaległości
- **Wtedy** kara wynosi 0 zł (brak opóźnienia)

INVEST: mała i mierzalna (dni × stawka); niezależna od modułu logistyki.

---

## R-06 Przedłużenie wypożyczenia (Should)

Jako klient chcę przedłużyć wypożyczenie z panelu, aby zatrzymać grę na kolejny okres,
jeśli nikt na nią nie czeka.

- **Zakładając**, że klient ma aktywne wypożyczenie bez kolejki
- **Gdy** wybiera przedłużenie o 7 dni
- **Wtedy** system wydłuża termin zwrotu i pobiera opłatę za nowy okres

INVEST: negocjowalna (dostępne okresy); testowalna porównaniem daty zwrotu.

---

## R-07 Wybór okresu wypożyczenia (Should)

Jako klient chcę wybrać okres 7, 14 lub 30 dni przy zamówieniu, aby dopasować
najem do swoich planów.

- **Zakładając**, że klient dodaje grę do zamówienia
- **Gdy** wybiera okres „14 dni”
- **Wtedy** system ustala termin zwrotu na 14 dni od odbioru i wylicza cenę dla tego wariantu

INVEST: mała, jeden wybór; testowalna sprawdzeniem terminu i ceny.

---

## R-08 Blokada kaucji na konsolę (Should)

Jako pracownik wypożyczalni chcę, aby przy wypożyczeniu konsoli system
blokował kaucję na karcie, aby zabezpieczyć sprzęt przed uszkodzeniami.

- **Zakładając**, że klient wypożycza konsolę z kaucją 300 zł
- **Gdy** zatwierdza zamówienie
- **Wtedy** operator płatności blokuje 300 zł, a zwolnienie następuje po akceptacji zwrotu na magazynie

INVEST: wartościowa (ochrona majątku); testowalna statusem blokady u operatora.

---

## R-09 Generowanie etykiety paczkomatowej (Should)

Jako klient chcę otrzymać etykietę nadawczą i zwrotną w panelu, aby wygodnie
odebrać i oddać sprzęt przez paczkomat.

- **Zakładając**, że zamówienie zostało opłacone
- **Gdy** klient otwiera szczegóły zamówienia
- **Wtedy** system udostępnia kod odbioru oraz kod zwrotu do paczkomatu

INVEST: niezależna od wyceny; testowalna obecnością kodów po opłaceniu.

---

## R-10 Zgłoszenie serwisowe uszkodzonej konsoli (Could)

Jako pracownik wypożyczalni chcę oznaczyć zwróconą konsolę jako uszkodzoną,
aby system utworzył zlecenie do zewnętrznego serwisu i wyłączył ją z oferty.

- **Zakładając**, że konsola wróciła z usterką
- **Gdy** pracownik oznacza ją do naprawy
- **Wtedy** system tworzy bilet serwisowy z numerem seryjnym i zmienia status na „W serwisie”

INVEST: mała; testowalna sprawdzeniem statusu i utworzonego biletu.

---

## R-11 Ceny zależne od popytu (Could)

Jako kierownik wypożyczalni chcę, aby stawka nowości rosła przy dużym popycie,
aby zwiększyć przychód z najbardziej obleganych tytułów.

- **Zakładając**, że tytuł jest nowością z 10 osobami w kolejce
- **Gdy** klient dodaje go do zamówienia
- **Wtedy** system nalicza stawkę podwyższoną wg reguły popytu

INVEST: negocjowalna (reguła wyceny); testowalna porównaniem stawek przy różnym popycie.

---

## R-12 Powiadomienia o terminie zwrotu (Should)

Jako klient chcę dostawać przypomnienia 48h i 24h przed końcem wypożyczenia,
aby zdążyć oddać sprzęt bez kary.

- **Zakładając**, że do końca okresu zostało 48h
- **Gdy** system uruchamia harmonogram powiadomień
- **Wtedy** klient otrzymuje przypomnienie e-mail/SMS

INVEST: wartościowa, ale odłożona poza bieżący zakres (zależność od bramki SMS).

---

## R-13 Przeglądanie katalogu konsol ze specyfikacją (Should)

Jako klient chcę przeglądać dostępne konsole z pełną specyfikacją zestawu,
aby wybrać urządzenie z potrzebnymi akcesoriami (liczba padów, pojemność dysku).

- **Zakładając**, że w katalogu jest konsola PS5 z 2 padami i dyskiem 1 TB
- **Gdy** klient otwiera jej kartę w sekcji konsol
- **Wtedy** system wyświetla model, pojemność dysku, liczbę padów, okablowanie i stan techniczny

INVEST: niezależna od wypożyczania gier; testowalna porównaniem danych karty ze specyfikacją egzemplarza.

---

## R-14 Wypożyczenie zestawu konsola + gry (Could)

Jako klient chcę wypożyczyć konsolę razem z kilkoma grami w jednym zamówieniu,
aby od razu mieć komplet do grania w promocyjnej cenie.

- **Zakładając**, że klient wybrał konsolę i 3 dostępne gry na tę platformę
- **Gdy** dodaje je jako jeden zestaw do zamówienia
- **Wtedy** system tworzy jedno wypożyczenie zestawu, nalicza cenę pakietu i blokuje kaucję za konsolę

INVEST: wartościowa (cross-selling); testowalna sprawdzeniem ceny pakietu i liczby egzemplarzy w zamówieniu.
