# Wymagania systemu wypożyczalni gier i konsol

## 1. Wymagania funkcjonalne (RDZEŃ SYSTEMU)

### Katalog i stany magazynowe

- **Baza gier:** Klienci muszą mieć możliwość wygodnego przeszukiwania katalogu z filtrami po platformie (PS5, Xbox, Nintendo Switch itp.), gatunku, kategorii wiekowej, cenie oraz aktualnej dostępności.
- **Baza konsol:** Osobna sekcja z dostępnymi urządzeniami, zawierająca dokładną specyfikację zestawu (model, pojemność dysku, liczba padów, okablowanie) oraz informację o stanie technicznym.
- **Limit wypożyczeń (leasing do 3 gier):** Jeden użytkownik może mieć aktywnie wypożyczone maksymalnie 3 gry jednocześnie (niezależnie od tego, czy wypożycza samą grę, czy zestaw z konsolą). System musi automatycznie blokować dodanie czwartego tytułu do aktywnego limitu, dopóki któryś z poprzednich nie wróci na magazyn.

### Konta użytkowników i bezpieczeństwo tożsamości

- **Weryfikacja 1 osoba = 1 konto:** Rejestracja wymaga weryfikacji tożsamości na podstawie karty płatniczej (np. preautoryzacja 1 zł / tokenizacja danych właściciela karty u operatora płatności). System musi blokować zakładanie wielu kont na tę samą kartę lub te same dane, żeby uniknąć obchodzenia limitów i blokad.
- **Panel klienta:** Użytkownik musi mieć wgląd w historię wypożyczeń, aktualnie posiadany sprzęt, daty zwrotu, status przesyłek, opłacone faktury/paragony oraz możliwość samodzielnego przedłużania wypożyczeń.

### Proces wypożyczenia, czas i kolejkowanie

- **Warianty czasowe:** Sztywny wybór okresu wypożyczenia przy składaniu zamówienia: 7 dni, 14 dni lub 1 miesiąc (30 dni).
- **Przedłużanie wypożyczenia:** Klient może przedłużyć najem z poziomu swojego panelu o kolejny dostępny okres, o ile na dany egzemplarz nie czeka już kolejna osoba w kolejce.
- **Kolejkowanie zamówień:** Jeśli gra lub konsola są niedostępne, klient może zapisać się do kolejki. Gdy egzemplarz wróci i przejdzie kontrolę stanu, system automatycznie rezerwuje go dla pierwszej osoby z listy i wysyła powiadomienie z ograniczonym czasem (np. 24h) na opłacenie zamówienia.

### Finanse, cennik i kaucje

- **Serwis transakcji:** Integracja z bramką płatniczą (BLIK, karty płatnicze, szybkie przelewy) obsługującą płatności cykliczne/przedłużenia, automatyczne zwroty oraz generowanie dokumentów sprzedaży.
- **System kaucji:** Automatyczne pobieranie lub blokowanie (preautoryzacja na karcie) kaucji zwrotnej – szczególnie przy wypożyczaniu konsol i drogich premier. Kaucja zwalnia się automatycznie po zatwierdzeniu zwrotu przez magazyn lub jest częściowo/całkowicie potrącana w przypadku uszkodzeń.
- **Ceny dynamiczne (zależne od zapotrzebowania):** Moduł wyceny, który dostosowuje stawkę wypożyczenia na podstawie aktualnego popytu, liczby osób w kolejce oraz statusu tytułu (np. wyższa stawka za nowości w pierwszym miesiącu od premiery, niższa dla starszych gier o małym obłożeniu).
- **Odsetki i opłaty karne:** Automatyczne naliczanie odsetek za każdy dzień opóźnienia w zwrocie sprzętu oraz opłat za nieterminowe odebranie paczki z paczkomatu (zwłaszcza jeśli przesyłka wróci do nadawcy i zablokuje towar w obiegu).

### Logistyka, serwis zewnętrzny i wsparcie

