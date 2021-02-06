# Renderowanie warunkowe

## `v-if`

Dyrektywa `v-if` jest używana do warunkowego renderowania bloku. Blok zostanie wyrenderowany tylko gdy wyrażenie dyrektywy zwróci wartość truthy.

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

Możliwe jest również dodanie bloku "else" poprzez `v-else`:

```html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

### Warunkowa grupa z `v-if` z użyciem `<template>`

Ponieważ `v-if` jest dyrektywną, musi zostać dołączona do jednego elementu. Co jeśli chcemy by dotyczyła wielu elementów? W tym przypadku możemy 
użyć `v-if` na elemencie `<template>`, który pełni rolę wrappera, który ma zostać niewyświetlony. Finalnie sam element `<template>` nie będzie wyrenderowany.


```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### `v-else`

Możesz użyć dyrektywę `v-else` do utworzenia bloku "else" dla `v-if`:

```html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```
Element z `v-else` musi wystąpić bezpośrednio za blokiem z `v-if` lub `v-else-if`, inaczej nie zostanie rozpoznany.

### `v-else-if`

Dyrektywa `v-else-if`, jak nazwa sugeruje, służy jako blok "else if" dla `v-if`. Może być użyty wielokrotnie:

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

Podobnie do `v-else`, element z dyrektywą `v-else-if` musi wystąpić bezpośrednio po elemencie z `v-if` lub `v-else-if`.

## `v-show`

Inną opcją do opcjonalnego wyświetlania elementu jest dyrektywa `v-show`, używa się podobnie:

```html
<h1 v-show="ok">Hello!</h1>
```

Różnica polega na tym, że element z `v-show` zawsze będzie renderowany i pozostanie w DOM, natomiast przełącza właściwość CSS `display` elementu na którym jest użyty.

`v-show` nie działa na elemencie `<template>`, ani nie współgra z `v-else` oraz `v-else-if`.

## `v-if`, a `v-show`

`v-if` to "prawdziwe" renderowanie warunkowe, ponieważ zapewnia że listenery i wszystkie komponenty potomne wewnątrz tego bloku będą poprawnie niszczone i rekreowane w zależności od spełnionego warunku.

`v-if` jest także **leniwy**: jeśli warunek nie jest spełniony w pierwszym renderowaniu, nie wyświetli nic -- blok warunkowe nie będzie wyrenderowany dopóki warunek nie będzie spełniony poraz pierwszy.

Dla porównaina `v-show` jest znacznie prostszy -- element jest zawsze renderowany niezależnie od stanu początkowego, przełącza jedynie właściwość CSS.

Podsumowując, `v-if` ma wyższy koszt przełączania, podczas gdy `v-show` ma wyższy koszt pierwotnego renderowania. Zalecane jest `v-show`, jeśli widoczność czegoś jest zmieniana bardzo częśto, `v-if` jeśli warunek prawdopodobnie nie będzie się zmieniał podczas działania.

## `v-if` z `v-for`

::: tip Wskazówka
Używanie `v-if` i `v-for` razem **nie jest rekomendowane**. Zobasz [wskazówkę](../style-guide/#avoid-v-if-with-v-for-essential) dla dalszych informacji.
:::
Kiedy `v-if` i `v-for` są użyte razem, `v-if` będzie ewaluowane pierwsze. Zobacz [list rendering guide](list#v-for-with-v-if).
