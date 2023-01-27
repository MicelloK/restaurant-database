# Funkcjonalność systemu

## Użytkownicy

Klient bez konta:

* wyświetlanie menu
    
    *klient ma możliwość wyświetlenia aktualnie obowiązującego menu*
* utworzenie konta indywidualnego lub firmowego

    *klient ma możliwość utworzenia konta indywidualnego lub firmowego, podając dla każdego przypadku niezbędne informacje. Dodane zostają wpisy do bazy danych w opowiednich tabelach. Od tego momentu klient otrzymuje funkcjonalności odpowiednio wybranego użytkownika systemu*


Klient indywidualny:

* rezerwacja stolika:

     *klient ma możliowść dodania do bazy danych rezerwacji wybranego (wolnego) stolika bądź stolików z datą początku i końca trwania rezerwacji*
* wyświetlanie menu

    *klient ma możliwość pobrania z bazy danych i wyświetlenia aktualnie obowiązującego menu lub menu zaplanowanego na przyszłość*
* składanie zamówienia

    *klient ma możliwość dodania do bazy danych zamówienia z aktualnie obowiązującego menu. Po złożeniu zamówienia, generowany jest rachunek, a wpis dodawany jest do historii zamówień*
* wyświetlanie historii zamówień

    *klient ma możliwość pobrania i wyświetlenia historii swoich zamówień. Ma też możliwość pobrania rachunku dla dowolnie wybranego zamówienia z listy*
* wyświetlenie listy wolnych stolików

    *klient ma możliwość pobrania i wyświetlenia listy wolnych stolików w danym przedziale czasowym*
    

Klient firmowy:
* rezerwacja stolika (indywidualnie bądź na firmę)

    *klient ma możliwość rezerwacji stolika jako klient indywidualny, lub rezerwacji stolika (lub kilku) na wydarzenie firmowe*
* wyświetlanie menu

    *klient ma możliwość pobrania z bazy danych i wyświetlenia aktualnie obowiązującego menu*
* składanie zamówienia (indywidualnie bądź na firmę)

    *klient ma możliwość dodania do bazy danych zamówienia z aktualnie obowiązującego menu, generowany jest rachunek, a wpis dodawany jest do historii zamówień. Klient ma możliwość złożenia zamówienia na firmę z rozliczeniem pod koniec miesiąca, w którym zostało złożone zamówienie*
* wyświetlanie historii zamówień

    *klient ma możliwość pobrania i wyświetlenia historii zamówień firmy. Ma możliwość pobrania rachunku dla dowolnie wybranego zamówienia z listy. Ma również możliwość pobrania rachunku za większą ilość zamówień*
* wyświetlanie listy rachunków

    *klient ma możliwość pobrania i wyświetlenia z bazy danych listy rachunków*

Pracownik:
* wystawianie rachunków

    *pracownik ma możliwość wyświetlenia i pobrania rachunku na wskazane przez klienta zamówienie*
* wyświetlanie listy wolnych/zajętych stolików

    *pracownik ma możliwość wyświetlenia wolnych w danej chwili stolików. Zarezerwowane stoliki nie wyświetlają się na liście wolnych miejsc podany w konfiguracji okres czasu przed wyznaczoną godziną*
* sprawdzanie historii zamówień

    *pracownik ma możliwość wyświetlenia historii zamówień klienta. Dla danego zamówienia pracownik ma możliwość sprawdzenia rachunku, a także wydrukowanie go na prośbę klienta*
* tworzenie zamówienia (przy kasie)

    *pracownik ma możliwość ręcznego stworzenia zamówienia na konto danego klienta. Jeżeli klient nie ma konta, pracownik ma możliwość utworzenia konta w systemie*

Kierownik zmiany:
* Uprawnienia zwykłego pracownika

    *jak wyżej*
* aktualizowanie menu

    *pracownik może zmienić część, bądź całość aktualnie obowiązującego menu, poprzez dodanie nowego wpisu do bazy danych. pracownik może przedłużyć, lub skrócić czas obowiązywania aktualnego menu. Podczas uaktualniania menu, pracownik musi podać datę początku i datę końca obowiązywania nowego menu*

Właściciel:

pełny dostęp do bazy danych, m.in.:
* aktualizowanie listy produktów

    *właściciel ma możliwość dodawania i usuwania listy produktów z bazy danych*
* edycja listy pracowników

    *właściciel może dodawać i usuwać pracowników z listy. Dodanie i usunięcie pracownika z listy polega na odpowiedniej edycji pola odpowiadającego za datę początku i końca pracy wybranego pracownika*
* zmiana uprawnień użytkownika

    *właściciel może zmienić uprawnienia pracownika poprzez edycję wybranego pola w bazie danych*

## System

Podstawowe funkcjonalności systemu:
* wyświetlanie informacji o zamówieniu (podliczanie kosztów, tworzenie rachunku, itp)

    *system automatycznie zbiera potrzebne dane oraz dokonuje niezbędnych obliczeń do wyświetlenia informacji o kosztach, rachunku, itp. tworzenia zamówienia. Pracownicy i klienci mają możliwość pobrania obliczonych informacji*
* sprawdzanie i aktualizowanie listy wolnych miejsc

    *na prośbę pracownika bądź klienta, system pobiera potrzebne informację z bazy danych i na ich podstawie wyświetla listę wolnych miejsc*
* automatyczne aktualizoanie rabatów

    *po spełnieniu określonych warunków, system automatycznie aktualizuje rabaty na koncie klienta. System na życzenie klienta, może wysłać infromacje na wskazany adres kontaktowy o nowym rabacie*
* automatyczne aktualizowanie dostępności stolików

    *system automatycznie sprawdza i aktualizuje listę wolnych stolików*

## Specyfikacje

* Conajmniej połowa produktów w menu musi być wymieniana co dwa tygodnie. Sprawdzane jest przy dodawaniu nowych produktów do Menu, czy w każdym dniu trwania nowego menu, spełniona będzie ta zasada

* Aktualne może być maksymalnie 1 menu

* Rachunek może zostać wygenerowany, pod warunkiem, że została wcześniej uregulowana płatność. Nadpłaty traktowane są jako "napiwek"

* Po wykonaniu 10 zamówień przez klienta, przypisywana jest zniżka do klienta o wartości 5% naliczana zawsze przy składaniu zamówienia

* Po wykonaniu łącznej liczby zamówień za ponad 1000 zł, przypisywana jest jednorazowa zniżka dla klienta na 7 dni od momentu złożenia zamówienia. Może być przypisana więcej niż jedna zniżka

* Zniżki nie łączą się. Pierwszeństwo ma zniżka jednorazowa

* Można usunąć klienta z bazy danych, jednak jego zamówienia i płatności pozostaną w bazie. Usunięte zostaną jedynie informację o danych osobowych klienta

* Można przedłużyć rezerwację pod warunkiem, że każdy stolik z rezerwacji będzie dostępny do nowego czasu zakończenia rezerwacji
