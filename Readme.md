Este material es una derivación del trabajo de:
* [Scott Domes](https://blog.bitsrc.io/react-16-lifecycle-methods-how-and-when-to-use-them-f4ad31fb2282)
* [Evheniy Bystrov](https://hackernoon.com/first-steps-in-react-ef8c000c0585)



# React
React es una libreria modular desarrollada por facebook para construir interfaces, rapidas con la menor cantidad de codigo.

## Componentes:
Los componentes le permiten dividir la IU en piezas independientes y reutilizables, y pensar en cada pieza de forma aislada.

* **Funcionales (presentacionales):** 
    Componentes sin estado ni ciclo de vida, requieren menos codigo que los `StateFul`.

    ```js
    function buildComponent (texto) { 
        return `<h1> ${text} </h1>`; 
    }

    const firstComponent = buildComponent ('Hola'); 
    const secondComponent = buildComponent ('Hola');

    firstComponent === secondComponent // true
    ```
    *Si utiliza funciones sin efectos secundarios, funciones puras, puede repetir el mismo resultado pasando los mismos parámetros.*


    Los componentes funcinales nos permiten trabajar con:

    * Props
    * Componentes Hijos
    * Variables y funcione puras.


    **Simple componente funcional:**
    ```js
    const StatelessComponent = () => <h5>Hello!</h5>
    ```

    **Props:**
    ```js
    const StatelessComponent2 = props => <h5>Hello {props.name}!</h5>
    ```

    **Variables internas:**
    ```js
    const StatelessComponent3 = () => {
        const name = 'jesuskinto';
        return <h5>Hello {name}!</h5>
    }
    ```

    **Props y variables internas:**
    ```js
    const StatelessComponent4 = props => {
        const color = props.status === 'success' ? 'green':'orange';
        const myStyle = { color: color }
        return <h5 style={myStyle}>Hello jesuskinto {props.status}!</h5>
    }
    ```

    **Componentes hijos:**
    ```js
    const StatelessComponentChildren = () => (
        <strong>this is children Component.</strong>
    )

    const StatelessComponentParent = () => (
        <p>
            This is a parent Component and <StatelessComponentChildren />
        </p>
    )
    ```

* **Clases (Contenedores):**
    Componentes con estado y ciclo de vida. son mas pesados que los anteriores, pero permiten realizar un alto grado de personalización

    ```js
    class Component {
        state = {};
        render() {};
    }

    const firstComponent = nuevo Componente (); 
    const secondComponent = nuevo Componente ();

    firstComponent === secondComponent // false
    ```

    *Cada objeto está construido a partir de una clase. Son instancias de la misma clase pero son diferentes debido al estado propio. No puedes comparar objetos.*

    Para crear componentes con estado debe usar **React.Component** o **React.PureComponent**.

    ### PureComponent
    Es exactamente lo mismo que Component excepto que maneja el shouldComponentUpdate método para ti, **por lo cual es más eficiente**

    ### State
    La razón principal del uso de componentes con estado es la administración del estado de su aplicación o componente.

    Puede cambiar el estado usando:
    
    ```js
    this.setState({comment: 'Hello'});
    ```

    ```js
    this.setState((prevState, props) => ({
        counter: prevState.counter + props.increment
    }));
    ```

    Pero no use el cambio manual:
    ```js
    // Wrong!!!
    this.state.comment = 'Hello';
    ```

## Lifecicle

Los métodos de ciclo de vida (con la excepción del constructor) son difíciles de razonar. Añaden complejidad a su aplicación. No los use a menos que deba hacerlo.

* Mounting
    * Constructor
    * getDerivedStateFromProps
    * render
    * componentDidMount
* Updating
    * getDerivedStateFromProps
    * shouldComponentUpdate
    * render
    * getSnapshotBeforeUpdate
    * componentDidUpdate
* Unmounting
    * componentWillUnmount
* Errors
    * getDerivedStateFromError
    * componentDidCatch

### Mounting
Estos métodos se llaman cuando se crea una instancia de un componente y se inserta en el DOM:

* Constructor
    > Los mayores casos de uso para el `contructor` son: Configuracion de estado inicial, creacion de `refs` y enlace de metodos.

    El primer componente en ser llamado es el constructor. 

    ```js
    class MyComponent extends Component {
        constructor(props) {
            super(props);
            this.state = {
            counter: 0,
            };
        }
    }
    ```

    Puedes inicializar tu estado sin la necesidad del constructor, este enfoque es preferido.
    ```js
    class MyComponent extends Component {
        state = {
            counter: 0,
        };
    }
    ```
    ```js
    class MyComponent extends Component {
        state = {
            counter: this.props.initialCounterValue,
        };
    }
    ```

* getDerivedStateFromProps
    > Es el ultimo metodo llamand antes del render, puedes usarl para crear estados derivados de los props iniciales.

    ```js
    static getDerivedStateFromProps(props, state) {
        return {
            age: calculateAge(props.birthdate)
        };
    }
    ```

* render
    > Devuelve el JSX de su componente real. Cuando trabaje con React, pasará la mayor parte del tiempo aquí.

* componentDidMount
    > Si necesita cargar datos (AJAX) o agregar event listener, aquí es donde lo debes hacer. 

    ```js
    componentDidMount() {
        fetch(`url`)
        .then(res => res.json())
        .then(json => this.setState({ data: json }));
    }
    ```
### Updating
* getDerivedStateFromProps
    > Updating state based on props, when the props themselves aren’t enough.

* shouldComponentUpdate
    > Este metodo pregunta si el react componente debe `actualizarse` cada vez que sus `props` y `states` cambien. Por defecto retorna `true`.
    
    > Si le preocupan los renders desperdiciados y otras tonterías, shouldComponentUpdate es un lugar increíble para mejorar el rendimiento.

    ```js
    shouldComponentUpdate() {
        ...
        return true
    }
    ```
* render
    > Lo mismo que el anterior
* getSnapshotBeforeUpdate
    > Este metodo se dispara junto con el render, puedes obtener el estado anterior de tus props y state antes de ser actualizados.

    > Podemos enviar valores al `componentDidUpdate` desde aca
    ```js
    getSnapshotBeforeUpdate(prevProps, prevState) {
        ...
        return null;
    }
    ```
* componentDidUpdate
    > Ahora los cambios han sido aplicados al DOM, aca tu tienes acceso a tres cosas: los anteriores props, el anterior state  y el retorno de getSnapshotBeforeUpdate

    ```js
    componentDidUpdate(prevProps, prevState, snapshot) {
        ...
    }
    ```

### Unmounting
* componentWillUnmount
    > Es hora de limpiar y remover cosas sobrantes del componente, cancel Requests, remove event listener... 

    ```js
    componentWillUnmount() {
       clearInterval(this.interval);
    }
    ``` 
### Error
* getDerivedStateFromError
    > Este metodo se dispara cuando alguno de tus componentes hijos tiene un error, usalo para actualizar el state error `this.state.hasError`.

    > No usar este metodo para ninguna otra cosa

    ```js
    static getDerivedStateFromError(error) {
        return {hasError: true}; 
    };
    ```

* componentDidCatch
    > A diferencia del anterior, ahora si podemos hacer cualquier efecto secundario
    
    ```js
    static componentDidCatch(error, info) {
        sendError(error, info); 
    };

    // error: would be the actual error message (Undefined Variable blah blah)
    // info: would be the stack trace (In Component, in div, etc).
    ```
    > **Note que componentDidCatch solo trabaja para errores generados en los metodos del ciclo de vida.**


## Refs

Refs provee una via para acceder a nodos del DOM o elementos de React creados con el metodo render.

### Cuando debes usarlo
* Enfoque, Selección de textos, Reproducción multimedia
* Disparadores de animaciones
* Integración con librerias de terceros

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}

```
[Mas](https://reactjs.org/docs/refs-and-the-dom.html)


# TODO

* [HOC](https://es.reactjs.org/docs/higher-order-components.html)
* [Hooks](https://es.reactjs.org/docs/hooks-intro.html)