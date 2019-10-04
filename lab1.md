<!-- class: center, middle, inverse -->

## Chapel
* Strona: [https://chapel-lang.org/](https://chapel-lang.org/)
* Dokumentacja [https://chapel-lang.org/docs/](https://chapel-lang.org/docs/) 
* Tutoriale [https://chapel-lang.org/tutorials.html](https://chapel-lang.org/tutorials.html)

## Quickstart
### Uruchomienie interaktywne  na Zeusie
* Tryb interaktywny - tutaj prosimy o 1 węzeł (node) i 12 rdzeni (core) 
```shell
 [plgrycerz@zeus ~]$ srun -N 1 --ntasks-per-node=12 -p plgrid  --pty /bin/bash
```
* Ustawiamy środowisko 
```shell
 [plgrycerz@n1024-amd mytest]$ module load plgrid/tools/chapel/1.20.0
 [plgrycerz@n1024-amd mytest]$ export  GASNET_PHYSMEM_MAX='128MB'
```
* Tworzymy plik Hello world! 

```chapel
use Memory;  // for physicalMemory()
config const printLocaleInfo = true;  // permit testing to turn this off

if printLocaleInfo then
  for loc in Locales do
    on loc {
      writeln("locale #", here.id, "...");
      writeln("  ...is named: ", here.name);
      writeln("  ...has ", here.numPUs(), " processor cores");
      writeln("  ...has ", here.physicalMemory(unit=MemUnits.GB, retType=real),
              " GB of memory");
      writeln("  ...has ", here.maxTaskPar, " maximum parallelism");
    }

writeln();

```
### Kompilacja
```shell
$ chpl -o hello_world hello_world.chpl
```
 ### Uruchomienie korzytające z jednego węzła (noda) jako całości (wiele węzłów)
 ```shell
$ ./hello_world -nl 1
```
Parametr -nl to specyfikuje ilość "jednostek maszynowych" (locale) . 

Zwykle jednostka  to jeden węzeł wielordzeniowy o wspólnej pamięci [https://chapel-lang.org/docs/primers/locales.html](https://chapel-lang.org/docs/primers/locales.html), ale nie musi tak być.  


### Uruchomienie wsadowe  na Zeusie

Przygotowywujemy plik z opisem zadania:

```bash
#!/bin/bash -l
## Nazwa zlecenia
#SBATCH -J MyHelloJob
## Liczba alokowanych węzłów
#SBATCH -N 1
## Liczba zadań per węzeł (domyślnie jest to liczba alokowanych rdzeni na
##węźle)
#SBATCH --ntasks-per-node=6
## Maksymalny czas trwania zlecenia (format HH:MM:SS)
#SBATCH --time=00:01:00
## Specyfikacja partycji
#SBATCH -p plgrid-testing
## Plik ze standardowym wyjściem
#SBATCH --output="output.out"
## Plik ze standardowym wyjściem błędów
#SBATCH --error="error.err"

## Zaladowanie modulu Chapel i ustawieniepotrzebnych zmiennych

module load plgrid/tools/chapel/1.20.0
export  GASNET_PHYSMEM_MAX='128MB'module add


## przejscie do katalogu z ktorego wywolany zostal sbatch
cd $SLURM_SUBMIT_DIR
chpl -o hello hello.chpl
# Domyslnie podaje liczbe zaalokowanych wezlow pamietana w zmiennej SLURM_NNODES
./hello -nl  $SLURM_NNODES
```
Uruchamiamy:
```bash
[plgrycerz@zeus mytest]$ sbatch run.sh
Submitted batch job 5603306
```
Sprawdzamy stan:
```bash
[plgrycerz@zeus mytest]$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5603306 plgrid-te MPIMytes plgrycer  R       0:15      1 n1024-amd

```

Po zakonczeniu zadania ogladame wyjscie i error:
```bash
locale #0...
  ...is named: n1024-amd
  ...has 6 processor cores
  ...has 252.238 GB of memory
  ...has 6 maximum parallelism

```
```bash
[plgrycerz@zeus mytest]$ cat error.err
 plgrid/tools/gcc/5.4.0 loaded.
 plgrid/tools/intel/18.0.0 loaded.
 plgrid/tools/impi/2018 loaded.
 plgrid/tools/chapel/1.20.0 loaded.
```

### Cwiczenie 1
- uruchomić program hello z większym parametrem nl na zaalokowanym jednym nodzie. Zaobserwować, co się stanie. 
```shell
$ ./hello_world -nl 3
```
```shell
$ ./hello_world -nl 12
```
- zaalokować 2 węzły po 6 corów i uruchomić program hello z różnymi parametrami -nl. Zaobserwować, co się stanie
---
## Równoległość Danych (wersja 1 locale składający się z 1 węzła i wielu rdzeni)

W języku Chapel mamy wsparcie dla dekompozycji domenowej oraz dekompozycji funkcjonalnej.  

Na tym laboratorium zajmiemy się dekompozycją domenową (dzielimy dane).

Zajmiemy sie opcja: 1 locale zawierajacy 1 węzeł z wieloma rdzeniami.

### Cwiczenie 2

Przeanalizować i uruchomić program [https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello3-datapar.chpl](https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello3-datapar.chpl)


Wynik jaki powinien wyjść [https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello3-datapar.good](https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello3-datapar.good)

### Przydział iteracji do tasków (data parallel, wersja dla 1 locale = 1 węzeł, wiele rdzeni)

```chapel
config const n = 10;
var sum: [1..n] real;

sum=0;

// Petla forall rozdziala interacje na taski
// zwykle jest tyle taskow ile rdzeni mamy na danej maszynie


// W przypadku  wsparcia dla data parallel
// Chapel ukrywa przed uzytkownikiem podzial iteracji pomiedzy taski
// mozna jednak przeprowadzic test, gdzie kazdy task ma swoja kopie tablicy
// obserwujac jak tablica jest wypelniana w kolejnych iteracjach widzimy,
// ktore iteracje naleza do tego samego tasku

forall i in 1..n with (in sum ) do {

        sum(i)=-1;
        writeln("iteracja " , i, " ", sum);
}

writeln(sum);
```

Przykładowy wynik dla dwóch rdzeni:
```shell
iteracja 1 -1.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0
iteracja 6 0.0 0.0 0.0 0.0 0.0 -1.0 0.0 0.0 0.0 0.0
iteracja 2 -1.0 -1.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0
iteracja 7 0.0 0.0 0.0 0.0 0.0 -1.0 -1.0 0.0 0.0 0.0
iteracja 3 -1.0 -1.0 -1.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0
iteracja 8 0.0 0.0 0.0 0.0 0.0 -1.0 -1.0 -1.0 0.0 0.0
iteracja 4 -1.0 -1.0 -1.0 -1.0 0.0 0.0 0.0 0.0 0.0 0.0
iteracja 9 0.0 0.0 0.0 0.0 0.0 -1.0 -1.0 -1.0 -1.0 0.0
iteracja 5 -1.0 -1.0 -1.0 -1.0 -1.0 0.0 0.0 0.0 0.0 0.0
iteracja 10 0.0 0.0 0.0 0.0 0.0 -1.0 -1.0 -1.0 -1.0 -1.0
0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0

```

Tutaj wersja ze wspólną tablicą 

```chapel
config const n = 10;
var sum: [1..n] real;

// wpisanie zer do całej tablicy 

sum=0;

forall i in 1..n  do {

        sum(i)=-1;
        writeln("iteracja " , i, " ", sum);
}

writeln(sum);
```

Wynik:
```shell
iteracja 1 -1.0 0.0 0.0 0.0 0.0 -1.0 0.0 0.0 0.0 0.0
iteracja 6 -1.0 -1.0 0.0 0.0 0.0 -1.0 0.0 0.0 0.0 0.0
iteracja 2 -1.0 -1.0 0.0 0.0 0.0 -1.0 -1.0 0.0 0.0 0.0
iteracja 7 -1.0 -1.0 -1.0 0.0 0.0 -1.0 -1.0 0.0 0.0 0.0
iteracja 3 -1.0 -1.0 -1.0 0.0 0.0 -1.0 -1.0 -1.0 0.0 0.0
iteracja 8 -1.0 -1.0 -1.0 -1.0 0.0 -1.0 -1.0 -1.0 0.0 0.0
iteracja 4 -1.0 -1.0 -1.0 -1.0 0.0 -1.0 -1.0 -1.0 -1.0 0.0
iteracja 9 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 0.0
iteracja 5 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0
iteracja 10 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0
-1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0 -1.0
```
### Cwiczenie 3 -

poprawic poniższy kod, aby działał poprawnie  

```chapel
config const n = 10;
var sk : real;

forall i in 1..n  with (+ reduce sk) do {
        sk=i;
        writeln("i=", i, " sk=", sk);
}

writeln (sk);

```
### Wyścig

Przy ukrytej komunikacji pomiędzy taskami może pojawić się wyścig.

```chapel
config const n = 10;
//domena jest jednowymiarowy zakres liczb 1..n
const D= {1..n};

//podzbior domeny
const DW=D[2..n];

var tab :[D] real;
for i in D do
        tab(i)=i;

writeln("tabela wejsciowa:");
writeln(tab);
//do kazdego (poza pierwszym) elementu tablicy kopiujemy dane od jego lewego sasiada
// operacja przeprowadzona rownolegle

forall i in DW  do {
        tab(i)=tab(i-1);

}

// wynik jest niedeterministryczny, nie znamy kolejnosci iteracji (wyscig)
writeln("wyscig");
writeln(tab);

// Rozwiazanie

// wartosci poczatkowe
for i in D do
        tab(i)=i;

// operacje zapisujemy w tymczasowej (rowniez wspoldzielonej) tablicy

var tabtemp:[D] real;
forall i in DW  do
        tabtemp(i)=tab(i-1);

//na koncu kopiujemy tablice

tab(DW)=tabtemp(DW);

writeln("bez wyscigu");
writeln(tab);

```
wynik:
```shell
tabela wejsciowa:
1.0 2.0 3.0 4.0 5.0 6.0 7.0 8.0 9.0 10.0
wyscig
1.0 1.0 1.0 1.0 1.0 1.0 1.0 1.0 1.0 1.0
bez wyscigu
1.0 1.0 2.0 3.0 4.0 5.0 6.0 7.0 8.0 9.0
```

###  Kolejnosc przegladania iteracji
```chapel

//ilosc punktow
config const n = 10;
//domena
const D= {1..n};

//zakres srodka domeny {2..n-1}
const DW=D.expand(-1);

var tab :[D] real;

// warunki brzegowe
tab=0;
writeln(tab);

tab[D.interior(1)]=1;
writeln(tab);

tab[D.interior(-1)]=1;
writeln(tab);

var tabtemp:[D] real;

writeln("Jacobi");

for j in 1..10 do {
        forall i in DW  do
                tabtemp(i)=(tab(i-1)+tab(i+1))/2;

        tab(DW)=tabtemp(DW);

}

writeln(tab);

// zerowanie tablicy z danymi

tab=0;
tab[D.interior(1)]=1;
tab[D.interior(-1)]=1;

//operacje wykonujemy w dwoch petlach - najpierw parzysci, potem nieparzysci

writeln("odd-even");

const allEvens = DW by 2 ;
const allOdds = DW by 2 align 1;

writeln(allEvens);
writeln(allOdds);

for j in 1..5 do {
        forall i in allEvens  do {
                tab(i)=(tab(i-1)+tab(i+1))/2;

        }


        forall i in allOdds  do {
                tab(i)=(tab(i-1)+tab(i+1))/2;

        }
}

writeln(tab);

```

Wynik:
```
0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0
0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 1.0
1.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 1.0
Jacobi
1.0 0.764648 0.570312 0.40918 0.335938 0.335938 0.40918 0.570312 0.764648 1.0
odd-even
{2..9 by 2}
{2..9 by 2 align 1}
1.0 0.757812 0.570312 0.382812 0.335938 0.289062 0.40918 0.529297 0.764648 1.0
```
### Zadanie 
- przeczytac tutorial [https://chapel-lang.org/tutorials/Oct2018/03-DataPar.pdf](https://chapel-lang.org/tutorials/Oct2018/03-DataPar.pdf) do 30 slajdu

- przygotowac implementacje swojego problemu używając metody Jakobiego (wersja na jeden locale, bez rozpraszania) 
- przetestować implementacje na  niewielkich danych
- przygotować podobną implementację problemu używając metody odd-even 
- ocenić poprawność wyników (testy na niewielkich danych)



