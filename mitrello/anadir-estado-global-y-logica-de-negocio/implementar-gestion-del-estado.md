# Implementar gestión del estado

## Definir el contexto de App usando ReactContext con TS

Usar ReactContext con Tipo de letra Aquí definiremos una estructura de datos para nuestra aplicación y la pondremos a disposición de todos los a través de la API de Contexto de React.

Crear un nuevo archivo llamado src/AppStateContext.tsx. Defina los datos de la aplicación, para ahora vamos a codificarlo:

{% code title="src/AppStateContext.tsx" %}
```typescript
import React, { createContext } from "react"

interface Task {
  id: string;
  text: string;
}
interface List {
  id: string;
  text: string;
  tasks: Task[];
}
export interface AppState {
    lists: List[]
}
interface AppStateContextProps {
    state: AppState
}
    
const AppStateContext = createContext<AppStateContextProps>({} as AppStateContextProps)
export const AppStateProvider = ({ children }: React.PropsWithChildren<{}>) => {
    return (
        <AppStateContext.Provider value={{ state: appData }}>
            {children}
        </AppStateContext.Provider>
    )
}
    
const appData: AppState = {
  lists: [
    {
      id: "0",
      text: "To Do",
      tasks: [{ id: "c0", text: "Generate app scaffold" }],
    },
    {
      id: "1",
      text: "In Progress",
      tasks: [{ id: "c2", text: "Learn Typescript" }],
    },
    {
      id: "2",
      text: "Done",
      tasks: [{ id: "c3", text: "Begin to use static typing" }],
    },
  ],
};


```
{% endcode %}

Como puedes ver, nuestro objeto de datos tiene el tipo AppState. 

Definámoslo junto con los tipos de los que depende.

{% code title="src/AppStateContext.tsx" %}
```typescript
interface Task {
  id: string;
  text: string;
}
interface List {
  id: string;
  text: string;
  tasks: Task[];
}
```
{% endcode %}

Decidí usar los términos Tarea/Lista para los tipos de datos y Columna/Tarjeta para UI componentes. Ahora usemos createContext para definir AppStateContext. Importar createContext desde reaccionar. Importar Reaccionar también, porque pronto definiremos un componente de proveedor:

Utilice createContext para definir el AppStateContext. Necesitaremos proporcionar el tipo para nuestro contexto. Definámoslo primero:

```typescript
interface AppStateContextProps {
    state: AppState
}
```

Por ahora, sólo queremos que nuestro appState esté disponible a través del contexto, por lo que es el único campo de nuestro tipo también. React quiere que proporcionemos el valor por defecto para nuestro contexto. Este valor sólo se usará si no envolvemos nuestra aplicación en nuestro AppStateProvider. Así que podemos omitirlo. Para hacerlo pasaremos un objeto vacío que lanzaremos a AppStateContextProps para crear la funciónContexto. Aquí usamos un operador para hacer que Typescript piense que nuestro objeto vacío tiene realmente el tipo AppStateContextProps.

```typescript
const AppStateContext = createContext<AppStateContextProps>({} as AppStateContextProps)
```

Ahora definamos el AppStateProvider. Pasará los datos de aplicación codificados a través de el proveedor AppStateContext:

```typescript
export const AppStateProvider = ({ children }: React.PropsWithChildren<{}>) => {
    return (
        <AppStateContext.Provider value={{ state: appData }}>
            {children}
        </AppStateContext.Provider>
    )
}
```

Nuestro componente sólo aceptará niños como apoyo. Usamos React.propsWithChildren tipo. Requiere un argumento genérico, pero no queremos tener ningún otro apoyo así que le pasamos un objeto vacío. Ve a src/index.tsx y envuelve el componente  en AppStateProvider.

{% code title="index.tsx" %}
```typescript
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import { AppStateProvider } from "./AppStateContext";
ReactDOM.render(
  <AppStateProvider>
    <App />
  </AppStateProvider>,
  document.getElementById("root")
);

```
{% endcode %}

Ahora podremos obtener el estado y el despacho de cualquier componente. Para facilitar el acceso a ellos, vamos a crear un gancho personalizado.

## Añadiendo artículos. Interfaces de tipografía vs. tipos

En este capítulo, definiremos las acciones y los reductores necesarios para crear nuevas tarjetas y componentes. Proporcionaremos el método de envío del reductor a través del Contexto Reaccionario y lo utilizaremos en nuestro componente AddNewItem.

### Definir las acciones

 Empezaremos añadiendo dos acciones: AGREGAR\_TAREAS y AGREGAR\_LISTAS. Para hacer esto tendremos que definir un tipo de acción. Abre src/AppStateContext y define un nuevo tipo:

{% code title="AppStateContext.tsx" %}
```typescript
type Action =
  | {
      type: "ADD_LIST";
      payload: string;
    }
  | {
      type: "ADD_TASK";
      payload: { text: string; taskId: string };
    };
```
{% endcode %}

La técnica que estamos usando aquí se llama unión discriminada. Hemos definido un tipo de acción y luego hemos pasado dos interfaces separadas por un Una línea Significa que la acción ahora puede resolver a una de las formas que hemos pasó. Cada interfaz tiene una propiedad de tipo. Esta propiedad será nuestro discriminante. Significa que que Typescript puede mirar esta propiedad y decir cuáles serán los otros campos de la interfaz. Por ejemplo, aquí hay una declaración "si":

```typescript
if (action.type === "ADD_LIST") {
    return typeof action.payload
    // Will return "string"
}

```

Aquí Typescript ya sabe que action.payload sólo puede ser una cadena. El interfaz que tiene tipo: "ADD\_LIST" tiene un campo de carga definido como cadena. Podemos ...lo usan para definir nuestros reductores.

