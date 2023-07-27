# Estilos en React

Hay muchas maneras de aplicar estilos a una aplicación React, y hay debates extensos sobre cuál es la mejor  **estrategia** y **enfoque** para aplicar estilos. Vamos a revisar unas cuantas de esas estrategias, cada una representando un enfoque sin darle mucho peso. Habrán argumentos a favor y en contra, pero como siempre, es cuestión de qué se ajusta mejor al equipo de desarrolladores y sus equipos.

Vamos a comenzar con el común CSS en React, pero después se van a explorar dos estrategias alternativas tales como **CSS-in-CSS** (con **Módulos CSS**) y **CSS-in-JS** (con **Styled Components**). Módulos CSS y Styled Components son dos enfoques entre muchos que pertenecen a ambos grupos de estrategias. También vamos a entender como se pueden incluir Gráficos Escalables de vectores (SVGs por sus siglas en Inglés), tales como un logo o ícono, en nuestra aplicación React.

![](images/css-style-strategies.png)

Si no quieres construir componentes de UI comúnes (e.g. botón, diálogo, dropdown) desde 0, puedes utilizar una [librería de UI popular compatible con React](https://www.robinwieruch.de/react-libraries/), que brinda acceso a todos estos componentes por defecto. Sin embargo, para aprender React es mejor construir todos estos componentes en lugar de utilizar soluciones pre-construidas. Como resultado, no usaremos ninguna librería de componentes UI.

![](images/ui-library.png)

Los siguientes enfoques para aplicar estilos y SVGs, vienen todos pre-configurados en Vite. Si estás a cargo de configurar las herramientas de compilación (e.g. Webpack), estas deben ser configuradas para habilitar la importación de archivos CSS o SVG. Debido a que estamos utilizando Vite, podemos utilizar todos estos archivos sin necesidad de cambiar la configuración. Por ejemplo, en tu archivo *src/main.jsx*, asegúrate de importar el archivo *src/index.css*.

{title="src/main.jsx",lang="javascript"}
~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

# leanpub-start-insert
import './index.css';
# leanpub-end-insert

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
~~~~~~~

Usa el siguiente CSS dentro del archivo *src/index.css*, para remover el margen y usar una fuente común con fuentes secundarias de respaldo:

{title="src/index.css",lang="css"}
~~~~~~~
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
~~~~~~~

Esencialmente, en este archivo puedes declarar todo el CSS que necesitas aplicar a nivel global dentro de tu proyecto.

### Ejercicios:

* Compara tu código con el del autor [código fuente](https://bit.ly/3xLnIXI).
  * Revisita todos los [cambios del código fuente ocurridos en esta sección](https://bit.ly/3f2gJ5U).
* Aprende más sobre [las distintas estrategias y enfoques de estilo en React](https://www.robinwieruch.de/react-css-styling/).

## CSS en React

El CSS en React común es muy similar al CSS estándar que ya debes haber aprendido. Cada elemento HTML en una aplicación web tiene un atributo `class` (`className` en React) que recibe los estilos por medio del archivo CSS:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <div className="container">
      <h1 className="headline-primary">My Hacker Stories</h1>
# leanpub-end-insert

      <SearchForm
        searchTerm={searchTerm}
        onSearchInput={handleSearchInput}
        onSearchSubmit={handleSearchSubmit}
      />

      {stories.isError && <p>Something went wrong ...</p>}

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
      )}
    </div>
  );
};
~~~~~~~

Se removió el `<hr />` porque el CSS se encarga de gestionar el borde en el paso siguiente. Vamos a importar el archivo CSS, que se logra con la ayuda de Vite que viene ya configurado para gestionar las importaciones:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
import * as React from 'react';
import axios from 'axios';

# leanpub-start-insert
import './App.css';
# leanpub-end-insert
~~~~~~~

Este archivo CSS va a definir las dos (y más) clases de CSS que utilizamos (y seguiremos utilizando) dentro del componente App. En el archivo *src/App.css*, definelos de la siguiente manera:

{title="src/App.css",lang="css"}
~~~~~~~
.container {
  height: 100vw;
  padding: 20px;

  background: #83a4d4; /* fallback for old browsers */
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
}

.headline-primary {
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
}
~~~~~~~

Al reiniciar la aplicación, ya deberías poder ver las primeras clases de estilo surgiendo efecto. A continuación, vamos al componente Item. Algunos de sus elementos también cuentan con el atributo `className`, sin embargo, vamos a utilizar una nueva técnica para aplicar los estilos aquí:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <li className="item">
    <span style={{ width: '40%' }}>
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
    </span>
# leanpub-start-insert
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
# leanpub-end-insert
      <button
        type="button"
        onClick={() => onRemoveItem(item)}
# leanpub-start-insert
        className="button button_small"
