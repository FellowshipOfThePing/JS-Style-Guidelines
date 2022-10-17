# Code Style Guidelines

# Preface

This guide is partially based on [Airbnb's JavaScript Guidelines](https://github.com/airbnb/javascript), and partially based on my radically (un)informed opinions. Most of these rules are very much up for debate (especially rules that aren't accompanied by a❓rationale), all that matters is that they're applied consistently throughout a codebase.

Some best practices around things like alignment aren't covered here, but are enforced via [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode). Make sure you've installed the Prettier extension in your IDE so your code conforms.

# General Best Practices

## Naming

- **Extensions:** Use `.js` extensions for all JS files, including component files.
- **Filenames:** Use PascalCase for component files (e.g. `SmallDropdown.js`) and camelCase in all other cases (e.g. `utils.js`).
- **Variables/Functions:** Use camelCase.
    - Keys to constant values in config objects - like in `[Routes.js](https://github.com/Sellerant/sellerant/blob/master/src/Routes.js)` - are a rare exception to this rule. For those names, use `ALL_CAPS_SNAKE_CASE`.
- ***Please for love of all things you hold dear,*** always use descriptive variable/function names.
    - Variable names should start with nouns
    - Function names should start with verbs

## Variables

- Always use  `let` & `const` to declare variables. Avoid using `var`.
    
    <aside>
    ❓ `var` is function-scoped, whereas `let` & `const` are block-scoped, meaning you're less likely to create naming conflicts or accidentally reassign variables. See [here](https://blog.usejournal.com/awesome-javascript-no-more-var-working-title-999428999994) for more details.
    
    </aside>
    

- Use one `const` or `let` declaration per variable or assignment.
    
    <aside>
    ❓ Readability + helps to avoid easy slip-ups, like accidentally including a `;` where a `,` should be.
    
    </aside>
    
    ```jsx
    // bad
    const lastName = 'Russell',
    			pals = ["David", "Isaiah", "Calvin"],
    	    isAnEngineer = true;
    
    // good
    const lastName = 'Russell';
    const pals = ["David", "Isaiah", "Calvin"];
    const isAnEngineer = true;
    ```
    

- Group all `const`s and `let`s separately
    
    ```jsx
    // bad
    let major = "CS";
    const school = "WashU";
    let location = "bumble frick nowhere";
    const age = 21;
    
    // good
    const school = "WashU";
    const age = 21;
    
    let major = "CS";
    let location = "bumble frick nowhere";
    ```
    

- When programmatically building up strings, use template strings instead of concatenation.
    
    <aside>
    ❓ Template strings give you a readable, concise syntax with proper newlines and string interpolation features.
    
    </aside>
    
    ```jsx
    // bad
    const myGreeting = "Hello " + name + "!";
    
    // good
    const myGreeting = `Hello ${name}!`;
    ```
    

## Functions

- Functions should be defined using arrow functions.
    
    ```jsx
    // bad
    function doSomething() {
    	  console.log("something");
    }
    
    // good
    const doSomething = () => {
    	  console.log("something");
    }
    ```
    
    <aside>
    ❗ There are several exceptions this rule, most notably in-object function definitions, which should not be expressed using arrow functions. See [here](https://medium.com/@chrisamori/when-not-to-use-javascript-arrow-functions-376aaf028d8d) for more details.
    
    </aside>
    
    ```jsx
    const myObject = {
        title: "object title!",
        command() {
    				console.log("I love JS!");
    		}
    }
    ```
    

- Avoid defining functions within other functions. If you need to reference the same variable within multiple functions, either refactor them to be the same function, or pass the values in as arguments.
    
    <aside>
    ❓ Readability + helps to avoid scope-related issues like accidentally creating [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures).
    
    </aside>
    
    ```jsx
    // bad
    const myFirstFunc = (value1, value2) => {
    		// ...
    
    		const mySecondFunc = () => {
    				if (value1) return value1;
    				return value2;
    		}
    		mySecondFunc();
    }
    
    // good
    const myFirstFunc = (value1, value2) => {
    		// ...
    
    		mySecondFunc(value1, value2);
    }
    
    const mySecondFunc = (value1, value2) => {
    		if (value1) return value1;
    		return value2;
    }
    ```
    

- Default parameters should always be defined last in a function.
    
    <aside>
    ❓ Allows for creation of new default params without having to change the arguments in every instance of the function. Also creates [cross-parameter availability](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters#earlier_parameters_are_available_to_later_default_parameters).
    
    </aside>
    
    ```jsx
    // bad
    const doSomething = (title, active = false, label) => {
    		// ...
    }
    
    // good
    const doSomething = (title, label, active = false) => {
    		// ...
    }
    ```
    

- Never reassign parameters.
    
    <aside>
    ❓ Can be [misleading](https://eslint.org/docs/rules/no-param-reassign.html) or lead to unexpected behavior
    
    </aside>
    
    ```jsx
    // bad
    const f1 = (a) => {
      a = 1;
      // ...
    }
    
    const f2 = (a) => {
      if (!a) { a = 1; }
      // ...
    }
    
    // good
    const f3 = (a) => {
      const b = a || 1;
      // ...
    }
    
    const f4 = (a = 1) => {
      // ...
    }
    ```
    

## Async

- Use `async` / `await` & `try` / `catch` whenever possible (as opposed to `then` / `catch`).
    
    <aside>
    ❓  `try` / `catch` blocks allow you to call as many promises as you want in a single block, and allow for parallel promises. You can also directly return values from a `try` / `catch` block, without having to relay returned callback values like in a promise chain.
    
    </aside>
    
    ```jsx
    // bad
    return firstRequest()
    		.then(res1 => secondRequest(res1.data))
    		.then(res2 => res2.data)
    		.catch(err => console.log(err));
    
    // good
    try {
    		let res1 = await firstRequest();
    		let res2 = await secondRequest(res1.data);
    		return res2.data;
    } catch (err) {
    		console.log(err);
    }
    ```
    

## Comparison Operators

- Use strict equality operators (`===` and `!==`) over loose ones (`==` and `!=`)
    
    <aside>
    ❓ Strict comparisons take data type into account when calculating equality. See the [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness) for more details.
    
    </aside>
    

## Misc.

*(We'll find proper places for these rules later)*

- Leave spaces between function declarations
    
    ```jsx
    // bad
    const funcOne = () => {
    		// ...
    }
    const funcTwo = () => {
    		// ...
    }
    
    // good
    const funcOne = () => {
    		// ...
    }
    
    const funcTwo = () => {
    		// ...
    }
    ```
    
- Using console.log() is an entirely valid method of debugging, but always remove unnecessary console calls before merging to master, otherwise the console gets crowded quickly.

# React Best Practices

## Component Tree Structure

- All src-level directories (`components`, `pages`, etc) should have `index.js` files for aggregating exports, and cross-directory imports should only reference these files (e.g., a file in `pages` should import components directly from `"../components"`).
    
    <aside>
    ❓ If everything in a directory is exported from the same place, re-organizing the directory is easy. Instead of having to rewrite imports in every file that uses a component after it's moved within the components directory, you only have to rewrite how it's imported into that one index file.
    
    </aside>
    
    ```jsx
    // bad
    import SmallDropdown from "../components/Dropdowns/SmallDropdown";
    
    // good
    import { SmallDropdown } from "../components";
    ```
    

- Requests (and any large functions) should be kept as high in the component tree as possible, and passed down to lower-level components. For example, if clicking a button triggers a request to the backend, that request should be defined in the page component, and passed down into the button as an `onClick` prop value.
    
    <aside>
    ❓ It's easier to debug function/request problems when everything lives in the same place, and components are more reusable when they're not tethered to specific functionality.
    
    </aside>
    

## Styling

- Components should be styled "in a vacuum", with indifference to the styling of their implementation. Avoid adding things like margin or absolute positioning to the top-layer tags of components, unless they're container components (in which case their sole purpose is to add that styling around other components).
    
    <aside>
    ❓ Styling components with a specific implementation in mind decreases reusability
    
    </aside>
    
    ```jsx
    // bad
    const BodyText = ({ text }) = {
    		return (
    				<div className="mt-3 mb-2">
    						<p className="text-sm text-gray-500">
    								{text}
    						</p>
    				</div>
    		)
    }
    
    <BodyText>Text Goes Here</BodyText>
    
    // ------------------------------------------------------------------- //
    
    // good
    const BodyText = ({ children }) = {
    		return (
    				<p className="text-sm text-gray-500">
    						{children}
    				</p>
    		)
    }
    
    <div className="mt-3 mb-2">
    		<BodyText>Text Goes Here</BodyText>
    </div>
    ```
    

- Inline CSS is sometimes necessary, but default to Tailwind
    
    ```jsx
    // bad
    <div style={{ height: "20px", width: "20px" }} />
    
    // good
    <div className="h-5 w-5" />
    
    // good (too specific for tailwind)
    <div style={{ transform: "translate(-35%, -20px)" }} />
    ```
    

## Components

- Components must be functional (not class based), and use arrow functions.
    
    ```jsx
    // bad (class-based)
    class Label extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }
    
    // bad (not Arrow function)
    function Label({ hello }) {
      return <div>{hello}</div>;
    }
    
    // good (w/ return statement)
    const Label = ({ hello }) => {
    	return <div>{hello}</div>;
    );
    
    // good (direct return)
    const Label = ({ hello }) => (
      <div>{hello}</div>
    );
    ```
    
- Use [hooks](https://www.notion.so/react-hooks-componentdidmount-fd4464366d5c480498aa33011a123b89) exclusively for state and component lifecycle management (useState, useEffect, useRef, etc.)
- Only include one component per file, with files & component sharing the same name.
    - Example - The `Sidebar` ****component should live in a file named `Sidebar.js`
    - Exception - If a component is only ever used by another specific component (e.g., a `Dropdown` component might share a file with a `DropdownItem` component that is only ever used in that dropdown).

## Naming

- **Components:** Use the filename as the component name. For example, `SmallDropdown.js` should contain a component named `SmallDropdown`.
- **Props:** Always use camelCase for prop names. Avoid using standard HTML prop names for for custom component props.
    
    ```jsx
    // bad (overwriting default prop name)
    <MyComponent style="fancy" />
    
    // bad (props not camelCase)
    <MyComponent my_prop="fancy" MyOtherProp="fancy" />
    
    // good
    <MyComponent myProp="fancy" />
    ```
    

## Props

- Components should be indifferent to prop structure
    
    <aside>
    ❓ Tightly coupling components to specific prop structures lower reusability
    
    </aside>
    
    <aside>
    ❗ This rule can be ignored for very large components that have many child components and rely on many different values to populate (the [`Sidebar`](https://github.com/Sellerant/sellerant/blob/master/src/components/Sidebar/Sidebar.js) component, for example, relies on the `ctaData` prop for rendering), or for components that are only used for very specific applications.
    
    </aside>
    
    ```jsx
    // bad
    const Foo = ({ data }) = {
    		return (
    				<div>
    						<p>{data.label}</p>
    						<p>{data.color}</p>
    						<p>{data.type}</p>
    				</div>
    		)
    }
    
    // good
    const Foo = ({ label, color, type }) = {
    		return (
    				<div>
    						<p>{label}</p>
    						<p>{color}</p>
    						<p>{type}</p>
    				</div>
    		)
    }
    ```
    

- Omit the value of the prop when it is explicitly true.
    
    ```jsx
    // bad
    <Foo hidden={true} />
    
    // good
    <Foo hidden />
    
    // good
    <Foo hidden={false} />
    ```
    

- Always include an `alt` prop on `<img>` tags, but do not use words like "image", "photo", or "picture".
    
    <aside>
    ❓ *Screenreaders already announce img elements as images, so there is no need to include this information in the alt text.*
    
    </aside>
    
    ```jsx
    // bad (no alt)
    <img src="hello.jpg" />
    
    // bad (alt contains "Picture")
    <img src="hello.jpg" alt="Picture of me waving hello" />
    
    // good
    <img src="hello.jpg" alt="Me waving hello" />
    ```
    

- Always include a `key` props in component list items, but avoid using an array index or non-unique value.
    
    <aside>
    ❓ *Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity.*
    
    </aside>
    
    ```jsx
    // bad
    {todos.map((todo, index) =>
      <Todo
        {...todo}
        key={index}
      />
    )}
    
    // good
    {todos.map(todo => (
      <Todo
        {...todo}
        key={todo.id}
      />
    ))}
    ```
    

- Prop definitions should be de-structured.
    
    <aside>
    ❓ Makes it easier to understand exactly what data is being passed into a component.
    
    </aside>
    
    ```jsx
    // bad
    const Foo = (props) => (
    		<div>{props.label}</div>
    )
    
    // good
    const Foo = ({ label }) => (
    		<div>{label}</div>
    )
    ```
    

- If a prop and state value share the same name, this will create a naming conflict. Solve this by adding the prefix "local" to the state value.
    
    ```jsx
    // bad (will cause error)
    const Foo = ({ label }) => {
    		const [label, setLabel] = useState(label);
    
    		return <div>{label}</div>;
    }
    
    // good
    const Foo = ({ label }) => {
    		const [localLabel, setLocalLabel] = useState(label);
    
    		return <div>{localLabel}</div>;
    }
    ```
    

- If a prop takes a string value as input, do not include curly braces around the string (unless you're amending the string using inline variables).
    
    ```jsx
    // bad
    <Route
        path={"/cta/:cid/todo"}
    		component={Cta}
    />
    
    // good
    <Route
        path="/cta/:cid/todo"
    		component={Cta}
    />
    
    // good
    <Route
        path={`${Routes.CTA_BASE_ROUTE}/:cid/todo`}
    		component={Cta}
    />
    ```
    

## Refs

- Always use the `useRef` hook or a [callback hook](https://reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node) to reference components. Avoid using document methods.
    
    <aside>
    ❗ There are rare cases (like when dealing with lists of components) in which using a document method like `getElementById()` is more practical, however `useRef()` will typically suffice, and is the preferred solution.
    
    </aside>
    
    ```jsx
    // bad (usually)
    const myRef = document.getElementById("myComponent");
    return <Foo id="myComponent" />;
    
    // good
    const myRef = useRef(null);
    return <Foo ref={myRef} />;
    
    ```
    

## Tags

- Always self-close tags that have no children.
    
    ```jsx
    // bad
    <Foo variant="stuff"></Foo>
    
    // good
    <Foo variant="stuff" />
    ```
    

## Functions

- If a function takes more than two arguments, define it as a named function instead of an inline anonymous function.
    
    ```jsx
    // bad
    <Foo 
    		onClick={(value1, value2, value3) => {
    				if (value1 > 10) {
    						console.log(value1)
    				} else {
    						console.log(value2 + value3)
    				}
    		}}
    />
    
    // good
    const handleClick = (value1, value2, value3) => {
    		if (value1 > 10) {
    				console.log(value1)
    		} else {
    				console.log(value2 + value3)
    		}
    }
    <Foo onClick={handleClick} />
    ```
    

## Component File Ordering

<aside>
❗ Each of these sections should be separated by a space, and sections **d** - **i** should be labelled with comments above each section.

</aside>

1. Package Imports
2. Local Imports
3. Constant Values
4. Component Definition
    1. useContext
    2. useState
    3. Custom/Package hooks (e.g., useHistory from react-router) excluding useHotKeys
    4. useEffect
    5. Component Functions (requests, mutations, etc.)
    6. Event Handler Functions (onClickBack/Up/Down/etc.)
    7. Key Commands (useHotKeys)
    8. CommandK functions (imported from the `commands` directory)
    9. Component JSX
5. File exports