# Functional Declarative Design: A Comprehensive Methodology for Statically-Typed Functional Programming Languages

## Abstract

Statically-typed functional languages boast numerous merits, such as robust type safety, immutability, and expressive syntax, but these can also pose challenges when designing software that effectively solves intricate problems and scales over time. While Object-Oriented Design (OOD) [1] has proven useful in structuring code in its domain, it does not always align with the functional programming paradigm. Predominantly, practices, principles, and methodologies, like Domain-Driven Design [2] and SOLID [3] principles, are centered on object-oriented and imperative programming. Therefore, there is a call for an approach specifically tailored to functional programming's unique requirements. This article introduces Functional Declarative Design (FDD), a counterpart to OOD, specifically crafted to address these needs. FDD offers a practical solution for the design and implementation of superior applications in functional programming. It not only introduces innovative solutions for functional programming difficulties but also reinterprets and adapts established principles from other paradigms, proving itself a comprehensive and adaptable methodology applicable across various functional programming languages and problem domains.

This article is based particularly on the ideas the authors present in their books “Functional Design and Architecture” [17, 18] the goal of which is to provide another perspective on this topic.

![Comparison of the two methodologies](images/two_methodologies.png?raw=true "Comparison of the two methodologies")
_Figure 1.1 Comparison of the two methodologies_

This figure describes how the principles of software design can be applied to Object-Oriented Design and Functional Declarative Design. It turns out every principle can be applied to every methodology however some principles require reformulation or are less appropriate in one of those methodologies.

## Functional Declarative Design Methodology

In this section, the Functional Declarative Design methodology is presented. For a better illustration of the ideas, a simple example is being developed: a sandwich-making mechanism that could be embedded into a cooking game.

### Tools for Managing Complexity

FDD emphasizes simplicity and the management of complexity in software design. The primary goal is to control both essential (domain-specific) and accidental (artificially introduced) complexity in code. Most important but not all tools for maintaining complexity in FDD are presented below.

