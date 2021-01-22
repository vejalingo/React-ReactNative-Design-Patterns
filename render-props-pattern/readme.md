## Render Props

The term `render prop` refers to a technique for sharing code between React components using a prop whose value is a function.
> [Reactjs blog](https://reactjs.org/docs/render-props.html)

A component with a render prop takes a function that returns a React element and calls it instead of implementing its own render logic.

Lets imaging we want to do the following
- `fetching data`, We want to have a component that abstracts away all the mess of HTTP and just serve us the data when it is done


Let's look at this markup

```jsx
const ProductDetailComponent = ({ product: { title, description }}) => ( 
  <> 
    <h2>{title}</h2> 
    <div>{description}</div> 
  </> 
) 
  
<FetchDataComponent url="https://randomuser.me/api/" 
  render={(data) => <ProductDetailComponent product={data.product} /> }
/>
```

As we can see above we have two different components `ProductDetailComponent` and `FetchDataComponent`.
`ProductDetailComponent` is more like a [presentation component](https://www.jetbrains.com/webstorm/guide/tutorials/react_typescript_tdd/presentation_components/?gclid=Cj0KCQiAjKqABhDLARIsABbJrGnHxoNGNmu1yHCEBHq4vyjcUGp1QE2hQ90SdAw-c9jH1lLmQYBj30waAsdMEALw_wcB&gclsrc=aw.ds)
`FetchDataComponent` on the other hand looks rather different, It has a url `prop` and has a `render` property which renders the `ProductDetailComponent`.

### How render props work

Lets reverse engineer the `FetchDataComponent` to see how this works

Lets zoom into the code again

```jsx
<FetchDataComponent 
  url="https://randomuser.me/api/" 
  render={(data) => <ProductDetailComponent product={data.product} /> }
/>
```

Our `FetchDataComponent` has an attribute `render` that takes a function that returns JSX markup. **Here is the thing**, the whole render-props pattern is about us invoking a function in our return method. let me show this by code:

```jsx
const FetchDataComponent = ({ url, render }) => render()
```

This is simply the  `render-props pattern`.
The way we used the `FetchDataComponent` means we at least need to send something into the `render()` call. Let's extract the function invocation and have a look at it:

```js
(data) => <ProductDetailComponent product={data.product} />
```

As we can see above that we need a parameter data and data seems to be an object, so where does this data come from? 

Well thats the thing with our `FetchDataComponent`, it does some heavy lifting for us namely carrying out HTTP calls.

### Lets take a deep look into how the component might look


```jsx
import React, { useState, useEffect} from 'react'

const FetchDataComponent = ({ url, render }) => {
  const [data, setData] = useState({})
  const [error, setError] = useState('')

  useEffect(() => {
      fetchData()
  }, [])

  const fetchData = async () => {
    try { 
    const results = await fetch(url); 
    const jsonRes = await results.json();

    setData(jsonRes); 

    catch (error) { 
      setError(error) 
    } 
  }
}

return data ? render(data) : <pre>{error.message}</pre>

```

To Top this up we can add error state `render prop` or loading state

```jsx
import React, { useState, useEffect} from 'react'

const FetchDataComponent = ({ url, render, handleError }) => {
  const [data, setData] = useState({})
  const [error, setError] = useState('')

  useEffect(() => {
      fetchData()
  }, [])

  const fetchData = async () => {
    try { 
    const results = await fetch(url); 
    const jsonRes = await results.json();

    setData(jsonRes); 

    catch (error) { 
      setError(error) 
    } 
  }
}

return data ? render(data) : handleError(error)

```

Now, We could use this component like this:

```jsx
<FetchDataComponent 
  url="https://randomuser.me/api/"
  render={(data) => <ProductDetailComponent product={data.product} />} 
  handleError={(error) => <pre>{error.message}</pre>} 
/>
```

**Challenge**
try to extend this component by adding a render prop for the loading state

Next [>> Render props](../controlling-state-pattern/) 