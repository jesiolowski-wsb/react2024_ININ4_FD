# Zadania z Programowania Funkcyjnego

## Zadanie 1: Kompozycja funkcji i currying

### Treść zadania

```javascript
/* 
Stwórz system formatowania tekstu używając kompozycji funkcji i currying.
Wymagania:
1. Stwórz następujące funkcje:
   - addHtmlWrapper - dodaje znacznik <html>
   - addFooter - dodaje znacznik <footer>
   - addBold - owija tekst znacznikiem <strong>
2. Każda funkcja powinna być currified
3. Użyj pipe z lodash do skomponowania tych funkcji
4. Przetestuj na tekście: "   Programowanie Funkcyjne   "

Przykładowy wynik:
<html><footer><strong>Programowanie Funkcyjne</strong></footer></html>
*/
```

### Rozwiązanie

```javascript
import { pipe } from 'lodash/fp'

// Funkcje currified
const addTag = tag => content => `<${tag}>${content}</${tag}>`

// Tworzenie specyficznych funkcji przy użyciu addTag
const addHtmlWrapper = addTag('html')
const addFooter = addTag('footer')
const addBold = addTag('strong')

// Funkcja trim do usunięcia białych znaków
const trim = str => str.trim()

// Kompozycja funkcji używając pipe
const formatText = pipe(
    trim,
    addBold,
    addFooter,
    addHtmlWrapper
)

// Test
const input = "   Programowanie Funkcyjne   "
console.log(formatText(input))
// Wynik: <html><footer><strong>Programowanie Funkcyjne</strong></footer></html>
```

## Zadanie 2: Czyste funkcje i niemutowalność

### Treść zadania

```javascript
/* 
Mamy system zarządzania biblioteką. Napisz czyste funkcje do:
1. Dodawania nowej książki do kolekcji
2. Aktualizacji statusu książki (wypożyczona/dostępna)
3. Usuwania książki z kolekcji

const przykladowaKolekcja = [
  { id: 1, tytul: "JavaScript", status: "dostępna" },
  { id: 2, tytul: "Python", status: "wypożyczona" }
];

WAŻNE: 
- Nie modyfikuj oryginalnej kolekcji
- Użyj spread operator lub map/filter
- Funkcje muszą być czyste (nie używaj Math.random, Date.now itp.)
*/
```

### Rozwiązanie

```javascript
import { pipe } from 'lodash/fp'

// Funkcja do dodawania nowej książki
const dodajKsiazke = (kolekcja, nowaKsiazka) => {
    // Generujemy nowe ID (w praktyce lepiej użyć UUID)
    const maxId = Math.max(...kolekcja.map(k => k.id))
    const ksiazkaZId = {
        ...nowaKsiazka,
        id: maxId + 1,
        status: 'dostępna' // domyślny status
    }
    return [...kolekcja, ksiazkaZId]
}

// Funkcja do aktualizacji statusu
const aktualizujStatus = (kolekcja, idKsiazki, nowyStatus) => {
    return kolekcja.map(ksiazka => 
        ksiazka.id === idKsiazki 
            ? { ...ksiazka, status: nowyStatus }
            : ksiazka
    )
}

// Funkcja do usuwania książki
const usunKsiazke = (kolekcja, idKsiazki) => {
    return kolekcja.filter(ksiazka => ksiazka.id !== idKsiazki)
}

// Przykład użycia:
const przykladowaKolekcja = [
    { id: 1, tytul: "JavaScript", status: "dostępna" },
    { id: 2, tytul: "Python", status: "wypożyczona" }
]

// Test funkcji
const nowaKsiazka = { tytul: "React" }
const zaktualizowanaKolekcja = pipe(
    kolekcja => dodajKsiazke(kolekcja, nowaKsiazka),
    kolekcja => aktualizujStatus(kolekcja, 1, "wypożyczona"),
)(przykladowaKolekcja)

console.log(zaktualizowanaKolekcja)
// Oryginalna kolekcja pozostaje niezmieniona
console.log(przykladowaKolekcja)
```

## Zadanie 3: Higher Order Functions

### Treść zadania

```javascript
/* 
Stwórz system filtrowania i transformacji danych używając HOF.
Dane wejściowe:
*/
const produkty = [
  { nazwa: "Laptop", cena: 3500, kategoria: "elektronika" },
  { nazwa: "Książka", cena: 45, kategoria: "literatura" },
  { nazwa: "Smartfon", cena: 1200, kategoria: "elektronika" }
];

/*
Wymagania:
1. Stwórz HOF o nazwie 'createProductFilter', która przyjmuje funkcję filtrującą
   i zwraca nową funkcję filtrującą produkty
2. Stwórz 3 funkcje filtrujące:
   - produkty tylko z konkretnej kategorii
   - produkty poniżej zadanej ceny
   - produkty zaczynające się od podanej litery
3. Użyj kompozycji funkcji, aby połączyć filtry

Przykład użycia:
const elektronikaPonizej2000 = compose(
  filterByCategory('elektronika'),
  filterByMaxPrice(2000)
);
*/
```

### Rozwiązanie

```javascript
import { compose } from 'lodash/fp'

// Higher Order Function do tworzenia filtrów
const createProductFilter = filterFn => products => products.filter(filterFn)

// Funkcje filtrujące
const filterByCategory = category => 
    createProductFilter(product => product.kategoria === category)

const filterByMaxPrice = maxPrice => 
    createProductFilter(product => product.cena <= maxPrice)

const filterByFirstLetter = letter => 
    createProductFilter(product => 
        product.nazwa.toLowerCase().startsWith(letter.toLowerCase())
    )

// Przykład użycia z kompozycją
const elektronikaPonizej2000 = compose(
    filterByCategory('elektronika'),
    filterByMaxPrice(2000)
)

// Użycie wielu filtrów
const laptopyPonizej4000 = compose(
    filterByFirstLetter('L'),
    filterByMaxPrice(4000),
    filterByCategory('elektronika')
)

// Test
const produkty = [
    { nazwa: "Laptop", cena: 3500, kategoria: "elektronika" },
    { nazwa: "Książka", cena: 45, kategoria: "literatura" },
    { nazwa: "Smartfon", cena: 1200, kategoria: "elektronika" }
]

console.log(elektronikaPonizej2000(produkty))
// Wyświetli tylko Smartfon, bo Laptop jest za drogi

console.log(laptopyPonizej4000(produkty))
// Wyświetli tylko Laptop
```

## Kluczowe koncepcje programowania funkcyjnego wykorzystane w rozwiązaniach:

1. **Immutability (niezmienność danych)**
   - Wszystkie operacje zwracają nowe kolekcje zamiast modyfikować istniejące
   - Użycie spread operatora do tworzenia kopii obiektów

2. **Kompozycja funkcji**
   - Użycie `pipe` i `compose` do łączenia funkcji
   - Tworzenie złożonych operacji z prostszych komponentów

3. **Currying**
   - Przekształcanie funkcji wieloargumentowych na serie funkcji jednoargumentowych
   - Ułatwienie częściowej aplikacji funkcji

4. **Higher Order Functions**
   - Funkcje przyjmujące inne funkcje jako argumenty
   - Funkcje zwracające inne funkcje
   - Tworzenie wyspecjalizowanych funkcji filtrujących

5. **Czyste funkcje**
   - Brak efektów ubocznych
   - Deterministyczne wyniki
   - Łatwiejsze testowanie i debugowanie
