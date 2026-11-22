1. Wymagania funkcjonalne (RDZEŃ SYSTEMU)
   Katalog i stany magazynowe
   Baza gier: Klienci muszą mieć możliwość wygodnego przeszukiwania katalogu z filtrami po platformie (PS5, Xbox, Nintendo Switch itp.), gatunku, kategorii wiekowej, cenie oraz aktualnej dostępności.
   Baza konsol: Osobna sekcja z dostępnymi urządzeniami, zawierająca dokładną specyfikację zestawu (model, pojemność dysku, liczba padów, okablowanie) oraz informację o stanie technicznym.
   Limit wypożyczeń (leasing do 3 gier): Jeden użytkownik może mieć aktywnie wypożyczone maksymalnie 3 gry jednocześnie (niezależnie od tego, czy wypożycza samą grę, czy zestaw z konsolą). System musi automatycznie blokować dodanie czwartego tytułu do aktywnego limitu, dopóki któryś z poprzednich nie wróci na magazyn.
   Konta użytkowników i bezpieczeństwo tożsamości
   Weryfikacja 1 osoba = 1 konto: Rejestracja wymaga weryfikacji tożsamości na podstawie karty płatniczej (np. preautoryzacja 1 zł / tokenizacja danych właściciela karty u operatora płatności). System musi blokować zakładanie wielu kont na tę samą kartę lub te same dane, żeby uniknąć obchodzenia limitów i blokad.
   Panel klienta: Użytkownik musi mieć wgląd w historię wypożyczeń, aktualnie posiadany sprzęt, daty zwrotu, status przesyłek, opłacone faktury/paragony oraz możliwość samodzielnego przedłużania wypożyczeń.
   Proces wypożyczenia, czas i kolejkowanie
   Warianty czasowe: Sztywny wybór okresu wypożyczenia przy składaniu zamówienia: 7 dni, 14 dni lub 1 miesiąc (30 dni).
   Przedłużanie wypożyczenia: Klient może przedłużyć najem z poziomu swojego panelu o kolejny dostępny okres, o ile na dany egzemplarz nie czeka już kolejna osoba w kolejce.
   Kolejkowanie zamówień: Jeśli gra lub konsola są niedostępne, klient może zapisać się do kolejki. Gdy egzemplarz wróci i przejdzie kontrolę stanu, system automatycznie rezerwuje go dla pierwszej osoby z listy i wysyła powiadomienie z ograniczonym czasem (np. 24h) na opłacenie zamówienia.
   Finanse, cennik i kaucje
   Serwis transakcji: Integracja z bramką płatniczą (BLIK, karty płatnicze, szybkie przelewy) obsługującą płatności cykliczne/przedłużenia, automatyczne zwroty oraz generowanie dokumentów sprzedaży.
   System kaucji: Automatyczne pobieranie lub blokowanie (preautoryzacja na karcie) kaucji zwrotnej – szczególnie przy wypożyczaniu konsol i drogich premier. Kaucja zwalnia się automatycznie po zatwierdzeniu zwrotu przez magazyn lub jest częściowo/całkowicie potrącana w przypadku uszkodzeń.
   Ceny dynamiczne (zależne od zapotrzebowania): Moduł wyceny, który dostosowuje stawkę wypożyczenia na podstawie aktualnego popytu, liczby osób w kolejce oraz statusu tytułu (np. wyższa stawka za nowości w pierwszym miesiącu od premiery, niższa dla starszych gier o małym obłożeniu).
   Odsetki i opłaty karne: Automatyczne naliczanie odsetek za każdy dzień opóźnienia w zwrocie sprzętu oraz opłat za nieterminowe odebranie paczki z paczkomatu (zwłaszcza jeśli przesyłka wróci do nadawcy i zablokuje towar w obiegu).
   Logistyka, serwis zewnętrzny i wsparcie
   Integracja z kurierami (Paczkomaty): Automatyczne generowanie etykiet nadawczych oraz zwrotnych (kod zwrotu w panelu klienta), wybór paczkomatu na mapie przy zamówieniu i bieżące pobieranie statusów przesyłek po API.
   Współpraca z zewnętrznym serwisem konsol: Moduł zgłoszeń serwisowych – gdy konsola wróci uszkodzona, pracownik oznacza ją do naprawy, a system generuje zlecenie dla zewnętrznego serwisu (z numerem seryjnym, opisem usterki i historią poprzednich napraw) i wyłącza sprzęt z oferty do czasu powrotu.
   Obsługa klienta: Wbudowany system zgłoszeń (ticketów) powiązany bezpośrednio z konkretnym zamówieniem i kontem klienta, ułatwiający rozpatrywanie reklamacji, problemów z dostawą czy usterek sprzętu.
