# Wprowadzenie

::: tip Wskazówka
Już znasz Vue 2 i chcesz się tylko dowiedzieć co nowego w Vue 3? Sprawdź [Migration Guide](/guide/migration/introduction.html)!
:::

## Czym jest Vue.js?

Vue (wymowa /vjuː/, jak angielskie **view**, lub po polsku **wju**) jest **nowoczesnym frameworkiem** do budowania interfejsu użytkownika. 
W przeciwieństwie do innych monolitycznych frameworków, Vue jest zaprojektowany od podstaw by być stopniowo adaptacyjny. Rdzenna biblioteka skupiona jest tylko an warstwie widoku i jest łatwa do integracji z innymi bibliotekami lub istniejącymi projektami. Z drugiej strony Vue doskonale nadaje się do tworzenia aplikacji Single-Page Application przy użyciu [nowoczesnych narzędzi](../guide/single-file-component.html) oraz [bibliotek wspierających](https://github.com/vuejs/awesome-vue#components--libraries)

Jeśli chcesz nauczyć się więcej o Vue przed dalszym zagłębianiem się, <a id="modal-player" class="vuemastery-trigger"  href="#">nagraliśmy film</a> omawiający podstawowe zasady przykładowego projektu.

<VideoLesson href="https://www.vuemastery.com/courses/intro-to-vue-3/intro-to-vue3" title="Watch a free video course on Vue Mastery">Zobacz darmowe kursy video na Vue Mastery</VideoLesson>

<common-vuemastery-video-modal/>

## Getting Started

<p>
  <ActionLink class="primary" url="installation.html">
    Instalacja
  </ActionLink>
</p>

::: tip
Oficjalny przewodnik zakłada średnio zaawansowaną znajomość HTML, CSS oraz JavaScript. Jeśli dopiero zaczynasz z programowaniem frontend, być może to nie jest najlepszy pomysł by zaczynać od nauki frameworku -- zapoznaj się z podstawami i wróć! Wiedza lub doświadczenie z innych frameworków pomaga, ale nie jest wymagana.
:::

Najprostszą drogą do wypróbowania Vue.js jest użycie [Hello World example](https://codepen.io/team/Vue/pen/KKpRVpx). Otwórz w nowej zakładce i podążaj za kolejnymi podstawowymi przykładami.

Strona [Instalacja](installation.md) dostarcza więcej opcji instalacji Vue. **Nie rekomendujemy** początkującym zaczynania z `vue-cli`, zwłaszcza jeśli nie jest się obeznanym z narzędziami opartymi o Node.js.ld tools.

## Deklaratywny Rendering

Sercem Vue.js jest system pozwalający nam na deklaratywne renderowanie danych w DOM używając prostej składni szablonów:

```html
<div id="counter">
  Counter: {{ counter }}
</div>
```

```js
const Counter = {
  data() {
    return {
      counter: 0
    }
  }
}

Vue.createApp(Counter).mount('#counter')
```

Właśnie stworzyliśmy naszą pierwszą aplikację Vue! Wygląda podobnie do renderowania string template, ale Vue zrobił znacznie więcej pod maską. Dane oraz DOM są teraz połączone i wszystko jest **reaktywne**. Skąd to wiemy? Spójrzmy na przykład poniżej, gdzie wartość `counter` jest zwiększana co sekundę i jak renderowany DOM się zmienia: 

```js{8-10}
const CounterApp = {
  data() {
    return {
      counter: 0
    }
  },
  mounted() {
    setInterval(() => {
      this.counter++
    }, 1000)
  }
}
```

<FirstExample />

W dodatku do tekstowej wstawki, możemy też przypisać atrybuty w taki sposób:

```html
<div id="bind-attribute">
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds to see my dynamically bound
    title!
  </span>
</div>
```

```js
const AttributeBinding = {
  data() {
    return {
      message: 'You loaded this page on ' + new Date().toLocaleString()
    }
  }
}

Vue.createApp(AttributeBinding).mount('#bind-attribute')
```

<common-codepen-snippet title="Attribute dynamic binding" slug="KKpRVvJ" />

Tutaj spotykamy coś nowego. Atrybut `v-bind` który widzisz nazywa się **dyrektywą** (ang. _directive_). Dyrektywy są poprzedzane `v-` by oznaczyć że są specjalnymi atrybutami dostarczanymi przez Vue, jak się zapewne domyślasz, aplikują specjalne reaktywne zachowania do renderowanego DOMu. Tutaj mówimy "_utrzymuj atrybut `title` tego elementu aktualnie z wartością `message` obecnej aktywnej instancji._".

## Obsługa Wejścia Użytkownika

By pozwolić użytkownikowi oddziaływać z twoją aplikacją, możemy użyć dyrektywy `v-on` by przypiąć słuchacza zdarzenia (ang. _event listener_) który wywołują metody na naszej instancji:

```html
<div id="event-handling">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```

```js
const EventHandling = {
  data() {
    return {
      message: 'Hello Vue.js!'
    }
  },
  methods: {
    reverseMessage() {
      this.message = this.message
        .split('')
        .reverse()
        .join('')
    }
  }
}

Vue.createApp(EventHandling).mount('#event-handling')
```

<common-codepen-snippet title="Event handling" slug="dyoeGjW" />

Zauważ, że w tej metodzie aktualizujemy stan naszej aplikacji bez dotykania DOM, wszystkie manipulacje DOM są obsługiwane przez Vue, a kod który piszesz jest skupiony na samej logice leżącej pod spodem.

Vue dostarcza również dyrektywy `v-model`, która tworzy dwustronne wiązanie (ang. _two-way binding_) pomiędzy komponentem formularza, a stanem aplikacji w prosty sposób:

```html
<div id="two-way-binding">
  <p>{{ message }}</p>
  <input v-model="message" />
</div>
```

```js
const TwoWayBinding = {
  data() {
    return {
      message: 'Hello Vue!'
    }
  }
}

Vue.createApp(TwoWayBinding).mount('#two-way-binding')
```

<common-codepen-snippet title="Two-way binding" slug="poJVgZm" />

## Warunki i pętle

Można łatwo przełączać widoczność elementów:

```html
<div id="conditional-rendering">
  <span v-if="seen">Now you see me</span>
</div>
```

```js
const ConditionalRendering = {
  data() {
    return {
      seen: true
    }
  }
}

Vue.createApp(ConditionalRendering).mount('#conditional-rendering')
```

Ten przykład pokazuje że możemy wiązać dane nie tylko do tekstu i atrybutów, ale i **struktury** DOMu. Co więcej, Vue dostarcza potężnego systemu efektów przejść (ang. _transition effect system_) który automatycznie nakłada [efekty przejść](transitions-enterleave.md) gdy elementy są wstawiane/aktualizowane/usuwane przez Vue.

Możesz zmienić `seen` z `true` na `false` w piaskownicy poniżej, aby zobaczyć efekt:

<common-codepen-snippet title="Conditional rendering" slug="oNXdbpB" tab="js,result" />

Jest wiele dyrektyw, każda ma własną specjalną funkcjonalność. Na przykład dyrektywa `v-for` może być użyta do wyświetlania listy wielu pozycji używając danych z tablicy:

```html
<div id="list-rendering">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```js
const ListRendering = {
  data() {
    return {
      todos: [
        { text: 'Learn JavaScript' },
        { text: 'Learn Vue' },
        { text: 'Build something awesome' }
      ]
    }
  }
}

Vue.createApp(ListRendering).mount('#list-rendering')
```

<common-codepen-snippet title="List rendering" slug="mdJLVXq" />

## Komponowanie komponentami

System komponentów jest innym ważnym konceptem w Vue, ponieważ stanowi abstrakcję pozwalającą na budowanie dużych aplikacji skomponowanych z mniejszych, samowystarczalnych, często reużywalnych komponentów. Jeśli pomyślimy o tym, każdy typ interfejsu aplikacji może być rozrysowany jako drzewo komponentów:

![Component Tree](/images/components.png)

W Vue, komponent jest po prostu instancją z predefiniowanymi opcjami. Rejestracja komponentu w Vue jest prosta: tworzymy obiekt komponentu tak jak utworzyliśmy z obiektem `App`, oraz rejestrujemy komponent przy pomocy metody `component`.
<!-- trzeba ogarnąć tutaj, dokumentacja dość zagmatwana, jakby niezgodna z przykładem -->

In Vue, a component is essentially an instance with pre-defined options. Registering a component in Vue is straightforward: we create a component object as we did with `App` objects and we define it in parent's `components` option:

```js
// Create Vue application
const app = Vue.createApp(...)

// Define a new component called todo-item
app.component('todo-item', {
  template: `<li>This is a todo</li>`
})

// Mount Vue application
app.mount(...)
```

Teraz możemy wkomponować go w szablonie innego komponentu:

```html
<ol>
  <!-- Create an instance of the todo-item component -->
  <todo-item></todo-item>
</ol>
```
Ale to by wtrenderowało taki sam tekst dla każdego elementu listy, co nie jest zbyt interesujące, ani przydatne. Powinniśmy być w stanie przekazać dane z rodzica do komponentu. Zmodyfikujmy definicję komponentu, aby przyjął [prop](component-basics.html#passing-data-to-child-components-with-props):

[//]: # scope?

```js
app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})
```

Teraz możemy przekazać element zadania do każdego componentu uzywając `v-bind`:

```html
<div id="todo-list-app">
  <ol>
    <!--
      Now we provide each todo-item with the todo object
      it's representing, so that its content can be dynamic.
      We also need to provide each component with a "key",
      which will be explained later.
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
```

```js
const TodoList = {
  data() {
    return {
      groceryList: [
        { id: 0, text: 'Vegetables' },
        { id: 1, text: 'Cheese' },
        { id: 2, text: 'Whatever else humans are supposed to eat' }
      ]
    }
  }
}

