# Mantenimiento en React

Una vez que una aplicación en React crece, el mantenimiento se vuelve una prioridad. Para prepararse para esta eventualidad, cubriremos la optimización del rendimiento, la seguridad de tipos, las pruebas y la estructura del proyecto. Cada uno puede fortalecer su aplicación para que adquiera más funciones sin perder calidad.

La optimización del rendimiento evita que las aplicaciones se ralenticen al garantizar un uso eficiente de los recursos disponibles. Los lenguajes de programación tipados como TypeScript detectan errores antes en el ciclo de retroalimentación. Las pruebas nos brindan una retroalimentación más explícita que la programación escrita y proporciona una forma de comprender qué acciones pueden romper la aplicación. Por último, la estructura del proyecto admite la gestión organizada de activos en carpetas y archivos, lo que es especialmente útil en escenarios donde los miembros del equipo trabajan en diferentes dominios.

## Rendimiento en React (Avanzado)

Esta sección está aquí solo para aprender sobre las mejoras de rendimiento en React. No necesitaríamos optimizaciones en la mayoría de las aplicaciones de React, ya que React está listo para usarse. Si bien existen herramientas más sofisticadas para medir el rendimiento en JavaScript y React, nos apegaremos a un simple `console.log ()` y las herramientas de desarrollo de nuestro navegador para el registro de salidas.

### No ejecutes al primer renderizado

