# Wprowadzenie do FP


# Spis tresci
- [Czym jest FP](#czym-jest-fp)
- [Jezyki FP vs jezyki z paradygmatem FP](#jezyki-fp-vs-jezyki-z-paradygmatem-fp)
- [Koncepty funkcyjnego programowania](#koncepty-funkcyjnego-programowania)
  - Pure functions
  - Immutability
  - First-class functions
  - Higher-order functions
  - Declarative code
  - Currying
  - Recursion
  - Popularne operacje na kolekcjach
- [Przykladowe uzycia](#przykladowe-uzycia)
- [Cwiczenia](#cwiczenia)
- [Przykłady użycia funkcyjnego paradygmatu](#przyklady-uzycia-funkcyjnego-paradygmatu)
  - Mosty do innych jezykow
- [Uzyteczne linki](#uzyteczne-linki)



# Czym jest FP

Funkcyjne programowanie (FP, functional programming) jest jednym z kilku wiodacych stylow programowania. Na skali popularnosci, zdecydowanie wciaz za style obiektowym (OOP, object-oriented programming), ale przed stylem reaktywnym (RP, reactive programming) czy reaktywno-funkcyjnym (RFP, reactive functional programming).  
Dzieki uniwersytetom, kursom, codecamp'om, itp. OOP stalo sie i pozostaje najbardziej znanym stylem, wobec czego zaloze na moment, ze jest z nim obeznany przynajmniej w podstawowym stopniu, i wyjawie co nastepuje:
- w FP nie ma:
  - klas
  - instancji
  - obiektow
  - stanu (z pewnymi wyjatkami)
  - metod
  - dziedziczenia
  - polimorfizmu
  - `this`, ani `self` (ani zadnego ich ekwiwalentu)
  - imperatywnego kodu

Istnieja zas nastepujace:
  - moduly
  - funkcje
  - abstrakcja (w sensie funkcji prywatnych i publicznych)
  - funkcje pierwszej kategorii i funkcje wyzszego rzedu
  - funkcje nazwane i anonimowe (lambdy)
  - deklaratywny kod
Oraz:
  - (typowo) kolekcje w formie list, a nie arrays
  - rekursja ogonowa (tail recursion)
  - currying
  - funkcje czastkowe i czastkowa aplikacja funkcji (partial application)
  - oraz inne, ktore dzis poznasz

# Jezyki FP vs jezyki z paradygmatem FP

Wiele jezykow zaimplementowalo elementy FP, ale tylko niektore staly sie jezykami czysto FP paradygmatowymi. Oto kilka przykladow:

Czesciowa implementacja w:
- Ruby
- Python
- PHP
- Java oraz Scala
- JavaScript
- C# oraz F#
- Lua

Jezyki funkcyjne:
- Elixir
- Erlang
- [Haskell](https://www.haskell.org/)
- Clojure
- [Elm](https://www.elm-lang.org/)

Warto tez wspomniec istnienie tzw. czysto funkcyjnych jezykow (purely functional languages). W tych jezykach mozemy pisac wylacznie czyste funkcje. Do tej rodziny naleza:
- Haskell
- Elm
- Idris
- [i inne](https://en.wikipedia.org/wiki/List_of_programming_languages_by_type#Pure)


Dla nieco lepszego zrozumienia (czysto) funkcyjnych jezykow polecam wpis ze strony Haskell:

> Kazda funkcja w j. Haskell jest funkcja w sensie matematycznym (t.j. "czysta"). Nawet operacje typu IO (Input/Output) sa niczym objasnienia tego, co nalezy zrobic, stworzone przez czysty kod. Nie ma 'statements' czy 'instructions', a sa jedynie 'expressions', ktore nie moga mutowac zmiennych (lokalnych czy globalnych) i nie maja dostepu do czasu czy losowych numerow.  
  

OK, wiec czemu w ogole powinnismy byc zainteresowani FP w naszym ulubionym jezyku? Poniewaz nasze programy beda benefitowac z nastepujacych:
- latwe do testowania, male funkcje
- przejrzystosc programu
- latwiejszy refaktoring
- latwiej bedzie nam rozumowac na temat programu
- mniej kodu do pisania, edycji, i czytania
- (jezeli uzywamy tylko lub duzo czystych funkcji) wieksza pewnosc jakosci kodu

No i radosc z pisania :-)

# Koncepty funkcyjnego programowania

Mala notka na wstepie. Poniewaz uzywamy funkcyjnego paradygmatu, to koncept `this` czy `self` wyrzucamy do lamusa. Nie tykamy sie lokalnego stanu w naszych funkcjach. Wszystkie przyklady sa z uzyciem JS, wobec czego bedziemy uzywali arrow function (`=>`) ile wlezie.  
I dodatkowa notka: warsztat korzysta z biblioteki [RamdaJS](https://ramdajs.com/), ktorej API wyglada bardziej przejrzyscie i "typowo", anizeli zabawy z `Array.prototype.method_name`.


## Pure functions

Spelnione musza byc dwa warunki, zebysmy mogli otrzymac funkcje typu pure ("czysta"; w odroznieniu od impure).
1. Niezaleznie od tego, ile razy wywolamy funkcje, zawsze otrzymamy ten sam rezultat (output) przy tych samych
argumentach (input).
2. Funkcja nie stworzy zadnych efektow ubocznych (side-effects), np. nie wywola HTTP request, ktory bedzie
mial wplyw na inny system (serwer, baze danych, etc.).  
  

Przyklady:

```javascript
// funkcja otrzymuje jakis numer jako argument (n) i dodaje do niego 1
var addOne = n => n + 1

// wielokrotne wywolanie funkcji z tym samym argumentem (input) produkuje ten sam rezultat (output)
addOne(2) // 3
addOne(2) // 3
addOne(2) // 3
```

Kiedy zaczyna sie niebezpieczenstwo impure functions? Wtedy, gdy nasza funkcja moze zaczac produkowac rozne rezultaty.

```javascript
var someNum = 1

var addOne = n => n + someNum

addOne(2) // 3
addOne(2) // 3
addOne(2) // 3
someNum++
addOne(2) // 4
addOne(2) // 4
addOne(2) // 4
```

Albo gorzej!

```javascript
var someNum = 1

var addOne = n => { someNum++; return n + someNum }

addOne(2) // 4
addOne(2) // 5
addOne(2) // 6
```

Czy kazde uzycie jakiejs wartosci spoza funkcji sprawia, ze funkcja staje sie impure? Nie koniecznie.

```javascript
var someNum = () => 1

var addOne = n => n + someNum()

addOne(2) // 3
addOne(2) // 3
addOne(2) // 3
```

Teraz `someNum` tez jest czysta funkcja, wobec czego wyprodukowana wartosc nigdy sie nie zmieni! Pure awesomeness 😀


## Immutability

Jedna z najprzyjemniejszych cech funkcyjnego jezyka jest niezmiennosc danych (immutability). Niestety, wiekszosc danych w JS jest mutowalna (z wyjatkiem string, number, bool, null, i undefined). Na szczescie, Ramda library kopiuje wszystkie dane, a wiec transformuje dane zamiast je mutowac.  
  
Prosty przyklad:

```javascript
var KIDS_STUFF = ['ball', 'dinosaur', 'lollypop']

var stuff = R.map(x => ({name: x, used: true}), KIDS_STUFF)
```

`stuff` jest nowym array (po transformacji), ktory w zaden sposob nie wplywa na ksztalt `KIDS_STUFF` array. 



## First-class functions

Funkcje w JS, podobnie jak w jezykach funkcyjnych, sa funkcjami pierwszej kategorii (czasem nazywanymi tez obywatelami pierwszej kategorii (first-class citizens), tzn. funkcje moga brac inne funkcje jako argumenty (input), oraz zwracac funkcje (output).

```javascript
// przyklad funkcji (R.map(fn, []), ktora przyjmuje funkcje addOne(n) jako argument 
var addOne = n => n + 1

R.map(addOne, [1,2,3])  // [2,3,4]
```



## Higher-order functions

Typowymi funkcjami wyzszego rzedu, bez ktorych zaden jezyk funkcjny obyc sie nie moze, sa `map`, `filter`, oraz `reduce`.  
  

`map` pozwala nam transformowac dane w kolekcji.  
`filter` pozwala nam wybierac konkretne dane z kolekcji.  
`reduce` pozwala nam zredukowac dane z kolekcji.  
  

Przyklady:

```javascript
// map
R.map(x => x + x, [1,2,3])  // [2, 4, 6]

// filter
R.filter(x => x > 2, [1,2,3])  // [3]

// reduce
R.reduce((acc, x) => acc + x, 0, [1,2,3])  // (1+2+3) == 6
```



## Declarative code

Piszac JS w paradygmacie FP bedziemy (zupelnie jak jezyki funkcyjne) uzywac deklaratywnego, raczej niz imperatywnego kodu. Jaka jest roznica pomiedzy stylem imperatywnym a deklaratywnym (imperative vs declarative)? Najprosciej rzecz ujmujac, imperatywny styl zmusza programiste do pisania w stylu JAK (HOW). Natomiast deklaratywny styl pozwala na pisanie w stylu CO (WHAT). A wiec nie "jak mam to zrobic?", ale "co mam zrobic?" (gdyby komputer nas pytal 😉).  
  
Wysmienitym przykladem jest uzywanie funkcji `R.map` (ktora jest podobna do natywnej metody `Array.prototype.map`), ktora jest deklaratywna alternatywa do petli typu `for` (for loop), i wyglada nastepujaco:

```javascript
R.map(number => number * number, [1,2,3])  // [1,4,9]
```

`R.map` bierze liste liczb (`[1,2,3]`), i aplikuje anonimowa funkcje (`=>`) do kazdej z nich. Dla celow wylacznie edukacyjnych, tu jest kod, ktory dokona tego samego, ale z uzyciem nazwanej funkcji w miejscu anonimowej):

```javascript
dblNumber = number => number * number

[dblNumber(1), dblNumber(2), dblNumber(3)]  // [1,4,9]
```

Nie interesuje nas JAK `R.map` zmienia kazdy element podanej listy. Obchodzi nas jedynie, CO ma zostac zrobione.  
  
Gdybysmy pisali to w stylu imperatywnym, musielibysmy zrobic to pewnie tak:

```javascript
var arr = [1,2,3]
var arr2 = []

for (var i=0; i < arr.length; i++) {
  arr2.push(arr[i] * arr[i])
}

arr2  // [1,4,9]
```
  
Deklaratywny styl pozwala na pisanie mniej kodu, ktory w dodatku jest bardziej przejrzysty.



## Currying

Rozwijanie funkcji (lub 'currying') to proces konwertowania funkcji, ktora bierze wiele argumentow w funkcje, ktora bierze tylko jeden argument.  
  
Najlepiej pokaze to prosty przyklad:


```javascript
// zamiast napisac funkcje add w ten sposob
var add = (a, b) => a + b

// napiszemy ja tak
var add = a => b => a + b

// i wywolamy ja nastepujaco
var sum = add(1)(2)  // => 3

// nie musimy podawac jej wszystkich argumentow od razu, mozemy robic to stopniowo
var addOne = add(1)
addOne(2)  // => 3

// uzywajac funkcji z Ramdy, zrobmy to tak
var addOne = R.add(1)
addOne(2)  // => 3

// mala dygresja: jesli chcesz dodac 1, mozesz tez uzyc R.inc
R.inc(2)  // => 3
```


## Recursion

Notka: tu wyjatkowo przyklad przy uzyciu Python (gwoli przejrzystosci).  

Recursion to forma loopowania w FP. Funkcja rekursywna wywoluje sama siebie az jej kod jej nie zatrzyma.  
  
Typowy przyklad to funkcja `factorial`:

```python
def factorial(n):
    if n>1:
        x = n*factorial(n-1)
    else:
        x = 1
    return x

print(factorial(6))  # 6*5*4*3*2*1 = 720
```

## Popularne operacje na kolekcjach

Jezyki funkcyjne typowo maja w swoich podstawowych bibliotekach zbior funkcji do pracy na kolekcjach. Ramda.js nie jest inna.

```javascript
// head
// bierze pierwszy element z listy lub string
R.head(['fi', 'fo', 'fum']); //=> 'fi'
R.head('abc'); //=> 'a'


// tail
// odwrotnosc head. Zwraca cala liste lub string z wyjatkiem pierwszego elementu
R.tail([1, 2, 3]);  //=> [2, 3]
R.tail('abc');  //=> 'bc'


// zip
// laczy ze soba dwie listy w krotki (tuples). JS nie ma krotek, wiec otrzymujemy array of arrays.
R.zip([1, 2, 3], ['a', 'b', 'c']); //=> [[1, 'a'], [2, 'b'], [3, 'c']]


// take
// bierze liczbe elementow z listy zaczynajac od lewej
R.take(2, ['foo', 'bar', 'baz']); //=> ['foo', 'bar']


// drop
// przeciwienstwo 'take'. Odrzuca liczbe elementow z listy zaczynajac od lewej
R.drop(2, ['foo', 'bar', 'baz']); //=> ['baz']


// all
// sprawdza, czy wszystkie elementy listy zgadzaja sie z predykatem
R.all(R.equals(3))([3, 3, 1, 3]); //=> false


// any
// sprawdza, czy ktorykolwiek z elementow listy zgadza sie z predykatem
R.any(n => n < 0)([1, 2]); //=> false
R.any(R.flip(R.lt)(0))([1, 2]); //=> false
R.any(R.lt(0))([1, 2]); //=> true


// includes
// sprawdza, czy element jest w liscie/string
R.includes(3, [1, 2, 3]); //=> true
R.includes(4, [1, 2, 3]); //=> false
R.includes({ name: 'Fred' }, [{ name: 'Fred' }]); //=> true
R.includes([42], [[42]]); //=> true
R.includes('ba', 'banana'); //=>true
```


## Pipe oraz Compose

Funkcyjne jezyki czesto pozwalaja na 'pipe'owanie' oraz na 'function composition'. To pierwsze we [front-endowym jezyku Elm](http://elm-lang.org/) wyglada tak:  
  
```elm
100 |> (+) 2 |> (*) 10 |> negate   -- -1020
```

Natomiast kompozycja funkcji tak:

```elm
fn = (+) 2 >> (*) 10 >> negate
fn 100  -- -1020
```

Jako, ze JS nie ma takich operatorow, Ramda przychodzi nam z pomoca oferujac dwie funkcje: `R.pipe` i `R.compose`. Uzywamy ich nastepujaco:

```javascript
R.pipe(R.add(2), R.multiply(10), R.negate)(100)  // -1020

// oraz

R.compose(R.negate, R.multiply(10), R.add(2))(100)  // -1020
```

`pipe` pozwala nam przeslac dane kolejno przez wybrane funkcje, natomiast `compose` komponuje funkcje razem ('laczy' je), i pozwala przeslac do nich dane w formie argumentu, jakgdyby do jednej funkcji.



# Przykladowe uzycia


### Dodawanie do Listy

Dodawanie elementu do listy zawsze stworzy nowa liste.  
  
Dodajmy element do listy:

```javascript
R.append('tests', ['write', 'more']); //=> ['write', 'more', 'tests']
```

Mozemy tez polaczyc dwie listy lub strings:

```javascript
R.concat('ABC', 'DEF'); // 'ABCDEF'
R.concat([4, 5, 6], [1, 2, 3]); //=> [4, 5, 6, 1, 2, 3]
```



### Usuwanie z Listy

Usuwanie z listy wymaga uzycia funkcji z modulu `R.filter`. Filter pozwala nam 'filtrowac' elementy w liscie, i wybrac jedynie te, o ktore nam chodzi.   
Filtrowanie odbywa sie za pomoca anonimowej funkcji (lub nazwanej), ktora przyjmuje jeden argument (element z listy), a produkuje `boolean`.
Funkcja bedzie wywolana dla kazdego elementu listy.

W ponizszym przykladzie uzywamy operatora `(!==)`, a wiec 'nie rowna sie', i 'usuwamy' z listy liczbe `1`.

```javascript
R.filter(number => number !== 1, [1,2,3])  // [2,3]

// lub uzywaja R.pipe

R.filter(R.pipe(R.equals(1), R.not), [1,2,3])  // [2,3]
```

Mozemy smialo usunac z listy wiecej niz jeden element.  
  
W przykladzie ponizej wybieramy jedynie liczby, ktore rownaja sie 3 lub sa wieksze (operator `(>=)`).

```javascript
R.filter(R.flip(R.gte)(3), [1,2,3,4,5])  // [3,4,5]
```


### Update'owanie Listy

Czasem musimy zmienic konkretny element (lub elementy) w liscie lub zmienic nawet wszystkie elementy. W funkcyjnych jezykach do transformacji list sluzy nam funkcja `map`.  
  
Przyklad update'u wszystkich elementow listy:

```javascript
R.map(n => R.add(n, n), [1,2,3])  // [2,4,6]

R.map(person => {
    person.age = R.inc(person.age); 
    return person
  }, 
  [{name: "Tom", age: 22}, {name: "Dick", age: 33}, {name: "Mortimer", age: 44}]
)  
// [{name: "Tom", age: 23}, {name: "Dick", age: 34}, {name: "Mortimer", age: 45}]
```

Przyklad zmiany jedynie osoby `Tom`.

```javascript
R.map(person => {
    if (person.name === "Tom") {
      person.age = R.inc(person.age)
    }
    return person
  }, 
  [{name: "Tom", age: 22}, {name: "Dick", age: 33}, {name: "Mortimer", age: 44}]
)  
// [{name: "Tom", age: 23}, {name: "Dick", age: 33}, {name: "Mortimer", age: 44}]
```


### Wybieranie z Listy

Wybieranie z listy jest podobne do usuwania z listy. Zasadniczo jest to to samo (tez uzywamy funkcji `R.filter`). Postanowilem rozpisac to na dwa koncepty, bo tak dyktuje mi doswiadczenie w uczeniu nowych funkcyjnych programistow 😄.

```javascript
R.filter(str => str == "John", ["Mary", "Yousef", "John", "Mark"])  // ["John"]
```

Notka: Zauwaz prosze, ze wybierajac cos z listy, otrzymujemy nie sam element, ale liste z elementem (lub wieloma elementami).  



# Cwiczenia

Do cwiczen potrzebujesz biblioteki Ramda.js. Polecam uzyc
- [REPL](https://ramdajs.com/repl?v=0.27.0), lub
- [zainstalowac](https://ramdajs.com/) Ramde lokalnie

```javascript
// Dane:
// =====
var PEOPLE = [
  {name: "John", age: 10},
  {name: "Jack", age: 20},
  {name: "Jake", age: 25},
  {name: "Jesse", age: 39},
  {name: "Jill", age: 54}
]

// Cwiczenie 1:
// ============
// Uzyj R.compose() do uproszczenia ponizszej funkcji.
var theirName = function(people) {
  var the_last_person = R.last(people)
  return R.prop('name', the_last_person)
}

// Cwiczenie 2:
// ============
// Uzyj R.compose(), R.prop(), oraz R.head(), zeby zwrocic imie pierwszej osoby.
var nameOfTheFirstPerson = undefined

// Cwiczenie 3:
// ============
// Uzyj R.compose(), R.map(), R.inc(), oraz R.filter(), zeby postarzec kazda z osob o 1 rok, 
// i zwrocic tylko tych, ktorzy maja 25 lat lub mniej.
var onlyTwentyFiveYearOlds = undefined

// Cwiczenie 4:
// ============
// Uzyj R.map() do stworzenia nowego array na bazie PEOPLE array, ktorego kazdy 
// element bedzie wygladal tak: [name, age] (np. ["John", 10]).
var newPeople = undefined

// Cwiczenie 5:
// ============
// Uzyj R.pipe, R.innerJoin(), i R.reduce() oraz R.add() do stworzenia nowego array 
// na bazie PEOPLE array.
// 1. R.innerJoin() powinien zbudowac nowy array jedynie z osob o wieku 10, 25, i 54. 
// 2. Uzyj R.reduce() i R.add(), zeby zsumowac wiek ludzi.
//
// Poprawny wynik: 89
var summedUpAges = undefined

// Cwiczenie 6:
// ============
// Powtorz cwiczenie nr 5, z jednym wyjatkiem. Zamiast uzywac R.reduce() i R.add(),
// uzyj R.sum() do zsumowania.
//
// Poprawny wynik: 89
var summedUpAges2 = undefined

// Cwiczenie 7:
// ============
// Uzyj R.pipe() oraz R.sum, R.add, R.inc, R.negate, zeby
// - zsumowac array [2,3]
// - dodac do niego 3
// - zwiekszyc o jeden oraz zanegowac
//
// Poprawny wynik: -9
var newNumber = undefined


```


# Przyklady uzycia funkcyjnego paradygmatu

Reasumujac, kazdy z trzech jezykow (gdy porownamy go z funkcyjnymi jezykami) pozwala poslugiwac sie imperatywnym kodem, ktory niekoniecznie pomaga nam napisac bezpieczny, pewny, mozliwie zwiezly program. Dzis, smiem twierdzic :), z pomoca przychodzi nam programowanie funkcyjne. A zatem, FP zacheca nas do trzymania sie nastepujacych, podstawowych zasad, ktore powinnismy powtarzac jak mantre:
- trzymac sie z dala od mutacji
- trzymac sie z dala od efektow ubocznych
- trzymac sie z dala od stanu obiektow czy funkcji

Lekarstwem na powyzsze problemy jest trzymanie sie nastepujacych zasad:
- piszmy czyste funkcje
- piszmy deklaratywny kod
- piszmy male funkcje, z ktorych mozemy komponowac wieksze funkcje, z ktorych z kolei bedzie skomponowany nasz program
- trzymajmy sie niemutowalnych danych, a gdy chcemy cos zmienic, klonujmy kolekcje

## Mosty do innych jezykow

- Ruby do Elixir
- C# do F#
- Java do Scala
- JS do Elm
- mozliwe inne przejscia
  - np. Python do Elixir
  - PHP do Elixir (zwlaszcza z frameworku Laravel do frameworku [Phoenix](https://www.phoenixframework.org/))



# Uzyteczne linki

## JS biblioteki
* [RamdaJS](https://ramdajs.com/)
* [Flow](https://flow.org)
* [Immutable](https://immutable-js.github.io/immutable-js/)
* [Chai Immutable](https://github.com/astorije/chai-immutable)

## PHP
* [Functional Programming and PHP](https://www.sitepoint.com/functional-programming-and-php/)
* [Mozliwa implementacja `tail`, choc na wzor UNIXowy](https://tekkie.dev/php/tail-functionality-in-php)
* [biblioteka Tarsana Functional](https://github.com/tarsana/functional)

## Python
* [Pure functions](https://www.pythoninformer.com/programming-techniques/functional-programming/pure-functions/)
* [Map/reduce](https://www.pythoninformer.com/programming-techniques/functional-programming/map-reduce-example/)

## Ruby
* [Functional programming](https://womanonrails.com/functional-programming-ruby)
* [Functional Programming In Ruby (Complete Guide)](https://www.rubyguides.com/2018/01/functional-programming-ruby/)
* [Functional Programming in Ruby for people who don’t know what functional programming is](https://medium.com/@heyamberwilkie/functional-programming-in-ruby-for-people-who-dont-know-what-functional-programming-is-f0c4ab7dc68c)

## Frontendowe jezyki funkcyjne kompilujace sie do JS
* [Elm](https://elm-lang.org)
* [Purescript](https://www.purescript.org/)
* [Clojurescript](https://clojurescript.org/)

## Artykuly
* [Why Ramda?](https://fr.umio.us/why-ramda/)
* [Why curry helps?](https://hughfdjackson.com/javascript/why-curry-helps/)
* [Why OO Sucks by Joe Armstrong](http://harmful.cat-v.org/software/OO_programming/why_oo_sucks)
* [Idiom: First-class function : generic composition](https://www.programming-idioms.org/idiom/36/first-class-function-generic-composition)

## Materialy do dalszej nauki (JS)
* [Awesome FP JS](https://github.com/stoeffel/awesome-fp-js)
* [Learning Functional Programming with JavaScript - Anjana Vakil - JSUnconf](https://www.youtube.com/watch?v=e-5obm1G_FY)
* [6 best JavaScript Functional programming libraries](https://www.dunebook.com/best-javascript-functional-programming-libraries/)