- **Integracja z kurierami (Paczkomaty):** Automatyczne generowanie etykiet nadawczych oraz zwrotnych (kod zwrotu w panelu klienta), wybór paczkomatu na mapie przy zamówieniu i bieżące pobieranie statusów przesyłek po API.
- **Współpraca z zewnętrznym serwisem konsol:** Moduł zgłoszeń serwisowych – gdy konsola wróci uszkodzona, pracownik oznacza ją do naprawy, a system generuje zlecenie dla zewnętrznego serwisu (z numerem seryjnym, opisem usterki i historią poprzednich napraw) i wyłącza sprzęt z oferty do czasu powrotu.
- **Obsługa klienta:** Wbudowany system zgłoszeń (ticketów) powiązany bezpośrednio z konkretnym zamówieniem i kontem klienta, ułatwiający rozpatrywanie reklamacji, problemów z dostawą czy usterek sprzętu.

## 2. Statusy w bazie danych

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

## 3. Wymagania pozafunkcjonalne

- **Bezpieczeństwo danych i płatności:** Pełna zgodność z RODO; dane kart płatniczych nie mogą być przechowywane w naszej bazie (wyłącznie tokeny z certyfikowanego operatora zgodnego z PCI-DSS).
- **Skalowalność w dniach premier:** System i mechanizm kolejkowania muszą wytrzymać nagłe skoki ruchu w momencie premiery głośnych gier bez dublowania rezerwacji tego samego egzemplarza.
- **Otwartość na integracje (API):** Łatwe podpięcie dodatkowych operatorów paczkomatowych lub drugiego serwisu naprawczego w przyszłości.

## 4. Dodatkowe rozwiązania, które powinny znaleźć się w systemie

Aby ograniczyć straty, przyspieszyć pracę magazynu i zwiększyć zyski, rekomenduję wdrożenie następujących modułów:

- **Ewidencja po numerach seryjnych i kodach QR/kreskowych:** Każda konsola, pad i pudełko z grą powinny mieć własny unikalny kod. Skaner przy pakowaniu i przyjmowaniu zwrotu wyeliminuje pomyłki i pozwoli dokładnie śledzić historię każdego egzemplarza (kto go wcześniej miał, ile razy był w serwisie).
- **Dokumentacja zdjęciowa (protokół wydania i zwrotu):** Możliwość szybkiego dodania zdjęć sprzętu przed zapakowaniem oraz po rozpakowaniu zwrotu (np. przez aplikację magazynową na telefonie). To najskuteczniejsze zabezpieczenie w razie sporów o potrącenie kaucji za uszkodzenia mechaniczne.
- **Automatyczne powiadomienia (SMS / e-mail / push):** Przypomnienia o oczekującej paczce w paczkomacie (ograniczy koszty nieodebranych przesyłek), alerty na 48h i 24h przed końcem okresu wypożyczenia oraz szybkie powiadomienia dla osób z kolejki.
- **System scoringowy klienta (poziom zaufania):** Klienci, którzy regularnie i terminowo zwracają sprzęt w idealnym stanie, mogą automatycznie zyskiwać przywileje (np. niższą kaucję lub priorytet w kolejce). Klienci z historią opóźnień lub zniszczeń trafiają na listę podwyższonego ryzyka (wyższa kaucja lub blokada konta).
- **Zestawy i akcesoria dodatkowe (cross-selling):** Możliwość dobrania przy wypożyczeniu konsoli dodatkowego kontrolera, stacji ładującej czy pakietu startowego (np. konsola + 3 gry w promocyjnej cenie).
- **Panel analityczny dla właściciela:** Raporty pokazujące zwrot z inwestycji (ROI) dla konkretnych gier i konsol, listę tytułów z najdłuższymi kolejkami (sygnał, czego dokupić więcej) oraz wskaźnik awaryjności sprzętu.

## 5. Zaktualizowane reguły rozliczania nieodebranej paczki

| Okres wypożyczenia | Akcja systemu po nieodebraniu | Rozliczenie finansowe |
| --- | --- | --- |
| 7 dni | Automatyczne anulowanie zamówienia | System zatrzymuje 100% opłaty za wypożyczenie. Kaucja (jeśli była pobrana) wraca do klienta w całości. |
| 14 dni / 1 miesiąc | Automatyczne anulowanie zamówienia | System potrąca kwotę odpowiadającą stawce za 7 dni (koszt blokady towaru i logistyki), a nadwyżkę oraz ewentualną kaucję zwraca automatycznie na konto/kartę klienta. |

### Uproszczony przepływ w systemie