Anteriormente cubrimos el useEffect Hook de React, que se utiliza para efectos secundarios. Se ejecuta la primera vez que un componente se renderiza (monta) y luego cada nueva renderización (actualización). Al pasarle un arreglo de dependencia vacía como segundo argumento, podemos decirle al hook que se ejecute solo en el primer renderizado. De manera inmediata, no hay forma de decirle al hook que se ejecute solo en cada re-renderización (actualización) y no en la primera renderización (montaje). Por ejemplo, examina este hook personalizado para la administración del estado con el hook useState de React y su estado semipersistente con almacenamiento local usando el hook useEffect de React:

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (key, initialState) => {
  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
# leanpub-start-insert
    console.log('A');
# leanpub-end-insert
    localStorage.setItem(key, value);
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

Mirando más de cerca las herramientas de desarrollador, podemos ver el registro de la primera vez que se renderizó el componente usando este hook personalizado. No tiene sentido ejecutar el efecto secundario para la representación inicial del componente, porque no hay nada que guardar en el localStorage excepto el valor inicial. Es una invocación redundante de la función y solo debe ejecutarse para cada actualización (re-renderización) del componente.

Como se mencionó, no hay ningún React Hook que se ejecute en cada re-renderizado, y no hay forma de decirle al hook `useEffect` en una forma idiomática de React que llame a su función solo en cada re-renderizado. Sin embargo, al usar el useRef Hook de React, que mantiene intacta su propiedad `ref.current` durante las repeticiones de renderizado, podemos mantener un *estado inventado* (sin volver a renderizar el componente en las actualizaciones de estado) del ciclo de vida de nuestro componente:

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (key, initialState) => {
# leanpub-start-insert
  const isMounted = React.useRef(false);
# leanpub-end-insert

  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
# leanpub-start-insert
    if (!isMounted.current) {
      isMounted.current = true;
    } else {
# leanpub-end-insert
      console.log('A');
      localStorage.setItem(key, value);
# leanpub-start-insert
    }
# leanpub-end-insert
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

Estamos explotando el `ref` y su propiedad mutable `current` para una gestión de estado imperativa que no desencadena un re - renderizado. Una vez que el hook es llamado desde su component por primera vez (renderizado de componente), la referencia `current` se inicializa con un booleano `false` llamado `isMounted`. Como resultado, la función de efecto secundario en `useEffect` no es llamada; solo el booleano `isMounted` se cambia a `true` en el efecto secundario. Cuando el hook se ejecuta de nuevo (re renderizado de componente), el booleano es evaluado en el efecto secundario. Puesto que es `true`, la función de efecto secundario se ejecuta. Durante el ciclo de vida del componente, el booleano `isMounted` seguirá siendo `true`. Estaba ahí para evitar llamar a la función de efecto secundario la primera vez que nuestro componente se renderiza usando nuestro hook personalizado.

Lo anterior fue solo para evitar la invocación de una simple función para un componente que se renderiza por primera vez. Pero imagina que tienes una operación computacional compleja en tu efecto secundario, o que el hook personalizado es usado frecuentemente en la aplicación. Es más práctico implementar esta técnica para evitar invocaciones innecesarias de funciones.

*Nota: Esta técnica no es solo utilizada para optimizaciones de rendimiento, sino por el hecho de tener un efecto secundario que se ejecuta solo cuando un componente se re renderiza. Lo usé bastantes veces, y sospecho que te encontrarás con un caso u otro eventualmente.*

### No re-renderices si no es necesario

Anteriormente, exploramos el mecanismo de re renderizado de React. Repetiremos este ejercicio para los componentes App y Lista. Para ambos componentes, agrega un *log*.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  console.log('B:App');
# leanpub-end-insert

  return ( ... );
};

const List = ({ list, onRemoveItem }) =>
# leanpub-start-insert
  console.log('B:List') ||
# leanpub-end-insert
  list.map(item => (
    <Item
      key={item.objectID}
      item={item}
      onRemoveItem={onRemoveItem}
    />
  ));
~~~~~~~

Puesto que el componente Lista no tiene cuerpo en la función, y los desarrolladores son personas flojas quienes no quieren rehacer el componente para un simple enunciado con *log*, el componente Lista utiliza el operador `||`. Este es un buen truco para agregar un enunciado *log* a un componente funcional sin un cuerpo en la función. Puesto que el `console.log()` del lado izquierdo de la operación siempre se evalúa a falso, el lado derecho del operador siempre se ejecuta.

{title="Code Playground",lang="javascript"}
~~~~~~~
function getTheTruth() {
  if (console.log('B:List')) {
    return true;
  } else {
    return false;
  }
}

console.log(getTheTruth());
// B:List
// false
~~~~~~~

Enfoquemosnos en el resultado que se muestra en las herramientas de desarrollador del navegador. Deberías ver un resultado similar. Primero el componente App se renderiza, seguido de sus componentes hijos (p. ej. el componente Lista).

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
B:App
B:App
B:List
~~~~~~~

Dado que un efecto secundario desencadena una búsqueda de datos después del primer renderizado, solo el componente App se renderiza, porque el componente Lista es reemplazado por un indicador de carga en un renderizado condicional. Una vez que los datos llegan, ambos componentes se renderizan nuevamente.

{title="Visualization",lang="text"}
~~~~~~~
// initial render
B:App
B:List

// data fetching with loading
B:App

// re-rendering with data
B:App
B:List
~~~~~~~

Hasta ahora, este comportamiento es aceptable, dado que todo se renderiza a tiempo. Ahora tomaremos este experimento un paso más adelante escribiendo en el campo de texto de entrada del componente SearchForm. Deberías ver los cambios con cada caracter puesto en el elemento:

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

Pero el componente Lista no debería re renderizarse. La característica de búsqueda no es ejecutada a través de su botón, así que la `lista` pasada al componente List debería permanecer igual. Este es un comportamiento de React por defecto, lo cual sorprende a mucha gente.

Si un componente padre se re renderiza, sus componentes hijos se re renderizan también. React hace esto por defecto porque prevenir un re renderizado de los componentes hijos podría llevar a errores, y el mecanismo de re renderizado de React sigue siendo rápido.

Algunas veces, sin embargo, queremos prevenir el re renderizado. Por ejemplo, grandes cantidades de datos puestos en una tabla no se deberían re renderizar si no son afectados por una actualización. Es más eficiente realizar una verificación de igualdad si algo cambió para el componente. Entonces, podemos usar la API memo de React para hacer esta verificación de igualdad para las props:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = React.memo(
# leanpub-end-insert
  ({ list, onRemoveItem }) =>
    console.log('B:List') ||
    list.map(item => (
      <Item
        key={item.objectID}
        item={item}
        onRemoveItem={onRemoveItem}
      />
    ))
# leanpub-start-insert
);
# leanpub-end-insert
~~~~~~~

However, the output stays the same when typing into the SearchForm's input field:

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

The `list` passed to the List component is the same, but the `onRemoveItem` callback handler isn't. If the App component re-renders, it always creates a new version of this callback handler. Earlier, we used React's useCallback Hook to prevent this behavior, by creating a function only on a re-render (if one of its dependencies has changed).

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleRemoveStory = React.useCallback(item => {
# leanpub-end-insert
    dispatchStories({
      type: 'REMOVE_STORY',
      payload: item,
    });
# leanpub-start-insert
  }, []);
# leanpub-end-insert

  ...

  console.log('B:App');

  return (... );
};
~~~~~~~