2. Statusy w bazie danych
   System musi ściśle kontrolować cykl życia każdego egzemplarza sprzętu (osobny status dla zamówienia i osobny dla fizycznego egzemplarza).

   | Status egzemplarza | Znaczenie | Akcja systemu |
   | --- | --- | --- |
   | Dostępny | Sprzęt na magazynie, sprawny, gotowy do wysyłki | Widoczny w katalogu jako możliwy do wypożyczenia |
   | Zarezerwowany | Przypisany do opłaconego zamówienia lub osoby z kolejki | Zablokowany dla innych użytkowników |
   | Wysłany | Nadany w paczkomacie, w drodze do klienta | Śledzenie statusu kuriera; po odbiorze startuje licznik dni |
   | Wypożyczony | W rękach klienta | Aktywne odliczanie terminu zwrotu |
   | Opóźniony | Przekroczony termin zwrotu lub nieodebrana paczka | Automatyczne naliczanie odsetek i monity do klienta |
   | W drodze powrotnej | Klient nadał przesyłkę zwrotną | Oczekiwanie na przyjęcie na magazyn |
   | Weryfikacja / Czyszczenie | Sprzęt dotarł na magazyn, trwa sprawdzenie stanu i płyt | Blokada sprzedaży do czasu akceptacji i zwolnienia kaucji |
   | W serwisie | Przekazany do zewnętrznego serwisu naprawczego | Wyłączony z oferty, otwarty bilet serwisowy |
   | Wycofany | Sprzęt trwale uszkodzony lub sprzedany | Zarchiwizowany w bazie |

3. Wymagania pozafunkcjonalne
   Bezpieczeństwo danych i płatności: Pełna zgodność z RODO; dane kart płatniczych nie mogą być przechowywane w naszej bazie (wyłącznie tokeny z certyfikowanego operatora zgodnego z PCI-DSS).
   Skalowalność w dniach premier: System i mechanizm kolejkowania muszą wytrzymać nagłe skoki ruchu w momencie premiery głośnych gier bez dublowania rezerwacji tego samego egzemplarza.
   Otwartość na integracje (API): Łatwe podpięcie dodatkowych operatorów paczkomatowych lub drugiego serwisu naprawczego w przyszłości.
4. Dodatkowe rozwiązania, które powinny znaleźć się w systemie
   Aby ograniczyć straty, przyspieszyć pracę magazynu i zwiększyć zyski, rekomenduję wdrożenie następujących modułów:
   Ewidencja po numerach seryjnych i kodach QR/kreskowych: Każda konsola, pad i pudełko z grą powinny mieć własny unikalny kod. Skaner przy pakowaniu i przyjmowaniu zwrotu wyeliminuje pomyłki i pozwoli dokładnie śledzić historię każdego egzemplarza (kto go wcześniej miał, ile razy był w serwisie).
   Dokumentacja zdjęciowa (protokół wydania i zwrotu): Możliwość szybkiego dodania zdjęć sprzętu przed zapakowaniem oraz po rozpakowaniu zwrotu (np. przez aplikację magazynową na telefonie). To najskuteczniejsze zabezpieczenie w razie sporów o potrącenie kaucji za uszkodzenia mechaniczne.
   Automatyczne powiadomienia (SMS / e-mail / push): Przypomnienia o oczekującej paczce w paczkomacie (ograniczy koszty nieodebranych przesyłek), alerty na 48h i 24h przed końcem okresu wypożyczenia oraz szybkie powiadomienia dla osób z kolejki.
   System scoringowy klienta (poziom zaufania): Klienci, którzy regularnie i terminowo zwracają sprzęt w idealnym stanie, mogą automatycznie zyskiwać przywileje (np. niższą kaucję lub priorytet w kolejce). Klienci z historią opóźnień lub zniszczeń trafiają na listę podwyższonego ryzyka (wyższa kaucja lub blokada konta).
   Zestawy i akcesoria dodatkowe (cross-selling): Możliwość dobrania przy wypożyczeniu konsoli dodatkowego kontrolera, stacji ładującej czy pakietu startowego (np. konsola + 3 gry w promocyjnej cenie).
   Panel analityczny dla właściciela: Raporty pokazujące zwrot z inwestycji (ROI) dla konkretnych gier i konsol, listę tytułów z najdłuższymi kolejkami (sygnał, czego dokupić więcej) oraz wskaźnik awaryjności sprzętu.

Zaktualizowane reguły rozliczania nieodebranej paczki

| Okres wypożyczenia | Akcja systemu po nieodebraniu | Rozliczenie finansowe |
| --- | --- | --- |
| 7 dni | Automatyczne anulowanie zamówienia | System zatrzymuje 100% opłaty za wypożyczenie. Kaucja (jeśli była pobrana) wraca do klienta w całości. |
| 14 dni / 1 miesiąc | Automatyczne anulowanie zamówienia | System potrąca kwotę odpowiadającą stawce za 7 dni (koszt blokady towaru i logistyki), a nadwyżkę oraz ewentualną kaucję zwraca automatycznie na konto/kartę klienta. |

Uproszczony przepływ w systemie
Wykrycie zwrotu po API kuriera: Przesyłka otrzymuje status Nieodebrana / Zwrot do nadawcy, a zamówienie przechodzi w stan Nieodebrana – w drodze powrotnej.
Powiadomienie klienta: System automatycznie wysyła informację (e-mail/SMS), że paczka nie została odebrana w terminie, zamówienie zostaje anulowane, a rozliczenie nastąpi po powrocie sprzętu na magazyn.
Przyjęcie na magazyn i weryfikacja: Pracownik skanuje kod zwrotu i potwierdza nienaruszony stan paczki.
Automatyczne rozliczenie i zwrot do puli:
Bramka płatnicza automatycznie wykonuje częściowy zwrot (dla wariantów 14/30 dni) i zwalnia blokadę kaucji.
Status zamówienia zmienia się na Anulowane (nieodebrane).
Egzemplarz wraca do statusu Dostępny lub automatycznie wpada jako rezerwacja dla pierwszej osoby w kolejce.
Jeśli klient nadal chce zagrać w dany tytuł, po prostu składa nowe zamówienie na standardowych zasadach.
