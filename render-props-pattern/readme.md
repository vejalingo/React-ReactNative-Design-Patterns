## Render Props

The term `render prop` often refers to a technique for merely sharing code between React components using a prop whose value is a function.
> [Reactjs blog](https://reactjs.org/docs/render-props.html)

A component with a render prop takes a function that returns a React element and calls it instead of implementing its own render logic.

Let's imagine that we want to do the following:
- `fetching data`. We want to have a component that enables you to be able to find a way to escape from mess that could occure from the  HTTP and thus,  serve us the data when it is done.


Let's look at this markup:

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

As you can see above, we have two different components `ProductDetailComponent` and `FetchDataComponent`.
`ProductDetailComponent` is more like a [presentation component](https://www.jetbrains.com/webstorm/guide/tutorials/react_typescript_tdd/presentation_components/?gclid=Cj0KCQiAjKqABhDLARIsABbJrGnHxoNGNmu1yHCEBHq4vyjcUGp1QE2hQ90SdAw-c9jH1lLmQYBj30waAsdMEALw_wcB&gclsrc=aw.ds)
`FetchDataComponent` on the other hand it looks rather different. It has a url `prop` and has a `render` property which renders the `ProductDetailComponent`.

### How render props work

Let's reverse engineer the `FetchDataComponent` in order see how this works.

Let's zoom into the code again.

```jsx
<FetchDataComponent 
  url="https://randomuser.me/api/" 
  render={(data) => <ProductDetailComponent product={data.product} /> }
/>
```

Our `FetchDataComponent` has a `render` feature that takes a function that returns JSX markup. **Here is the thing**, the whole render-props pattern is about you invoking a function in your return method.


Let me show you this by code:
```jsx
const FetchDataComponent = ({ url, render }) => render()
```

This is simply the  **render-props pattern**
The way we used the `FetchDataComponent` means we at least need to send something into the `render()` call. Let us extract the function invocation and have a look at it:

```js
(data) => <ProductDetailComponent product={data.product} />
```

As you can see from the above mentioned, you need a parameter data and data can be seen as an object, so where does this data come from? 

Well that is a thing with our `FetchDataComponent`, it does some heavy lifting for us namely carrying out HTTP calls.

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
