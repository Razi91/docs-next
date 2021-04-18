# Rejestracja Komponentów

> Ta strona zakłada, że już przeczytałeś/aś stronę [Podstawy Komponentów](component-basics.md). Jeżeli komponenty są dla ciebie nowością, przejdź to wspomnianej strony.

## Nazwy Komponentów

Komponentowi zawsze nadawana jest określona nazwa. Widzieliśmy to już podczas globalnej rejestracji:

```js
const app = Vue.createApp({...})

app.component('my-component-name', {
  /* ... */
})
```

Nazwa komponentu to pierwszy argument funkcji `app.component`. W powyższym przkładzie jest to "my-component-name".

Nazwa, którą nadasz komponentowi może zależeć od tego, gdzie go zamierzasz wykorzystywać. Przy używaniu bezpośrednio w DOMie (w przeciwieństwie do szablonów w łańcuchu albo [komponentów w pliku](../guide/single-file-component.html)), zalecamy przestrzegać [zasad wytyczonych przez W3C](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) dla nazw swoich własnych komponentów:

1. Nazwa ta musi zawierać wyłącznie małe litery
2. Może także posiadać myślnik(i) (tj., składać się z wielu wyrazów oddzielonych myślnikami)

Pomoże to uniknąć ewentualnych konfliktów z obecnymi oraz przyszłymi elementami HTML

Więcej zaleceń odnośnie nazewnictwa komponentów znajdziesz w [Poradniku Stylistycznym](../style-guide/#base-component-names-strongly-recommended).

### Casing

Podczas definiowania komponentów w szablonie łańcuchowym, czy też komponentu w pliku, istnieją dwa sposoby ich nazywania:

#### Z użyciem kebab-case

```js
app.component('my-component-name', {
  /* ... */
})
```

Definiując komponenty z użyciem kebab-case, musisz z niego korzystać również przy odwoływaniu się do jego elementu, np. `<my-component-name>`.

#### Z użyciem PascalCase

```js
app.component('MyComponentName', {
  /* ... */
})
```

Podczas definiowania komponentu z użyciem PascalCase, możesz korzystać z dowolnego casingu przy odwoływaniu się do elementu. Oznacza to, że zarówno `<my-component-name>` jak i `<MyComponentName>` są dopuszczalne. Warto zaznaczyć, że tylko to pierwsze jest dozwolone dla odniesień bezpośrednio w DOMie.

## Globalna rejestracja

Do tej pory tworzyliśmy komponenty wyłącznie za pomocą metody `app.component`

```js
Vue.createApp({...}).component('my-component-name', {
  // ... opcje ...
})
```

Tak utworzone komponenty były **globalnie dostępne** dla aplikacji. To znaczy, że mogą być użyte w szablonie dowolnego innego komponentu:

```js
const app = Vue.createApp({})

app.component('component-a', {
  /* ... */
})
app.component('component-b', {
  /* ... */
})
app.component('component-c', {
  /* ... */
})

app.mount('#app')
```

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

Dotyczy to również wszystkich podkomponentów, co oznacza, że każdy z tych trzech powyższych komponentów będzie dostępny _wewnątrz siebie nawzajem_.

## Lokalna rejestracja

Globalna rejestracja nie stanowi zbyt często idealnego rozwiązania. Dla przykładu, jeśli korzystasz z narzędzia takiego jak Webpack, rejestrowanie komponentów w taki sposób może sprawić, że nawet te nieużywane mogą zostać zawarte w buildzie. Niepotrzebnie zwiększa to ilość kodu JavaScript wymaganego do pobrania przez użytkownika.

W takich przypadkach możesz zdefiniować komponenty jako zwykłe obiekty JavaScript:

```js
const ComponentA = {
  /* ... */
}
const ComponentB = {
  /* ... */
}
const ComponentC = {
  /* ... */
}
```

A następnie wybrać te, z których chesz skorzystać, we opcji `components`:

```js
const app = Vue.createApp({
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

Dla każdej własności obiektu `components`, klucz obiektu będzie stanowił nazwę własnego elementu, podczas gdy wartość pod danym kluczem zawierać będzie opcje dla takiego komponentu.

Miej na uwadze, że **lokalnie zarejestrowane komponenty _nie są_ dostępne w podkomponentach**. Przykładowo, jeśli chcesz, aby `ComponentA` był dostępny w `ComponentB`, musiał(a)byś użyć:

```js
const ComponentA = {
  /* ... */
}

const ComponentB = {
  components: {
    'component-a': ComponentA
  }
  // ...
}
```

Albo jeżeli korzystasz z modułów ES2015, jak w Babel i Webpack, powyższe może wyglądać następująco:

```js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
  // ...
}
```

Zauważ, że w ES2015+, umieszczanie nazwy zmiennej jak `ComponentA` wewnątrz obiektu, stanowi skrócony zapis dla `ComponentA: ComponentA` co znaczy, że nazwa zmiennej stanowi zarówno nazwę elementu do wykorzystania w szablonie, jak i obiekt zawierający opcje komponentu.

## Moduły

Jeśli nie korzystasz z modułów za pomocą `import`/`require`, to możesz na razie pominąć tę sekcję. W przeciwnym razie, mamy dla ciebie specjalne rady.

### Lokalna rejestracja z użyciem modułów

Jeżeli wciąż tu jesteś, to najpewniej korzystasz z modułów, jak w narzędziach pokroju Babel, czy Webpack. W takich wypadkach zalecamy utworzenie folderu `components`, gdzie każdy plik stanowi odrębny komponent.

Następnie musisz zaimportować każdy komponent, którego będziesz chciał(a) użyć, zanim go lokalnie zarejestrujesz. Na przykład, w hipotetycznym pliku `ComponentB.js` lub `ComponentB.vue`:

```js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  }
  // ...
}
```

Teraz `ComponentA` i `ComponentC` mogą być wykorzystane w szablonie `ComponentB`.
