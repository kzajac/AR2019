<!-- class: center, middle, inverse -->

## Chapel
* Strona: [https://chapel-lang.org/](https://chapel-lang.org/)
* Dokumentacja [https://chapel-lang.org/docs/](https://chapel-lang.org/docs/) 

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


Wynik powinien wyjść [https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello3-datapar-good](https://github.com/chapel-lang/chapel/blob/master/test/release/examples/hello3-datapar-good)

### Cwiczenie 3 

Operacje redukcji



### Cwiczenie 4 

Wyścig

### Cwiczenie 5

Domeny 

### Zadanie 



