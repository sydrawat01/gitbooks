---
description: It's all bout fashun and making your components look good.
---

# 💅🏼 Styled Components

## [Styled Components](https://styled-components.com/)

```bash
yarn add styled-components
```

Styled components is an external, open source library for, well, creating styled components, or components that have inline, scoped styles.

To use it in a file, just import it to the required file:

```jsx
import styled from 'styled-components';
...
const StyledDiv = styled.div`
  display: inline-block;
  border: 1px solid lightgray;
  border-radius: 10% 10%;
  box-shadow: 0 2px 2px #ccc;
  background: lightblue;
  text-align: center;
  padding: 20px;
  width: 200px;
  margin: 20px;
  cursor: pointer;

  @media(min-width: 500px) {
    width: 200px;
  }
`;

//...

const Person = (props) => {
  return(
    <StyledDiv>
      <h1>Some text</h1>
    </StyledDiv>
  );
}
```

The unusual back ticks ` `` ` here are a new JavaScript feature called [tagged template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template\_literals#Tagged\_templates).

Styled components lets us write actual CSS in JS. This means we can use all the features of CSS - media queries, all pseudo selectors, nesting, etc.

### Conditional Styling in styled-components

Since inside the back-ticks of the styled components is basically a string template, we can manipulate the CSS values inside of it:

```jsx
const StyledPara = styled.p`
  color: ${(props) => (props.len <= 2 ? 'red' : null)};
  font-weight: ${(props) => (props.len <= 1 ? 'bold' : null)};
`;
```

We render the `StyledPara` like so:

```jsx
<StyledPara len={this.state.persons.length}> This is a test paragraph.</StyledPara>
```

We use the props to render the content conditionally, or ideally add the style properties based on the dynamic content inside the callback function, which is inside CSS styling of the styled-component.
