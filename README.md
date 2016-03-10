Advanced React links for purely functional two-way data binding.

- Implements standard React 0.14 links API
- API extensions:
    - Declarative binding to event handlers.
    - Simple form validation.
    - Link to plain object and array members with pure updates.
    - Derivative boolean links for checkbox and radio groups.
- Reference implementation for 'linked' tags:
    - Standard tags: `<Input />` (with validation), `<Select />`, `<TextArea />`
    - Custom tags: `<Radio />`, `<Checkbox />`

```javascript
var linkToArray = Link.state( this, 'phonebook' );

var list = linkToArray.map( ( itemLink, i ) => (
    <div key={ i }>
        <Input valueLink={ itemLink.at( 'name' ) } />            
    </div>
));
```    

> This technology is one of the key components of [NestedReact](https://github.com/Volicon/NestedReact) architecture, 
> helping you to build large-scale React applications with a powerful and fast [NestedTypes](https://github.com/Volicon/NestedTypes/)
> classical OO models.

# Installation

`npm install valuelink`

CommonJS module, MIT License. No side dependencies.

```javascript
// Links
import Link from 'valuelink'

// React components with linked tags
import { Input } from 'valuelink/tags.jsx'
```

# API

## Create link

- Create custom link: `new Link( value, requestChange )`

    ```javascript
    var customLink = new Link( this.value, x => this.value = x );
    ```

- Create link to react component's state attribute:

    ```javascript
    var nameLink = Link.state( this, 'name' );
    ```

## Update link
 
- Set link value: `link.set( x )` or `link.requestChange( x )` 

    ```javascript
    <button onClick={ () => boolLink.set( !boolLink.value ) } />
    ```

- Update link value: `link.update( prevValue => newValue )` 

    ```javascript
    <button onClick={ () => boolLink.update( x => !x ) } />
    ```

- Create action to handle UI event: `link.action( ( prevValue, Event ) => newValue )`

    ```javascript
    <button onClick={ boolLink.action( x => !x ) } />
    ...
    const setValue = ( x, e ) => e.target.value;
    ...
    <input  value={ link.value }
            onChange={ link.action( setValue ) } />
    ```

## Links validation

Links has `link.check( condition, error = 'Invalid value' )` method which can be used to
check the sequence of conditions. Checks can be chained. 

`condition` is predicate function `linkValue => isValid` taking link value as an argument.
Whenever `condition` returns falsy value, `link.error` will take the value of corresponding `error`. 
`link.error` field may be analyzed by custom `<Input />` control to indicate an error. 

This mechanics can be used to add ad-hoc validation in `render`. 

- Simple asserts:

    ```javascript
    var numLink = List.state( this, 'num' )
                    .check( x => x >= 0 && x <=5 );
                    
    console.log( numLink.error );                    
    ```
    
- Validation asserts with custom error objects
    ```javascript
    var numLink = List.state( this, 'num' )
                    .check( x => x >= 0 && x <=5, 'Number must be between 0 and 5' );
                    
    console.log( numLink.error );                    
    ```

- Chained validation asserts
    ```javascript
    var numLink = List.state( this, 'num' )
                    .check( x => x >= 0, 'Negative numbers are not allowed' )
                    .check( x => x <= 5, 'Number should be not greater than 5' );
                    
    console.log( numLink.error );                    
    ```

## Links to object and arrays      

If linked value is plain object or array, it's possible to generate
  links to their members. Whenever this derivative links will be
  updated, it will lead to proper purely functional update of the
  whole structure. I.e. if you have array in component state,
    and link to its element will be updated, it will lead to proper
    update of stateful component.

- Take link to array or object member: `link.at( key )`
    ```javascript
        const firstElementLink = arrayLink.at( 0 );
    ``` 
    or
    ```javascript
        const nameLink = objectLink.at( 'name' );
    ``` 

- Map and filter through array or object: `link.map( iterator )` 
    ```javascript
    var list = stringArrayLink.map( ( itemLink, index ) => {
        if( itemLink.value ){        
            return (
                <div key={ index }>
                    <Input valueLink={ itemLink } />            
                </div>
            );
        }
    });
    ```
    
## Offhand boolean links

- Link to the presence of value in array: `arrayLink.contains( value )`
    ```javascript
    const optionXLink = arrayLink.contains( 'optionX' );
    ```

- Link to value equality: `link.equals( value )`
    ```javascript
    const optionXLink = stringLink.equals( 'optionX' );
    ```

# Data binding examples

Here are the set of working [examples](/index.html) for typical data binding use cases.
Sources are [here](/example/main.jsx).