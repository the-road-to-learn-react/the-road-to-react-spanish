## Módulos CSS en React

Los módulos CSS en React, son un enfoque **CSS-in-CSS** avanzado. El archivo CSS se mantiene igual, en el cuál puedes aplicar extensiones CSS tales como Sass, pero su uso dentro de componentes React cambia. Para habilitar módulos CSS en Vite, renombra el archivo *src/App.css** a *src/App.module.css*. Esta acción se puede realizarr desde la línea de comando, partiendo de la raíz del directorio de tu proyecto:

{title="Command Line",lang="text"}
~~~~~~~
mv src/App.css src/App.module.css
~~~~~~~

Dentro del archivo renombrado *src/App.module.css*, realiza las primeras definiciones de clases de CSS, tal como antes:

{title="src/App.module.css",lang="css"}
~~~~~~~
.container {
  height: 100vw;
  padding: 20px;

  background: #83a4d4; /* fallback for old browsers */
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
}

.headlinePrimary {
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
}
~~~~~~~

Importa el archivo *src/App.module.css*, nuevamente utilizando una ruta relativa. Esta vez, importalo como un objeto de JavaScript, donde puedes asignar nombre del objeto (aquí: `styles`) que tu quieras:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
import * as React from 'react';
import axios from 'axios';

# leanpub-start-insert
import styles from './App.module.css';
# leanpub-end-insert
~~~~~~~

En vez de definir la propiedad `className` como una cadena de texto mapeada a un archivo CSS, accede a la clase CSS directamente desde el objeto `styles`, y asignalo a tus elementos por medio de una expresión "JavaScript en JSX".

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <div className={styles.container}>
      <h1 className={styles.headlinePrimary}>My Hacker Stories</h1>
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

Por medio del uso de Plantillas Literales de JavaScript, se pueden añadir multiples clases CSS utilizando el objeto styles dentro del atribruto `className` que es uno por cada elemento:
{title="src/App.jsx",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <li className={styles.item}>
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
        className={`${styles.button} ${styles.buttonSmall}`}
# leanpub-end-insert
      >
        Dismiss
      </button>
    </span>
  </li>
);
~~~~~~~

Utilizando JSX, también se pueden añadir estilos en línea de forma más dinámica. Además, es posible añadir una extensión CSS tal como Sass para habilitar funcionalidades avanzadas tales como Anidación de Selectores CSS (ver sección anterior). Pero por ahora, nos quedamos con opciones nativas de CSS:

{title="src/App.module.css",lang="css"}
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

Entonces, las clases CSS del botón en el archivo *src/App.module.css*:

{title="src/App.module.css",lang="css"}
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

.buttonSmall {
  padding: 5px;
}

.buttonLarge {
  padding: 10px;
}
~~~~~~~

Aquí se comienza a adoptar una convención de nombres pseudo BEM, en contraste a los `button_small` y `button_large` vistos en la sección anterior. Si la convención de nombres anterior se mantiene, solo podremos acceder al estilo utilizando notación de brackets  `styles['button_small']`, lo que nos obligaría a ser más específicos debido a la limitación que tiene JavaScript con respecto al caractér guión bajo. Las mismas limitaciones aplican para clases definidas usando guión (`-`). Para contrarrestar esto, ahora podemos utilizar `styles.buttonSmall` en su lugar (ver: Item component):

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <form onSubmit={onSearchSubmit} className={styles.searchForm}>
# leanpub-end-insert
    <InputWithLabel ... >
      <strong>Search:</strong>
    </InputWithLabel>

    <button
      type="submit"
      disabled={!searchTerm}
# leanpub-start-insert
      className={`${styles.button} ${styles.buttonLarge}`}
# leanpub-end-insert
    >
      Submit
    </button>
  </form>
);
~~~~~~~

El componente SearchForm recibe los estilos también. Tiene que utilizar interpolación de cadenas de texto para poder aplicar dos estilos dentro de un elemento por medio de las plantillas literales de JavaScript. Una alternativa es la librería [clsx](https://bit.ly/3DNEA3R), que se instala vía línea de comando como una dependencia del projecto:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
import clsx from 'clsx';

...

// somewhere in a className attribute
className={clsx(styles.button, styles.buttonLarge)}
~~~~~~~

La librería también ofrece estilos condicionales; donde el lado izquierdo de la propiedad del objeto, debe ser usasdo como un [nombre de propiedad computado](https://mzl.la/2XuN651) y solo se aplica si el lado derecho es evaluado como verdadero:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
import clsx from 'clsx';

...

// somewhere in a className attribute
className={clsx(styles.button, { [styles.buttonLarge]: isLarge })}
~~~~~~~

Finalmente, continua con el componente InputWithLabel:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <label htmlFor={id} className={styles.label}>
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
        className={styles.input}
# leanpub-end-insert
      />
    </>
  );
};
~~~~~~~

Y completa el resto de estilos en el archivo *src/App.module.css*

{title="src/App.module.css",lang="css"}
~~~~~~~
.searchForm {
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

Aquí también aplica la misma precaución que en la sección anterior: Algunos de estos estilos tales como `input` y `label` podrían ser más eficientes estando dentro de un archivo global *src/index.css* sin necesidad de módulos CSS.

De nuevo, los Módulos CSS -- tal como cualquier otro enfoque CSS-in-CSS -- podrían utilizar Sass para tener acceso a opciones más avanzadas tales como la anidación de selectores. La ventaja que ofrecen los módulos CSS es que estos reciben un error de JavaScript cada vez que un estilo no ha sido definido. En el enfoque estándard de CSS, estilos inexistentes JavaScript y archivos CSS podrían pasar desapercibidos. 

### Exercises:

* Compara tu código con el del autor [código fuente](https://bit.ly/3xM1HYw).
  * Revisita todos los [cambios del código fuente ocurridos en esta sección](https://bit.ly/3dxsZLz).
* Lee más sobre [CSS Modules en Vite](https://bit.ly/3S7peLJ).
* Opcional: [Déjanos saber lo que piensas sobre esta sección](https://forms.gle/iuU7WaeJVwHN2pFCA).