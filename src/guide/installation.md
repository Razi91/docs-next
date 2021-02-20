# Instalacja

Vue.js jest zaprojektowane w taki sposób, by być stopniowo przyswajalne. Oznacza to, że może zostać zintegrowane z projektem na wiele sposobów, w zależności od wymagań.

Są trzy sposoby na dodanie Vue.js do projektu:

1. Importowanie poprzez [CDN](#cdn)
2. Instalacja paczki z [npm](#npm)
3. Skorzystanie z oficjalnego [CLI](#cli) w celu stworzenia _rusztowania_ dostarczającego funkcjonalność obowiązkową dla nowoczesnej organizacji pracy frontend developera (np. odświeżanie aplikacji tuż po wprowadzeniu zmian w kodzie, wykrywanie błędów programistycznych i stylistycznych podczas zapisywania zmian i wiele innych)

## Informacje odnośnie wydania

Najnowsza wersja: ![npm](https://img.shields.io/npm/v/vue/next.svg)

Szczegóły dotyczące każdej wersji dostępne są na [GitHubie](https://github.com/vuejs/vue-next/blob/master/CHANGELOG.md).

## Vue Devtools

> Obecnie w fazie Beta - integracja z Vuex i Routerem nie została jeszcze zaimplementowana

Podczas pracy z Vue, zachęcamy również do zainstalowania [Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools) w twojej przeglądrce, pozwalających na inspekcję oraz debugowanie aplikacji Vue w bardziej przystępnym interfejsie.

[Pobierz wtyczkę dla przeglądarki Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/ljjemllljcmogpfapbkkighbhhppjdbg)

[Pobierz dodatek do przeglądarki Firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)

[Pobierz osobną aplikację](https://github.com/vuejs/vue-devtools/blob/dev/packages/shell-electron/README.md)

## CDN

W celu prototypowania lub nauki Vue, możesz skorzystać z najnowszego wydania dodjąc poniższy znacznik:

```html
<script src="https://unpkg.com/vue@next"></script>
```

## npm

Instalacja poprzez npm jest rekomendowaną metodą instalacji w przypadku pisania większych aplikacji. Vue działa bardzo dobrze z bundlerami takimi jak [Webpack](https://webpack.js.org/) czy [Rollup](https://rollupjs.org/). Co więcej, Vue dostarcza odpowiednia narzędzia do obsługi [Komponentów w Pliku](../guide/single-file-component.html) przez bundlery.

```bash
# latest stable
$ npm install vue@next
```

## CLI

Vue dostarcza [oficjalne CLI](https://github.com/vuejs/vue-cli) dla układania fundamentów pod ambitne SPA, wnosząc w gratisie narzędzia niezbędne do odpowiedniej organizacji pracy. Wystarczy kilka minut, aby móc korzystać z odświeżania aplikacji po zapisaniu zmian, sprawdzania ich pod kątem błędów, a także z buildów gotowych do użycia w produkcji. Więcej informacji znajdziesz w [dokumentacji Vue CLI](https://cli.vuejs.org).

::: tip
CLI zakłada posiadanie wiedzy w zakresie Node.js oraz powiązanych narzędzi do tworzenia buildów. Jeżeli podobne narzędzia jak i Vue są dla ciebie obce, zachęcamy do zapoznania się z [poradnikiem](./introduction.html) bez wykorzystywania tych narzędzi, zanim zaczniesz używać CLI.
:::

Dla Vue 3, powinno się korzystać z Vue CLI w wersji 4.5 lub nowszej dostępnej na `npm` jako `@vue/cli`. Aby dokonać aktualizacji, należy zainstalować ponownie `@vue/cli`:

```bash
yarn global add @vue/cli
# ALBO
npm install -g @vue/cli
```

Następnie, trzeba uruchomić poniższą komendę w projekcie aplikacji Vue:

```bash
vue upgrade --next
```

## Vite

[Vite](https://github.com/vitejs/vite) to narzędzie wykorzystywane do tworzenia buildów, pozwalające na błyskawiczne serwowanie kodu, dzięki importowaniu Modułów ES.

Można bardzo szybko stworzyć projekt Vue za pomocą Vite, używając poniższych komend w terminalu.

Z npm:

```bash
$ npm init @vitejs/app <project-name>
$ cd <project-name>
$ npm install
$ npm run dev
```

Lub z Yarn:

```bash
$ yarn create @vitejs/app <project-name>
$ cd <project-name>
$ yarn
$ yarn dev
```

## Omówienie poszczególnych buildów

W [folderze `dist/` pakietu npm](https://cdn.jsdelivr.net/npm/vue@3.0.2/dist/) znajdziesz wiele różnych buildów Vue.js. Oto ich przegląd, pokazujący z którego buildu skorzystać w jakich okolicznościach.

### Bezpośrenio z CDN albo bez bundlera

#### `vue(.runtime).global(.prod).js`:

- Do używania poprzez `<script src="...">` w przeglądarce.
- Kompilacja szablonów w przeglądarce:
  - `vue.global.js` to "pełny" build, który zawiera zarówno kompilator, jak i środowisko uruchomieniowe, dlatego ten build wspiera kompilowanie szablonów w locie.
  - `vue.runtime.global.js` zawiera tylko środowisko uruchomieniowe oraz wymaga wcześniejszej kompilacji szablonów przy tworzeniu builda.
- Optymalizuje wszystkie wewnętrzne moduły Vue - stanowi pojedynczy plik, niezależny od innych. To oznacza, że musisz importować wszystko wyłącznie z tego modułu, aby mieć pewność, że wszędzie korzystasz z tej samej instancji kodu.
- Posiada twardo zakodowane gałęzie prod/dev, gdzie ta pierwsza jest zminifikowana. W produkcji należy używać plików `*.prod.js`.

:::tip
Globalne buildy nie są buildami [UMD](https://github.com/umdjs/umd), lecz [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE), których trzeba używać poprzez `<script src="...">`.
:::

#### vue(.runtime).esm-browser(.prod).js:

- Do używania poprzez natywne wsparcie dla Modułów ES (w przeglądarce: `<script type="module">`).
- Wspóldzieli środowisko uruchomieniowe, dependency inlining oraz twardo zakodowane zachowanie prod/dev z globalym buildem.

### Z Bundlerem

#### vue(.runtime).esm-bundler.js:

- Do używania z bundlerami takimi jak `webpack`, `rollup` czy `parcel`.
- Pozostawia gałęzie prod/dev ze `strażnikami process.env.NODE_ENV` (musi zostać zmienione przez bundler)
- Nie dostarcza zminifikowanych buildów (musi być to zrobione włącznie z resztą kodu po stworzeniu bundla)
- Importuje zależności (np. `@vue/runtime-core`, `@vue/runtime-compiler`)
  - Zależności te są również buildami `esm-bundler`a, dlatego też importują własne zależności (np. `@vue/runtime-core` importuje `@vue/reactivity`)
  - Oznacza to, że **możesz** zainstalować/zaimportować te moduły indywidualnie, bez obaw o używanie ich różnych instancji, przy czym musisz się upewnić, że ich wersje się pokrywają.
- Kompilacja szablonów w przeglądarce:
  - `vue.runtime.esm-bundler.js` **(domyślny)** działa jedynie w środowisku uruchomieniowym i wymaga wcześniejszej kompilacji szablonów. To jest domyślne wejście dla bundlerów (poprzez pole `module` w `package.json`), ponieważ przy korzystaniu z bundlera, szablony są uprzednio kompilowane (np. w plikach `*.vue`).
  - `vue.esm-bundler.js`: zawiera kompilator środowiska uruchomieniowego.
    Należy z tego korzystać, jeżeli przy używaniu bundlera nadal chcesz kompilować szablony w czasie wykonywania (np. szablony w DOMie lub szablony w łańcuchu języka JavaScript). Potrzebna będzie wtedy odpowiednia konfiguracja bundlera, żeby przypisywał vue do tego pliku.

### Renderowanie po stronie serwera

#### `vue.cjs(.prod).js`:

- Do używania przy wykorzystaniu renderowania po stronie serwera w Node.js poprzez `require()`.
- Jeżeli stworzysz bundle za pomocą webpacka, ustawiając przy tym `target: 'node'` oraz odpowiednio uzewnętrznisz `vue`, ten build zostanie załadowane.
- Buildy plików prod/dev są uprzednio tworzone, ale odpowiedni plik jest wykorzystywany, w zależności od wartości `process.env.NODE_ENV`.

## Środowisko uruchomieniowe + Kompilator, a samo środowisko uruchomieniowe

Jeśli musisz kompilować szablony po stronie klienta (np. przekazując łańcuch w opcji template lub montując go w elemencie używając jego HTMLu wewnątrz DOMu jako szablonu), będziesz potrzebować kompilatora, a co za tym idzie, pełnego builda.

```js
// Wymaga kompilatora
Vue.createApp({
  template: '<div>{{ hi }}</div>'
})

// Nie wymaga kompilatora
Vue.createApp({
  render() {
    return Vue.h('div', {}, this.hi)
  }
})
```

Przy korzystaniu z `vue-loader`, szablony wewnątrz plików `*.vue` są uprzednio kompilowane do kodu języka JavaScript. Nie potrzebujesz kompilatora w ostatecznym buildzie, dlatego też możesz używać builda zawierającego tylko środowisko uruchomieniowe.
