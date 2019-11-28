<!-- class: center, middle, inverse -->





###  Cwiczenie 1  
Instrukcja <i>begin</i> powoduje utworzenie i asynchroniczne uruchomienie osobnego tasku wykonującego instrukcje po niej następującą: 
```chapel
begin writeln("hello world");
writeln("goodbye");
```

Wynik:
```bash
goodbye
hello world
```
lub 
```bash
hello world
goodbye
```
###  Cwiczenie 2
Po <i> begin </i> można uzywać złozonych instrukcji lub funkcji:
```chapel
begin {
  for i in 1..10 do
    writeln("Hi there!");
  writeln("Oh, I forgot to say something!");
}

begin sayHiBack();

for i in 1..10 do
  writeln("Goodbye...");


proc sayHiBack() {
  for i in 1..10 do
    writeln("Well, hello to you too!");
}
```

Wynik:
```bash
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Goodbye...
Well, hello to you too!
Hi there!
Hi there!
Hi there!
Hi there!
Hi there!
Hi there!
Hi there!
Hi there!
Hi there!
Hi there!
Oh, I forgot to say something!

```
###  Cwiczenie 3

Instrukcja <i>cobegin </i> tworzy i uruchamia ustaloną ilość tasków i czeka na ich zakończenie .
Zwykle używamy, aby utworzyć pokrewny zestaw heterogenicznych zadań lub mały, stały zestaw homogenicznych zadań.
Przydatna, gdy zadanie nadrzędne zależy od ukończenia się wszystkich zadań.
```chapel
cobegin {
/*task 1*/
  writeln("Hi!");
/*task 2*/
 sayHello();
  /* To wykona sie w ramach jednego tasku (task 3)*/
  {
    writeln("Greetings1");
    writeln("Greetings2");
  }
}
/*Program glówny*/
writeln("See you later!");

proc sayHello() {
  writeln("Hello!");
}
```
Przykładowy wynik:
```bash
Hi!
Greetings1
Hello!
Greetings2
See you later!
```
### Cwiczenie 4

Jeśli w ramach instrukcji cobegin znajdzie się zagnieżdżona instrukcja begin to cobegin nie czeka na jej zakończenie.
```chapel
cobegin {
  writeln("Hi!");
  printHellos();
}
writeln("Sorry, I've gotta leave...");

proc printHellos() {
  writeln("Beginning...");
  begin {
    for i in 1..10 do
      writeln("Hello!");
  }
  writeln("I'm moving on...");
}
```
Przykładowy wynik:
```bash
Beginning...
Hello!
Hello!
Hi!
Hello!
Hello!
I'm moving on...
Hello!
Hello!
Hello!
Sorry, I've gotta leave...
Hello!
Hello!
Hello!
```
### Cwiczenie 5
Instrukcja coforall przyporządkowuje każdą iterację do osobnego tasku. Uzywamy, gdy potrzebujemy stworzyć zbiór homogenicznych zadań.
Przydatna, gdy zadanie nadrzędne zależy od ukończenia się wszystkich zadań.
```chapel
config const numTasks = here.maxTaskPar;
coforall t in 0..#numTasks {
        writeln("Hello from task ", t, " of ", numTasks);
}
writeln("All tasks done");

```
```bash
Hello from task 0 of 4
Hello from task 3 of 4
Hello from task 1 of 4
Hello from task 2 of 4
All tasks done

```
### Cwiczenie 5
Taski moga działac na wspólnych zmiennych. Aby uniknąć wyścigu, stosujemy typ 'sync'

```chapel
var n: int =10;
// typ ‘sync’ pamieta nie tylko wartosc, ale tez stan (empty/full)  
// tutaj wartosci kazdego elementu buff$ są niezainicjalizowane, wiec startują ze stanem empty 
var buff$: [0..n] sync int;
var data: int;

begin producer();
consumer();

proc producer() {
  for i in 0..n {
      buff$[i] = i;  // czekaj, az zmienna bedzie w stanie empty, zapisz jej nową wartosc, pozostaw w stanie pelny
  }
}

proc consumer() {
  for i in 0..n {
        // czekaj az zmienna bedzie w stanie full, odczytaj jej warosc, pozostaw w stanie empty
        data=buff$[i];
        writeln(data);   
  }
}
```
wynik
```bash
0
1
2
3
4
5
6
7
8
9
10
```
### Przykład zastosowania do sekcji krytycznej:

```chapel
var lock$: sync bool;

lock$ = true;
 
critical_section();

var lockval= lock$;
```

### Przykład zastosowania do utworzenia zmiennej future
```chapel
var future$: sync real;
begin future$ = compute();
res = computeSomethingElse();
useComputedResults(future$, res);
```

### Cwiczenie 6 

Zmienne typu atomic - zmienne, na których działania tasków są atomowe (nie mogą być przerwane w trakcie).
Również służą do synchronizacji.

```chapel
var count: atomic int, done: atomic bool;
config const numTasks = here.maxTaskPar;

proc barrier(id) {
  const myCount= count.fetchAdd(1);
  if(myCount< numTasks-1) then {
        writeln("Waiting ... ", id);
        done.waitFor(true);
        writeln("TAIL: The end from task ", id, " of ", numTasks);
  }
  else {
    writeln("HEAD: I am the first to end ", id, " of ", numTasks );
    done.testAndSet();
   }
}

coforall i in 1..#numTasks {
        barrier(i);
}
```
Wynik
```bash
Waiting ... 1
HEAD: I am the first to end 4 of 4
Waiting ... 3
TAIL: The end from task 1 of 4
TAIL: The end from task 3 of 4
Waiting ... 2
TAIL: The end from task 2 of 4
```

### Wiecej informacji

* Tutorial [https://chapel-lang.org/tutorials/Oct2018/04-TaskPar.pdf](https://chapel-lang.org/tutorials/Oct2018/04-TaskPar.pdf)
* User guide [https://chapel-lang.org/docs/users-guide/index.html#task-parallelism](https://chapel-lang.org/docs/users-guide/index.html#task-parallelism)
* Wiecej o zmiennych sync [https://chapel-lang.org/docs/primers/syncsingle.html](https://chapel-lang.org/docs/primers/syncsingle.html)
* Wiecej o zmiennych atomic [https://chapel-lang.org/docs/primers/atomics.html?highlight=atomic](https://chapel-lang.org/docs/primers/atomics.html?highlight=atomic)


### Projekt
[https://github.com/kzajac/AR2019/blob/master/proj2.md](https://github.com/kzajac/AR2019/blob/master/proj2.md)
