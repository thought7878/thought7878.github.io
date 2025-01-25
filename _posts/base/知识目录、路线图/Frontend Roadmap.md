# Internet
## How Does The Internet Work  
互联网如何工作

## What is HTTP?  
什么是HTTP？

## Domain Name  
域名

## Hosting  
托管

## DNS

## Browsers

# HTML
## HTML Basics

## Semantic HTML


## Forms and Validations


## Accessibility


## Basics of SEO


# CSS

## CSS Basics


## Making layouts


## Responsive Web Design


# JavaScript

## Basics


## DOM Manipulation


## Fetch API



# Version Control Systems

## Git

## GitHub

## GitLab

## BitBucket

# Package Managers

## npm

## pnpm

## Yarn

# Framework
Web frameworks are designed to write web applications. Frameworks are collections of libraries that aid in the development of a software product or website. Frameworks for web application development are collections of various tools. Frameworks vary in their capabilities and functions, depending on the tasks set. They define the structure, establish the rules, and provide the development tools required.  
Web 框架旨在编写 Web 应用程序。框架是帮助开发软件产品或网站的库的集合。 Web 应用程序开发框架是各种工具的集合。框架的能力和功能各不相同，具体取决于任务集。他们定义结构、建立规则并提供所需的开发工具。

## React
React is an open-source JavaScript library for building user interfaces, primarily for single-page applications. Developed and maintained by Facebook, it allows developers to create reusable UI components that efficiently update and render as data changes. React uses a virtual DOM for performance optimization and supports a unidirectional data flow. Its component-based architecture promotes modularity and reusability. React’s ecosystem includes tools like Redux for state management and React Native for mobile app development. The library’s declarative nature, efficient rendering, and strong community support have made it one of the most popular choices for front-end development in modern web applications.  
React是用于构建用户界面的开源JavaScript库，主要用于单页应用程序。它由Facebook开发和维护，允许开发人员创建可重复使用的UI组件，以随着数据的变化有效更新和渲染。 React使用虚拟DOM进行性能优化，并支持单向数据流。其基于组件的体系结构促进了模块化和可重复性。 React的生态系统包括用于状态管理的REDUX等工具，用于移动应用程序开发的 React Native。该库的声明性质，高效的渲染和强有力的社区支持使其成为现代Web应用程序中前端开发的最受欢迎的选择之一。

## Vue.js


## Angular


## Svelte


## SolidJS


## Qwik


# Modern CSS

## Tailwind CSS

## CSS Architecture
CSS架构

CSS architecture refers to the methodologies and organizational strategies used to structure and maintain CSS code in large-scale web projects. It focuses on creating scalable, maintainable, and modular stylesheets to manage the growing complexity of web applications. Key concepts include naming conventions (like BEM or SMACSS), component-based design, separation of concerns, and the use of preprocessors (such as Sass or Less). CSS architecture often employs techniques like CSS modules, utility classes, or CSS-in-JS solutions to improve code reusability and reduce specificity conflicts. The goal is to create a systematic approach to styling that enhances collaboration among developers, reduces code duplication, and facilitates easier updates and maintenance of the visual design across an entire application or website.  
CSS 架构是指在大型 Web 项目中用于构建和维护 CSS 代码的方法和组织策略。它专注于创建可扩展、可维护和模块化的样式表，以管理日益复杂的 Web 应用程序。关键概念包括命名约定（如 BEM 或 SMACSS）、基于组件的设计、关注点分离以及预处理器（如 Sass 或 Less）的使用。 CSS 架构通常采用 CSS 模块、实用程序类或 CSS-in-JS 解决方案等技术来提高代码可重用性并减少特异性冲突。目标是创建一种系统的样式方法，以增强开发人员之间的协作，减少代码重复，并促进整个应用程序或网站的视觉设计更轻松的更新和维护。

## CSS Preprocessors

### PostCSS

### Sass


# Build Tools
Build tools are software utilities designed to automate the process of creating executable applications from source code. They handle tasks such as compiling, linking, minifying, and bundling code, as well as running tests and managing dependencies. Common build tools include Make, Gradle, Maven, Webpack, and Gulp. These tools streamline development workflows by reducing manual steps, ensuring consistency across different environments, and optimizing output for production. They often support features like incremental builds, parallel processing, and custom task definitions. Build tools are crucial in modern software development, especially for large-scale projects, as they improve efficiency, reduce errors, and facilitate continuous integration and deployment processes.  

