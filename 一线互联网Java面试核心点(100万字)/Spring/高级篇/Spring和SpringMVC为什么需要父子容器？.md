# Spring和SpringMVC为什么需要父子容器？

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">首先，它们帮助划分功能边界，使得大型应用程序更易于管理。通过将不同模块或层次的组件分别放置在父子容器中，我们能够清晰地定义每个容器的职责，从而提高了代码的可维护性和可扩展性。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">其次，父子容器在规范整体架构方面起到了关键作用。例如，我们可以将业务逻辑层（Service）和数据访问层（DAO）交给Spring管理，而将控制器层（Controller）交给SpringMVC管理。这种规范化有助于提高代码的可读性，并使团队协作更加顺畅。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">此外，父子容器还能限制组件之间的依赖关系，确保模块之间的隔离。子容器可以访问父容器中的组件，但反之则不成立。这有助于减少意外的依赖和提高代码的稳定性。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">另一个优势是方便切换子容器。如果我们需要更改子容器，例如从Spring MVC切换到Struts，只需更改子容器的配置而无需更改父容器。这提供了更好的可维护性和扩展性，使得应用程序更容易适应不同的技术栈或框架。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">最后，父子容器的使用还有助于节省资源。父容器中的Bean可以在整个应用程序范围内共享，而不必每次创建。这对于大型应用程序来说尤为重要，可以降低内存和性能开销。</font>

<font style="color:rgb(55, 65, 81);background-color:rgb(247, 247, 248);">综上所述，父子容器在Spring框架中的应用不仅有助于功能性划分，还有助于架构的规范化、模块化、松耦合和可维护性。虽然一些现代框架如Spring Boot可能减少了对父子容器的需求，但在大型和复杂的应用程序中，父子容器仍然是一种有用的设计模式，有助于管理和组织应用程序的各个部分。</font>



> 更新: 2023-09-07 17:08:58  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/vmo9gda5x6y3o7sg>