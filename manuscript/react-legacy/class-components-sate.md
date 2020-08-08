## React Class Components: State

Antes de los Hooks de React, los componentes de tipo clase eran superiores a los componentes funcionales porque podían contener estados. Con un constructor de clase, podemos establecer un estado inicial para el componente. También, la instancia del componente `this` le da acceso al estado actual (`this.state`) y el método actualizador del estado del componente (`this.setState`):

{title="Code Playground",lang="javascript"}
~~~~~~~
class App extends React.Component {
  constructor(props) {
    super(props);

# leanpub-start-insert
    this.state = {
      searchTerm: 'React',
    };
# leanpub-end-insert
  }

  render() {
# leanpub-start-insert
    const { searchTerm } = this.state;
# leanpub-end-insert

    return (
      <div>
        <h1>My Hacker Stories</h1>

        <SearchForm
          searchTerm={searchTerm}
# leanpub-start-insert
          onSearchInput={() => this.setState({
            searchTerm: event.target.value
          })}
# leanpub-end-insert
        />
      </div>
    );
  }
}
~~~~~~~

Si el objeto estado tiene más de una propiedad, el método `setState` realiza solo una actualización superficial. Solo las propiedades pasadas a `setState` son sobreescritas, y todas las otras propiedades en el objeto estado permanecen intactas. Dado que la gestión del estado es importante para las aplicaciones frontend, no había forma de implementarlo en componentes tipo clase sin hooks para los componentes funcionales.

En un componente de clase en React, hay dos APIs dedicadas (`this.state` y `this.setState`) para gestionar el estado de un componente. En un componente funcional, los hooks useState y useReducer de React manejan esto. Objetos relacionados están empaquetados en un hook de estado, mientras que un componente de clase debe usar una API de estado general. Esta fue una de las mayores razones para introducir los Hooks de React, y alejarse de los componentes de clase.

### Ejercicios

* Escribe un componente de clase con estado (p.ej. Input).