* Free monads [19]. In FDD, Free monads are considered a functional counterpart to object-oriented interfaces (Java and C# interfaces, abstract classes in C++). Free monads offer their unique properties and capabilities. Free monadic interfaces are interpretable, monadic (capable of handling sequences of actions), and relatively simple.  By defining an abstract Free monadic language for a given domain, complexity is hidden behind the interpretation process, with interpreters serving a similar role as class implementations of an object-oriented interface. Business logic written against interfaces, whether Free monad or object-oriented, depends only on the necessary information, allowing for the substitution of implementations, even at runtime.

* Domain-specific languages (DSLs) [22, 23]. Free monads and algebraic data types can be used to create embedded domain-specific languages (eDSLs) or domain-agnostic application frameworks. DSLs, primarily embedded DSLs, have a great complexity reduction power and allow make the actual business logic much more transparent from the business point of view.

* Hierarchical Free Monads (HFM). HFM is an approach that enables the organization of Free monadic languages into a hierarchy, allowing for separate interfaces for various subsystems and easier merging. This approach is simple and resembles the mainstream practice in which the object-oriented interfaces could be nested, could have methods that accept and return other interfaces and could be organized hierarchically. Free monads have this ability too, and this differs them from effect systems. Domain modeling with Hierarchical Free Monads can lead to well-designed domain-specific business logic, provided the developer follows the appropriate design principles.

* Diagrams For Modeling and Requirements Analysis. FDD introduces several types of diagrams for requirements analysis and modeling: mind maps, necessity diagrams, elements diagrams, and architecture diagrams.

### Diagrams and Visual Representations For Modeling and Requirements Analysis

Diagrams in FDD are a recommended but not a required way to analyze the business domain, functional and non-functional requirements and to model the architecture of an application.

* _Mind maps_: These are used for collecting and brainstorming requirements.

![Mind map: Sandwich making topics](images/top_mind_map.png?raw=true "Mind map: Sandwich making topics")
_Figure 1.2 Mind map: Sandwich making topics_

![Mind map: Recipe subtopics](images/recepe_mind_map.png?raw=true "Mind map: Recipe subtopics")
_Figure 1.3 Mind map: Recipe subtopics_

![Mind map: Ingredients subtopics](images/ingredients_mind_map.png?raw=true "Mind map: Ingredients subtopics")
_Figure 1.4 Mind map: Ingredients subtopics_

![Mind map: Making process subtopics](images/making_process_mind_maps.png?raw=true "Mind map: Making process subtopics")
_Figure 1.5 Mind map: Making process subtopics_

In the mind maps above, the domain of sandwich making is investigated. Figure 1.2 provides three high-level topics each of which is expanded in figures 1.3 (Recipe), 1.4 (Ingredients), and 1.5 (Making process). The task is simple: having a user-defined recipe for a sandwich, and try to produce it with a machine by feeding it with the available ingredients.

* _Necessity diagrams_: unstructured block-like concept maps [21] for representing big blocks of an application that naturally emerge from mind maps.

![Necessity diagram](images/necessity_diagram.png?raw=true "Necessity diagram")
_Figure 1.6 Necessity diagram_

* _Elements diagrams_: unstructured block-like concept maps for brainstorming the technical domain of the task and expanding the larger blocks from necessity diagrams. There is no limit to granularity or specificity. The diagrams are allowed to be unorganized and unstructured. Blocks may be attributed to layers (e.g., application, domain model, interoperability) and types (e.g., concept, subsystem, model, library, data).

![Elements diagram](images/elements_diagram.png?raw=true "Elements diagram")
_Figure 1.7 Elements diagram_

_Architecture diagrams_: structured block-like concept maps that shape the high-level application architecture. These diagrams finalize the knowledge obtained through the creation of mind maps, necessity diagrams, and elements diagrams.

![Architecture diagram](images/architecture_diagram.png?raw=true "Architecture diagram")
_Figure 1.8 Architecture diagram_

FDD encourages an iterative approach, allowing for cyclic and sporadic design processes rather than adhering to a strict waterfall model. Figure 1.8 gives an overview of this iterative approach:

![Iterative approach in FDD](images/iterative_process.png?raw=true "Iterative approach in FDD")
_Figure 1.9 Iterative approach in FDD_
  
### Domain Modeling With Interpretable Domain-Specific Languages

Domain-specific languages (DSLs), particularly embedded DSLs (eDSLs), are central to domain modeling in FDD. Interpretable eDSLs are a recommended way to model the business domain and brainstorm the way of its encoding. Mind maps and elements diagrams work as a source of domain knowledge for designing eDSLs. Interpretable eDSLs can be based on simple or parametrized algebraic data types (ADTs), generalized ADTs (GADTs), and, most importantly, Free monads.

Free monads play a crucial role in FDD as a powerful counterpart to OOP interfaces. Free monadic functional interfaces allow for the creation of Free monadic embedded Domain-Specific Languages (eDSLs) or domain-agnostic application frameworks. Scenarios written on top of Free monadic interfaces are considered a part of business logic.

The following code reflects the domain of sandwich making directly taken from the mind maps:

```haskell
data BreadType = Baguette | Toast

data Component
  = Bread BreadType
  | Tomato
  | Salt
  | Cheese
```

The next snipped demonstrates a domain-specific language for composing sandwich recipes and introduces two domain types for complete and incomplete sandwiches:

```haskell
data SandwichBody = SandwichBody BreadType [Component]
  
data Sandwich = Sandwich BreadType (Maybe BreadType) [Component]

data SandwichConstructor next
  = StartNewSandwich BreadType Component (SandwichBody -> next)
  | AddComponent Component SandwichBody (SandwichBody -> next)
  | FinishSandwich (Maybe BreadType) SandwichBody (Sandwich -> next)

type SandwichRecipe a = Free SandwichConstructor a
```

This language allows constructing of sandwiches and following the requirements for their structure as it’s described in Figure 1.3. It’s possible to start making a new sandwich, then either finish it or add another component. The sequencing of actions (starting a sandwich should go first) is made through the dependency over the value SandwichBody that is either returned as a result or accepted as an argument. Smart constructors corresponding to the methods of this language communicate the idea of dependency through data in a more explicit way:

```haskell
startNewSandwich :: BreadType -> Component -> SandwichRecipe SandwichBody
addComponent :: Component -> SandwichBody -> SandwichRecipe SandwichBody
finishSandwich :: Maybe BreadType -> SandwichBody -> SandwichRecipe Sandwich
```

This language design is not of course free of flaws, but it illustrates the concept well. A typical recipe is shown below:

```haskell
mySandwich :: SandwichRecipe Sandwich
mySandwich = do
  body1 <- startNewSandwich Toast Tomato
  body2 <- addComponent Cheese body1
  body3 <- addComponent Salt body2
  finishSandwich Nothing body3
```

The same script of creating a sandwich with one toast, one tomato, one piece of cheese, and a little bit of salt can be written in a more functional way using the same monadic property of the language:

```haskell
mySandwich :: SandwichRecipe Sandwich
mySandwich
   = startNewSandwich Toast Tomato
  >= addComponent Cheese
  >= addComponent Salt
  >= finishSandwich Nothing
```

In FDD, Free monads are considered the most capable functional interface. A Free monad interface can either represent a subsystem or a domain notion with its behavior. Free monadic interfaces are interpretable, and interpreters of interfaces are considered their implementations.

Free monad functional interfaces share many desirable properties with object-oriented interfaces, such as:

* Abstraction: An interface provides an abstraction of behavior, defining a set of methods without specifying their implementation details. It focuses on what a subsystem can do rather than how it does it.
* Method Signatures: An interface declares the method signatures (name, parameters, and return types) that an interface implementation must define. It outlines the operations that a subsystem can perform.
* Contractual Obligation: Implementing an interface implies a contractual obligation for an implementation to provide the methods declared in the interface. This ensures that different implementations can be used interchangeably if they implement the same interface.
* Multiple Implementations: An interface can have multiple implementations, allowing a variety of instances to exhibit the same behavior within different implementation contexts. This enables polymorphism, where different implementations can be treated uniformly based on their shared interface.
* Separation of Concerns: Interfaces promote the separation of concerns by decoupling the specification of behavior from its implementation. Implementations of the interface can provide their own behavior while adhering to the interface contract.
* Encapsulation: Interfaces facilitate encapsulation by providing a well-defined boundary between the external world and the internal implementation. Client code interacts with an implementation through its interface, shielding the internal details.
* Interface Segregation: The principle of interface segregation suggests that interfaces should be fine-grained and focused on specific responsibilities. This allows clients to depend on specific interfaces rather than having to rely on large, monolithic interfaces.
* Contract Evolution: Interfaces should ideally be stable and evolve in a backward-compatible manner. Once an interface is established, changes should be made carefully to avoid breaking existing implementations.

In addition to these properties, Free monads possess their own:

1. Declarative: Using A Free monadic interface means using a monadic eDSL. This eDSL allows for composing declarative scenarios that represent a business domain and are free of non-related implementation details.
2. Monadic. Free monadic scenarios will be monadic and monadically composable. All the generic monadic facilities such as Control.Monad will work for every Free monadic eDSL out of the box, as well will work the do notation in Haskell (and for comprehension in Scala).
3. Interpretable. A Free monadic eDSL should be traversed and interpreted against a different context, mostly, a real environment.

Using a specific Free monad implementation (normal Free [24], Church-encoded Free [24], “No remorse” Free [25]) may affect the performance of the code but not its conceptual structure.

Free monad functional interfaces adhere to the SOLID design principles as much as OO interfaces do.

### The Hierarchical Free Monads Approach

The Hierarchical Free Monads (HFM) approach allows for the construction of complex application architectures while maintaining proper separation of concerns, making it an invaluable tool in the FDD methodology. The core idea is to organize Free monadic languages (interfaces) hierarchically by nesting lower-level Free interfaces as arguments or return values of methods of top-level Free interfaces.

![Free monad interfaces organized hierarchically](images/hfm.png?raw=true "Free monad interfaces organized hierarchically")
_Figure 1.10 Free monad interfaces organized hierarchically_

Extending the task of making sandwiches, we could be required to make a restaurant menu composer. In addition to sandwiches, it could be a code for making a circle or square pizzas. The following listing introduces such a language:

```haskell
data Crust = ThickCrust | ThinCrust
data PizzaComponent = Salami | AmericanCheese
data Pizza = Pizza Crust [PizzaComponent]

data PizzaConstructor next
 = MakeCirclePizza Crust [PizzaComponent] (Pizza -> next)
 | MakeSquarePizza Crust [PizzaComponent] (Pizza -> next)

type PizzaRecipe a = Free PizzaConstructor a
```

These two languages can finally be merged into a single hierarchy with a unified language, for example, a cooking machine that is able to cook both:

```haskell
data Meal
 = PreparedPizza Pizza
 | PreparedSandwich Sandwich
 deriving (Show, Eq, Ord)

data CookingMethod next
 = MakePizza (PizzaRecipe Pizza) (Pizza -> next)
 | MakeSandwich (SandwichRecipe Sandwich) (Sandwich -> next)

type CookingMachine a = Free CookingMethod a
```

Smart constructors of this language are responsible for wrapping the result into the Meal type:

```haskell
makePizza :: PizzaRecipe Pizza -> CookingMachine Meal
makePizza receipe =
  PreparedPizza <$> liftF (MakePizza receipe id)

makeSandwich :: SandwichRecipe Sandwich -> CookingMachine Meal
makeSandwich receipe =
  PreparedSandwich <$> liftF (MakeSandwich receipe id)
```

The corresponding diagram looks like the following:

![Free monad interfaces for making meals](images/hfm_example.png?raw=true "Free monad interfaces for making meals")
_Figure 1.11 Free monad interfaces for making meals_

Free monad interfaces resemble object-oriented interfaces in the most accurate way. It’s possible to do information hiding, abstraction, and encapsulation with Hierarchical Free Monads in a similar way it’s done in object-oriented languages. For example, it’s possible to ask for an abstracted random recipe for an abstracted meal without revealing what exactly the cooking machine used. This can be achieved by adding another value constructor to CookingMethod:

```haskell
data CookingMethod next
 = MakePizza (PizzaRecipe Pizza) (Pizza -> next)
 | MakeSandwich (SandwichRecipe Sandwich) (Sandwich -> next)
 | MakeRandomMeal (CookingMachine Meal -> next)

type CookingMachine a = Free CookingMethod a

makeRandomMeal :: CookingMachine Meal
makeRandomMeal = join $ liftF (MakeRandomMeal id)
```

This makes the following script possible:

```haskell
sampleCookingMachine :: CookingMachine [Meal]
sampleCookingMachine = do
 sandwich   <- makeSandwich mySandwich
 randomMeal <- makeRandomMeal
 pure [sandwich, randomMeal]
```

Randomizing the meal is now the responsibility of the interpreter, and it can adopt any randomizing algorithm which is an implementation detail and should not be accounted for in the recipes (business logic). 

### Inversion of Control and Dependency Injection

Writing business logic against interfaces is generally known as Inversion of Control. This makes the logic to be less fragile by decoupling it from the implementation details. The HFM approach fully supports this, and makes it difficult to bypass the restrictions, in contrast to other approaches.

The HFM approach provides other interesting properties such as transactional contexts that can be made similar to STM. Alternatively, a transactional context can be made an abstracted adapter of a transactional database with a convenient interface for the business logic developer. It is completely possible to switch between the two by just replacing the interpreters, even in the program’s runtime. From the application behavior point of view, the Free monad scripts will behave as if the dependencies are switched on the fly although internally, the Free monad mechanism works differently. Still, this possibility, while being achieved due to the value-level interpreters, can be seen as Dependency Injection.

The mechanisms of implementation substitution can vary for Free monad architectures, but the general idea is to pass specific interpreters as values and add conditions on when to switch them.

The following code listing outlines the idea of substitutable interpreters for the pizza machine while there is only one sandwich-making machine (interpreter) available. The actual implementation can select a pizza maker from the dictionary provided:

```haskell
runSandwichRecipe :: SandwichRecipe a -> IO a
runPizzaRecipe :: PizzaRecipe a -> IO a

type PizzaMakers = Map String (PizzaRecipe Pizza -> IO Pizza)

runCookingMachine
 :: (SandwichRecipe Sandwich -> IO Sandwich)
 -> PizzaMakers
 -> CookingMachine a
 -> IO a
```

The Hierarchical Free Monads approach facilitates the use of real-world interpreters for Free languages, as well as mock interpreters for testing purposes.

### Free Monad Application Architecture

In the FDD methodology, there are two types of application architectures:

* Domain-specific. Free interfaces serve as eDSLs that reflect a particular business domain. This approach focuses on domain modeling with Free monads and resembles a significant part of Domain-Driven Design.
* Domain-agnostic. Free interfaces represent domain-agnostic subsystems such as logging, database, filesystem, and networking. This approach is geared towards building general-purpose application frameworks.

The HFM approach is used to achieve a 3-layered architecture.

![Three layers of a Free monad application architecture](images/three_layered_architecture.png?raw=true "Three layers of a Free monad application architecture")
_Figure 1.12 Three layers of a Free monad application architecture_

The three layers in this approach are

* Functional interfaces in the form of hierarchical Free monad languages. These interfaces are primarily pure and devoid of implementation details. Separate Free languages describe specific subsystems such as logging, database, filesystem, and networking. One or several unifying Free languages integrate these languages by nesting them within its methods.
* Business logic layer. This layer consists of pure, interpretable Free scenarios built on top of the Hierarchical Free Monads layer. These scenarios describe the sequence of actions required to achieve specific business objectives.
* Implementation layer. This layer contains Free interpreters (pure or impure) for each Free language. Free interpreters are responsible for evaluating Free scenarios step-by-step and connecting them to real-world actions. Free interpreters can also be nested and organized in a way that allows for the substitution of implementations.

The HFM approach also respects purity and takes advantage of the separation of pure and impure layers. Pure layer: Free monad interfaces and business logic. Impure layer: interpreters (implementation).

![Pure and impure layers](images/pure_and_impure.png?raw=true "Pure and impure layers")
_Figure 1.13 Pure and impure layers_

By leveraging the 3-layered application architecture with Free monads, the FDD methodology enables developers to create robust, maintainable, and testable applications that effectively separate concerns and responsibilities across different layers.

### Functional Design Patterns

Design patterns and functional idioms play a significant role in the FDD methodology, as they provide general solutions to common design problems, saving time and effort during the implementation of typical programming tasks.

FDD comes with its own definitions for design patterns and functional idioms.

**DEFINITION**. A design pattern is the “external” solution to certain types of problems. A pattern is an auxiliary compound mechanism that helps to solve a problem in an abstract, generic way. Design patterns describe how the system should work.

**DEFINITION**. A functional idiom is the “internal” solution to certain types of problems. It addresses the natural properties of the object and the immutable transformations of those properties. The idiom describes what the object is and what inseparable mathematical properties it has.

In particular, OOD patterns address objects and mutable interactions between them. An OOD pattern is constructed by using classes, interfaces, inheritance, and encapsulation. In turn, functional idioms introduce new meanings and operations for domain data types.

Architectural design patterns represent a mechanism used for the whole skeleton of the application. Examples of architectural functional design patterns include the Free monad pattern, ReaderT pattern, Final Tagless/mtl, Service Handle pattern, and effect systems. These patterns provide guidance for organizing functional applications and managing dependencies and side effects.

Functional design patterns address more specific problems within functional programming. Examples of these patterns include the MVar request-response pattern, Typed-untyped pattern, Typed avatar pattern, Control structure pattern, Bracket pattern, and HKD pattern. These patterns provide reusable solutions for implementing common functional programming tasks and workflows.

![Typed-untyped design pattern](images/typed_untyped_pattern.png?raw=true "Typed-untyped design pattern")
_Figure 1.14 Typed-untyped design pattern_

Lastly, functional idioms are essential building blocks in functional programming that define specific behaviors and properties. Examples of functional idioms include monoid, functor, applicative functor, monad, foldable, and traversable, among others. These idioms form the basis of functional programming, enabling developers to leverage their mathematical properties to write expressive, concise, and reusable code.

In summary, design patterns and functional idioms are vital components of the FDD methodology. They provide developers with a toolbox of reusable solutions and abstractions that enable them to build more maintainable, modular, and robust functional applications.

### Design Principles

The "divide and conquer" principle is a general design principle that emphasizes separating responsibilities for easier understanding and maintenance. This principle helps reduce accidental complexity.

SOLID design principles implement the "divide and conquer" principle in various ways and can be effectively applied to functional design, particularly with Free monads:

* Single Responsibility principle (SRP): Each Free monadic language should address only the needs of a single subsystem or domain notion.
* Open-close principle (OCP): A unified top-level Free monadic language that nests Free monadic languages of particular subsystems enabling adding more subsystems without affecting the existing business logic.
* Liskov Substitution principle (LSP): Multiple implementations for a single Free monad language can be substituted on the fly without breaking the business logic.
* Interface Segregation principle (ISP): Separate Free monadic languages should be focused and coherent, avoiding unnecessary dependencies in the business logic.
* Dependency Inversion principle (DIP): Business logic should depend on interfaces only, not on the actual implementation, which is provided later in the interpreting process.

By embracing these design principles and the FDD methodology, developers can create functional applications that are modular, maintainable, and adaptable to changing requirements.

## Case Studies and Practical Applications of Functional Declarative Design

This methodology was not developed in a vacuum. It has been used extensively in the development of various real-world technologies and has been proven effective in practical applications. The following case studies illustrate the wide-ranging impact of this methodology in diverse settings:

* _Juspay's PureScript Presto_ [26]: An innovative framework for creating mobile applications. The design revolves around an expressive eDSL, enabling developers to define complex app functionalities with relative ease.

* _Juspay's PureScript Presto.Backend_ [27]: A powerful framework specifically designed for crafting RESTful backend services. This was a natural evolution from Presto, expanding on its foundation with added capabilities such as logging, integration with HTTP APIs, support for key-value and SQL databases, and advanced state handling.

* _Juspay's EulerHS_ [28]: This is a Haskell reincarnation of the PureScript Presto.Backend framework. EulerHS, tailored for building comprehensive web services and RESTful backends, has opened the door for more Haskell-based projects within the company.

All these frameworks were instrumental in driving Juspay's growth, a leading financial company in India. The company's financial services are primarily built on top of Presto, Presto.Backend, and EulerHS, and newer projects have been entirely developed using EulerHS. The methodology's abstraction of complex implementation details behind user-friendly interfaces has simplified the codebase, making it accessible not only to developers but also to managers.

* _Enecuum's Node_ [29]: This is a comprehensive framework for constructing distributed, concurrent, multithreaded applications. Initially conceived for building blockchains, it has been successfully used by Enecuum to develop its own blockchain system.

* _Hydra_ [30]: More than just a functional Free Monadic framework, Hydra is a demonstration of the methodology discussed in this article. Designed for building web and command-line interface (CLI) applications, it offers three engines: Free Monads, Church-Encoded Free Monads, and Final Tagless. Its rich feature set includes support for RESTful apps, SQL databases, key-value databases, multithreading, logging, concurrency, and a built-in testing framework.

Each of these projects serves as a testament to the power and flexibility of the Functional Declarative Design methodology. By simplifying complex tasks, promoting code reusability, and abstracting away implementation details, it has significantly enhanced productivity and improved the quality of software across multiple domains.

Also, the same FDD methodology and the HFM approach were used to implement custom monadic STM libraries in both Haskell and C++ (_stm-free_ [31], _cpp_stm_free_ [32]). These two STM libraries are fully operational although less efficient than a native Haskell’s STM. However, the approach helped to introduce a Haskell-like reliable, declarative, and composable (monadic) STM for C++ which can be seen as a success of the methodology because other STMs in C++ do not possess these properties and are very fragile in usage.

## Conclusion

The Functional Declarative Design (FDD) methodology presents a comprehensive approach to functional programming that harmoniously blends various concepts, including Domain-Driven Design, functional interfaces, design patterns, and SOLID principles. It proposes a versatile framework for effectively designing and building software applications in a functional programming environment.

At its core, FDD emphasizes the significance of abstracting away complex implementation details behind functional interfaces, allowing the development of more maintainable and understandable codebases. The central role of Free Monads in this approach can't be overstated, serving as a powerful counterpart to object-oriented interfaces and facilitating the creation of declarative, abstract, and monadic language designs.

The methodology also introduces a clear separation of concerns through a 3-layered application architecture, fostering a clean delineation between business logic, functional interfaces and services, and implementation details. This structure enhances the adaptability of the system, promoting efficient testing and evolution of the software over time.

Moreover, FDD reshapes our understanding of popular design principles, contextualizing them within a functional programming setting. Principles such as Single Responsibility, Open-Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion are all meticulously incorporated within the FDD paradigm, facilitating their integration into functional design.

The real-world applications of FDD demonstrate its practical relevance. From mobile app development to web services and blockchain technology, this methodology has proven its worth in various business and technological contexts. It enables businesses to leverage the power of functional programming, harnessing it to create robust, scalable, and maintainable systems.

In conclusion, the FDD methodology presents a compelling vision of functional programming that is deeply rooted in practicality. Its impact on the field of functional programming is significant, serving as a roadmap for developers who aspire to master and leverage the power of functional paradigms in their day-to-day coding tasks. With FDD, we have a chance to witness the full potential of functional programming, ultimately contributing to the evolution of the software development landscape.

## Misc

### Importance

As the demand for functional programming skills increases and organizations continue to recognize the value of functional programming languages, the need for a comprehensive methodology like FDD becomes more pronounced. By establishing a well-defined structure and approach, FDD helps developers overcome the challenges commonly associated with the functional programming paradigm. The methodology revisits the ideas of mainstream software engineering in a functional setting in a way never done to this extent before. Functional Declarative Design, therefore, represents a valuable contribution to the field of functional programming, opening the door to more accessible and maintainable software development practices.

This article aims to address a significant argument that has raised concerns. It pertains to the need for consistent availability of resources on high-level design practices and architectures over time. The "State of Haskell Survey" [4, 5, 6, 7, 8] conducted by Taylor Fausak sheds light on this matter. Notably, the survey question "Which of the following Haskell topics would you like to see more written about?" revealed a considerable interest in best practices, design patterns, and application architectures. These topics consistently emerged as the top choices, exhibiting a notable margin. The table below presents the survey results categorized by years:

```
| Year | Best Practices | Design Patterns | Application Architectures |
|------|----------------|-----------------|--------------------------|
| 2018 |     41.4%      |      27.6%      |          27.6%           |
| 2019 |      57%       |       44%       |           42%            |
| 2020 |      29%       |       22%       |           22%            |
| 2021 |      56%       |       42%       |           42%            |
| 2022 |      50%       |       40%       |           37%            |
```

The table provides a comprehensive overview of the survey results, highlighting the popularity percentages for each topic (Best Practices, Design Patterns, and Application Architectures) across different years.

### Background

Functional Design as a Software Engineering discipline was never well-covered compared to Object-Oriented Design. Notable achievements include (but are not limited by) this list:

* Category Theory and Abstract Algebra in application to generic functional programming: lenses, folds, traversals [9], monads [10] et cetera.
* Algebraic domain modeling [11].
* Railway-Oriented Programming (ROP) is an architectural design pattern for a composable way to handle operations that can result in success or failure, commonly known as "railway tracks" or "success and failure paths." [12]
* Domain-Driven Design being rethought for functional domain modeling [13, 14]
* Effect systems for tracking and managing computational effects within a program [15, 16].

Despite the variety of ideas, they are yet disunited and are addressing their own fractions of the Software Engineering discipline in Functional Programming. Many important answers can be certainly found and implied, especially from works by Scott Wlaschin, Debasish Ghosh, and other authors. The most comprehensive methodologies such as Railway-Oriented Programming provide a practical perspective on how to do things. In general, the material on Software Engineering in functional languages is vast, reach, and deep, but still, it doesn’t cover the subject well. Such questions as “What is a counterpart to object-oriented interfaces in functional languages” or “What design patterns in functional languages exist” or “What mainstream design principles are applicable to Functional Programming and whether the latter has its own design principles” should be addressed more carefully.

This article is based particularly on the ideas the authors present in their books “Functional Design and Architecture” [17, 18] the goal of which is to provide another perspective on this topic.
  
## Bibliography

[1] What Is Object-Oriented Design?
https://web.archive.org/web/20070630045531/http://www.objectmentor.com/omSolutions/oops_what.html 
[accessed 28/05/2023].

[2] E. Evans, “Domain-Driven Design: Tackling Complexity in the Heart of Software”, Addison-Wesley Professional 2003

[3] R.C. Martin, “Design Principles and Design Patterns”, pp 4-16, 2000. Available at:
http://staff.cs.utu.fi/~jounsmed/doos_06/material/DesignPrinciplesAndPatterns.pdf
[accessed 28/05/2023].

[4] “2018 State of Haskell Survey Results” https://taylor.fausak.me/2018/11/18/2018-state-of-haskell-survey-results/
[accessed 28/05/2023].

[5] “2019 State of Haskell Survey Results” https://taylor.fausak.me/2019/11/16/haskell-survey-results/
[accessed 28/05/2023].

[6] “2020 State of Haskell Survey Results” https://taylor.fausak.me/2020/11/22/haskell-survey-results/
[accessed 28/05/2023].

[7] “2021 State of Haskell Survey Results” https://taylor.fausak.me/2021/11/16/haskell-survey-results/
[accessed 28/05/2023].

[8] “2022 State of Haskell Survey Results” https://taylor.fausak.me/2022/11/18/haskell-survey-results/
[accessed 28/05/2023].

[9] “Haskell Library. lens: Lenses, Folds and Traversals.” https://hackage.haskell.org/package/lens
[accessed 28/05/2023].

[10] S. Liang, P. Hudak, and M. Jones, “Monad transformers and modular interpreters,” in Proceedings of the 22nd ACM SIGPLAN-SIGACT symposium on Principles of programming languages, pp. 333–343, 1995.

[11] S. Maguire, “Monad transformers and modular interpreters,” Self-published, 2021. Available at:
https://leanpub.com/algebra-driven-design
[accessed 28/05/2023].

[12] S. Wlaschin, “Railway Oriented Programming.” Available at:
https://fsharpforfunandprofit.com/rop/
[accessed 28/05/2023].

[13] S. Wlaschin, “Domain Modeling Made Functional: Tackle Software Complexity with Domain-Driven Design and F#,” Pragmatic Bookshelf, 2018.

[14] D. Ghosh, “Functional and Reactive Domain Modeling”, Manning Publications, 2016.

[15] O. Kiselyov, A. Sabry, and C. Swords, “Extensible effects: an alternative to monad transformers,” ACM SIGPLAN Notices, vol. 48, no. 12, pp. 59–70, 2013.

[16] O. Kiselyov and H. Ishii, “Freer monads, more extensible effects,” ACM SIGPLAN Notices, vol. 50, no. 12, pp. 94–105, 2015.

[17] A. Granin, “Functional Design and Architecture: Building Real Programs in Haskell,” Self-published, 2020. Available at:
https://leanpub.com/functional-design-and-architecture
[accessed 28/05/2023].

[18] A. Granin, “Functional Design and Architecture,” Manning Publications, 2023.

[19] E. Kmett, “Free Monads for Less (Part 1 of 3): Codensity.”
http://comonad.com/reader/2011/free-monads-for-less/
[accessed 28/05/2023].

[20] “What is mind mapping? What Are Its Uses?”
https://www.mindmaps.com/what-is-mind-mapping/
[accessed 28/05/2023].

[21] “Concept maps”
https://learningcenter.unc.edu/tips-and-tools/using-concept-maps/
[accessed 28/05/2023].

[22] M. Fowler, Rebecca Parsons, “Domain-Specific Languages,” Addison-Wesley Professional, 2010.

[23] D. Ghosh, “DSLs in Action,” Manning Publications, 2010.

[24] “Haskell Library. free: Monads for free”:
https://hackage.haskell.org/package/free
[accessed 28/05/2023].

[94] Dependency injection for records-of-functions.” https://hackage.haskell.org/package/dep-t [accessed 07/10/2022].

[25] Atze van der Ploeg, O. Kiselyov, “Reflection without Remorse. Revealing a hidden sequence to speed up monadic reflection.” Available at:
https://okmij.org/ftp/Haskell/zseq.pdf

[26] “PureScript Presto”:
https://github.com/juspay/purescript-presto
[accessed 28/05/2023].

[27] “PureScript Presto Backend”:
https://github.com/juspay/purescript-presto-backend
[accessed 28/05/2023].

[28] “EulerHS”:
https://github.com/juspay/euler-hs
[accessed 28/05/2023].

[29] “Enecuum Node”:
https://github.com/Enecuum/Node
[accessed 28/05/2023].

[30] “Hydra”:
https://github.com/graninas/Hydra
[accessed 28/05/2023].

[31] “stm-free: A little STM on Free monads for Haskell”:
https://github.com/graninas/stm-free
[accessed 28/05/2023].

[32] “cpp_stm_free: C++ Software Transactional Memory library”:
https://github.com/graninas/cpp_stm_free
[accessed 28/05/2023].

