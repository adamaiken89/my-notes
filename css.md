# Modern CSS

## Why Modern

CSS is evolving

- Mix a lot of old practices and modern way while no one could do a clear cut

Not necessary to mix a lot of additional tool to write good CSS

## Agenda

- When to use Relative Unit
  - Zoom friendly to margin, padding and gap
  - Different behaviour of font-size vs other properties

- Logical Properties
  - Handle multi-language (vertical vs horizontal)
  - Replacing most properties

## Reading Syntax Spec

Formal grammar for CSS property/function values (MDN, W3C specs)

- Component value types
  - Keywords: literal words (e.g. `auto`, `ease-in`)
  - CSS-wide keywords: `inherit`, `initial`, `revert`, `revert-layer`, `unset` (implicit in all)
  - Literals: `/` and `,` separate value parts
    - `border-radius: 10px 20px / 30px 40px` — slash separates horizontal and vertical radii
    - `font: italic bold 1em/1.5 sans-serif` — slash separates size and line-height
    - `font-family: "Helvetica", Arial, sans-serif` — comma separates list items
  - Data types: `<angle>`, `<length>`, `<string>` etc.
    - `transform: rotate(45deg)` — `<angle>`
    - `width: 100px` — `<length>`
    - `content: "hello"` — `<string>`
    - `font-size: 1.2` — `<number>`
    - `opacity: 0.5` — `<number>` in [0,1] range

- Combinators (precedence: juxtaposition > `&&` > `||` > `|`)
  - Juxtaposition (space): mandatory, exact order — `border: 1px solid black`
  - `&&`: all mandatory, any order — `border-image: url(...) 30% 30% stretch` can be `stretch 30% 30%`
  - `||`: at least one, any order — `border-image: url(...) 30% stretch` or `url(...) stretch 30%`
  - `|`: exactly one (exclusive) — `text-align: left | center | right | justify`
  - `[ ]`: grouping — `flex: [ <'flex-grow'> <'flex-shrink'>? ] || <'flex-basis'>`

- Multipliers
  - No multiplier: exactly 1 — `display: block`
  - `?`: 0 or 1 (optional) — `flex: 1 1 auto` vs `flex: 1 auto`
  - `*`: 0 or more — `text-shadow` multiple shadows
  - `+`: 1 or more — `transform: translateX(10px) rotate(45deg)`
  - `{A,B}`: between A and B times — `border-radius` 1–4 values
  - `#`: 1+ times, comma-separated — `font-family` list
  - `!`: group must produce at least 1 value — `[ bold? smaller? ]!`

- Reference: <https://developer.mozilla.org/en-US/docs/Web/CSS/Value_definition_syntax>


## Good Property

- initial
- revert
- inherit (*)
- unset

## Reference

CSS in Depth
<https://www.manning.com/books/css-in-depth-second-edition>

Mozilla CSS Reference
<https://developer.mozilla.org/en-US/docs/Web/CSS>

Refactoring UI
<https://refactoringui.com>
