---
description: Introducción
---

# React con TypeScript

## Por que utilizar Typescript con React

Lo revolucionario de React es que permite describir una aplicación como un árbol de componentes. El componente puede representar un elemento, como un botón o una entrada. Puede ser un grupo de elementos que representan un formulario de acceso. O puede ser una página completa que se componen de multiplicar los componentes más simples. Los componentes pueden pasar la información, a traves del arbol de componentes, de padres a hijos. Pueden también transmiten funciones como llamadas de retorno. Así que si algo sucede en un elemento hijo puede notificar a su padre llamando a la función de devolución de llamada pasada. Aquí es donde Typescript se vuelve muy útil. Puedes usarlo para definir interfaces de tus componentes, para que puedas estar seguro de que tu componente recibe solo las entradas correctas. Si trabajaste con React antes, probablemente sepas que puedes especificar la composición de los componentes utilizando prop-types.

```jsx
import PropTypes from 'prop-types';
    const Greeting = ({name}) => {
    return (
        <h1>Hello, {name}</h1>
    );
}
Greeting.propTypes = {
    name: PropTypes.string
};
```

Si se puede hacer con los prop-types, ¿por qué se necesitas Typescript? Por varias razones:

* No necesitas ejecutar tu aplicación para saber si tienes errores tipográficos.

  Typecript puede ser ejecutado por tu editor de código para que puedas ver los errores de la misma manera que

  los haces.

* Sólo se pueden usar prop-types con componentes. En tu aplicación

  probablemente tienen funciones y clases que no están usando React. Es importante

  ser capaz de proporcionar tipos para ellos también.