构建工具是软件实用程序，旨在自动化从源代码创建可执行应用程序的过程。它们处理诸如编译、链接、缩小和打包代码等任务，以及运行测试和管理依赖关系。常见的构建工具包括Make、Gradle、Maven、Webpack和Gulp。这些工具通过*减少手动步骤、确保跨不同环境的一致性、优化生产输出*来简化开发工作流程。它们通常支持增量构建、并行处理和自定义任务定义等功能。构建工具在现代软件开发中至关重要，尤其是对于大型项目，*因为它们提高了效率，减少了错误，并促进了持续集成和部署过程*。
## Linters formatters
Linters and formatters are tools used in software development to improve code quality and consistency. Linters analyze source code to detect programming errors, bugs, stylistic issues, and suspicious constructs, often enforcing a set of predefined or custom rules. Formatters automatically restructure code to adhere to a consistent style, adjusting elements like indentation, line breaks, and spacing. Together, these tools help maintain code standards across projects and teams, enhance readability, catch potential errors early, and reduce the cognitive load on developers during code reviews. Popular examples include ESLint for JavaScript linting and Prettier for code formatting, both of which can be integrated into development workflows and IDEs for real-time feedback and automatic corrections.  
Linters和格式化器是软件开发中用于*提高代码质量和一致性*的工具。 `Linters`分析源代码以检测编程错误，错误，风格问题和可疑结构，通常会执行一组预定义或自定义规则。`格式化器`会自动重组代码以遵守一致的样式，调整缩进，换行符和间距等元素。这些工具共同帮助维护项目和团队之间的代码标准，增强可读性，尽早捕获潜在的错误，并减少代码审查期间开发人员的认知负担。流行的示例包括用于JavaScript刺激的ESLINT和代码格式的更漂亮，这两者都可以集成到开发工作流程和IDE中，以进行实时反馈和自动校正。
### Prettier
### ESLint

## Module Bundlers
Module bundlers are development tools that combine multiple JavaScript files and their dependencies into a single file, optimized for web browsers. They resolve and manage dependencies, transform and optimize code, and often support features like code splitting and lazy loading. Popular module bundlers include Webpack, Rollup, and Parcel. These tools address challenges in managing complex JavaScript applications by organizing code into modules, eliminating global scope pollution, and improving load times. Bundlers typically support various file formats, enable the use of modern JavaScript features through transpilation, and integrate with task runners and other build tools. Their primary goal is to streamline the development process and enhance application performance in production environments.

模块捆绑器是一种开发工具，它将多个JavaScript文件及其依赖项组合成一个文件，针对Web浏览器进行了优化。它们解决和管理依赖项，转换和优化代码，并且通常支持代码拆分和延迟加载等功能。流行的模块捆绑器包括Webpack、Rollup和Parcel。这些工具通过将代码组织成模块来解决管理复杂JavaScript应用程序的挑战，消除全局范围污染，并缩短加载时间。捆绑器通常支持各种文件格式，通过转译启用现代JavaScript功能，并与任务运行器和其他构建工具集成。它们的主要目标是简化开发过程并提高生产环境中的应用程序性能。
### Vite


### Webpack


### esbuild


### SWC (Speedy Web Compiler)


### Rollup


### Parcel


# Testing

## Jest

## Vitest

## Playwright

## Cypress

# Authentication Strategies
Authentication strategies are methods or techniques used to verify the identity of a user or system in order to grant access to a protected resource. There are several different authentication strategies that can be used, including:

- Basic Authentication
- Session Based Authentication
- Token Based Authentication
- JWT Authentication
- OAuth
- SSO

You don’t necessarily need to learn all of these, how to implement and the ins and outs from the get go. But it’s important to know what they are and how they work. This will help you make better decisions when choosing an authentication strategy for your application.

# Web Security
Web security knowledge encompasses understanding and implementing practices to protect websites, web applications, and web services from various cyber threats. Key areas include:

1. HTTPS and TLS for secure data transmission
2. Cross-Site Scripting (XSS) prevention
3. SQL injection protection
4. Cross-Site Request Forgery (CSRF) mitigation
5. Content Security Policy (CSP) implementation
6. Secure authentication and session management
7. Input validation and sanitization
8. Protection against clickjacking
9. Secure cookie handling
10. Regular security updates and patch management

Web security also involves understanding common vulnerabilities listed in the OWASP Top Ten, implementing proper access controls, and using secure coding practices. It requires ongoing education and vigilance as new threats emerge. Effective web security strategies often include a combination of preventive measures, regular security audits, and incident response planning to ensure the confidentiality, integrity, and availability of web resources and user data.

## HTTPS

## CORS

