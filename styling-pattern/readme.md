## Styling Patterns

- [Inline Styles](#inline-styles)
- [Stylesheet Component](#stylesheet-component)


### Inline Styles

With React Native, you style your application using JavaScript. All of the core components accept a prop named style. The style names and values usually match how CSS works on the web, except names are written using camel casing, e.g. backgroundColor rather than background-color.

The `style` prop can be a plain old JavaScript object. That's what we usually use for example code. You can also pass an array of styles - the last style in the array has precedence, so you can use this to inherit styles.

```jsx
import React from "react"
import { Text, View } from "react-native"
import { customStyles } = "./customStyles"
```

> An object is utilized to pass in styles

```jsx
export const SayName = name => <Text style={{ color: "red", fontSize: 20 }}>Hello World, My name is: {name}</Text>
```

> Array of styles

```jsx
export const SayName = name => <Text style={[customStyles, { color: "red", fontSize: 20 }]}>Hello World, My name is: {name}</Text>
```

As a component grows in complexity, it is often cleaner to use [StyleSheet.create](#stylesheet-component) to define several styles in one place


### Stylesheet Component

A StyleSheet is an abstraction similar to CSS StyleSheets

```jsx
const SayName = name => (
	<View style={styles.sBackgroundColor}>
		<Text style={[styles.sTitle, { fontSize: 50 }]}>Hello World, My name is: {name}</Text>
	</View>
)

const styles = StyleSheet.create({
	sBackgroundColor: {
		backgroundColor: "#61dafb",
		marginTop: 16,
	},
	sTitle: {
		color: "#20232a",
		fontSize: 20
	},
});

export default SayName
```

> This is the most scalable approach and will allow you to write `Clean Code` and overall improve code quality

Next [>> Putting it all together](../combining-patterns/)