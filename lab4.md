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

### Cwiczenie 2

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
// poddomeny na konkrentnych locale
for L in Locales {
  on L {
    const indices = BA.localSubdomain();
     writeln("locale id=",L.id, " localSubdomain=", indices);
    }
  }


writeln("Block Array Index Map");
writeln(BA);
writeln();

```

Wynik:
```bash
[plgrycerz@n1024-amd ~]$ ./test -nl 2
locale id=0 localSubdomain={1..4, 1..8}
locale id=1 localSubdomain={5..8, 1..8}
Block Array Index Map
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1

[plgrycerz@n1024-amd ~]$ ./test -nl 4
locale id=0 localSubdomain={1..4, 1..4}
locale id=1 localSubdomain={1..4, 5..8}
locale id=2 localSubdomain={5..8, 1..4}
locale id=3 localSubdomain={5..8, 5..8}
Block Array Index Map
0 0 0 0 1 1 1 1
0 0 0 0 1 1 1 1
0 0 0 0 1 1 1 1
0 0 0 0 1 1 1 1
2 2 2 2 3 3 3 3
2 2 2 2 3 3 3 3
2 2 2 2 3 3 3 3
2 2 2 2 3 3 3 3
```
### Cwiczenie 3

Wymuszenie własnego  podziału 

```chapel
use BlockDist;
config const n = 8;
const Space = {1..n, 1..n};

// ustawiamy  zakres: pierwszy wymiar: liczba lokale-s, drugi wymiar: 1    
var MyLocaleView = {0..#numLocales, 1..1};
 
// Inny przykład  pierwszy wymiar: 2, drugi wymiar: połowa z liczby locale-s
// Uwaga: to zadziała tylko dla parzystej liczby locale-s:
// zasada: iloczyn wymiarów musi dać liczbę locale-s
//var hf=Locales.size/2;
//var MyLocaleView = {1..2, 1..hf};


//przekształcamy tablice Locales na tablice o wymiarach zgodnych z wyżej zdefioniowanym zakresem 
var MyLocales: [MyLocaleView] locale = reshape(Locales, MyLocaleView);

const BlockSpace2 = Space dmapped Block(boundingBox=Space,
                                        targetLocales=MyLocales);
var BA2: [BlockSpace2] int;
forall ba in BA2 do
  ba = here.id;

writeln("Block Array Index Map");
writeln(BA2);
writeln();
```

### Wiecej informacji o różnych podziałach
[https://chapel-lang.org/docs/primers/distributions.html](https://chapel-lang.org/docs/primers/distributions.html)

### Zadanie
* Zmodyfikuj i uruchom zadanie z poprzedniego laboratorium na kilku węzłach uzywajac dowolnej wersji podziału blokowego. 
* Wskazówka - slajd 31 i 32 z [https://chapel-lang.org/tutorials/Oct2018/03-DataPar.pdf](https://chapel-lang.org/tutorials/Oct2018/03-DataPar.pdf)

### Projekt
[https://github.com/kzajac/AR2019/blob/master/proj1.md](https://github.com/kzajac/AR2019/blob/master/proj1.md)