### Definir appStateReducer 

Dentro de src/AppStateContext.tsx define appStateReducer, debería verse así:

```typescript
   
const appStateReducer = (state: AppState, action: Action): AppState => {
  switch (action.type) {
    case "ADD_LIST": {
      // Reducer logic here...
      return {
        ...state,
      };
    }
    case "ADD_TASK": {
      // Reducer logic here...
      return {
        ...state,
      };
    }
    default: {
      return state;
    }
  }
};
```

No tenemos que definir constantes para nuestros tipos de acción. El tipo de acción te dará un error si intentas comparar el tipo de acción con algo que no puede ser. Aquí también hay otra trampa, note que usamos paréntesis rizados para definir el alcance del bloque para nuestras declaraciones de caso. Sin esos corchetes, nuestras constantes serían visibles en todo el bloque de interruptores. Digamos que tienes tu reductor definido así, sin corchetes:

### Proporcionar el envío a través del contexto

 Abra el src/AppStateContext.tsx y actualice el AppStateProvider:

```typescript
export const AppStateProvider = ({ children }: React.PropsWithChildren<{}>) => {
  const [state, dispatch] = useReducer(appStateReducer, appData);
  return (
    <AppStateContext.Provider value={{ state, dispatch }}>
      {children}
    </AppStateContext.Provider>
  );
};
```

Ahora proporcionamos el valor del estado desde nuestro appStateReducer en lugar de usar appData de código duro. 

### Agregar listas 

El reductor necesita devolver una nueva instancia de un objeto. Usaremos el operador de difusión para obtener todos los campos del estado anterior. Luego estableceremos el campo de listas para que sea un nuevo arreglo con las listas antiguas más el nuevo ítem:



```typescript
case "ADD_LIST": {
  return {
    ...state,
    lists: [
      ...state.lists,
      { id: uuid(), text: action.payload, tasks: [] },
    ],
  };
}
```

La nueva columna tiene campos de texto, id y tareas. El campo de texto contiene el título de la lista, obtenemos su valor de action.payload, las listas serán una matriz vacía y el id de cada lista tiene que ser único. Usaremos uuid para generar nuevos identificadores.

 Necesitamos instalar esta biblioteca. No incluye definiciones de tipos, así que también las instalamos:

```bash
$ yarn add uuid @types/uuid
```

Ahora importa uuid dentro del src/AppStateContext:

```typescript
import uuid from 'uuid'
```

### Añadiendo tareas 

Agregar tareas es un poco más complejo porque necesitan ser agregadas a listas específicas de la matriz de tareas. Necesitaremos encontrar la lista por su identificación. Añadamos el método findItemIndexById. Crear un nuevo archivo src/utils/findItemIndexById. Vamos a definir una función que aceptará cualquier objeto que tenga un campo id: string. Definir un nuevo elemento de la interfaz.

Ahora usaremos el tipo genérico T que amplía el artículo. Eso significa que hemos restringido nuestro genérico para tener los campos que se definen en la interfaz de Item. En este caso el campo id. Definir la función

```typescript
export const findItemIndexById = <T extends Item>(items: T[], id: string) => {
    return items.findIndex((item: T) => item.id === id)
}  
```

Ahora vuelve a src/AppStateContext y añade el código para el bloque ADD\_TASK:

```typescript
    case "ADD_TASK":
      {
        const targetLaneIndex = findItemIndexById(
          state.lists,
          action.payload.taskId
        );
        state.lists[targetLaneIndex].tasks.push({
          id: uuid(),
          text: action.payload.text,
        });
      }
```

Aquí primero encontramos el índice de la lista de objetivos y lo guardamos en la constante TargetListIndex. Luego empujamos un nuevo objeto de tarea a la lista con ese índice. Y luego devolvemos un nuevo objeto, creado a partir del estado antiguo usando la sintaxis de dispersión de objetos. 

### Acciones de despacho 

Ve a src/App.tsx y actualiza el código. Ahora también obtenemos la función de despacho de el uso del gancho AppState.

```typescript

const App = () => {
  const { state, dispatch } = useAppState();
  return (
    <AppContainer>
      {state.lists.map((list, i) => (
        <Column id={list.id} text={list.text} key={list.id} index={i} />
      ))}
      <AddNewItem
        toggleButtonText="+ Add another list"
        onAdd={(text) => dispatch({ type: "ADD_LIST", payload: text })}
      />
    </AppContainer>
  );
};
```

Actualice también la función AddNewItem onAdd. Ahora llamaremos al método de envío allí, pasando el texto como una carga útil. Abre src/Column.tsx y actualízalo también:

```typescript
export const Column = ({ text, index, id }: ColumnProps) => {
  const { state, dispatch } = useAppState();
  return (
    <ColumnContainer>
      <ColumnTitle>{text}</ColumnTitle>
      {state.lists[index].tasks.map((task, i) => (
        <Card text={task.text} key={task.id} index={i} />
      ))}
      <AddNewItem
        toggleButtonText="+ Add another card"
        onAdd={(text) =>
          dispatch({ type: "ADD_TASK", payload: { text, taskId: id } })
        }
        dark
      />
    </ColumnContainer>
  );
};
```

Aquí también llamamos la función de despacho. Pasamos la taskId junto al texto porque necesitamos saber qué lista contendrá la nueva tarea. Lancemos la aplicación y comprobemos que podemos crear nuevas tareas y listas.

## Mover los artículos 

Podemos añadir nuevos artículos, es hora de moverlos. Empezaremos con las columnas. 

### Mover las columnas 

Añade un nuevo tipo de acción:













