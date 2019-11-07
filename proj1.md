<!-- class: center, middle, inverse -->

## Projekt 1 Dekompozycja domenowa w zrównoleglaniu rozwiązywania równań różniczkowych cząstkowych.

Porównać wydajnosc roznych wersji algorytmu opracowywanego na laboratorium:
  
 * uruchomionego na jednym wielordzeniowym wezle ze wspólną pamięcią (1 locale)
 * uruchomionego na wybranej konfiguracji wielowezłowej z rozproszoną pamięcią (np 10 wezlow po 1 rdzeniu)
 * wersje Jakobiego  i wersje odd-even

### Uwagi:

 * Nalezy doswiadczalnie dobrac rozmiar problemu, aby uzyskac przyspieszenie. Generalnie dla wiekszych rozmiarow
 problemu przyspieszenie (i efektywnosc) powinny byc lepsze niz dla mniejszych
 
 * Zmierzyć(ODDZIELNE wykresy dla każdego z wybranych rozmiarów problemu):
    - przyspieszenie S(p)=T(1)/T(p)
    -  efektywność E(p)= S(p)/p
 
 * do pomiaru czasu w jezyku Chapel służą <a href=https://chapel-lang.org/docs/primers/timers.html> Timery </a>
 
 * aby pomiary byly miarodajne, należy podać uśrednione wyniki z 10 pomiarów
   
 * na wykresach należy przedstawić zależność S(p), E(p), gdzie zmienną niezależna jest p
 
 * p wyliczamy poprzez sumę liczby tasków (np. here.maxTaskPar) ze wszystkich locale 
    
  Należy dostarczyć sprawozdanie z wynikami pomiarów oraz wnioski.

### Terminy:

* Grupa pon 11:15 - 18.11
* Grupa sr 14:40 - 20.11
* Grupa czw 9:35 - 28.11

Przed upłynięciem tych terminów zajęcia odbywają się w formie nieobowiązkowych konsultacji projektowych (sale laboratoryjne lub p. 3.54) 



  
