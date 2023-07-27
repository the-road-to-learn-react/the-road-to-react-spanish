## Styled Components en React

En los enfoques planteados anteriormente partiendo desde CSS-in-CSS, los Styled Components son uno de los varios enfoques utilizados en **CSS-in-JS**. Escogí los Styled Components porque son la herrmaienta más popular. Viene en forma de dependencia de JavaScript, así que debe ser instalado desde la línea de comandos:

{title="Command Line",lang="text"}
~~~~~~~
npm install styled-components
~~~~~~~

Then import it in your *src/App.jsx* file:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
import * as React from 'react';
import axios from 'axios';
# leanpub-start-insert
import styled from 'styled-components';
# leanpub-end-insert
~~~~~~~

Como el nombre lo sugiere, CSS-in-JS se define directamente dentro de un archivo JavaScript. Dentro del archivo *src/App.jsx*, define tu primer Styled Component:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const StyledContainer = styled.div`
  height: 100vw;
  padding: 20px;

  background: #83a4d4;
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
`;

const StyledHeadlinePrimary = styled.h1`
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
`;
~~~~~~~

Al utilizar Styled Components, se utilizan las plantillas literales de la misma forma en que se utilizan las funciones de JavaScript. Todo lo que esté dentro de los acentos graves (backtips, en Inglés) puede ser visto como un argumento y el objeto `styled` te da acceso a todos los elementos HTML necesarios (e.g. div, h1) usando la notación de puntos. Una vez la función es invocada con el estilo correspondiente, un componente React es retornado, y este puede ser utilizado dentro del componente App:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <StyledContainer>
      <StyledHeadlinePrimary>My Hacker Stories</StyledHeadlinePrimary>
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
# leanpub-start-insert
    </StyledContainer>
# leanpub-end-insert
  );
};
~~~~~~~

Esta clase de componente React, sigue las mismas reglas que cualquier otro componente React. Todo lo que sea pasado dentro de las etiquetas del elemento, puede ser accedido dentro del componente React utilizando la propiedad `children`. Para el componente Item, no vamos a utilizar estilos en línea esta vez, vamos a definir un Styled Component dedicado. `StyledColumn` recibe sus estilos de manera dinámica usando una propiedad React:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <StyledItem>
    <StyledColumn width="40%">
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
# leanpub-start-insert
    </StyledColumn>
    <StyledColumn width="30%">{item.author}</StyledColumn>
    <StyledColumn width="10%">{item.num_comments}</StyledColumn>
    <StyledColumn width="10%">{item.points}</StyledColumn>
    <StyledColumn width="10%">
      <StyledButtonSmall
# leanpub-end-insert
        type="button"
        onClick={() => onRemoveItem(item)}
      >
        Dismiss
# leanpub-start-insert
      </StyledButtonSmall>
    </StyledColumn>
  </StyledItem>
# leanpub-end-insert
);
~~~~~~~

La propiedad flexible `width` está disponible para su uso dentro de la plantilla literal del Styled Component a modo de argumento, dentro de una función en línea. Puedes observar que estamos utilizando funciones flecha dentro del Styled Component. Al omitir los brackets que delimitan el cuerpo de la función, estas realizan el return de forma implicita, dando como resultado una función en línea muy concisa:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const StyledItem = styled.li`
  display: flex;
  align-items: center;
  padding-bottom: 5px;
`;

const StyledColumn = styled.span`
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;

  a {
    color: inherit;
  }

  width: ${(props) => props.width};
`;
~~~~~~~

Otras características avanzadas de CSS tales como anidado, están también disponibles por defecto en los Styled Components. Los elementos anidados son accesibles y el elemento actual puede ser seleccionado utilizando el operador de CSS `&`:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const StyledButton = styled.button`
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;

  &:hover {
    background: #171212;
    color: #ffffff;
  }
`;
~~~~~~~

También puedes crear versiones especializadas de un styled component al aisgnarle otro componente de la librería de funciones. En este caso, el botón especializado va a heredar todas las reglas de estilo base pertenecientes al componente StyledButton definido anteriormente:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const StyledButtonSmall = styled(StyledButton)`
  padding: 5px;
`;

const StyledButtonLarge = styled(StyledButton)`
  padding: 10px;
`;

const StyledSearchForm = styled.form`
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
`;
~~~~~~~

Al utilizar un styled component tal como StyledSearchForm, el elemento base sobre el cuál este se construye (form) será renderizado en el HTML final que se muestra en el navegador. Por tanto, podemos seguir utilizando atributos HTML nativos (`onSubmit`, `type`, `disabled`) aquí:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <StyledSearchForm onSubmit={onSearchSubmit}>
# leanpub-end-insert
    <InputWithLabel
      id="search"
      value={searchTerm}
      isFocused
      onInputChange={onSearchInput}
    >
      <strong>Search:</strong>
    </InputWithLabel>

# leanpub-start-insert
    <StyledButtonLarge type="submit" disabled={!searchTerm}>
# leanpub-end-insert
      Submit
# leanpub-start-insert
    </StyledButtonLarge>
  </StyledSearchForm>
# leanpub-end-insert
);
~~~~~~~

Finalmente, el componente InputWithLabel, decorado con sus styled components aún por definir:
Finally, the InputWithLabel decorated with its yet undefined styled components:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <StyledLabel htmlFor={id}>{children}</StyledLabel>
# leanpub-end-insert
      &nbsp;
# leanpub-start-insert
      <StyledInput
# leanpub-end-insert
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
      />
    </>
  );
};
~~~~~~~

Y sus styled components correspondientes, que son definidos en el mismo archivo:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const StyledLabel = styled.label`
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
`;

const StyledInput = styled.input`
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
`;
~~~~~~~

CSS-inJS con styled components nos permite pasar de pensar en definir estilos a enfocarnos en construir componentes React realies. Los Styled Components no son mas que estilos definidos en formato de componentes React sin archivos CSS intermediarios. Si un styled component no es utilizado en JavaScript, el IDE/editor de código te va a avisar. Los Styled Components se empaquetan junto a otros elementos de JavaScript cuando se trata de una aplicación lista para producción. No hay archivos CSS extras, solo JavaScript cuando se utiliza la estrategia CSS-en-JS. Ambas estrategias, CSS-in-JS y CSS-in-CSS, y sus enfoques (e.g. Styled Components y Módulos CSS) son populares entre desarrolladores React. Uitiliza lo que mejor se adapte a ti y a tu equipo.

### Ejercicios:

* Compara tu código con el del autor [código fuente](https://bit.ly/3dwNzM9).
  * Revisita todos los [cambios del código fuente ocurridos en esta sección](https://bit.ly/3UAbj2e).
* Lee mas sobre [mejores prácitcas para Styled Components en React](https://www.robinwieruch.de/styled-components/).
* Usualmente no existe un archivo *src/index.css* para estilos globales al utilizar Styled Components. Descubre como utilizar estilos globales cuando trabajas con Styled Components utilizando tu búscador favorito.
* Opcional: [Déjanos saber lo que piensas sobre esta sección](https://forms.gle/5vFxvg9hSNAna37S8).