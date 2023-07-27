## SVGs en React

Para crear una aplicación moderna de React, probablemente vamos a necesitar SVGs. En vez de darle a cada botón una etiqueta textual, podriamos utilizar algún icono. En esta sección, vamos a utilizar Gráficos Vectoriales Escalables (SVG) a modo de icono para uno de nuestros componentes React.

**Importante:** 

esta sección se conecta con el tema "CSS en React" cubierto anteriormente, lo cuál va a permitir que los íconos SVG tengan un buen look y funcionen adecuadamente. Es totalmente aceptable que utilizes otro enfoque para aplicar estilos (e.g. Módulos CSS, Styled Components), o ningún estilo en absoluto, aunque el SVG podría no verse bien sin este.

Vite no incluye soporte para SVG por defecto. Para permitir SVGs en Vite, tenemos que instalar uno de los plugins de Vite con la ayuda de la línea de comando:

{title="Command Line",lang="text"}
~~~~~~~
npm install vite-plugin-svgr --save-dev
~~~~~~~

A continuación, el nuevo plugin para SVGs puede ser utilizado en las configuraciones de Vite:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
# leanpub-start-insert
import svgr from 'vite-plugin-svgr';
# leanpub-end-insert

// https://vitejs.dev/config/
export default defineConfig({
# leanpub-start-insert
  plugins: [react(), svgr()],
# leanpub-end-insert
});
~~~~~~~

Eso es todo en cuanto a la configuración general. El siguiente icono es un SVG tomado de [Flaticon's Freepick](https://bit.ly/3E16SEz). Muchos de los SVGs en ese sitio web son gratis para su uso, aunque requieren que menciones al autor. Puedes descargar el ícono desde [aquí](https://bit.ly/2Z2EoeA) en formato SVG y colocarlo en tu projecto como *src/check.svg*. Descargar el archivo es la forma recomendada, a continuación la definición verbose del SVG:

{title="src/check.svg",lang="html"}
~~~~~~~
<?xml version="1.0" encoding="iso-8859-1"?>
<!-- Generator: Adobe Illustrator 18.0.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
   viewBox="0 0 297 297" style="enable-background:new 0 0 297 297;" xml:space="preserve">
  <g>
    <path d="M113.636,272.638c-2.689,0-5.267-1.067-7.168-2.97L2.967,166.123c-3.956-3.957-3.956-10.371-0.001-14.329l54.673-54.703
      c1.9-1.9,4.479-2.97,7.167-2.97c2.689,0,5.268,1.068,7.169,2.969l41.661,41.676L225.023,27.332c1.9-1.901,4.48-2.97,7.168-2.97l0,0
      c2.688,0,5.268,1.068,7.167,2.97l54.675,54.701c3.956,3.957,3.956,10.372,0,14.328L120.803,269.668
      C118.903,271.57,116.325,272.638,113.636,272.638z M24.463,158.958l89.173,89.209l158.9-158.97l-40.346-40.364L120.803,160.264
      c-1.9,1.902-4.478,2.971-7.167,2.971c-2.688,0-5.267-1.068-7.168-2.97l-41.66-41.674L24.463,158.958z"/>
  </g>
</svg>
~~~~~~~

Ahora podemos importar SVGs (similar a CSS) como componentes React. En *src/App.jsx*, usa la siguiente sintáxis para importar el SVG:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
import * as React from 'react';
import axios from 'axios';

import './App.css';

# leanpub-start-insert
import { ReactComponent as Check } from './check.svg';
# leanpub-end-insert
~~~~~~~

Aquí estamos importando un SVG para ser utilizado como un icono. Sin embargo, esto sirve en diversas situaciones, tales como logos o fondos. Ahora, en vez del texto "Dismiss" que tenía el botón, vamos a pasar el componente SVG con los atributos `height` y `width`:

{title="src/App.jsx",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <li className="item">
    <span style={{ width: '40%' }}>
      <a href={item.url}>{item.title}</a>
    </span>
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
      <button
        type="button"
        onClick={() => onRemoveItem(item)}
        className="button button_small"
      >
# leanpub-start-insert
        <Check height="18px" width="18px" />
# leanpub-end-insert
      </button>
    </span>
  </li>
);
~~~~~~~

Sin importar del enfoque para aplicar estilos que estes utilizando, puedes asignarle un efecto hover a tu SVG también, utilizando el enfoque  de CSS básico, sería algo como esto definido en el archivo *src/App.css*:

{title="src/App.css",lang="css"}
~~~~~~~
.button:hover > svg > g {
  fill: #ffffff;
  stroke: #ffffff;
}
~~~~~~~

El plugin de Vite hace que utilizar SVGs sea bastante fácil, sin mucha configuración extra necesaria. Esto sería distinto si creas un proyecto React desde 0 sin herramientas de compilado tales como Webpack, porque tienes que hacerte cargo de todo por tu cuenta. De cualquier modo, SVGs hacen que tu aplicación sea más accesible, usalos siempre que la situación lo amerite.

### Exercises:

* Compara tu código con el del autor [código fuente](https://bit.ly/3DNI6v5).
  * Revisita todos los [cambios del código fuente ocurridos en esta sección](https://bit.ly/3xLtOY6).
* Añade otro icono SVG a la aplicación.
* Integra librerías de terceros tales como [react-icons](https://bit.ly/3nayoJ7) en tu aplicación y usa sus símbolos SVG importandolos como componentes React.
* Opcional: [Déjanos saber lo que piensas sobre esta sección](https://forms.gle/3yGgMDR2VQ5WksSXA).