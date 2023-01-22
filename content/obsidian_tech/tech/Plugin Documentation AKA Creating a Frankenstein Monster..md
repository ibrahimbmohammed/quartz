[[Drawing 2023-01-11 23.01.25.excalidraw]]



A plug-in (plugin, addin, add-in, addon, add-on or snap-in; but see extension) is a computer program that interacts with a host application (a web browser or an email client, for example) to provide a certain, usually very specific, function “on demand”. Applications support plugins for many reasons. 


https://mauriziostorani.wordpress.com/2008/06/27/pros-and-cons-of-plugin-systems/

**A good plugin architecture is difficult to achieve from scratch, but offers its own rewards. It makes the software flexible and simple to maintain by localising complexity. The foremost skill it requires is the ability to write [loosely coupled code](http://en.wikipedia.org/wiki/Loose_coupling).**



Designing a good plugin environment has many of the same challenges as designing a good library. If you are producing both the environment and the plugins yourself, then it's not so bad since you can update all the plugins as the environment evolves, but if the plugin api is open to all, 
- Then it requires careful planning and execution to get the design right to avoid too many plugin rewrites as the environment evolves.

"[Second-system syndrome](http://en.wikipedia.org/wiki/Second-system_effect)" described by Fred Brooks advocates that the second system developed is often excessively generic, aiming for ultimate flexibility, sometimes producing a 

A pluggable design is often seen as a way out when requirements are non-existent or underspecified. To compensate, the software is made as flexible as possible to try to handle "whatever comes along".


Why Plugings

-   Enable developers outside the core product to add feature
-   Make it easy to add these features
-   Reduce the size of the core  
-   Enable independent release
-   To support features yet unforeseen.
-   To reduce the size of an application.

pros
-   Extensibility: the application can be dynamically extended to include new features.

-   Parallel development: since features can be implemented as separate components, they can     be developed in parallel by different teams.

-   Clear development direction: since the plugin framework ideally provides a well-defined interface and documentation for plugin writers, developers have a clear roadmap for development.

-   Simplicity: a plugin typically has one function, and so developers have a single focus



cons

-   Extensibility: does the plugin interface anticipate the ways plugin writers what to extend the app, or does it restrict extension. Designing extensibility to meet all use cases often takes several iterations, or extremely good requirements analysis.

-   Maintainability: the provider of the plugin framework not only has to make sure the plugin interface satisfies indented use cases, is clear and well documented, but also that it can evolve. Managing versions and backwards compatibility with existing plugins can be very hard. Hard enough that many practical implementations don't bother, and push the onus on plugin writers to update their plugins with each version.

-   Complexity: although each plugin works when tested alone, interactions between plugins can cause new problems, with bugs appearing only with certain combinations of plugins.

-   Testing: testing plugins can be difficult if the plugin system does not provide some form of mock plugin runner for testing, which is sometimes not possible, and testing is only available by running the plugin for real, which slows down development.

-   Artifical separation: a plugin typically has a single focus, but what constitues a single focus is set by the plugin api provider. If a plugin writer finds he needs a plugin that can reasonably do 2 things (as defined by the plugin api) in close tandem, he may end up having to implement two plugins and find ways of providing communication between them that is not presently provided by the api. He's then having to work around or against the plugin framework.


Major issue..

- A well defined Standard. (SDK, Docs)
- Security Conciousness
- Performance is not an issue in general. Nonetheless plugins can influence heavily the startup performance. Just try to start Eclipse SDK, then one of the commercial offering built on top of Eclipse (Rational, SAP, BEA etc). You will see the difference :-).