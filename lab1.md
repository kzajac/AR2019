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
 ### Uruchomienie korzytające z jednego noda jako całości (wiele corów)
 ```shell
$ ./hello_world -nl 1
```
Parametr -nl to specyfikuje ilość "jednostek maszynowych" (locale) . 

Zwykle jednostka  to jeden węzeł wielocorowy o wspólnej pamięci [https://chapel-lang.org/docs/primers/locales.html](https://chapel-lang.org/docs/primers/locales.html), ale nie musi tak być.  


### Uruchomienie wsadowe  na Zeusie

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
## Równoległość Danych (1 węzeł wiele rdzeni)

W języku Chapel mamy wsparcie dla dekompozycji domenowej oraz dekompozycji funkcjonalnej.  

Na tym laboratorium zajmiemy się dekompozycją domenową (dzielimy dane) wielu rdzeniach jednego węzła.

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

poprawic poniższy kod, aby działał poprawnie dla dwóch  węzłów

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

### Domeny 

### Zadanie 



