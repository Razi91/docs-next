# Podstawy komponentów

## Sztandarowy przykład

Oto przykład komponentu Vue:

```js
// Stwórz aplikację Vue
const app = Vue.createApp({})

// Zdefiniuj nowy globalny komponent zwany 'button-counter'
app.component('button-counter', {
  data() {
    return {
      count: 0,
    }
  },
  template: `
    <button @click="count++">
      You clicked me {{ count }} times.
    </button>`,
})
```

::: info
Pokazujemy tu bardzo prosty przykład, ale w typowej aplikacji Vue używamy komponentów w pliku. Więcej informacji znajdziesz [w tej sekcji](single-file-component.html).
:::

Komponenty to instancje wielokrotnego użytku, posiadające nazwę: w tym wypadku `<button-counter>`. Możemy skorzystac z tego komponentu jako własnego elementu w instancji głównego komponentu aplikacji:

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

```js
app.mount('#components-demo')
```

<common-codepen-snippet title="Component basics" slug="abORVEJ" tab="js,result" :preview="false" />

Ponieważ komponenty stanowią instancje wielokrotnego użytku, przyjmują takie same opcje co główna instancja, takie jak `data`, `computed`, `watch`, `methods`, oraz metody cyklu życia komponentów.

## Ponowne używanie komponentów

Możesz ponownie wykorzystywać komponenty tyle razy, ile zechcesz:

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

<common-codepen-snippet title="Component basics: reusing components" slug="rNVqYvM" tab="html,result" :preview="false" />

Zauważ, że klikając w te przyciski, każdy zachowuje swój odrębny `count`. Dzieje się tak, ponieważ za każdym razem, gdy korzystasz z komponentu, tworzona jest jego nowa **instancja**.

## Organizacja komponentów

Powszechne jest organizowanie komponentów w aplikacji na wzór drzewa:

![Component Tree](/images/components.png)

Przykładowo, aplikacja może posiadać komponent Nagłówka, Paska Bocznego i Strefy Zawartości, na którą składają się z reguły inne komponenty dla linków nawigacyjnych, wpisów na blogu, itd.

Aby korzystać z tych komponentów w szablonach, muszą być zarejestrowane, aby Vue wiedziało o ich istnieniu. Są dwa sposoby rejestracji komponentów: **globalny** oraz **lokalny**. Do tej pory rejestrowaliśmy nasze komponenty globalnie, używając do tego metody `component` naszej aplikacji.

```js
const app = Vue.createApp({})

app.component('my-component-name', {
  // ... options ...
})
```

Komponenty zarejestrowane globalnie mogą być wykorzystywane w szablonie każdego innego komponentu w aplikacji.

To wszystko, co musisz do tej pory wiedzieć o rejestrowaniu komponentów. Kiedy skończysz czytać tę stronę i opanujesz wszystkie poruszone zagadnienia, zalecamy powrócić do niej, aby przeczytać cały poradnik odnośnie [rejestracji komponentów](component-registration.md).

## Przekazywanie danych do komponentów-dzieci z wykorzystaniem props

Wcześniej wspomnieliśmy o tworzeniu komponentu dla wpisu na blogu. Problem polega na tym, że ten komponent będzie nieprzydatny, dopóki nie będziesz w stanie przekazać mu danych, takich jak tytuł, czy zawartość konkretnego wpisu do wyświetlenia. Tutaj w grę wchodzą _Props_ .

_Props_ (z ang. _Properties_, czyli _Własności_) to atrybuty, które możesz zarejestrować w komponencie. Aby przekazać własność _title_ komponentowi wpisu, możemy ją umieścić na liście atrybutów przyjmowanych przez komponent, za pomocą opcji `props`:

```js
const app = Vue.createApp({})

app.component('blog-post', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`,
})

app.mount('#blog-post-demo')
```

Gdy wartość jest przekazana atrybutowi z _props_, staje się dostępny w instancji komponentu, również z poziomu szablonu, tak jak każda inna własność komponentu.

Komponent może posiadać tyle `props`ów, ile chcesz. Domyślnie, każda wartość może być podana każdemu takiemu atrybutowi.

Kiedy już zarejestrujesz `prop`, możesz przekazać mu dane jak niżej:

```html
<div id="blog-post-demo" class="demo">
  <blog-post title="My journey with Vue"></blog-post>
  <blog-post title="Blogging with Vue"></blog-post>
  <blog-post title="Why Vue is so fun"></blog-post>