1. **Wykrycie zwrotu po API kuriera:** Przesyłka otrzymuje status Nieodebrana / Zwrot do nadawcy, a zamówienie przechodzi w stan Nieodebrana – w drodze powrotnej.
2. **Powiadomienie klienta:** System automatycznie wysyła informację (e-mail/SMS), że paczka nie została odebrana w terminie, zamówienie zostaje anulowane, a rozliczenie nastąpi po powrocie sprzętu na magazyn.
3. **Przyjęcie na magazyn i weryfikacja:** Pracownik skanuje kod zwrotu i potwierdza nienaruszony stan paczki.
4. **Automatyczne rozliczenie i zwrot do puli:**
   - Bramka płatnicza automatycznie wykonuje częściowy zwrot (dla wariantów 14/30 dni) i zwalnia blokadę kaucji.
   - Status zamówienia zmienia się na Anulowane (nieodebrane).
   - Egzemplarz wraca do statusu Dostępny lub automatycznie wpada jako rezerwacja dla pierwszej osoby w kolejce.
   - Jeśli klient nadal chce zagrać w dany tytuł, po prostu składa nowe zamówienie na standardowych zasadach.

### AI Łowca Luk


1. **Gra ma kilka egzemplarzy o różnych statusach** — może być niejasne, czy tytuł jest dostępny, gdy tylko część jego egzemplarzy czeka na wypożyczenie.
2. **Egzemplarz jest zarezerwowany, ale jeszcze nie został wysłany** — może zostać błędnie uznany za dostępny, jeśli rezerwacja nie jest uwzględniana w filtrze.
3. **Zwrócona gra ma status „weryfikacja / czyszczenie”** — może pojawić się w wynikach jako dostępna, mimo że nie jest jeszcze gotowa do wypożyczenia.
4. **Gra jest opóźniona lub w drodze powrotnej** — status może zostać pomylony z dostępnością, choć egzemplarz nadal nie znajduje się w magazynie.
5. **Dostępność zmienia się w trakcie wyszukiwania** — egzemplarz może zostać wypożyczony lub zarezerwowany między pobraniem wyników a ich wyświetleniem.
6. **Katalog zawiera tę samą grę w kilku wydaniach** — edycje, wersje regionalne lub różne nośniki mogą zostać potraktowane jako jeden tytuł albo jako osobne pozycje.
7. **Gra ma przypisanych kilka gatunków** — nie jest określone, czy filtr ma zwracać pozycje pasujące do każdego wybranego gatunku, czy do dowolnego z nich.
8. **Klient wybiera kilka platform jednocześnie** — kryteria nie określają, czy wyniki mają pasować do wszystkich platform, czy do przynajmniej jednej.
9. **Dane platformy mają różne nazwy lub formaty** — niespójne zapisy w katalogu mogą sprawić, że część gier nie zostanie dopasowana do filtra.
10. **Gra nie ma przypisanego gatunku albo platformy** — brak metadanych może wykluczyć ją z wyników, mimo że pozostałe kryteria pasują.
11. **Klient zatwierdza wyszukiwanie bez ustawienia filtrów** — nie wiadomo, czy ma wtedy zobaczyć cały katalog, czy komunikat o braku pozycji.
12. **Filtry wzajemnie się wykluczają** — na przykład wybrana platforma i gatunek mogą nie mieć żadnego wspólnego tytułu.
13. **Wyniki są podzielone na strony** — liczba pozycji pasujących do filtrów może być większa niż liczba pokazana na pierwszej stronie.
14. **Wyniki nie ładują się z powodu błędu katalogu** — awaria lub przekroczenie czasu odpowiedzi może zostać pomylone z sytuacją, w której żadna gra nie spełnia filtrów.
15. **Brak wyników, ale kolejka jest niedostępna dla danego tytułu** — sugestia zapisu może być nieaktualna lub nie mieć zastosowania, gdy kolejka nie istnieje albo klient już do niej należy.

## Ćwiczenie „Łowca luk” — R-04 Kolejkowanie niedostępnego egzemplarza (Must)

Wybrana historyjka: **R-04** (jedna historyjka z trzech Must, na ćwiczenie).
Prompt: „Łowca luk” z pasta z zajęć. Wynik: 15 przypadków brzegowych.

