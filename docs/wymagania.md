# Wypożyczalnia gier i konsol

## Granica systemu

System obsługuje wypożyczanie gier planszowych/wideo oraz konsol: katalog
dostępnych pozycji, rezerwacje, wypożyczenia i zwroty, naliczanie opłat oraz
powiadomienia dla klientów.

## Aktorzy

### Aktorzy ludzcy

| Aktor | Czego potrzebuje od systemu
| Klient | Przeglądać katalog, rezerwować i wypożyczać gry oraz konsole, widzieć terminy zwrotu i naliczone opłaty.
| Pracownik wypożyczalni | Obsługiwać wypożyczenia i zwroty, potwierdzać rezerwacje, rejestrować stan sprzętu przy oddaniu.  
| Administrator | Zarządzać katalogiem (dodawać/edytować pozycje), kontami użytkowników i cennikiem.  
| Kierownik wypożyczalni | Przeglądać raporty o wypożyczeniach, przychodach i dostępności sprzętu.

### Systemy zewnętrzne

| System zewnętrzny | Po co się łączy  
| System płatności | Pobieranie opłat za wypożyczenie oraz kar za opóźnienia/uszkodzenia.
| Poczta e-mail | Wysyłanie potwierdzeń rezerwacji i przypomnień o terminie zwrotu.

## Wymagania niefunkcjonalne

Każde wymaganie ma konkretną liczbę + warunek oraz sposób sprawdzenia.
Liczby będą podstawą decyzji architektonicznych na Lab 3.

| ID | Obszar | Wymaganie (liczba + warunek) | Jak sprawdzimy |
| --- | --- | --- | --- |
| NF-01 | Wydajność | Potwierdzenie rezerwacji w **< 2 s dla 95% żądań** przy **200 równoczesnych użytkownikach** (scenariusz premiery). | Test obciążeniowy (k6/JMeter) na środowisku stage, raport percentyli p95/p99. |
| NF-02 | Dostępność (uptime) | Dostępność systemu **≥ 99,5% w miesiącu** (maks. ok. 3,6 h przerwy), poza oknem serwisowym ogłaszanym z 48 h wyprzedzeniem. | Monitoring uptime (np. UptimeRobot/Grafana), miesięczny raport SLA z alertami. |
| NF-03 | Bezpieczeństwo / płatności | **0 danych kart (PAN/CVV)** w naszej bazie — wyłącznie tokeny operatora **PCI-DSS**; pełna zgodność z RODO. | Audyt bezpieczeństwa + przegląd schematu bazy; skan kodu na obecność numerów kart; potwierdzenie certyfikatu operatora. |
| NF-04 | Spójność kolejki | **0 przypadków dublowania rezerwacji** tego samego egzemplarza przy 200 równoczesnych zapisach do kolejki w premierę. | Test współbieżności (równoległe zapisy R-04) + weryfikacja unikalności rezerwacji w bazie. |
| NF-05 | Dostępność dla osób z niepełnosprawnościami | Zgodność panelu klienta z **WCAG 2.1 na poziomie AA** dla kluczowych ścieżek (katalog, zamówienie, zwrot). | Audyt WCAG (axe/Lighthouse) + test z czytnikiem ekranu; raport z listą naruszeń = 0 krytycznych. |
| NF-06 | Prywatność (RODO) | Realizacja prawa do bycia zapomnianym / eksportu danych w **≤ 30 dni** od zgłoszenia; logi dostępu do danych osobowych przechowywane **≥ 12 mies.** | Przegląd procedury RODO + test usunięcia konta (z zachowaniem hasha karty wg R-03) i eksportu danych. |
| NF-07 | Koszt | Koszt infrastruktury **≤ 2000 zł/mies.** przy 200 równoczesnych użytkownikach i średnim ruchu; autoskalowanie tylko na czas premier. | Miesięczny przegląd kosztów (billing chmury) + prognoza kosztu skoku premierowego. |

Gotowe, gdy: każde z co najmniej 5 wymagań ma liczbę i sposób sprawdzenia (powyżej 7).

## Wersja 0 wymagań — bez AI

Baza gier - użytkownicy mogą przeszukać dostępne pozycje,
baza konsol - klienci przeglądają dostępne urządzenia do wypożyczenia,
baza użytkowników - Weryfikacja kont na podstawie kart aby było jedno konto na osobę, możliwość zarządzania swoimi zamówieniami
umowy z kurierami - wysyłka gier i urządzeń za pośrednictwem paczkomatów
serwis - współpraca z zewnętrznym serwisem do napraw konsol
gier w leasing x3
serwis transakcji
obsługa klienta
system kuacji
kolejkowanie zamówień
serwis do wypożyczeń
czas wypożyczeń 7 dni 14 dni 1 miesiąc
możliwość przedłużenia wypożyczenia
ceny w zależności od zapotrzebowania
odsetki za nieterminowe odebranie
statusy w bazie
