## React Imperativo

React es inherentemente declarativo, comienza con JSX y termina con hooks. En JSX, le decimos a React *qué* renderizar y no *cómo* renderizarlo. En un Hook de efecto secundario (useEffect), expresamos cuándo lograr *qué* en lugar de *cómo* lograrlo. Algunas veces, sin embargo, queremos acceder los elementos renderizados de JSX imperativamente, en casos como estos:

* acceso de lectura/escritura a los elementos via DOM API:
  * medir (leer) el ancho o alto de un elemento.
  * establecer (escribir) el estado focus de un campo tipo input.
* implementación de animaciones más complejas:
  * configurar transiciones
  * orquestar transiciones
* integración de librerías de terceros:
  * [D3](https://d3js.org/) es una librería de gráficas, imperativa y muy popular.

Debido a que la programación imperativa en React es a menudo detallada y contraintuitiva, solo veremos un pequeño ejemplo para establecer el enfoque de un campo de entrada (input) de manera imperativa. Para la forma declarativa, simplemente configura el atributo autofocus del input:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => (
  <>
    <label htmlFor={id}>{children}</label>
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
# leanpub-start-insert
      autoFocus
# leanpub-end-insert
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

Esto funciona, pero solo si uno de los componentes reusables se renderiza una vez. Por ejemplo, si el componente App renderiza dos componentes InputWithLabel, solo el último componente renderizado recibe el auto-focus en su renderizado. Sin embargo, como tenemos un componente reusable aquí, podemos pasar una prop dedicada y dejar que el desarrollador decida si su elemento input debería tener auto-focus o no:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      <h1>Mis Historias de Hacker</h1>

      <InputWithLabel
        id="search"
        value={searchTerm}
# leanpub-start-insert
        isFocused
# leanpub-end-insert
        onInputChange={handleSearch}
      >
        <strong>Search:</strong>
      </InputWithLabel>

      ...
    </div>
  );
};
~~~~~~~

Using just `isFocused` as an attribute is equivalent to `isFocused={true}`. Within the component, use the new prop for the input field's `autoFocus` attribute:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
# leanpub-start-insert
  isFocused,
# leanpub-end-insert
  children,
}) => (
  <>
    <label htmlFor={id}>{children}</label>
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
# leanpub-start-insert
      autoFocus={isFocused}
# leanpub-end-insert
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

La implementación funciona, pero sigue siendo una implementación declarativa. Le estamos diciendo a React *qué* hacer y no *cómo* hacerlo. Aunque es posible hacerlo con el enfoque declarativo, refactoricemos este escenario a un enfoque imperativo. Queremos ejecutar el método `focus()` programáticamente a través de API DOM vía input una vez que ha sido renderizado:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
  isFocused,
  children,
}) => {
# leanpub-start-insert
  // A
  const inputRef = React.useRef();
# leanpub-end-insert

# leanpub-start-insert
  // C
  React.useEffect(() => {
    if (isFocused && inputRef.current) {
      // D
      inputRef.current.focus();
    }
  }, [isFocused]);
# leanpub-end-insert

  return (
    <>
      <label htmlFor={id}>{children}</label>
      &nbsp;
# leanpub-start-insert
       {/* B */}
# leanpub-end-insert
      <input
# leanpub-start-insert
        ref={inputRef}
# leanpub-end-insert
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
      />
    </>
  );
};
~~~~~~~

Todos los pasos esenciales están marcados con comentarios que son explicados paso a paso:

* (A) First, create a `ref` with **React's useRef hook**. This `ref` object is a persistent value which stays intact over the lifetime of a React component. It comes with a property called `current`, which, in contrast to the `ref` object, can be changed.
* (B) Second, the `ref` is passed to the input field's JSX-reserved `ref` attribute and the element instance is assigned to the changeable `current` property.
* (C) Third, opt into React's lifecycle with React's useEffect Hook, performing the focus on the input field when the component renders (or its dependencies change).
* (D) And fourth, since the `ref` is passed to the input field's `ref` attribute, its `current` property gives access to the element. Execute its focus programmatically as a side-effect, but only if `isFocused` is set and the `current` property is existent.

This was an example of how to move from declarative to imperative programming in React. It's not always possible to go the declarative way, so the imperative approach can be whenever it's necessary. This lesson is for the sake of learning about the DOM API in React.

### Ejercicios:

* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Imperative-React).
  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Component-Composition...hs/Imperative-React?expand=1).
* Read more about [React's useRef Hook](https://reactjs.org/docs/hooks-reference.html#useref).