| # | Sytuacja | Decyzja | Uzasadnienie |
| --- | --- | --- | --- |
| 1 | Klient jest już w kolejce na ten tytuł i zapisuje się ponownie | Kryterium | Oczywista luka, system nie blokuje duplikatu |
| 2 | Klient wypożyczył już tę grę i zapisuje się do kolejki na nią | Odrzucone | pokryte |
| 3 | Egzemplarz wraca, ale weryfikacja daje wynik „uszkodzony” | Kryterium | Kolejka nie może dostać uszkodzonego egzemplarza |
| 4 | Egzemplarz wraca, a kolejka jest pusta | Odrzucone | pokryte |
| 5 | Dwa egzemplarze tego samego tytułu wracają jednocześnie | Nowa historyjka | Wiele egzemplarzy w kolejce to osobny przepływ |
| 6 | Pierwsza osoba w kolejce ma już 3 aktywne wypożyczenia | Kryterium | Interakcja z limitem R-02 |
| 7 | Pierwsza osoba usuwa lub traci konto podczas oczekiwania | Kryterium | Martwy wpis blokowałby egzemplarz |
| 8 | Klient anuluje zapis w tej samej chwili, gdy egzemplarz jest rezerwowany | Odrzucone | pokryte |
| 9 | Klient płaci dokładnie po upływie 24h | Kryterium | Granica terminu musi być jednoznaczna |
| 10 | Wysyłka powiadomienia się nie udaje, a 24h już biegnie | Nowa historyjka | Obsługa awarii powiadomień to osobny temat |
| 11 | Operator płatności jest niedostępny przy opłacie rezerwacji | Odrzucone | zależność |
| 12 | Egzemplarz w kolejce zostaje wycofany | Kryterium | Status „Wycofany” zamyka kolejkę |
| 13 | Wybrany okres (7/14/30 dni) w zamówieniu z kolejki | Odrzucone | niezwiązane |
| 14 | Zarezerwowany egzemplarz trafia do serwisu | Odrzucone | pokryte |
| 15 | Setki zapisów w dniu premiery | Odrzucone | pokryte |

Efekt:
- **Dopisano do R-04 w `historyjki.md`** (kryteria 1, 3, 6, 7, 9, 12).
- **Propozycje nowych historyjek** (do decyzji zespołu, nie dodane do pliku): wielokrotne egzemplarze w kolejce (poz. 5), awaria powiadomień o rezerwacji (poz. 10).
- **Odrzucone** z jednym słowem uzasadnienia: poz. 2, 4, 8, 11, 13, 14, 15.

Uwaga do ćwiczenia: sam prompt wygenerował przypadki, ale ich wybór i uzasadnienia
są do weryfikacji przez zespół. Część „pokryte” opiera się na wymaganiach
niefunkcjonalnych (np. brak dublowania rezerwacji).

---

## Ćwiczenie „Łowca luk” — R-02 Limit 3 aktywnych wypożyczeń (Must)

Druga historyjka z trzech Must (ta sama procedura co R-04).

| # | Sytuacja | Decyzja | Uzasadnienie |
| --- | --- | --- | --- |
| 1 | Zestaw konsola + gry: ile pozycji liczy się do limitu | Kryterium | Wymagania mówią „niezależnie od zestawu”, ale nie liczbę |
| 2 | Koszyk z 3 grami przy 1 aktywnej: limit sprawdzany dla całego zamówienia | Kryterium | Brak zasady dla wielu pozycji naraz |
| 3 | Dwie karty/okna zatwierdzają zamówienia równolegle | Odrzucone | duplikat |
| 4 | Zamówienie w statusie „Wysłany” przed odbiorem | Kryterium | „Aktywne” nie jest zdefiniowane |
| 5 | Zamówienie anulowane po nieodebraniu | Kryterium | Anulowana pozycja nie może blokować limitu |
| 6 | Egzemplarz w „Weryfikacja / Czyszczenie” po zwrocie | Kryterium | Status przejściowy, nie wiadomo, czy liczy się do limitu |
| 7 | Rezerwacja z kolejki (R-04) przy 2 aktywnych | Kryterium | Interakcja z R-04 |
| 8 | Przedłużenie wypożyczenia (R-06) zwiększa licznik | Odrzucone | pokryte |
| 9 | Egzemplarz wycofany w trakcie wypożyczenia | Kryterium | Zwolnienie slotu |
| 10 | Administrator obchodzi limit na koncie | Odrzucone | zakres |
| 11 | Pracownik ręcznie wypożycza ponad limit | Nowa historyjka | Wyjątki dla personelu to osobny przypadek użycia |
| 12 | Admin zmienia wartość limitu w trakcie działania | Odrzucone | zakres |
| 13 | Dwa konta na jedną kartę obchodzą limit | Odrzucone | pokryte |
| 14 | Błąd bazy przy sprawdzaniu limitu | Kryterium | Zasada „brak wpuszczenia przy błędzie” |
| 15 | Ten sam klient w dwóch oknach, jedna gra w koszyku | Odrzucone | duplikat |

