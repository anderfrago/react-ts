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









