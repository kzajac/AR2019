## Projekt 2 Dekompozycja funkcjonalna na przykładzie algorytmu Branch and Bound

### Wprowadzenie

Znaną klasą problemów sa zadania decyzyjne. Przykladem takiego problemu moze być [optymalizacja zagospodarownia miejsca w okreslonym obszarze potrzebna m.in w projektowaniu ukladow VLSI](https://www.mcs.anl.gov/~itf/dbpp/text/node21.html#SECTION02370000000000000000).

Powyższy link pokazuje jeden ze sposobow zrownoleglania takiego problemu za pomoca drzewa i algorymu "branch-and-bound search". Zadanie ma na celu zapoznianie sie z tym algorytmem i zastosowanie go do problemu decyzyjnego na przykladzie problemu komiwłojażera.
    
### Problem komiwłojażera (travelling salesman problem)
    
* Dane jest N miast oraz dlugosci odcinkow pomiedzy miastami, należy znalezc cykl o minimalnej dlugosci zawierajacy wszystkie miasta.
    
*  Uwaga: użycie algorytmu branch-and-bound jest obowiązkowe dla tego ćwiczenia! 

### Zadanie

* Zaimplementuj rownolegly algorytm branch and bound za pomocą znanych sobie narzędzi do programowania równoległego (preferowany Chapel, ale może tez byc MPI lub inne narzędzie poznane na TPR). 

* Zbadać jaka gruboziarnistość poddrzew jest oprymalna dla zaproponowanego rozwiązania. Czy dużo cienkich poddrzew, czy raczej mało grubych poddrzew? Przedstawić wykresy przyspieszenia i efektywności dla dwóch przypadkow grubosci poddrzew.

* Należy zbadać dwie wersje podejścia do aktualnego minimum:
    * Wersja podstawowa  - wycinanie gałezi następuje w obrębie każdego tasku osobno, taski nie przesyłają sobie aktualnego minimum
    * Wersja rozszerzona - taski wymieniają aktualne minimum pomiedzy soba. 

### Terminy:

* Grupa pon 11:15 - 13.01.20
* Grupa sr 14:40 - 15.01.20
* Grupa czw 9:35 - 16.01.10

Przed upłynięciem tych terminów zajęcia odbywają się w formie nieobowiązkowych konsultacji projektowych (sale laboratoryjne lub p. 3.54) 

Uwagi:

* W  dniu 12.12 konsultacje nie odbędą się
* W dniu 27.01.2020 jestem nieobecna  - prosze o oddanie zadań najpóźniej w tygodniu 20-23 stycznia !




