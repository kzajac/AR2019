## Projekt 2 Dekompozycja funkcjonalna na przykładzie algorytmu Branch and Bound

### Wprowadzenie

Znaną klasą problemów sa zadania decyzyjne. Przykladem takiego problemu moze być [https://www.mcs.anl.gov/~itf/dbpp/text/node21.html#SECTION02370000000000000000](optymalizacja zagospodarownia miejsca w okreslonym obszarze potrzebna m.in w projektowaniu ukladow VLSI). Powyższy link pokazuje jeden ze sposobow zrownoleglania takiego problemu za pomoca drzewa i algorymu "branch-and-bound search". Zadanie ma na celu zapoznianie sie z tym algorytmem i zastosowanie go do problemu decyzyjnego na przykladzie problemu komiwłojażera.
    
### Problem komiwłojażera (travelling salesman problem)
    
* Dane jest N miast oraz dlugosci odcinkow pomiedzy miastami, należy znalezc cykl o minimalnej dlugosci zawierajacy wszystkie miasta.
    
*  Uwaga: użycie algorytmu branch-and-bound jest obowiązkowe dla tego ćwiczenia! 

### Zadanie

Zaimplementuj rownolegly algorytm branch and bound. Kolejne poddrzewa powinny być przydzielane do "głodnych" workerów.
    należy zmierzyć jaka gruboziarnistość poddrzew jest oprymalna dla zaproponowanego rozwiązania. Czy dużo cienkich poddrzew, czy raczej mało grubych poddrzew? Przedstawić wykresy przyspieszenia i efektywności dla dwóch przypadkow grubosci poddrzew.
    wersja podstawowa - wycinanie gałezi następuje w obrębie każdego tasku osobno, taski nie przesyłają sobie aktualnego minimum
    wersja rozszerzona taski komunikują aktualne minimum pomiedzy soba. 

### Terminy:

* Grupa pon 11:15 - 18.11
* Grupa sr 14:40 - 20.11
* Grupa czw 9:35 - 28.11

Przed upłynięciem tych terminów zajęcia odbywają się w formie nieobowiązkowych konsultacji projektowych (sale laboratoryjne lub p. 3.54) 



