.. toctree::
   :maxdepth: 2

ReactGridsheet
==========================

.. note::

  - `Examples </products/react-gridsheet/examples>`__
  - `GitHub <https://github.com/righ/react-gridsheet>`__
  - `NPM <https://www.npmjs.com/package/react-gridsheet>`__

Installation
-------------------------

.. code-block:: shell

  $ npm install react-gridsheet --save

.. code-block:: shell

  $ yarn add react-gridsheet


How to use
-------------------------

.. code-block:: jsx

  import * as React from "react";
  import { GridSheet } from "react-gridsheet";

  export default function App() {
    return (<div>
      <GridSheet
        data={[ // Required: any[][]
           ["a", 1, true],
           ["b", 2, false],
           ["c", 3, null],
        ]}
        options={{ // Optional
          // all options are optional
        }}
        className="some-class"
        style={{ maxWidth: "100%" }}
      />
    </div>);
  }

.. note::

  More examples are `here </products/react-gridsheet/examples>`__.


options prop
-------------------------

:options.cells:

  ``options.cells`` affects the cells matching the keys.

  - `default` (lowercase) field matches all rows, columns and cells.

    - You can set default height and width here.

      - defaultHeight and defaultWidth has been dropped.

  - An upppercase letter field means a config for a column.

    - e.g. **A**: 1st column, **B**: 2nd column

  - A number Fields means a config for a row.

    - e.g. **1**: 1st row, **2**: 2nd column

  - An upppercase letter + A number field means a unique cell.

    - e.g. **A1**: Most top left cell.
    - If a cell matches multiple cell configs, it applys the configs in the following order: `cell` > `column` > `row` > `default`.

  :label: Header's label instead of Column ID. Only row and column configs work.
  :width: Horizontal header's width. (px)
  :height: Vertical header's height. (px)
  :style: Cell style object. (React.CSSProperties)
  :verticalAlign: This field equals css `vertical-align` property.
  :render: Renderer identity. (string)
  :parser: Parser identity. (string)


  Example:

  .. code-block:: javascript

    cells: {
      default: {
        width: 100, // px
        height: 25, // px
      },
      A: {
        style: {
          borderBottom: "double 2px #ff0000",
        },
      },
      1: {
        height: 50,
      },
    }

:options.sheetHeight:

  - Sheet width size.
  - default: ``500``

:options.sheetWidth:

  - History (undo, redo) size.
  - default: ``1000``

:options.historySize:

  - History (undo, redo) size.
  - default: ``10``

:options.headerHeight:

  - Horizontal header height. (number)
  - default: ``20``

:options.headerWidth:

  - Vertical header width. (number)
  - default: ``50``

:options.editingOnEnter:

  - Whether `ENTER` key gets down and set the cell editing.
  - default: ``true``

:options.cellLabel:

  - Whether cell labels (navigator) show.
  - default: ``true``

:options.mode:

  - color mode. It allows ``"light"`` or ``"dark"``.
  - default: ``"light"``

:options.renderers:

  - You can create a custom Renderer inheriting ``Renderer`` class.

    - It has to be registered here. The key identifies the renderer.
    - You can override methods on the Renderer class.

      - stringify: changes cell value to string when the cell editing and pasting. (from v0.5.7)
      - string: renders cell value when type of the cell is `string`.
      - bool: renders cell value when type of the cell is `boolean`.
      - number: renders cell value when type of the cell is `number`.
      - date: renders cell value when type of the cell is `date`.
      - array: renders cell value when type of the cell is `array`.
      - object: renders cell value when type of the cell is `object`.
      - null: renders cell value when type of the cell is `null`.
      - undefined: renders cell value when type of the cell is `undefined`.

  - To use the renderer, you have to specify the renderer's identity to ``options.cells[column].renderer``.
  - default: ``{}``

  .. code-block:: jsx

    import * as React from "react";
    import {
      GridSheet,
      Renderer,
    } from "react-gridsheet";

    class QuoteRenderer extends Renderer {
      string(value) {
        return `"${value}"`;
      }
      stringify(value) {
        return "" + value;
      }
    }

    export default function App() {
      return (<GridSheet
        data={[ // any[][]
          ["a", 1, true],
          ["b", 2, false],
          ["c", 3, null],
        ]}
        options={{
          renderers: {
            quote: new QuoteRenderer(),
          },
          cells: {
            A: { renderer: "quote" },
          },
        }}
      />)
    }

:options.parsers:

  - You can create a custom Parser inheriting ``Parser`` class.

    - It has to be registered here. The key identifies the parser.
    - You can override methods on the Parser class.

      - callback: receives parsed value and returns value that should be stored into the cell. (from v0.5.7)

        - parsed value is passed to first argument.
        - previous value is passed to second argument.

      - parse: parses inputted value.

        - This calls methods in the order of the `parseFunctions`.
        - This continues parsing until the parse succeeds.
          If parsing does not succeeded until the end, the inputted value will be the parsed value.

          - parse success means not returning ``undefined`` or ``null``.

        - This parses inputted value in the following order:

          - number: parses it to number type.
          - date: parses it to date type.
          - bool: parses it to boolean type.

  - To use the parser, you have to specify the parser's identity to ``options.cells[column].parser``.

  - default: ``{}``

  .. code-block:: jsx

    import * as React from "react";
    import {
      GridSheet,
      Parser,
    } from "react-gridsheet";

    class EvalParser extends Parser {
      parseFunctions = [
        this.evaluate,
      ];

      evaluate(value) {
        return eval(value);
      }
      callback(parsed: any, old: any) {
        console.debug(`before: ${old}, after: ${parsed}`);
        return parsed;
      }
    }

    export default function App() {
      return (<GridSheet
        data={[ // any[][]
          ["a", 1, true],
          ["b", 2, false],
          ["c", 3, null],
        ]}
        options={{
          parsers: {
            eval: new EvalParser(),
          },
          cells: {
            C: { parser: "eval" },
          },
        }}
      />)
    }

:options.resize:

  :both: allows resizing both directions.
  :vertical: allows resizing vertically.
  :horizontal: allows resizing horizontally.
  :none: does not allow resizing.

:options.onSave:

  - A callback function on you saving.

    - Saving events are emitted by `Ctrl + s` or `Command + s`.

  - ``(matrix, cellsOption, positions) => void``

    - `positions` argument has 3 keys. Every values gets cell position indexes like ``[Y as number, X as number]``.

      - pointing: A pointing cell.
      - selectingFrom: A cell selected (dragged) from. If no cells dragged, it will be ``[-1, -1]``.
      - selectingTo: A cell selected (dragged) to. If no cells dragged, it will be ``[-1, -1]``.


:options.onChange:

  - A callback function on ``matrix`` or ``options.cells`` changed.
  - ``(matrix, cellsOption, positions) => void`` (same as onSave)

:options.onSelect:

  - A callback function on select or dragging cells.
  - ``(matrix, cellsOption, positions) => void`` (same as onSave)
