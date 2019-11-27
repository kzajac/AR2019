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



### Zadanie
*
### Projekt
[https://github.com/kzajac/AR2019/blob/master/proj2.md](https://github.com/kzajac/AR2019/blob/master/proj2.md)