Since the callback handler gets the `item` passed as an argument in its function signature, it doesn't have any dependencies and is declared only once when the App component initially renders. None of the props passed to the List component should change now. Try it with the combination of `memo` and `useCallback`, to search via the SearchForm's input field. The "B:List" output disappears, and only the App component re-renders with its "B:App" output.

![](images/memo.png)

While all props passed to a component stay the same, the component renders again if its parent component is forced to re-render. That's React's default behavior, which works most of the time because the re-rendering mechanism is fast enough. However, if re-rendering decreases the performance of a React application, `memo` helps prevent re-rendering.

Sometimes `memo` alone doesn't help, though. Callback handlers are re-defined each time in the parent component and passed as *changed* props to the component, which causes another re-render. In that case, `useCallback` is used for making the callback handler only change when its dependencies change.

### Don't rerun expensive computations

Sometimes we'll have performance-intensive computations in our React components -- between a component's function signature and return block -- which run on every render. For this scenario, we must create a use case in our current application first.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const getSumComments = stories => {
  console.log('C');

  return stories.data.reduce(
    (result, value) => result + value.num_comments,
    0
  );
};
# leanpub-end-insert

const App = () => {
  ...

# leanpub-start-insert
  const sumComments = getSumComments(stories);
# leanpub-end-insert

  return (
    <div>
# leanpub-start-insert
      <h1>My Hacker Stories with {sumComments} comments.</h1>
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

If all arguments are passed to a function, it's acceptable to have it outside the component. It prevents creating the function on every render, so the `useCallback` hook becomes unnecessary. The function still computes the value of summed comments on every render, which becomes a problem for more expensive computations.

![](images/usememo-1.png)

Each time text is typed in the input field of the SearchForm component, this computation runs again with an output of "C". This may be fine for a non-heavy computation like this one, but imagine this computation would take more than 500ms. It would give the re-rendering a delay, because everything in the component has to wait for this computation. We can tell React to only run a function if one of its dependencies has changed. If no dependency changed, the result of the function stays the same. React's useMemo Hook helps us here:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const sumComments = React.useMemo(() => getSumComments(stories), [
    stories,
  ]);
# leanpub-end-insert

  return ( ... );
};
~~~~~~~

For every time someone types in the SearchForm, the computation shouldn't run again. It only runs if the dependency array, here `stories`, has changed. After all, this should only be used for cost expensive computations which could lead to a delay of a (re-)rendering of a component.

![](images/usememo-2.png)

Now, after we went through these scenarios for `useMemo`, `useCallback`, and `memo`, remember that these shouldn't necessarily be used by default. Apply these performance optimization only if you run into a performance bottlenecks. Most of the time this shouldn't happen, because React's rendering mechanism is pretty efficient by default. Sometimes the check for utilities like `memo` can be more expensive than the re-rendering itself.

### Ejercicios:

* Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Performance-in-React).
  * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Performance-in-React?expand=1).
* Read more about [React's memo API](https://reactjs.org/docs/react-api.html#reactmemo).
* Read more about [React's useCallback Hook](https://reactjs.org/docs/hooks-reference.html#usecallback).
* Download *React Developer Tools* as an extension for your browser. Open it for your application in the browser via the browser's developer tools and try its various features. For example, you can use it to visualize React's component tree and its updating components.
* Does the SearchForm re-render when removing an item from the List with the "Dismiss" button? If it's the case, apply performance optimization techniques to prevent re-rendering.
* Does each Item re-render when removing an item from the List with the "Dismiss" button? If it's the case, apply performance optimization techniques to prevent re-rendering.
* Remove all performance optimizations to keep the application simple. Our current application doesn't suffer from any performance bottlenecks. Try to avoid [premature optimzations](https://en.wikipedia.org/wiki/Program_optimization). Use this section as reference, in case you run into performance problems.
