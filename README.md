# gloss 💅
HOC for making style a first class citizen of components. Inline styles for all.

Combines a few things:

- [JSS](https://github.com/cssinjs/jss) styles
- [motion-nice-styles](https://github.com/motion/gloss/tree/master/packages/nice-styles) for powerful JS syntax for styles
- Advanced theme engine
- Babel plugin to allow simpler $style props

#### features
- small library, relies on other small libraries
- auto prefixes
- supports pseudos
- supports media queries
- themes are far easier way to restyle multiple elements
- powerful js object-based styles
- dynamic and static styles
- keeps html easy to read

#### install

```js
npm install --save gloss
```

To use the gloss decorator, add this to your babel config:

```js
{
  "babel": {
    "plugins": [
      ["gloss/transform", { "decoratorName": "style" }]
    ]
  }
}
```

#### usage
Gloss must first be instantiated, and supports two options:

```js
import gloss from 'gloss'

export const style = gloss({
  // when on, slightly faster performance but no theme support
  dontTheme: false,
  // optional object with base styles that are accessible using $$props
  baseStyles: {
    fullscreen: {
      position: 'absolute',
      top: 0,
      right: 0,
      bottom: 0,
      left: 0
    }
  }
})

// to use later in app:
// import { style } from './path/to/glossSetup'
```

For more information on how to write styles, see:

- shorthands: [motion-nice-styles](https://github.com/motion/gloss/tree/master/packages/nice-styles)
- base synax: [JSS](https://github.com/cssinjs/jss)

#### examples

```js
// Gloss comes with a shorthand for simple styles:
import gloss from 'gloss'

const glossy = gloss()

export const Title = glossy('h1', {
  border: [1, '#eee'],
  background: 'azure',
  fontSize: 22
})

export const Page = glossy('section', (props) => ({
  padding: 20,
  background: props.background,
}))
```

A small view using the decorator:

```js
import gloss from 'gloss'

const style = gloss()

@style class extends Component {
  render() {
    return <h1 $black $bg="#fff">Test</h1>
  }

  static style = {
    h1: {
      fontSize: 22,
      color: [255, 255, 255],
      '&:hover': { color: 'red', },
      borderBottom: [2, 'solid yellow']
    },
    black: {
      color: 'black',
    },
    bg: color => ({
      background: color,
      borderBottom: [2, 'solid', color]
    })
  }
}
```

#### themes

Use themes for really easy variant looks for components. Gives you complete control to
change multiple elements with a single prop.

```js
import gloss from 'gloss'

const style = gloss()

@style class Title extends React.Component {
  render() {
    return (
      <base>
        <h1>Test</h1>
      </base>
    )
  }

  static style = {
    base: {
      padding: 10,
      transform: {
        rotate: '45deg',
      },
      filter: {
        grayscale: 0,
      }
    },
    h1: {
      fontSize: 22,
    }
  }

  static theme = {
    big: {
      base: {
        padding: 20,
      },
      h1: {
        fontSize: 50,
      }
    },
    tint: props => ({
      base: {
        background: [props.color, 0.5],
      },
      h1: {
        color: props.color,
      }
    })
  }
}

// Use
React.render(<Title big tint="yellow" />, document.getElementById('app'))
```

#### Differences between style and theme:

- Theme requires a further nesting of objects, to specify which tag to target for each style
- Theme passes in all props if you specify a function! This gives more power to use any prop to affect the styling within a given specific theme property.

#### base styles

Helpful for maintaining a common set of styles for every component. Using `$$` to access keeps things explicit.

```js
import gloss from 'gloss'

const style = gloss({
  baseStyles: {
    row: {
      flexFlow: 'row',
    },
  },
})

@style
class extends React.Component {
  render() {
    return (
      <div $$row>
        <h1>Test1</h1>
        <h1>Test2</h1>
      </div>
    )
  }

  static style = {
    div: {
      background: 'yellow',
    },
  }
}
```


#### more advanced
how to make low level components

```js
import gloss from 'gloss'

const style = gloss({
  baseStyles: {
    red: {
      background: 'red',
    },
    style: obj => obj,
  }
})

@style
export default class Section {
  static defaultProps = {
    maxHeight: 'auto',
    minHeight: 800,
    background: colors.primary,
    height: 'auto',
    color: '#444',
    position: 'relative',
    overflow: 'hidden',
    padding: [90, 0],
    [media.small]: {
      padding: [50, 0],
    },
  }

  render() {
    const { children, windowHeight, maxHeight, minHeight, attach, ...props } = this.props

    if (windowHeight) {
      props.height = Math.max(minHeight, Math.min(maxHeight, window.innerHeight))
    }

    return (
      <section $$style={props} $$red {...attach}>
        {children}
      </section>
    )
  }

  static theme = {
    centered: {
      section: {
        justifyContent: 'center',
      },
    },
  }
}
```

#### contributing

After cloning this repo, run `npm run bootstrap`. You can then link it into an app and make changes.