# leanpub-end-insert
      >
        Dismiss
      </button>
    </span>
  </li>
);
~~~~~~~

Como puedes ver, también se puede utilizar el atributo `style` para elementos HTML. En JSX, los estilos pueden ser asignados en línea como un objeto de JavaScript a dichos atributos. De esta forma, podemos definir propiedades dinámicas de estilo en archivos JavaScript en lugar de archivos CSS que son mayoritariamente estáticos. Este enfoque es conocido como **estilo en línea**, que es útil para prototipado rápido y definiciones de estilo dinámicas. Estilo en línea debería ser usado ocacionalmente, pero ten en cuenta, definir los estilos en un archivo CSS aparte, siempre da como resultado un JSX mas conciso.

En tu *src/App.css*, define las nuevas clases CSS. Características básicas de CSS son utilizadas acá, porque características avanzadas (e.g. Anidado) pertenecientes a extenciones de CSS (e.g. Sass) no se incluyen en este ejemplo, dado que representan [configuraciones opcionales](https://bit.ly/3E1a2bM):

{title="src/App.css",lang="css"}
~~~~~~~
.item {
  display: flex;
  align-items: center;
  padding-bottom: 5px;
}

.item > span {
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.item > span > a {
  color: inherit;
}
~~~~~~~

El estilo perteneciente al botón presente en el componente anterior aún no está aquí, así que vamos a definir un estilo base para el botón y otras dos más específicas (small y large). Una de las especificaciones para el botón ya ha sido utilizada, la otra será utilizada en los pasos siguientes:

{title="src/App.css",lang="css"}
~~~~~~~
.button {
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;
}

.button:hover {
  background: #171212;
  color: #ffffff;
}

.button_small {
  padding: 5px;
}

.button_large {
  padding: 10px;
}
~~~~~~~

Aparte de estos enfoques para aplicar estilos en React, las convenciones de nombrado ([Líneas directivas de CSS](https://mzl.la/3m5avnb)) representan otro tema aparte. El snippet de CSS anterior seguía reglas BEM, que definen modificaciones de una clase haciendo uso del guión bajo (`_`). Escoge cualquier convención de nombrado que se ajuste a ti y a tu equipo. Sin más que añadir, vamos a aplicar los estilos correspondientes al siguiente componente React:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <form onSubmit={onSearchSubmit} className="search-form">
# leanpub-end-insert
    <InputWithLabel ... >
      <strong>Search:</strong>
    </InputWithLabel>

    <button
      type="submit"
      disabled={!searchTerm}
# leanpub-start-insert
      className="button button_large"
# leanpub-end-insert
    >
      Submit
    </button>
  </form>
);
~~~~~~~

También podemos pasar el atributo `className` como una propiedad al componente React en cuestión. Por ejemplo, podemos usar esta opción para pasarle un estilo flexible al componente SearchForm  por medio de una propiedad `className` el cuál puede contener alguna de las clases predefinidas (e.g. `button_large` o `button_small`) en un archivo CSS aparte. Por último, aplica los estilos correspondientes al componente InputWithLabel:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <label htmlFor={id} className="label">
# leanpub-end-insert
        {children}
      </label>
      &nbsp;
      <input
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
# leanpub-start-insert
        className="input"
# leanpub-end-insert
      />
    </>
  );
};
~~~~~~~

En el archivo *src/App.css*, añade las clases restantes:

{title="src/App.css",lang="css"}
~~~~~~~
.search-form {
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
}

.label {
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
}

.input {
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
}
~~~~~~~

Por simplicidad, aplicamos estilos a los elementos tales como label e input de forma individual en el archivo *src/App.css*. Sin embargo, en una aplicación real, sería mucho mejor definir estos elementos una sola vez de forma global en el archivo *src/index.css*. Dado que los componentes React pueden estar divididos en varios archviso, compartir estilos entre todos estos se convierte en una necesidad. Después de todo, esta es el objetivo principal de CSS en React. Sin extensiones de CSS tales como Sass (Syntactically Awesome Style Sheets), aplicar estilos puede ser mucho más complicado debido a que el CSS anidado no está disponible en CSS nativo.

### Ejercicios:

* Compara tu código con el del autor [código fuente](https://bit.ly/3qZE0rQ).
  * Revisita todos los [cambios del código fuente ocurridos en esta sección](https://bit.ly/3R5s3M8).
* Intenta pasar `className` como una propiedad, partiendo desde el componente App hasta llegar a SearchForm, ya sea asignando `button_small` o `button_large` como valor al `className` del elemento botón.
* Lée más sobre [Hojas de estilo CSS en Vite](https://bit.ly/3S7peLJ).
  * Lée más sobre como utilizar Sass en Vite para utilizar características avanzadas de CSS tales como el anidado.
* Opcional: [Di lo que piensas sobre esta sección](https://forms.gle/RovYbjYF9McD1h6c7).