const app = Vue.createApp(TodoList)

app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})

app.mount('#todo-list-app')
```

<common-codepen-snippet title="Intro-Components-1" slug="VwLxeEz" />

To wymyślony przykład, ale rozdzieliliśmy naszą aplikacje w dwie mniejsze jednostki i komponent potomny jest dobrze oddzielony od rodzica przez interfejs włąsności (_prop_). Teraz możemy dalej udoskonalać nasz komponent `<todo-item>` bardziej złożonymi szablonami nie oddziaływując na szablon komponentu rodzica i aplikacji.

W większych aplikacjach podział aplikacji na mniejsze komponenty jest niezbędny aby rozwój był możliwy. Porozmawiamy więcej o komponentach [w dalszej części przewodnika](component-basics.md), najpierw (wymyślony) przykład jak szablon aplikacji mógłby wyglądać:

```html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

### Stosunek do Custom Elements

Można zauważyc że komponenty Vue są bardzo podobne do **Custom Elements**, części [Web Components Spec](https://www.w3.org/wiki/WebComponents/). Jest to spowodowane tym, że komponenty Vue są luźno oparte na tej specyfikacji. Na przykład komponenty Vue implementują [Slot API](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) o specjalny atrybut `is`. Jednakże, jest kilka różnic:

1. Specyfikacja komponentów webowych została sfinalizowana, ale nie jest natywnie implementowana we wszystkich przeglądarkach. Safari 10.1+, Chrome 54+ oraz Firefox64+ natywnie wspierają komponenty webowe. Dla porównania, komponenty Vue działają konsekwentnie we wszystkich wspieranych przeglądarkach (IE11 i wzwyż). Kiedy potrzebne, komponenty Vue mogą być opakowywane wewnątrz natywnych **Custom Element**.

[//]: # 'TODO: link to compatibility build'

2. Komponenty Vue dostarczają ważnych funkcjonalnośći które nie są dostępne w Custom Elements, szczególnie kross-komponentowy przepływ danych, komunikacja przez custom events i zintegrowane narzędzia do budowania.

Mimo że Vue nie używa Custom Elements wewnętrznie, posiada [świetną interoperacyjność](https://custom-elements-everywhere.com/#vue) w kontekście konsumowania i dystrybuowania Custom Elements. Vue CLI także daje możliwość budowania komponentów Vue które rejestrują się jako Custom Elements.


## Chcesz więcej?

Zaledwie wprowadziliśmy najbardziej podstawowe cechy Vue.js, resztę obejmie ten poradnik wraz z  bardziej zaawansowanymi cechami i większymi szczegółami, więc koniecznie przeczytaj to wszystko!
