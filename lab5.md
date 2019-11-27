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
Instrukcja <i>cobegin </i> tworzy i uruchamia ustalona ilość tasków i czeka na ich zakończenie  
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

Jeśli w ranach instrukcji cobegin znajdzie się zagnieżdżona instrukcaj begin to cobegin nie czeka na jej zakończenie.
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
### Cwieczenie 5
Instrukcja coforall przyporządkowuje każdą iterację do osobnego tasku. 
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
### Zadanie
*
### Projekt
[https://github.com/kzajac/AR2019/blob/master/proj2.md](https://github.com/kzajac/AR2019/blob/master/proj2.md)
