<!-- class: center, middle, inverse -->

## Chapel
* Strona: [https://chapel-lang.org/](https://chapel-lang.org/)
* Dokumentacja [https://chapel-lang.org/docs/](https://chapel-lang.org/docs/) 

### Uruchomienie interaktywne  na Zeusie
* Tryb interaktywny - tutaj prosimy o 1 węzeł i 12 corów 
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
### Uruchomienie wsadowe  na Zeusie

### Zadanie
- uruchomić program hello z większym parametrem 
```shell
$ ./hello_world -nl 3
```
```shell
$ ./hello_world -nl 12
```
- zaalokować 2 węzły po 6 corów i uruchomić program hello z różnymi parametrami -nl
---
