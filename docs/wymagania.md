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