Efekt:
- **Dopisano do R-02 w `historyjki.md`** (kryteria 1, 2, 4, 5, 6, 7, 9, 14).
- **Propozycja nowej historyjki:** wyjątki od limitu dla pracownika (poz. 11).
- **Odrzucone:** poz. 3, 8, 10, 12, 13, 15.
1. **Wykrycie zwrotu po API kuriera:** Przesyłka otrzymuje status Nieodebrana / Zwrot do nadawcy, a zamówienie przechodzi w stan Nieodebrana – w drodze powrotnej.
2. **Powiadomienie klienta:** System automatycznie wysyła informację (e-mail/SMS), że paczka nie została odebrana w terminie, zamówienie zostaje anulowane, a rozliczenie nastąpi po powrocie sprzętu na magazyn.
3. **Przyjęcie na magazyn i weryfikacja:** Pracownik skanuje kod zwrotu i potwierdza nienaruszony stan paczki.
4. **Automatyczne rozliczenie i zwrot do puli:**
   - Bramka płatnicza automatycznie wykonuje częściowy zwrot (dla wariantów 14/30 dni) i zwalnia blokadę kaucji.
   - Status zamówienia zmienia się na Anulowane (nieodebrane).
   - Egzemplarz wraca do statusu Dostępny lub automatycznie wpada jako rezerwacja dla pierwszej osoby w kolejce.
   - Jeśli klient nadal chce zagrać w dany tytuł, po prostu składa nowe zamówienie na standardowych zasadach.

### AI Łowca Luk


1. **Gra ma kilka egzemplarzy o różnych statusach** — może być niejasne, czy tytuł jest dostępny, gdy tylko część jego egzemplarzy czeka na wypożyczenie.
2. **Egzemplarz jest zarezerwowany, ale jeszcze nie został wysłany** — może zostać błędnie uznany za dostępny, jeśli rezerwacja nie jest uwzględniana w filtrze.
3. **Zwrócona gra ma status „weryfikacja / czyszczenie”** — może pojawić się w wynikach jako dostępna, mimo że nie jest jeszcze gotowa do wypożyczenia.
4. **Gra jest opóźniona lub w drodze powrotnej** — status może zostać pomylony z dostępnością, choć egzemplarz nadal nie znajduje się w magazynie.
5. **Dostępność zmienia się w trakcie wyszukiwania** — egzemplarz może zostać wypożyczony lub zarezerwowany między pobraniem wyników a ich wyświetleniem.
6. **Katalog zawiera tę samą grę w kilku wydaniach** — edycje, wersje regionalne lub różne nośniki mogą zostać potraktowane jako jeden tytuł albo jako osobne pozycje.
7. **Gra ma przypisanych kilka gatunków** — nie jest określone, czy filtr ma zwracać pozycje pasujące do każdego wybranego gatunku, czy do dowolnego z nich.
8. **Klient wybiera kilka platform jednocześnie** — kryteria nie określają, czy wyniki mają pasować do wszystkich platform, czy do przynajmniej jednej.
9. **Dane platformy mają różne nazwy lub formaty** — niespójne zapisy w katalogu mogą sprawić, że część gier nie zostanie dopasowana do filtra.
10. **Gra nie ma przypisanego gatunku albo platformy** — brak metadanych może wykluczyć ją z wyników, mimo że pozostałe kryteria pasują.
11. **Klient zatwierdza wyszukiwanie bez ustawienia filtrów** — nie wiadomo, czy ma wtedy zobaczyć cały katalog, czy komunikat o braku pozycji.
12. **Filtry wzajemnie się wykluczają** — na przykład wybrana platforma i gatunek mogą nie mieć żadnego wspólnego tytułu.
13. **Wyniki są podzielone na strony** — liczba pozycji pasujących do filtrów może być większa niż liczba pokazana na pierwszej stronie.
14. **Wyniki nie ładują się z powodu błędu katalogu** — awaria lub przekroczenie czasu odpowiedzi może zostać pomylone z sytuacją, w której żadna gra nie spełnia filtrów.
15. **Brak wyników, ale kolejka jest niedostępna dla danego tytułu** — sugestia zapisu może być nieaktualna lub nie mieć zastosowania, gdy kolejka nie istnieje albo klient już do niej należy.

