<!-- class: center, middle, inverse -->

## Projekt 1 Dekompozycja domenowa w zrównoleglaniu rozwiązywania równań różniczkowych cząstkowych.

Porównać wydajnosc roznych wersji algorytmu opracowywanego na laboratorium:
  
 * uruchomionego na jednym wielordzeniowym wezle (1 locale)
 * uruchomionego na wybranej konfiguracji wielowezłowej (np 10 wezlow po 2 rdzenie)
 * wersje Jakobiego  i wersje odd-even

### Uwagi:

 * Zmierzyć(ODDZIELNIE dla RÓŻNYCH rozmiarow problemu):
       - przyspieszenie S(p)=T(1)/T(p)
       -  efektywność E(p)= S(p)/p

* Nalezy doswiadczalnie dobrac rozmiar problemu, aby uzyskac przyspieszenie. Generalnie dla wiekszych rozmiarow
 problemu przyspieszenie (i efektywnosc) powinny byc lepsze niz dla mniejszych.
 
 * aby pomiary byly miarodajne, należy podać uśrednione wyniki z 10 pomiarów
   
 * na wykresach należy przedstawić zależność S(p), E(p), gdzie zmienną niezależna jest p
 
 * p wyliczamy poprzez sumę liczby tasków (here.maxTaskPar) ze wszystkich locale 
    
  Należy dostarczyć sprawozdanie z wynikami pomiarów oraz wnioski.

Terminarz:

  
