<!-- class: center, middle, inverse -->

###  Cwiczenie 1
* zaalokować dwa węzły po 6 rdzeni

Tryb interaktywny 
```shell
 [plgrycerz@zeus ~]$ srun -N 2 --ntasks-per-node=6 -p plgrid  --pty /bin/bash
```

Tryb wsadowy w pliku z opisem zadania ustawiamy opcje:
```shell
#SBATCH -N 2
## Liczba zadań per węzeł (domyślnie jest to liczba alokowanych rdzeni na
##węźle)
#SBATCH --ntasks-per-node=6
```
Uruchomienie - zwykle ustawiamy: ilosc locales = ilosc węzłów.

Dla dwóch węzłów:


```shell
./hello -nl 2
```

* przeanalizować i wykonac kod: [https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello4-datapar-dist.chpl](https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello4-datapar-dist.chpl)
* przykładowy wynik: [https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello4-datapar-dist.good](https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello4-datapar-dist.good)

###Cwiczenie 2

Wykonac ponizszy kod dla roznej liczby locale (roznej wartosci parametru nl)

```chapel
use BlockDist;

config const n = 8;

const Space = {1..n, 1..n};

const BlockSpace = Space dmapped Block(boundingBox=Space);

var BA: [BlockSpace] int;

// za elementy tablicy podstawiamy identyfikator locale
forall ba in BA do
  ba = here.id;

writeln("Block Array Index Map");
writeln(BA);
writeln();
```

Zadanie
* Zmodyfikuj i uruchom zadanie z poprzedniego laboratorium na kilku węzłach. 
* Wskazówka - slajd 31 i 32 z [https://chapel-lang.org/tutorials/Oct2018/03-DataPar.pdf](https://chapel-lang.org/tutorials/Oct2018/03-DataPar.pdf)