## Ćwiczenie „Łowca luk” — R-03 Jedno konto na osobę (Must)

Trzecia historyjka z trzech Must.

1. **Jednorazowe karty wirtualne (np. Revolut, Wise)**: Użytkownik używa karty jednorazowej o unikalnym numerze PAN, która po weryfikacji ulega zniszczeniu, co pozwala na wielokrotną rejestrację tej samej osoby.
2. **Wiele kart do jednego rachunku bankowego**: Ta sama osoba posiada kilka fizycznych lub wirtualnych kart (np. debetową i kredytową) przypisanych do tego samego konta i danych osobowych, ale o różnych numerach.
3. **Odnowienie lub zastrzeżenie karty (nowy numer PAN)**: Użytkownik zastrzega zgubioną kartę i otrzymuje z banku nową o innym numerze, dzięki czemu może zarejestrować drugie konto mimo posiadania już pierwszego.
4. **Karty współdzielone (np. konto małżeńskie lub firmowe)**: Dwie różne osoby posługują się kartami o tym samym numerze (lub jedną kartą), co blokuje drugiej osobie możliwość założenia własnego, legalnego konta.
5. **Błąd weryfikacji z powodu braku środków lub limitów na karcie**: Preautoryzacja (np. na kwotę 0 lub 1 PLN) zostaje odrzucona przez bank z powodu zablokowanych płatności internetowych lub braku środków na karcie.
6. **Przekroczenie czasu oczekiwania (timeout) po stronie operatora płatności**: Operator płatności autoryzuje kartę z opóźnieniem po tym, jak system uznał połączenie za zerwane i przerwał proces rejestracji.
7. **Brak autoryzacji 3D Secure przez użytkownika**: Użytkownik zamyka okno banku lub nie potwierdza transakcji w aplikacji mobilnej w wyznaczonym czasie, pozostawiając proces rejestracji w stanie zawieszenia.
8. **Równoczesna rejestracja z dwóch sesji (race condition)**: Użytkownik wysyła dwa formularze rejestracyjne z tą samą kartą w ułamku sekundy, zanim pierwszy wpis o karcie zostanie zapisany w bazie jako zajęty.
9. **Użycie karty przedpłaconej (prepaid) lub podarunkowej**: Użytkownik rejestruje się przy użyciu anonimowej karty przedpłaconej, która nie jest powiązana z żadną konkretną tożsamością.
10. **Wygasła karta podpięta do istniejącego konta**: Karta na pierwszym koncie straciła ważność, a użytkownik próbuje zarejestrować nowe konto z nową kartą wydaną w miejsce wygasłej.
11. **Odrzucenie karty przez operatora ze względów bezpieczeństwa (anti-fraud)**: Operator płatności blokuje weryfikację z powodu podejrzenia nadużycia (np. nietypowa lokalizacja IP), mimo że karta jest ważna i unikalna.
12. **Użycie portfeli cyfrowych (Apple Pay / Google Pay)**: Tokenizacja urządzenia generuje wirtualny numer karty (DPAN) różny od numeru fizycznej karty (FPAN), umożliwiając zarejestrowanie dwóch kont na tę samą kartę.
13. **Usunięcie konta przez użytkownika i ponowna rejestracja**: Użytkownik korzysta z prawa do bycia zapomnianym (RODO), usuwa konto, a następnie próbuje założyć nowe z tą samą kartą, podczas gdy jej hash nadal figuruje w systemie jako użyty.
14. **Błędne dane karty wpisane przez użytkownika (literówka)**: Użytkownik podaje niepoprawny numer karty, datę ważności lub kod CVV, co powoduje odrzucenie weryfikacji przed sprawdzeniem duplikatu.
15. **Karta wydana w kraju lub walucie nieobsługiwanej przez operatora**: Zagraniczny użytkownik próbuje przejść weryfikację kartą spoza obsługiwanego regionu, co skutkuje odrzuceniem autoryzacji przez bramkę płatniczą.