</div>
```

<common-codepen-snippet title="Component basics: passing props" slug="PoqyOaX" tab="html,result" :preview="false" />

Jednakże, w typowej aplikacji komponent będzie najprawdopodobniej posiadał tablicę wpisów w `data`:

```js
const App = {
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue' },
        { id: 2, title: 'Blogging with Vue' },
        { id: 3, title: 'Why Vue is so fun' },
      ],
    }
  },
}

const app = Vue.createApp(App)

app.component('blog-post', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`,
})

app.mount('#blog-posts-demo')
```

Później chcemy renderować komponent dla każdego wpisu:

```html
<div id="blog-posts-demo">
  <blog-post
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
  ></blog-post>
</div>
```

W przykładzie powyżej korzystamy z dyrektywy `v-bind`, aby dynamicznie przekazywać wartości do `props`ów. Jest to szczególnie przydatne, kiedy nie znasz dokładnie zawartości, którą trzeba będzie wyświetlić.

To wszystko, co musisz wiedzieć do tej pory o `props`ach. Kiedy skończysz czytać tę stronę i opanujesz wszystkie poruszone zagadnienia, zalecamy powrócić do niej, aby przeczytać cały poradnik odnośnie [propsów](component-props.html).

## Nasłuchiwanie zdarzeń komponentów-dzieci

W trakcie rozwijania komponentu `<blog-post>`, niektóre funkcje mogą wymagać komunikacji z komponentem-rodzicem. Przykładowo, możemy zdecydować się dodać możliwość powiększania rozmiaru czcionki wpisów, pozostawiając domyślny rozmiar w pozostałych elementach.

W komponencie-rodzicu, możemy zaimplementować tę funkcję, dodając własność `postFontSize` w data.

```js
const App = {
  data() {
    return {
      posts: [
        /* ... */
      ],
      postFontSize: 1,
    }
  },
}
```

Możemy ją później wykorzystać w szablonie, aby kontrolować rozmiar czcionki wszystkich wpisów.

```html
<div id="blog-posts-events-demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      :key="post.id"
      :title="post.title"
    ></blog-post>
  </div>
</div>
```

Teraz, dodajmy przycisk powiększający tekst każdego wpisu:

```js
app.component('blog-post', {
  props: ['title'],
  template: `
    <div class="blog-post">
      <h4>{{ title }}</h4>
      <button>
        Enlarge text
      </button>
    </div>
  `,
})
```

Problem polega na tym, że przycisk nic nie robi:

```html
<button>Enlarge text</button>
```

Klikając w ten przycisk musimy zakomunikować rodzicowi, że powinien powiększyć tekst wszystkich wpisów. Żeby rozwiązac ten problem, instancje komponentów posiadają własny system zdarzeń. Rodzic może wybrać, których zdarzeń nasłuchiwać, za pomocą `v-on` lub `@`, tak samo jak w przypadku natywnych zdarzeń DOM.

```html
<blog-post ... @enlarge-text="postFontSize += 0.1"></blog-post>
```

Następnie, komponent-dziecko może wyemitować zdarzenie, wywołując wbudowaną metodę [**`$emit`** method](../api/instance-methods.html#emit), przekazując w niej nazwę zdarzenia:

```html
<button @click="$emit('enlargeText')">Enlarge text</button>
```

Dzięki `@enlarge-text="postFontSize += 0.1"`, rodzic przechwyci zdarzenie i odpowiednio zmieni wartość `postFontSize`.

<common-codepen-snippet title="Component basics: emitting events" slug="KKpGyrp" tab="html,result" :preview="false" />

Możemy umieścić nazwy emitowanych zdarzeń przez komponent w opcji `emits`:

```js
app.component('blog-post', {
  props: ['title'],
  emits: ['enlargeText'],
})
```

Pozwala to na sprawdzanie wszystkich emitowanych zdarzeń, a także [sprawdzania ich poprawności](component-custom-events.html#validate-emitted-events).

### Emitowanie zdarzenia z wartością

Czasami przydatne jest emitowanie zdarzenia z konkretną wartością. Na przykład, możemy chcieć by komponent `<blog-post>` był odpowiedzialny za to, o ile powiększyć tekst wpisu. W takim wypadku możemy przekazać funkcji `$emit` drugi argument, który jest wartością emitowaną w zdarzeniu:

```html
<button @click="$emit('enlargeText', 0.1)">Enlarge text</button>
```

Kiedy nasłuchujemy zdarzenia w rodzicu, możemy odczytać wyemitowaną wartość za pomocą `$event`:

```html
<blog-post ... @enlarge-text="postFontSize += $event"></blog-post>
```

Jeśli obsługujemy zdarzenie używając funkcji:

```html
<blog-post ... @enlarge-text="onEnlargeText"></blog-post>
```

Emitowana wartość zostanie przekazana jako jej pierwszy argument:

```js
methods: {
  onEnlargeText(enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

### Używanie `v-model` w komponentach

Własne zdarzenia mogą być również wykorzystywane do tworzenia `input`ów działających z `v-model`. Pamiętaj, że:

```html
<input v-model="searchText" />
```

Działa tak samo jak:

```html
<input :value="searchText" @input="searchText = $event.target.value" />
```

Kiedy użyte na komponencie, `v-model` działa zaś tak:

```html
<custom-input
  :model-value="searchText"
  @update:model-value="searchText = $event"
></custom-input>
```

::: warning
Miej na uwadze, że skorzystaliśmy z `model-value` zapisanym w kebab-case, bo pracujemy na szablonach w DOMie. Szczegółowe wyjaśnienie różnic między nazywaniem atrybutów w kebab-case, a w camelCase znajdziesz w sekcji [Uwagi dotyczące parsowania szablonów DOM](#dom-template-parsing-caveats)
:::

Aby to zadziałało, `<input>` wewnątrz komponentu musi:

- Przypisać `value` do własności `modelValue`
- Wyemitować w `inpucie` zdarzenie `update:modelValue` z nową wartością

Oto jak wygląda taki komponent:

```js
app.component('custom-input', {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  template: `
    <input
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    >
  `,
})
```

Teraz `v-model` powinien działać z tym komponentem bez zarzutu:

```html
<custom-input v-model="searchText"></custom-input>
```

Innym sposobem implementacji `v-model` w tym komponencie, jest wykorzystanie możliwości definiowania metod typu getter i setter w `computed`. Metoda `get` powinna zwracać wartość atrybutu `modelValue`. Z kolei metoda `set` powinna emitować odpowiednie zdarzenie:

```js
app.component('custom-input', {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  template: `
    <input v-model="value">
  `,
  computed: {
    value: {
      get() {
        return this.modelValue
      },
      set(value) {
        this.$emit('update:modelValue', value)
      },
    },
  },
})
```

To wszystko, co musisz wiedzieć do tej pory o własnych zdarzeniach. Kiedy skończysz czytać tę stronę i opanujesz wszystkie poruszone zagadnienia, zalecamy powrócić do niej, aby przeczytać cały poradnik odnośnie [własnych zdarzeń](component-custom-events.md).

## Dystrybucja zawartości z użyciem slotów

Tak jak w elementach HTML, czasami chcemy przekazać komponentowi zawartość jak niżej:

```html
<alert-box> Something bad happened. </alert-box>
```

Może to wyświetlić coś takiego:

<common-codepen-snippet title="Component basics: slots" slug="jOPeaob" :preview="false" />

Żeby tego dokonać, wystarczy użyć elementu `<slot>`, dostarczanego przez Vue:

```js
app.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `,
})
```

Jak widać powyżej, korzystamy z elementu `<slot>` jak ze swoistego _elementu zastępczego_ dla naszej zawartości - tylko tyle i aż tyle!

To wszystko, co musisz wiedzieć do tej pory o slotach. Kiedy skończysz czytać tę stronę i opanujesz wszystkie poruszone zagadnienia, zalecamy powrócić do niej, aby przeczytać cały poradnik odnośnie [slotów](component-slots.md).

## Dynamiczne komponenty

Czasem przydatne jest przełączanie się między komponentami, jak chociażby w interfejsie zakładek:

<common-codepen-snippet title="Component basics: dynamic components" slug="oNXaoKy" :preview="false" />

Powyższe jest możliwe dzięki specjalnemu elementowi `<component>`, który posiada atrybut `is`:

```html
<!-- Komponent zmienia się wtedy, gdy zmienia się wartość currentTabComponent -->
<component :is="currentTabComponent"></component>
```

W tym przykładzie, `currentTabComponent` może zawierać albo:

- nazwę zarejestrowanego komponentu, albo
- obiekt opcji komponentu

Zobacz [tę piaskownicę](https://codepen.io/team/Vue/pen/oNXaoKy), aby poeksperymentować z całym kodem lub [tę wersję](https://codepen.io/team/Vue/pen/oNXapXM), gdzie komponenty są przypisywane za pomocą obiektu opcji, zamiast nazwy.

Możesz również użyć własności `is` do tworzenia zwykłych elementów HTML.

To wszystko, co musisz wiedzieć do tej pory o dynamicznych komponentach. Kiedy skończysz czytać tę stronę i opanujesz wszystkie poruszone zagadnienia, zalecamy powrócić do niej, aby przeczytać cały poradnik odnośnie [dynamicznych i asynchronicznych komponentów](./component-dynamic-async.html).

## Uwagi dotyczące parsowania szablonów DOM

Niektóre elementy, takie jak `<ul>`, `<ol>`, `<table>`, czy `<select>` mają pewne ograniczenia co do znaczników, które mogą się pojawić wewnątrz nich. Natomiast elementy pokroju `<li>`, `<tr>` lub `<option>` mogą występować jedynie wewnątrz konkretnych znaczników.

To może sprawić problemy podczas używania komponentów z elementami, które posiadają takie restrykcje:

```html
<table>
  <blog-post-row></blog-post-row>
</table>
```

Nasz własny komponent `<blog-post-row>` zostanie uznany za niewłaściwy, powodując błędy przy renderowaniu. Aby to obejść, możemy użyć dyrektywy `v-is`:

```html
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```

:::warning
Wartość `v-is` jest traktowana jak wyrażenie języka JavaScript, dlatego musimy otoczyć nazwę komponentu apostrofami:

```html
<!-- Niepoprawne, nic nie zostanie wyświetlone -->
<tr v-is="blog-post-row"></tr>

<!-- Poprawne -->
<tr v-is="'blog-post-row'"></tr>
```

:::

Co więcej, nazwy atrybutów w HTML nie rozróżniają wielkości liter, więc każda wielka litera zostanie potraktowana przez przeglądarki jako mała. To oznacza, że korzystając z szablonów wewnątrz DOMu, nazwy własności zapisane w camelCase oraz nazwy parametrów obsługujących zdarzenia muszą być zapisane w stylu kebab-case (tj. oddzielone myślnikami):

```js
// camelCase w JavaScript

app.component('blog-post', {
  props: ['postTitle'],
  template: `
    <h3>{{ postTitle }}</h3>
  `,
})
```

```html
<!-- kebab-case w HTML -->

<blog-post post-title="hello!"></blog-post>
```

Trzeba zaznaczyć, że **te ograniczenia _nie_ obowiązują, jeżeli używasz szablonów łańcuchowych z jednego z poniższych źródeł:**:

- Szablon łańcuchowy (e.g. `template: '...'`)
- [Komponent w pliku (`.vue`)](single-file-component.html)
- `<script type="text/x-template">`

To wszystko, co musisz wiedzieć do tej pory o uwagach dotyczących parsowania szablonów w DOM - tak naprawdę, to już koniec _Podstaw Vue_. Gratulacje! Wciąż jest dużo do nauki, ale najpierw, zachęcamy zrobić sobie przerwę, żeby samemu pobawić się z Vue i napisać coś fajnego.

Kiedy już opanujesz wszystkie zagadnienia, które przerobiliśmy, zachęcamy również do przeczytania całego poradnika odnośnie [dynamicznych i asynchronicznych komponentów](component-dynamic-async.html), ale też pozostałych stron w sekcji _Komponenty Od Kuchni_ w pasku bocznym.
