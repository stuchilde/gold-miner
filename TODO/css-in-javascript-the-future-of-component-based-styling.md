> * 原文地址：[CSS in JavaScript: The future of component-based styling](https://medium.freecodecamp.com/css-in-javascript-the-future-of-component-based-styling-70b161a79a32)
> * 原文作者：[Jonathan Z. White](https://medium.freecodecamp.com/@JonathanZWhite)
> * 译文出自：[掘金翻译计划](https://github.com/xitu/gold-miner)
> * 译者：[bambooom](https://github.com/bambooom)
> * 校对者：

# JavaScript 中的 CSS：基于组件的样式的未来

![](https://cdn-images-1.medium.com/max/1000/1*yVKDbwtvfoakj3RZ9g8ARQ.png)

图片所属 [@jonathanzwhite](https://twitter.com/JonathanZWhite)

> By adopting inline styles, we can get all of the programmatic affordances of JavaScript.This gives us the benefits of something like a CSS pre-processor (variables, mixins, and functions). It also solves a lot of the problems that CSS has, such as global namespacing and styling conflicts.

使用行内样式使我们可以获得 JavaScript 的所有编程支持。这给了我们类似 CSS 预处理器（变量、混入和函数）的好处。它也解决了 CSS 的很多问题，如全局命名空间和样式冲突。

> For a deep dive into the problems that CSS in JavaScript solves, check out the famous presentation: [React CSS in JS](https://speakerdeck.com/vjeux/react-css-in-js). For a case study on the performance improvements you get from Aphrodite, you can read [Inline CSS at Khan Academy: Aphrodite](http://engineering.khanacademy.org/posts/aphrodite-inline-css.htm). If you want to learn more about CSS in JavaScript best practices, check out [Airbnb’s styleguide](https://github.com/airbnb/javascript/tree/master/css-in-javascript).

如果想要更深入了解 JavaScript 中的 CSS 所解决的问题，可以查看著名的演示幻灯：[React CSS in JS](https://speakerdeck.com/vjeux/react-css-in-js)。有关使用 Aphrodite 性能优化的案例研究，你可以阅读 [行内 CSS 在可汗学院：Aphrodite](http://engineering.khanacademy.org/posts/aphrodite-inline-css.htm)。如果你想了解更多，可以阅读 [Airbnb 的风格指南](https://github.com/airbnb/javascript/tree/master/css-in-javascript).

> In addition we’ll be using inline JavaScript styles to build components to address some of the fundamentals of design I covered in one of my previous articles: [Before you can master design, you must first master the fundamentals](https://medium.freecodecamp.com/before-you-can-master-design-you-must-first-master-the-fundamentals-1981a2af1fda).

此外，我们将使用行内 JavaScript 样式来构建组件，以解决我之前的一篇文章（[掌握设计之前，必须掌握基本原理](https://medium.freecodecamp.com/before-you-can-master-design-you-must-first-master-the-fundamentals-1981a2af1fda)）中涉及的一些设计基础问题。

### 一个启发性的例子 ###

> Let’s start off with a simple example: creating and styling a button.

让我们从一个简单的例子开始，构建一个按钮并给它添加样式。

> Normally the component and its associated styles would go in the same file: `Button` and `ButtonStyles`. This is because they fall under the same concern: the view. However, for this example, I broke up the code into multiple gists to make it more digestible.

一般来说，组件及其样式在同一个文件中：`Button` 和 `ButtonStyles`。这是因为他们都属于视图层。但是，下面的例子中，我将代码拆分成多个 gist，以便更容易理解。

下面就是按钮组件：

```javascript
...

function Button(props) {
  return (
    <input
      type="button"
      className={css(styles.button)}
      value={props.text}
    />
  );
}
```

> This is nothing unexpected — just a stateless React component. Where Aphrodite comes into play is in the `className` property. The function `css` takes in a `styles` object and converts it into css. The `styles` object is created with Aphrodite’s `StyleSheet.create({ ... })` function. You can see the output of `StyleSheet.create({ ... })` with this [Aphrodite playground](https://output.jsbin.com/qoseye?).

这没什么特别的，只是一个无状态的 React 组件。Aphrodite 起作用的地方是在 `className` 属性中。`css` 函数接受一个 `styles` 对象为参数并将其转换为 `css`。`styles` 对象是由 Aphrodite 的函数 `StyleSheet.create({ ... })` 创建的，你可以用 [Aphrodite playground](https://output.jsbin.com/qoseye?) 来看下这个函数的输出结果。

**下面是按钮的样式表：**

```javascript
...

const gradient = 'linear-gradient(45deg, #FE6B8B 30%, #FF8E53 90%)';

const styles = StyleSheet.create({
  button: {
    background: gradient,
    borderRadius: '3px',
    border: 0,
    color: 'white',
    height: '48px',
    textTransform: 'uppercase',
    padding: '0 25px',
    boxShadow: '0 3px 5px 2px rgba(255, 105, 135, .30)',
  },
});
```

> One of the benefits of Aphrodite is that migration is straightforward and the learning curve is low. Properties like `border-radius` become `borderRadius` and values become strings. Pseudo-selectors, media queries, and font definitions all work. In addition, vendor prefixes are added automatically.

Aphrodite 的优势之一就是迁移是很直观的，学习曲线很平缓。类似 `border-radius`  变成 `borderRadius`，值变成字符串。伪类选择器、媒体查询、字体定义都可以正常工作。另外也自动添加浏览器引擎前缀。

**下面就是按钮的样子：**

![](https://cdn-images-1.medium.com/max/800/1*x1ccRv9UGvcxBvz4TvC4Qg.png)

> With this example in mind, **let’s take a look at how we can use Aphrodite to build a basic visual design system**, focusing on the following design fundamentals: typography and spacing.

以这个例子为基础，**让我们来看看我们如何使用 Aphrodite 来构建一个基本的视觉设计系统**，着重关注字体排版和间距两个设计基础元素。

### Fundamental №1 —Typography ###

Let’s start off with typography, a fundamental basis for design. **The first step is to define typography constants**. And unlike Sass or Less, constants for Aphrodite can go in a JavaScript or JSON file.

#### Define typography constants

When creating constants, **use semantic names for your variables**. For example, instead of naming one of your font-sizes `h2`, use a name like `displayLarge` that describes its *role*. Similarly, for font-weights, instead of naming one of your weights `600`, give it a name like `semibold` to describe its *effect*.

```
export const fontSize = {
  // heading
  displayLarge: '32px',
  displayMedium: '26px',
  displaySmall: '20px',
  heading: '18px',
  subheading: '16px',

  // body
  body: '17px',
  caption: '15px',
};

export const fontWeight = {
  bold: 700,
  semibold: 600,
  normal: 400,
  light: 200,
};

export const tagMapping = {
  h1: 'displayLarge',
  h2: 'displayMedium',
  h3: 'displaySmall',
  h4: 'heading',
  h5: 'subheading',
};

export const lineHeight = {
  // heading
  displayLarge: '48px',
  displayMedium: '48px',
  displaySmall: '24px',
  heading: '24px',
  subheading: '24px',

  // body
  body: '24px',
  caption: '24px',
};
```

It’s important to get the values right for variables like font-sizes and line-heights. This is because they directly affect the vertical rhythm within a design. Vertical rhythm is a concept that helps you achieve consistent spacing between elements.

For more on vertical rhythm, you can read this article: [Why is Vertical Rhythm an Important Typography Practice?](https://zellwk.com/blog/why-vertical-rhythms/)

![](https://cdn-images-1.medium.com/max/800/1*Ehj9XMvQ9wJNhxWNqwXfKw.png)

[Use a calculator to determine line-heights](https://drewish.com/tools/vertical-rhythm/)

There is a science behind choosing the values for your line-heights and font-sizes. We can use mathematic ratios to generate a set of potential sizes candidates. A few weeks ago, I wrote an article detailing the methodology: [Typography can make or break your design: a process for choosing type](https://medium.freecodecamp.com/typography-can-make-your-design-or-break-it-7be710aadcfe). For determining font-sizes, you use [Modular Scale](http://www.modularscale.com/). For determining line-heights, you can use this [vertical rhythm calculator](https://drewish.com/tools/vertical-rhythm/).

#### Define a heading component ####

After defining our typography constants, the next step is to create a component to consume the values. **The goal of the component is to enforce consistency in design and implementation for headings across the codebase.**

```
import React, { PropTypes } from 'react';
import { StyleSheet, css } from 'aphrodite/no-important';
import { tagMapping, fontSize, fontWeight, lineHeight } from '../styles/base/typography';

function Heading(props) {
  const { children, tag: Tag } = props;
  return <Tag className={css(styles[tagMapping[Tag]])}>{children}</Tag>;
}

export default Heading;

export const styles = StyleSheet.create({
  displayLarge: {
    fontSize: fontSize.displayLarge,
    fontWeight: fontWeight.bold,
    lineHeight: lineHeight.displayLarge,
  },
  displayMedium: {
    fontSize: fontSize.displayMedium,
    fontWeight: fontWeight.normal,
    lineHeight: lineHeight.displayLarge,
  },
  displaySmall: {
    fontSize: fontSize.displaySmall,
    fontWeight: fontWeight.bold,
    lineHeight: lineHeight.displaySmall,
  },
  heading: {
    fontSize: fontSize.heading,
    fontWeight: fontWeight.bold,
    lineHeight: lineHeight.heading,
  },
  subheading: {
    fontSize: fontSize.subheading,
    fontWeight: fontWeight.bold,
    lineHeight: lineHeight.subheading,
  },
 }); 
```

The `Heading` component is a stateless function that takes in a tag as a property and returns the tag with its associated style. This is possible because we defined the tag mappings earlier in the constants file.

```
...
export const tagMapping = {
  h1: 'displayLarge',
  h2: 'displayMedium',
  h3: 'displaySmall',
  h4: 'heading',
  h5: 'subheading',
};
```

At the bottom of the component file, we define our `styles` object. This is where we use the typography constants.

```
export const styles = StyleSheet.create({
  displayLarge: {
    fontSize: fontSize.displayLarge,
    fontWeight: fontWeight.bold,
    lineHeight: lineHeight.displayLarge,
  },
  
  ...
});
```

And this is how the `Heading` component would be used:

```
function Parent() {
  return (
    <Heading tag="h2">Hello World</Heading>
  );
}
```

With this approach, **we reduce unexpected variability in our type system**. We avoid the pitfall of a hundred different font sizes by removing the need for global styles and standardizing headings across the codebase. In addition, this approach we took to building the `Heading` component can be applied to building a `Text` component for body copy.

### Fundamental №2 — Spacing ###

**Spacing controls both vertical and horizontal rhythm in design**. That makes spacing pivotal to establishing a visual design system. Just like in the typography section, the first step to address spacing is to define spacing constants.

#### Define spacing constants ###

When defining spacing constants for the margins between elements, we can adopt a mathematic approach. Using a `spacingFactor` constant, we can generate a set of distances based on a common factor. **This approach ensures that we have logical and consistent spacing between elements.**

```
const spacingFactor = 8;
export const spacing = {
  space0: `${spacingFactor / 2}px`,  // 4
  space1: `${spacingFactor}px`,      // 8
  space2: `${spacingFactor * 2}px`,  // 16
  space3: `${spacingFactor * 3}px`,  // 24
  space4: `${spacingFactor * 4}px`,  // 32
  space5: `${spacingFactor * 5}px`,  // 40
  space6: `${spacingFactor * 6}px`,  // 48

  space8: `${spacingFactor * 8}px`,  // 64
  space9: `${spacingFactor * 9}px`,  // 72
  space13: `${spacingFactor * 13}px`, // 104
};
```

The example above uses a linear scale, one to thirteen. However, experiment with different scales and ratios. Designs require different scales based on their purpose, their audience, and the devices they target. As an example,** here are the first six computed distances using the golden ratio** with a `spacingFactor` of eight.

    Golden Ratio (1:1.618)

    8.0 x (1.618 ^ 0) = 8.000
    8.0 x (1.618 ^ 1) = 12.94
    8.0 x (1.618 ^ 2) = 20.94
    8.0 x (1.618 ^ 3) = 33.89
    8.0 x (1.618 ^ 4) = 54.82
    8.0 x (1.618 ^ 5) = 88.71

This is what the spacing scale would look like in code. I added a helper function to handle the computation and round off the output to its nearest pixel value.

```
const spacingFactor = 8;
export const spacing = {
  space0: `${computeGoldenRatio(spacingFactor, 0)}px`,  // 8
  space1: `${computeGoldenRatio(spacingFactor, 1)}px`,  // 13
  space2: `${computeGoldenRatio(spacingFactor, 2)}px`,  // 21
  space3: `${computeGoldenRatio(spacingFactor, 3)}px`,  // 34
  space4: `${computeGoldenRatio(spacingFactor, 4)}px`,  // 55
  space5: `${computeGoldenRatio(spacingFactor, 5)}px`,  // 89
};

function computeGoldenRatio(spacingFactor, exp) {
  return Math.round(spacingFactor * Math.pow(1.618, exp));
}
```

After defining our spacing constants, we can use them to add margins to elements in our design. **One approach we can take is to import the spacing constants and consume them in components**.

For example, let’s add a `marginBottom` to the `Button` component.

```
import { spacing } from '../styles/base/spacing';

...

const styles = StyleSheet.create({
  button: {
    marginBottom: spacing.space4, // adding margin using spacing constant
    ...
  },
});
```

This works in most scenarios. However, what happens if we want to change the `marginBottom` property of the button based on where the button is place?

One way to achieve variable margins is to override the margin style from the consuming parent component. An alternative approach is to **create a **`Spacing`**component to control the vertical margins on elements**.

```
import React, { PropTypes } from 'react';
import { spacing } from '../../base/spacing';

function getSpacingSize(size) {
  return `space${size}`;
}

function Spacing(props) {
  return (
    <div style={{ marginBottom: spacing[getSpacingSize(props.size)] }}>
      {props.children}
    </div>
  );
}

export default Spacing;
```

Using this approach, we can remove the responsibility of setting margins out of the child component and into the parent component. **This way, the child component becomes layout agnostic, not requiring any knowledge of where to place itself in relation to other elements.**

This works because components like buttons, inputs, and cards may need variable margins. For example, a button in a form may need larger margins than a button in a navigation bar. The caveat is that if a component always has consistent margins, then it would make more sense to handle margins within the component.

Also you may have noticed that the examples only use `marginBottom`. This is because **defining all your vertical margins in one direction allows you avoid collapsing margins and keep track of the vertical rhythm of your design**. You can read more on this in Harry Robert’s article, [Single-direction margin declarations](https://csswizardry.com/2012/06/single-direction-margin-declarations/).

On a final note, you can also use the spacing constants you defined as padding.

```
import React, { PropTypes } from 'react';
import { StyleSheet, css } from 'aphrodite/no-important';
import { spacing } from '../../styles/base/spacing';

function Card(props) {
  return (
    <div className={css(styles.card)}>
      {props.children}
    </div>
  );
}

export default Card;

export const styles = StyleSheet.create({
  card: {
    padding: spacing.space4}, // using spacing constants as padding
    
    background: 'rgba(255, 255, 255, 1.0)',
    boxShadow: '0 3px 17px 2px rgba(0, 0, 0, .05)',
    borderRadius: '3px',
  },
});
```

By using using the same spacing constants for both margins and padding, you can achieve more visual consistency in your design.

Here’s what the result might look like:

![](https://cdn-images-1.medium.com/max/800/1*oDkbVmgCJ4ss5fuRNvzoUg.png)

[By using spacing constants for your margins and padding, you can achieve more visual consistency.](https://twitter.com/JonathanZWhite)

Now that you have a grasp of CSS in JavaScript, go out and experiment. Try incorporating inline JavaScript styles into your next project. I think **you’ll appreciate being able to work in a single context to handle all of your styling and view concerns**.

On the topic of CSS and JavaScript, what are some new developments that you’re excited about? Personally I’m excited about async/await. Leave me a note or send me a [tweet](https://twitter.com/jonathanzwhite) on Twitter.

You can find me on Medium where I publish every week. Or you can follow me on [Twitter](https://twitter.com/JonathanZWhite), where I post non-sensical ramblings about design, front-end development, and virtual reality.

*P.S. If you enjoyed this article, it would mean a lot if you clicked the 💚 and shared with friends.*

[![](https://cdn-images-1.medium.com/max/600/1*mxQhZLqG7l5dMLvxYAklgw.png)](http://mrwhite.space/signup)

[![](https://cdn-images-1.medium.com/max/600/1*UOsjAdUZ9O0QSyfXOpQPbA.png)](https://twitter.com/JonathanZWhite)
---

> [掘金翻译计划](https://github.com/xitu/gold-miner) 是一个翻译优质互联网技术文章的社区，文章来源为 [掘金](https://juejin.im) 上的英文分享文章。内容覆盖 [Android](https://github.com/xitu/gold-miner#android)、[iOS](https://github.com/xitu/gold-miner#ios)、[React](https://github.com/xitu/gold-miner#react)、[前端](https://github.com/xitu/gold-miner#前端)、[后端](https://github.com/xitu/gold-miner#后端)、[产品](https://github.com/xitu/gold-miner#产品)、[设计](https://github.com/xitu/gold-miner#设计) 等领域，想要查看更多优质译文请持续关注 [掘金翻译计划](https://github.com/xitu/gold-miner)。
