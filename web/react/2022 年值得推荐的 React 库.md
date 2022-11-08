# 2022 年值得推荐的 React 库

原文：https://www.robinwieruch.de/react-libraries/

`React` 出现已经有一定的时间了。从那时起，围绕组件驱动的库发展了一个全面而强大的库生态系统。来自其他编程语言或库/框架的开发者通常很难弄清楚**使用 `React` 创建 `Web` 应用程序的所有库。**

从本质上讲，`React` 使用户能够使用[函数组件](https://www.robinwieruch.de/react-function-component/)创建组件驱动的用户界面。不过，它带有几个内置的解决方案，例如，用于本地状态、副作用和性能优化的 `React Hooks`。但毕竟，你在这里只处理函数（组件和钩子）来创建 `UI`。

以下文章将会为你**如何选择库来构建 `React` 应用程序**提供指导。

# 目录

- [如何创建 React 项目](#heading-1)
- [React 状态管理](#heading-2)
- [React 数据获取](#heading-3)
- [使用 React Router](#heading-4)
- [React 中的 CSS 样式](#heading-5)
- [React UI 库](#heading-6)
- [React 动画库](#heading-7)
- [React 中的可视化和图表库](#heading-8)
- [React 中的表单库](#heading-9)
- [React 类型检查](#heading-10)
- [React 代码结构：样式和格式](#heading-11)
- [React 验证](#heading-12)
- [React 托管](#heading-13)
- [React 中的测试](#heading-14)
- [React 和不可变数据结构](#heading-15)
- [React 国际化](#heading-16)
- [React中的富文本编辑器](#heading-17)
- [React 中的支付](#heading-18)
- [React 中的时间](#heading-19)
- [React 桌面应用程序](#heading-20)
- [使用React进行手机开发](#heading-21)
- [React VR/AR](#heading-22)
- [React的原型设计](#heading-23)
- [React 组件文档](#heading-24)

# 如何创建 React 项目

对于每个React初学者来说，在加入React 时如何建立一个React项目都是未知的。有许多框架可供选择。React社区的现状是Facebook的[create-react-app (CRA)](https://github.com/facebook/create-react-app)。它的配置为零，并为你提供了一个极简的开箱即用的React应用程序。所有工具都对你隐藏，但你可以稍后更改工具（例如 eject 或 craco）。

> 继续阅读：[了解为什么像React这样的框架很重要](https://www.robinwieruch.de/why-frameworks-matter/)

然而，现在CRA建立在过时的工具上——这导致了较慢的开发体验。[Vite](https://vitejs.dev/) 是时下最受欢迎的新产品之一，它的开发和生产[速度令人难以置信](https://twitter.com/rwieruch/status/1491093471490412547)，而且有很多模板可供选择(如React、React + TypeScript)。

如果你已经熟悉 React，你可以选择它最流行的（元）框架之一作为替代：[Next.js](https://nextjs.org/) 和 [Gatsby.js](https://www.gatsbyjs.org/)。这两个框架都建立在 React 之上，因此你应该已经熟悉 [React 的基础知识](https://www.roadtoreact.com/)。这个领域另一个流行但更新的框架是 [Remix](https://remix.run/)，它在 2022 年绝对值得一试。

> 继续阅读：[了解有关网站和 Web 应用程序的更多信息](https://www.robinwieruch.de/web-applications/)

虽然Next.js最初用于服务器端渲染(例如动态web应用程序)，但Gatsby.js主要用于静态站点（例如静态网站，如博客和登陆页面）。但是，在过去几年中，两个框架之间的线路都有模糊，因为 Next.js 允许你选择加入静态站点，而 Gatsby 允许你选择加入服务器端渲染。在这个阶段，我会说 Next.js 赢得了大多数用例的流行之战。

> 继续阅读：[如何创建现代 JavaScript 项目](https://www.robinwieruch.de/javascript-project-setup-tutorial/)

如果你只是想了解像create-react-app这样的工具在底层是如何工作的，试着自己从头开始[创建一个React项目](https://www.robinwieruch.de/minimal-react-webpack-babel-setup/)。你将从一个简单的HTML JavaScript项目开始，自己添加React及其支持工具(如Webpack, Babel)。这不是你在日常工作中必须要处理的事情，但这是了解底层工具的一个很好的学习经验。

**建议：**

- Vite 用于客户端 React 应用程序
  - CRA 备选
- Next.js 主要用于服务器端渲染的 React 应用程序
  - 前沿技术：Remix
  - Gatsby 用于静态生成器 备选
- 可选学习经验：从头开始创建 React 项目

# React 状态管理

React自带两个内置钩子来管理本地状态:[useState](https://www.robinwieruch.de/react-usestate-hook)和[useReducer](https://www.robinwieruch.de/react-usereducer-hook/)。如果状态需要全局管理，你可以选择加入React内置的[useContext Hook](https://www.robinwieruch.de/react-usecontext-hook/)，在不使用道具的情况下将道具从顶级组件传递到底层组件，从而避免了props drilling的问题。

> 继续阅读：[了解何时使用useState和useReducer](https://www.robinwieruch.de/react-usereducer-vs-usestate/)

这三个React钩子都能让开发者在React中实现强大的状态管理，这些状态管理可以通过React的useState/useReducer钩子放在组件中，也可以通过与React的useContext钩子结合在一起进行全局管理。

> 继续阅读: [学习如何结合useState/useReducer和useContext](https://www.robinwieruch.de/react-state-usereducer-usestate-usecontext/)

如果你发现自己过于频繁地使用React的Context来处理共享/全局状态，你一定要看看[Redux](https://redux.js.org/)，它是最流行的状态管理库。它允许你管理应用程序的全局状态，任何连接到其全局存储的React组件都可以读取和修改应用程序的全局状态。

> 继续阅读: [学习Redux](https://www.robinwieruch.de/react-redux-tutorial/)

如果你碰巧使用Redux，你一定也应该查看[Redux Toolkit](https://redux-toolkit.js.org/)。它是Redux核心之上的一个很棒的API，极大地改善了开发者使用Redux的体验。

作为替代方案，如果你喜欢全局store的总体概念，但不喜欢 Redux 的处理方式，请查看其他流行的本地状态管理解决方案，例如 [Zustand](https://github.com/pmndrs/zustand)、[Jotai](https://github.com/pmndrs/jotai)、[XState](https://github.com/statelyai/xstate) 或 [Recoil](https://github.com/facebookexperimental/Recoil)。

**建议：**

- useState/useReducer 用于共存或共享状态

- 选择使用 useContext 来启用

  一些

  全局状态

  - 可选学习经验：学习如何结合useState/useReducer和useContext

- Redux(或另一种选择)用于许多全局状态

# React 数据获取

React的内置钩子非常适合UI状态，但当涉及到远程数据的状态管理(因此也包括数据获取)时，我建议使用一个专门的数据获取库，比如[React Query](https://react-query.tanstack.com/)，它自带内置的状态管理功能。虽然React Query本身并不被看作是一个状态管理库，因为它主要用于从api获取你的远程数据，但它会为你处理这些远程数据的所有状态管理(例如缓存，乐观更新)。

> 继续阅读: [了解React Query是如何工作的](https://www.robinwieruch.de/react-hooks-fetch-data/)

React Query是为使用[REST api](https://www.robinwieruch.de/node-express-server-rest-api/)而设计的。然而，现在它也支持[GraphQL](https://www.roadtographql.com/)。但是如果你正在为你的React前端寻找一个专用的GraphQL库，可以选择[Apollo Client](https://www.apollographql.com/docs/react/)(流行版)、[urql](https://formidable.com/open-source/urql/)(轻量级版)或[Relay](https://github.com/facebook/relay) (Facebook版)。

> 继续阅读: [React中关于本地和远程数据状态的所有内容](https://www.robinwieruch.de/react-state/)

如果你已经在使用 Redux，并且想在 Redux 中添加具有集成状态管理的数据获取功能，而不是添加 React Query，你可能需要查看[ RTK Query](https://redux-toolkit.js.org/rtk-query/overview)，它将数据获取巧妙地集成到 Redux 中。

**建议：**

- React Query (REST APIs, GraphQL APIs)
  - 使用axios作为获取库
- Apollo Client (GraphQL APIs)
- 可选的学习经验：学习React Query是如何工作的

# 使用 React Router

如果你使用的是像Next.js或Gatsby.js这样的React框架，路由已经为你解决了。然而，如果你使用React时没有框架，只用于客户端渲染(如CRA)，那么最强大、最流行的路由库是[React Router](https://reactrouter.com/)。

> 继续阅读: [学习使用React Router](https://www.robinwieruch.de/react-router/)

在你的React项目中引入路由器之前，当你正要学习React时，你可以先尝试一下React的[条件渲染](https://www.robinwieruch.de/conditional-rendering-react/)。它不是路由的替代品，但在小型应用程序中，它经常以这种方式交换组件。

**建议：**

- React Router
  - 可选学习经验：学习使用React Router

# React 中的 CSS 样式

React中有很多关于样式/CSS的选项，甚至更多的意见，所以把所有的东西放在一个部分是不够的。如果你想更深入地了解这个主题，了解所有的选项，请查看以下指南。

> 继续阅读: [React CSS样式](https://www.robinwieruch.de/react-css-styling/)

但是让我们先从概述开始。作为一个React初学者，可以通过使用一个带有所有CSS属性的样式对象作为HTML样式属性的键/值对来开始使用内联样式和基本的CSS。

```jsx
const Headline = ({ title }) =>
  <h1 style={{ color: 'blue' }}>
    {title}
  </h1>
复制代码
```

内联样式可以在React中通过JavaScript动态和编程的方式添加样式，而外部CSS文件可以包含React应用的所有剩余样式：

```jsx
import './Headline.css';

const Headline = ({ title }) =>
  <h1 className="headline" style={{ color: 'blue' }}>
    {title}
  </h1>
复制代码
```

一旦你的应用程序增长，还有很多其他的样式选择。首先，我建议你将CSS模块作为众多CSS中CSS解决方案之一来研究。CRA支持CSS模块，并提供了一种将CSS封装到组件范围内的模块的方法。这样，它就不会意外地泄露到其他React组件的样式中。尽管你的应用程序的某些部分仍然可以共享样式，但其他部分不必访问它。在React中，CSS模块通常是将CSS文件放在React组件文件中：

```jsx
import styles from './style.module.css';

const Headline = ({ title }) =>
  <h1 className={styles.headline}>
    {title}
  </h1>
复制代码
```

其次，我想向你推荐所谓的风格组件，作为React的众多CSS-in-JS解决方案之一。这种方法是通过一个名为[styles-components](https://www.robinwieruch.de/react-styled-components/)(或其他选择，如emotion或)的库来实现的，它将样式与JavaScript放在React组件的JavaScript文件或共存文件的旁边：

```jsx
import styled from 'styled-components';

const BlueHeadline = styled.h1`
  color: blue;
`;
const Headline = ({ title }) =>
  <BlueHeadline>
    {title}
  </BlueHeadline>
复制代码
```

第三，我想推荐[Tailwind CSS](https://tailwindcss.com/)作为最流行的实用优先CSS解决方案。它提供了预定义的CSS类，你可以在React组件中使用它们，而不用自己定义它们。这让你作为一个开发者更有效率，也让你的React应用程序的设计系统更加一致，但同时也需要了解所有的类：

```jsx
const Headline = ({ title }) =>
  <h1 className="text-blue-700">
    {title}
  </h1>
复制代码
```

选择 CSS-in-CSS、CSS-in-JS 还是函数式 CSS 取决于你。所有策略都适用于较大的React应用程序。最后一点提示：如果你想在React中有条件地应用一个className，请使用像clsx这样的工具。

**建议：**

- CSS-in-CSS 用 CSS Modules
- CSS-in-JS 用 styles-components (最流行)
  - 备选: Emotion 或 Stitches
- 函数式的 CSS 用 Tailwind CSS
- 可选: 有条件地应用一个className 选择使用 clsx

# React UI 库

对于初学者来说，从零开始构建可重用的组件是一个很好的学习经验，值得推荐。无论它是下拉菜单、单选按钮还是复选框，你最终都应该知道如何创建这些UI组件。

然而，在某些时候，你想要使用一个UI库，它可以让你访问许多共享相同设计系统的预构建组件。以下所有的UI库都带有基本组件，如按钮、下拉菜单、对话框和列表:

- [Material UI (MUI)](https://material-ui.com/) (最流行)
- [Mantine](https://mantine.dev/) (最推荐的)
- [Chakra UI](https://chakra-ui.com/) (最推荐的)
- [Ant Design](https://ant.design/)
- [Radix](https://www.radix-ui.com/)
- [Primer](https://primer.style/react/)
- [NextUI](https://nextui.org/)
- [Tailwind UI](https://www.tailwindui.com/) (不是免费的)
- [Semantic UI](https://www.robinwieruch.de/react-semantic-ui-tutorial)
- [React Bootstrap](https://react-bootstrap.github.io/)

尽管所有这些UI库都带有许多内部组件，但它们不能使每个组件都像只关注一个UI组件的库那样强大。例如，[React-table-library](https://react-table-library.com/)允许你在React中创建功能强大的表组件，同时也提供了主题(如Material UI)，可以很好地融入流行的UI组件库中。

# React 动画库

web应用中的任何动画都是从CSS开始的。最终你会发现CSS动画不能满足你的需求。通常开发者会选择[React Transition Group](https://reactcommunity.org/react-transition-group/)，这样他们就可以使用React组件来执行动画了。React的其他知名动画库有：

- [Framer Motion](https://www.framer.com/motion/) (最推荐的)
- [react-spring](https://github.com/react-spring/react-spring) (也经常推荐的)
- [react-motion](https://github.com/chenglou/react-motion)
- [react-move](https://github.com/sghall/react-move)
- [Animated](https://facebook.github.io/react-native/docs/animated) (React Native)

# React 中的可视化和图表库

如果你真的想要自己从头开始创建图表，那么就没有办法绕过[D3](https://d3js.org/)。这是一个低级的可视化库，可以为你提供创建令人惊叹的图表所需的一切。然而，学习D3是一个完全不同的冒险，因此许多开发人员只是选择一个React图表库，它可以为他们做所有的事情，以换取灵活性。以下是一些流行的解决方案：

- [Recharts](http://recharts.org/)
- [react-chartjs](https://github.com/reactchartjs/react-chartjs-2)
- [visx](https://github.com/airbnb/visx)
- [Victory](https://formidable.com/open-source/victory/)

# React 中的表单库

React 中最受欢迎的表单库是 React Hook Form。它提供了从验证（最流行的集成是 yup 和 zod）到提交到表单状态管理所需的一切。过去更流行的另一种选择是Formik。两者都是你的 React 应用程序的有效解决方案。这个领域的另一个选择是 React Final Form。毕竟，如果你已经在使用 React UI 库，你还可以查看他们的内置表单解决方案。

**建议：**

- React Hook Form
  - 使用yup或zod集成进行验证
- 如果使用UI库，请检查内置表单是否支持所有需求

# React 类型检查

React自带一个内部的类型检查，叫做[PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)。通过使用PropTypes，你可以为React组件定义prop。当一个错误类型的prop被传递给组件时，你会在运行应用程序时得到一个错误消息:

```jsx
import PropTypes from 'prop-types';

const List = ({ list }) =>
  <div>
    {list.map(item => <div key={item.id}>{item.title}</div>)}
  </div>

List.propTypes = {
  list: PropTypes.array.isRequired,
};
复制代码
```

然而，PropTypes不再包含在React核心库中。在过去的几年里，PropTypes变得不那么流行了，取而代之的是TypeScript：

```ts
type Item = {
  id: string;
  title: string;
};

type ListProps = {
  list: Item[];
};

const List: React.FC<ListProps> = ({ list }) =>
  <div>
    {list.map(item => <div key={item.id}>{item.title}</div>)}
  </div>
复制代码
```

如果你真的想在React中拥抱类型，[TypeScript](https://www.typescriptlang.org/)是现在的最佳选择。

**建议：**

- 如果需要类型化的JavaScript，使用TypeScript

# React 代码结构：样式和格式

本质上，有两种方法可以遵循代码结构的常识：

如果你想要一种统一的、通用的代码风格，在你的React项目中使用ESLint。像ESLint这样的检测程序在你的React项目中强制执行特定的代码风格。例如，你可以在ESLint中要求遵循一个流行的风格指南(如Airbnb风格指南)。之后，将ESLint与你的IDE/编辑器集成，它会指出你的每一个错误。

> 继续阅读: [React文件/文件夹结构](https://www.robinwieruch.de/react-folder-structure/)

如果你想采用统一的代码格式，在React项目中使用Prettier。它是一个固执的代码格式化器，只有少量可选择的配置。你可以将其集成到编辑器或IDE中，以便在每次保存文件时对代码进行格式化。虽然Prettier不能取代ESLint，但它可以很好地与ESLint集成。

**建议：**

- ESLint + Prettier

# React 验证

在React应用程序中，你可能希望引入带有注册、登录和退出等功能的身份验证。其他功能，如密码重置和密码更改功能通常也需要。这些特性远远超出了React的范畴，因为后台应用程序会为你管理这些东西。

> 继续阅读: [如何准备React Router认证](https://www.robinwieruch.de/react-router-authentication/)

最好的学习经验是自己实现一个带有身份验证的后端应用程序(例如[GraphQL backend](https://www.robinwieruch.de/graphql-apollo-server-tutorial/))。然而，由于身份验证有很多安全风险，而且并非每个人都知道细节，我建议使用现有的众多身份验证/后端即服务解决方案中的一种:

- [Firebase](https://www.robinwieruch.de/complete-firebase-authentication-react-tutorial/)
- [Auth0](https://auth0.com/)
- [AWS Cognito](https://aws.amazon.com/cognito/)

**建议：**

- 选择一个认证服务或BaaS(如Firebase)
- 可选学习经验：自定义后端

# React 托管

你可以像部署其他web应用一样部署和托管React应用。如果你想拥有完全的控制权，选择像[Digital Ocean](https://m.do.co/c/fb27c90322f3)这样的东西。如果你想找人来处理所有的事情，[Cloudflare Workers](https://workers.cloudflare.com/)、[Netlify](https://www.netlify.com/)或[Vercel](https://vercel.com/)(特别是针对Next.js)是流行的解决方案。如果你已经在使用像Firebase这样的第三方后台服务，你可以检查他们是否也提供托管服务(例如[Firebase hosting](https://firebase.google.com/docs/hosting))。

# React 中的测试

如果你想深入了解React中的测试，请阅读这篇文章:如何在React中测试组件。这里有一个要点:测试一个React应用的主干是Jest。它提供了测试运行器、断言库和监视/模仿/存根功能。一个全面的测试框架所需要的一切。

至少，你可以使用[React-test-renderer](https://reactjs.org/docs/test-renderer.html)在Jest测试中呈现React组件。这已经足够用Jest执行所谓的[Snapshot Tests](https://www.robinwieruch.de/react-testing-jest/)了。Snapshot Tests的工作方式如下:一旦运行测试，就会创建React组件中呈现的DOM元素的快照。当你在某个时间点再次运行测试时，将创建另一个快照，该快照将用作前一个快照的差异。如果差异不相同，Jest将发出抱怨，你要么必须接受快照，要么更改组件的实现。

最终，你会发现自己正在使用流行的[React测试库(RTL)](https://www.robinwieruch.de/react-testing-library/)——它是在Jest测试环境中使用的——来为React创建一个更复杂的测试库。RTL使呈现组件和在HTML元素上模拟事件成为可能。之后，Jest用于DOM节点上的断言。

如果你正在寻找React端到端(E2E)测试的测试工具，[Cypress](https://www.robinwieruch.de/react-testing-cypress/)是最受欢迎的选择。不过另一个越来越受欢迎的是[Playwright](https://playwright.dev/)。

**建议：**

- Unit/Integration: Jest + React Testing Library (最流行的)
- Snapshot Tests: Jest
- E2E Tests: Cypress

# React 和不可变数据结构

Vanilla JavaScript 为你提供了大量的内置工具来处理数据结构，就好像它们是不可变的一样。但是，如果你和你的团队觉得需要执行不可变的数据结构，那么最流行的选择之一就是[Immer](https://github.com/immerjs/immer)。我个人不使用它，因为JavaScript可以用于管理不可变的数据结构，但任何时候有人问起JS的不可变性，就会有人推荐它。

# React 国际化

当涉及到React应用程序的国际化i18n时，你不仅需要考虑翻译，还需要考虑多元化、日期和货币的格式，以及其他一些事情。以下是最常用的处理它的库：

- [FormatJS](https://github.com/formatjs/formatjs)
- [react-i18next](https://github.com/i18next/react-i18next)

# React中的富文本编辑器

说到React中的富文本编辑器，我只能想到以下这些，因为我还没有在React中使用过其他的编辑器：

- [Draft.js](https://draftjs.org/)
- [Slate.js](https://www.slatejs.org/)
- [ReactQuill](https://github.com/zenoamaro/react-quill)

# React 中的支付

和其他网络应用一样，最常见的支付提供商是Stripe和PayPal。两者都可以巧妙地集成到React中。这是一个与React集成的Stripe Checkout。

- [PayPal](https://developer.paypal.com/docs/checkout/)
- [React Stripe Elements](https://github.com/stripe/react-stripe-js) or [Stripe Checkout](https://stripe.com/docs/payments/checkout)

# React 中的时间

JavaScript本身在最近几年的日期和时间处理上做得非常棒。因此，实际上不需要使用库来处理它们。然而，如果你的React应用需要处理大量的日期、时间和时区，你可以引入一个库来为你管理这些东西。以下是你的选择：

- [date-fns](https://github.com/date-fns/date-fns)
- [Day.js](https://github.com/iamkun/dayjs)
- [Luxon](https://github.com/moment/luxon/)

# React 桌面应用程序

[Electron](https://www.electronjs.org/) 是跨平台桌面应用程序的框架。然而，也有其他选择，例如：

- [Tauri](https://github.com/tauri-apps/tauri) (fairly new)
- [NW.js](https://nwjs.io/)
- [Neutralino.js](https://github.com/neutralinojs/neutralinojs)

# 使用React进行手机开发

想将React从网页带到手机平台的解决方案仍然是React Native。如果你想帮助一个框架创建React Native应用程序，请查看[Expo](https://www.robinwieruch.de/react-native-expo/)。

> 继续阅读: [学习React Native](https://www.robinwieruch.de/react-native-navigation/)

# React VR/AR

通过React，我们可以深入研究虚拟现实或增强现实。老实说，我没有使用过这些库中的任何一个，但它们是我在React中所熟悉的AR/VR库:

- [react-three-fiber](https://github.com/pmndrs/react-three-fiber) (流行的 3d 库, 我也看到过VR的例子)
- [react-360](https://facebook.github.io/react-360/)
- [aframe-react](https://github.com/supermedium/aframe-react)

# React的原型设计

如果你有UI/UX背景，你可能想要使用一个工具来快速创建React组件、布局或UI/UX概念的原型。我过去使用[Sketch](https://www.sketch.com/)，但后来改用[Figma](https://www.figma.com/)。虽然我两个都喜欢，但我现在不后悔使用Figma。[Zeplin](https://zeplin.io/)是另一种选择。对于粗糙而轻量级的草图，我喜欢使用E[xcalidraw](https://excalidraw.com/)。如果你正在寻找交互式UI/UX设计，请查看[InVision](https://www.invisionapp.com/)。

# React 组件文档

如果你负责为组件编写文档，有各种各样的React文档工具。我已经在许多项目中使用了Storybook，我只能推荐它，但我也听说过其他解决方案的好处：

- [Docusaurus](https://github.com/facebook/docusaurus)
- [Docz](https://github.com/doczjs/docz)
- [Styleguidist](https://github.com/styleguidist/react-styleguidist)

毕竟，React生态系统可以被看作是React的一个框架，但它的核心仍然是React的灵活性。它是一个灵活的框架，你可以根据自己的了解做出想要选择加入哪些库的决定。你可以从小处开始，只添加库来解决特定的问题。相反，如果你只需要React，你可以只使用这个库来保持轻量级。

# 结语

好了以上就是本文的所有内容，如有问题，欢迎指正