## Content Security Policy

## OWASP Security Risks

# Web Components



# Type Checkers
## TypeScript

# Server-side rendering
Server-side rendering (SSR) is a technique used in web development where web pages are generated on the server and sent to the client as fully rendered HTML. This approach contrasts with client-side rendering, where the browser builds the page using JavaScript. SSR improves initial page load time and search engine optimization (SEO) by providing complete content to crawlers. It’s particularly beneficial for content-heavy sites and applications requiring fast first-page loads. SSR can be implemented with various frameworks like Next.js for React or Nuxt.js for Vue.js. While it can increase server load and complexity, SSR offers advantages in performance perception, especially on slower devices or networks, and can be combined with client-side hydration for dynamic interactivity after initial load.

服务器端渲染（SSR）是Web开发中使用的一种技术，其中网页在服务器上生成并作为完全渲染的超文本标记语言发送到客户端。这种方法与客户端渲染形成对比，客户端渲染中浏览器使用JavaScript构建页面。SSR通过向爬虫提供完整的内容来*提高初始页面加载时间和搜索引擎优化*（SEO）。对于需要快速首页加载的内容繁重的网站和应用程序来说，它特别有益。SSR可以使用各种框架来实现，例如用于React的Next. js或用于Vue.js的Nuxt.js。虽然它可以增加服务器负载和复杂性，但SSR在性能感知方面提供了优势，尤其是在较慢的设备或网络上，并且可以与客户端水化相结合，以在初始加载后实现动态交互性。

## React
### Next.js

### Astro

### react-router

## Vue.js
### Nuxt.js

## Angular

## Svelte
### Svelte Kit

# GraphQL
GraphQL is a query language and runtime for APIs, developed by Facebook. GraphQL’s flexibility and efficiency make it popular for building complex applications, especially those with diverse client requirements. It’s particularly useful for mobile applications where bandwidth efficiency is crucial. While it requires a paradigm shift from REST, many developers and organizations find GraphQL’s benefits outweigh the learning curve, especially for large-scale or rapidly evolving APIs.

GraphQL是一种查询语言和API运行时，由Facebook开发。GraphQL的灵活性和效率使其在构建复杂应用程序时很受欢迎，尤其是那些具有不同客户需求的应用程序。它对于带宽效率至关重要的移动应用程序特别有用。虽然它需要从REST进行范式转变，但许多开发人员和组织发现GraphQL的好处超过了学习曲线，尤其是对于大规模或快速发展的API。

## Apollo

## Relay Modern



# PWA---Progressive Web Apps
Progressive Web Apps (PWAs) are web applications that use modern web capabilities to deliver an app-like experience to users. They combine the best features of web and native apps, offering reliability, speed, and engagement. PWAs are built using web technologies (HTML, CSS, JavaScript) but provide features typically associated with native apps, such as offline functionality, push notifications, and home screen installation. They are responsive, work across different devices and browsers, and are discoverable through search engines. PWAs use service workers for background processing and caching, enabling faster load times and offline access. This approach allows developers to create cross-platform applications that are both cost-effective to develop and easy to maintain, while providing users with a seamless, app-like experience directly through their web browser.  
Progressive Web应用程序（PWAS）是使用现代Web功能为用户提供类似应用程序的体验的Web应用程序。它们结合了网络和本机应用程序的最佳功能，提供可靠性，速度和参与度。 PWA是使用Web Technologies（HTML，CSS，JavaScript）构建的，但提供了通常与本机应用程序相关联的功能，例如离线功能，推送通知和主屏幕安装。它们反应灵敏，在不同的设备和浏览器上工作，并且可以通过搜索引擎发现。 PWA使用服务工作者进行背景处理和缓存，从而使加载时间更快并脱机访问。这种方法使开发人员可以创建跨平台应用程序，这些应用程序既可以开发又易于维护，同时直接通过其Web浏览器为用户提供了无缝的，类似应用的体验。

## Frontend Performance Best Practices
[链接](https://roadmap.sh/best-practices/frontend-performance)

## Measure & Improve Performance
### PRPL Pattern

### RAIL Model


### Performance Metrics


### Lighthouse


### Browser DevTools


## Web/Browser API

### Storage

### Web Sockets

### Server Sent Events


### Service Workers


### Location


### Notifications


### Device Orientation


### Payments


### Credentials



# Static Site Generators

## Next.js

## Astro

## Vuepress

## Eleventy

## Nuxt.js

# Mobile Applications

## React Native

## Flutter

## Ionic

# Desktop Applications
## Electron

## Tauri

## Flutter

