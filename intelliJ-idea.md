# IntelliJ Idea

- best Java editor, period
- Understanding the features and shortcuts can greatly boost your productivity

## Features

### Auto-Completion

- Supports Camel Case jumping
- Supports postfix completion to define variables

## Kick-start

- Idea IDE own tutorial (Press `Shift` twice, then type "Getting Started" to open the tutorial)
<https://www.jetbrains.com/help/idea/getting-started.html> Office Tutorial

## Shortcuts

- `Cmd+Shift+A`: Find actions/settings
- `Shift` (double tap): Search everywhere (files, classes, symbols)
- `Cmd+E`: Recent files
- `Cmd+B` or `Cmd+Click`: Go to definition
- `Cmd+O`: Go to class
- `Cmd+Alt+L`: Reformat code
- `Alt+Enter`: Show intention actions/quick fixes
- `Cmd+/` or `Cmd+Shift+/`: Comment/uncomment line or block
- `Shift+F6`: Rename symbol
- `Cmd+D`: Duplicate line

### Java-Specific Shortcuts & Tricks

- `Cmd+N`: Generate code (getters, setters, constructors, equals, hashCode, toString, etc.)
- `Cmd+Alt+V`: Introduce variable (extract selected expression to variable)
- `Cmd+Alt+M`: Extract method
- `Cmd+Alt+F`: Extract field
- `Cmd+Alt+C`: Extract constant
- `Cmd+Shift+T`: Generate or go to test class
- `Ctrl+O`: Override methods
- `Ctrl+I`: Implement methods
- `Cmd+P`: Show method parameter info (while calling or inside method call)
- `Ctrl+J`: Show JavaDoc for symbol at caret
- `Cmd+Alt+B`: Go to implementation
- `Cmd+Alt+Left/Right`: Navigate between methods in the file
- `Ctrl+Shift+Space`: Smart type completion (context-aware suggestions)
- `Cmd+F12`: File structure popup (list of methods, fields)
- `Cmd+Y`: Delete line (safer than cut for quick removal)
- `Cmd+Shift+↑ / ↓`: Move code element up/down (works for methods, fields, statements)

#### Functional Java Tricks

- Type `psvm` + `Tab`: Expands to `public static void main(String[] args)`
- Type `sout` + `Tab`: Expands to `System.out.println()`
- Type `iter` + `Tab`: Expands to for-each loop for collections and arrays
- Type `itar` + `Tab`: Expands to iterate array loop

#### Navigation Tricks

- `Cmd+U`: Go to super method/class
- `Ctrl+Shift+A` then type "Show Bytecode": View Java bytecode of the current file
- Highlight variable and press `Cmd+Shift+F7`: Highlight all usages in file

#### Reformat and Optimize Imports

- `Ctrl+Alt+O`: Optimize imports
- `Cmd+Alt+L`: Reformat code (can be run on selection or file)

#### Live Templates

- Use `Cmd+J` to see available live templates for Java (like `psf`, `psfi`, `psfs` for public static final fields)

#### Quick Fix and Refactor

- Use `Alt+Enter` for context-aware code fixes and suggestion, including implementing unimplemented methods, importing missing classes, and converting loops/lambdas.

> Pro Tip: Right-click a method or class for "Refactor" menu – use "Rename",
  "Change Signature", "Move", etc.

## Plugins

- **Key Promoter X**: Learn shortcuts as you work
- **Rainbow Brackets**: Colorful bracket matching
- **Lombok**: Support for Lombok annotations
- **.ignore**: Manage .gitignore and other ignore files
- **String Manipulation**: Advanced string operations
- **GitToolBox**: Enhanced Git integration
- **SonarLint**: Real-time code quality and security analysis
- **Prettier**: Code formatter for JavaScript, TypeScript, CSS, and more
- **CheckStyle-IDEA**: Check Java code style
- **Maven Helper**: Analyze and exclude conflicting dependencies
- **Database Navigator**: Enhanced database tools
- **Grep Console**: Colorize and filter console output
- **Translation**: Translate text directly in IDE
- **CodeGlance**: Minimap view of your code
- **AceJump**: Quick cursor navigation
- **RestfulTool**: RESTful service development toolkit
