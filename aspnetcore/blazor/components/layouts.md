---
title: ASP.NET Core Blazor layouts
author: guardrex
description: Learn how to create reusable layout components for Blazor apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2024
uid: blazor/components/layouts
---
# ASP.NET Core Blazor layouts

[!INCLUDE[](~/includes/not-latest-version.md)]

This article explains how to create reusable layout components for Blazor apps.

## Usefulness of Blazor layouts

Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall presentation. Placing a copy of the markup for these elements into all of the components of an app isn't efficient. Every time that one of these elements is updated, every component that uses the element must be updated. This approach is costly to maintain and can lead to inconsistent content if an update is missed. *Layouts* solve these problems.

A Blazor layout is a Razor component that shares markup with components that reference it. Layouts can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other features of components.

## Create a layout component

To create a layout component:

* Create a Razor component defined by a Razor template or C# code. Layout components based on a Razor template use the `.razor` file extension just like ordinary Razor components. Because layout components are shared across an app's components, they're usually placed in the app's `Shared` or `Layout` folder. However, layouts can be placed in any location accessible to the components that use it. For example, a layout can be placed in the same folder as the components that use it.
* Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>. The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property (<xref:Microsoft.AspNetCore.Components.RenderFragment> type) for the rendered content inside the layout.
* Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.

> [!NOTE]
> For more information on <xref:Microsoft.AspNetCore.Components.RenderFragment>, see <xref:blazor/components/index#child-content-render-fragments>.

The following `DoctorWhoLayout` component shows the Razor template of a layout component. The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar (`<nav>...</nav>`) and the footer (`<footer>...</footer>`).

`DoctorWhoLayout.razor`:

:::moniker range=">= aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/9.0/BlazorSample_BlazorWebApp/Components/Layout/DoctorWhoLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-8.0 < aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/8.0/BlazorSample_BlazorWebApp/Components/Layout/DoctorWhoLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-7.0 < aspnetcore-8.0"

:::code language="razor" source="~/../blazor-samples/7.0/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-6.0 < aspnetcore-7.0"

:::code language="razor" source="~/../blazor-samples/6.0/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-5.0 < aspnetcore-6.0"

:::code language="razor" source="~/../blazor-samples/5.0/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor":::

:::moniker-end

:::moniker range="< aspnetcore-5.0"

:::code language="razor" source="~/../blazor-samples/3.1/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor":::

:::moniker-end

## `MainLayout` component

In an app created from a [Blazor project template](xref:blazor/project-structure), the `MainLayout` component is the app's [default layout](#apply-a-default-layout-to-an-app). Blazor's layout adopts the [:::no-loc text="Flexbox"::: layout model](https://developer.mozilla.org/docs/Glossary/Flexbox) ([W3C specification](https://www.w3.org/TR/css-flexbox-1/)).

:::moniker range=">= aspnetcore-8.0"

[Blazor's CSS isolation feature](xref:blazor/components/css-isolation) applies isolated CSS styles to the `MainLayout` component. By convention, the styles are provided by the accompanying stylesheet of the same name, `MainLayout.razor.css`. The ASP.NET Core framework implementation of the stylesheet is available for inspection in the ASP.NET Core reference source (`dotnet/aspnetcore` GitHub repository):

* [Blazor Web App `MainLayout.razor.css`](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/BlazorWeb-CSharp/BlazorWeb-CSharp/Components/Layout/MainLayout.razor.css)
* [Blazor WebAssembly `MainLayout.razor.css`](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Layout/MainLayout.razor.css)

[!INCLUDE[](~/includes/aspnetcore-repo-ref-source-links.md)]

:::moniker-end

:::moniker range=">= aspnetcore-5.0 < aspnetcore-8.0"

[Blazor's CSS isolation feature](xref:blazor/components/css-isolation) applies isolated CSS styles to the `MainLayout` component. By convention, the styles are provided by the accompanying stylesheet of the same name, `MainLayout.razor.css`.

:::moniker-end

:::moniker range=">= aspnetcore-8.0"

<!-- UPDATE 11.0 Is https://github.com/dotnet/aspnetcore/issues/52768 addressed
                 to resolve the following limitation? -->

## Statically-rendered layout components

When a Blazor Web App adopts per-page/component rendering (the `Routes` component doesn't specify an interactive render mode), layout components are rendered statically on the server. Applying an interactive render mode directly to a layout isn't supported because Blazor doesn't support serializing a <xref:Microsoft.AspNetCore.Components.RenderFragment> (`@Body` in this case) as a root component parameter. For example, placing `@rendermode InteractiveServer` at the top of the `MainLayout` component results in the following runtime exception:

> :::no-loc text="System.InvalidOperationException: Cannot pass the parameter 'Body' to component 'MainLayout' with rendermode 'InteractiveServerRenderMode'. This is because the parameter is of the delegate type 'Microsoft.AspNetCore.Components.RenderFragment', which is arbitrary code and cannot be serialized.":::

This applies to any layout component that inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> in an app that adopts per-page/component rendering.

This scenario might be addressed in a future release of Blazor. For more information, see [[Blazor] Support serializing render fragments from SSR (`dotnet/aspnetcore` #52768)](https://github.com/dotnet/aspnetcore/issues/52768). In the meantime, you can adopt the following approach in a Blazor Web App that adopts per-page/component rendering.

Create a wrapper component that's capable of interactivity. In the following example, a wrapper component contains a [Blazor section](xref:blazor/components/sections) that can receive content from a child component.

In the `_Imports.razor` file, add an [`@using`](xref:mvc/views/razor#using) directive for sections (<xref:Microsoft.AspNetCore.Components.Sections?displayProperty=fullName>):

```razor
@using Microsoft.AspNetCore.Components.Sections
```

Create the following interactive wrapper component in the `Pages` folder.

`Pages/InteractiveWrapper.razor`:

```razor
@rendermode InteractiveServer

<div>
    <SectionOutlet SectionName="top-bar" />
</div>

@ChildContent

@code {
    [Parameter]
    public RenderFragment? ChildContent { get; set; }
}
```

The `Counter` component can use the wrapper component and set interactive section content. In the following example, a counter button is placed in the section.

`Pages/Counter.razor`:

```razor
@page "/counter"
@rendermode InteractiveServer

<InteractiveWrapper>

    <SectionContent SectionName="top-bar">
        <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
    </SectionContent>

    <PageTitle>Counter</PageTitle>

    <h1>Counter</h1>

    <p role="status">Current count: @currentCount</p>

    <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

</InteractiveWrapper>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Other components around the app can also wrap content in the `InteractiveWrapper` component and set interactive section content.

:::moniker-end

## Apply a layout

### Make the layout namespace available

Layout file locations and namespaces changed over time for the Blazor framework. Depending on the version of Blazor and type of Blazor app that you're building, you may need to indicate the layout's namespace when using it. When referencing a layout implementation and the layout isn't found without indicating the layout's namespace, take any of the following approaches:

* Add an `@using` directive to the `_Imports.razor` file for the location of the layouts. In the following example, a folder of layouts with the name `Layout` is inside a `Components` folder, and the app's namespace is `BlazorSample`:

  ```razor
  @using BlazorSample.Components.Layout
  ```

* Add an `@using` directive at the top of the component definition where the layout is used:

  ```razor
  @using BlazorSample.Components.Layout
  @layout DoctorWhoLayout
  ```

* Fully qualify the namespace of the layout where it's used:

  ```razor
  @layout BlazorSample.Components.Layout.DoctorWhoLayout
  ```

### Apply a layout to a component

Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that has an [`@page`](xref:mvc/views/razor#page) directive. The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute> and applies the attribute to the component class.

The content of the following `Episodes` component is inserted into the `DoctorWhoLayout` at the position of `@Body`.

`Episodes.razor`:

:::moniker range=">= aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/9.0/BlazorSample_BlazorWebApp/Components/Pages/Episodes.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-8.0 < aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/8.0/BlazorSample_BlazorWebApp/Components/Pages/Episodes.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-6.0 < aspnetcore-8.0"

:::code language="razor" source="~/../blazor-samples/6.0/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-5.0 < aspnetcore-6.0"

:::code language="razor" source="~/../blazor-samples/5.0/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor":::

:::moniker-end

:::moniker range="< aspnetcore-5.0"

:::code language="razor" source="~/../blazor-samples/3.1/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor":::

:::moniker-end

The following rendered HTML markup is produced by the preceding `DoctorWhoLayout` and `Episodes` component. Extraneous markup doesn't appear in order to focus on the content provided by the two components involved:

* The H1 "database" heading (`<h1>...</h1>`) in the header (`<header>...</header>`), navigation bar (`<nav>...</nav>`), and trademark information in the footer (`<footer>...</footer>`) come from the `DoctorWhoLayout` component.
* The H2 "episodes" heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.

```html
<header>
    <h1 ...>...</h1>
</header>

<nav>
    ...
</nav>

<h2>...</h2>

<ul>
    <li>...</li>
    <li>...</li>
    <li>...</li>
</ul>

<footer>
    ...
</footer>
```

Specifying the layout directly in a component overrides a *default layout*:

* Set by an `@layout` directive imported from an `_Imports.razor` file, as described in the following [Apply a layout to a folder of components](#apply-a-layout-to-a-folder-of-components) section.
* Set as the app's default layout, as described in the [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section later in this article.

### Apply a layout to a folder of components

Every folder of an app can optionally contain a template file named `_Imports.razor`. The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders. Therefore, an `_Imports.razor` file containing `@layout DoctorWhoLayout` ensures that all of the components in a folder use the `DoctorWhoLayout` component. There's no need to repeatedly add `@layout DoctorWhoLayout` to all of the Razor components (`.razor`) within the folder and subfolders.

`_Imports.razor`:

```razor
@layout DoctorWhoLayout
...
```

The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.

Specifying a layout in `_Imports.razor` overrides a layout specified as the router's [default app layout](#apply-a-default-layout-to-an-app), which is described in the following section.

> [!WARNING]
> Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts. To control the default app layout, specify the layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component. For more information, see the following [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section.
> 
> The same condition results when using an `_Imports.razor` file to apply a layout to a folder of components with the `@layout` directive and the layout component itself is in the same folder or folder hierarchy of the `_Imports.razor` file. An infinite loop of applying the layout occurs because the `@layout` directive is also applied to the layout component. To avoid recursion problems, we recommend storing layout components in their own folder (for example, `Layouts`), away from where `_Imports.razor` files are applying them.

> [!NOTE]
> The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with an [`@page`](xref:mvc/views/razor#page) directive.

### Apply a default layout to an app

Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.RouteView> component. Use the <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout%2A> parameter to set the layout type:

```razor
<RouteView RouteData="routeData" DefaultLayout="typeof({LAYOUT})" />
```

In the preceding example, the `{LAYOUT}` placeholder is the layout (for example, `DoctorWhoLayout` if the layout file name is `DoctorWhoLayout.razor`). You may need to identify the layout's namespace depending on the .NET version and type of Blazor app. For more information, see the [Make the layout namespace available](#make-the-layout-namespace-available) section.

Specifying the layout as a default layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.RouteView> is a useful practice because you can override the layout on a per-component or per-folder basis, as described in the preceding sections of this article. We recommend using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to set the app's default layout because it's the most general and flexible approach for using layouts.

### Apply a layout to arbitrary content (`LayoutView` component)

To set a layout for arbitrary Razor template content, specify the layout with a <xref:Microsoft.AspNetCore.Components.LayoutView> component. You can use a <xref:Microsoft.AspNetCore.Components.LayoutView> in any Razor component. The following example sets a layout component named `ErrorLayout` for the `MainLayout` component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template (`<NotFound>...</NotFound>`).

```razor
<Router ...>
    <Found ...>
        ...
    </Found>
    <NotFound>
        <LayoutView Layout="typeof(ErrorLayout)">
            <h1>Page not found</h1>
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>
```

You may need to identity the layout's namespace depending on the .NET version and type of Blazor app. For more information, see the [Make the layout namespace available](#make-the-layout-namespace-available) section.

:::moniker range=">= aspnetcore-8.0"

> [!IMPORTANT]
> Blazor Web Apps don't use the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parameter (`<NotFound>...</NotFound>` markup), but the parameter is supported for backward compatibility to avoid a breaking change in the framework. The server-side ASP.NET Core middleware pipeline processes requests on the server. Use server-side techniques to handle bad requests. For more information, see <xref:blazor/components/render-modes#static-server-side-rendering-static-ssr>.

:::moniker-end

:::moniker range="= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

:::moniker-end

## Nested layouts

A component can reference a layout that in turn references another layout. For example, nested layouts are used to create a multi-level menu structures.

The following example shows how to use nested layouts. The `Episodes` component shown in the [Apply a layout to a component](#apply-a-layout-to-a-component) section is the component to display. The component references the `DoctorWhoLayout` component.

The following `DoctorWhoLayout` component is a modified version of the example shown earlier in this article. The header and footer elements are removed, and the layout references another layout, `ProductionsLayout`. The `Episodes` component is rendered where `@Body` appears in the `DoctorWhoLayout`.

`DoctorWhoLayout.razor`:

:::moniker range=">= aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/9.0/BlazorSample_BlazorWebApp/Components/Layout/DoctorWhoLayout2.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-8.0 < aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/8.0/BlazorSample_BlazorWebApp/Components/Layout/DoctorWhoLayout2.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-7.0 < aspnetcore-8.0"

:::code language="razor" source="~/../blazor-samples/7.0/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-6.0 < aspnetcore-7.0"

:::code language="razor" source="~/../blazor-samples/6.0/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-5.0 < aspnetcore-6.0"

:::code language="razor" source="~/../blazor-samples/5.0/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor":::

:::moniker-end

:::moniker range="< aspnetcore-5.0"

:::code language="razor" source="~/../blazor-samples/3.1/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor":::

:::moniker-end

The `ProductionsLayout` component contains the top-level layout elements, where the header (`<header>...</header>`) and footer (`<footer>...</footer>`) elements now reside. The `DoctorWhoLayout` with the `Episodes` component is rendered where `@Body` appears.

`ProductionsLayout.razor`:

:::moniker range=">= aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/9.0/BlazorSample_BlazorWebApp/Components/Layout/ProductionsLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-8.0 < aspnetcore-9.0"

:::code language="razor" source="~/../blazor-samples/8.0/BlazorSample_BlazorWebApp/Components/Layout/ProductionsLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-7.0 < aspnetcore-8.0"

:::code language="razor" source="~/../blazor-samples/7.0/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-6.0 < aspnetcore-7.0"

:::code language="razor" source="~/../blazor-samples/6.0/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor":::

:::moniker-end

:::moniker range=">= aspnetcore-5.0 < aspnetcore-6.0"

:::code language="razor" source="~/../blazor-samples/5.0/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor":::

:::moniker-end

:::moniker range="< aspnetcore-5.0"

:::code language="razor" source="~/../blazor-samples/3.1/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor":::

:::moniker-end

The following rendered HTML markup is produced by the preceding nested layout. Extraneous markup doesn't appear in order to focus on the nested content provided by the three components involved:

* The header (`<header>...</header>`), production navigation bar (`<nav>...</nav>`), and footer (`<footer>...</footer>`) elements and their content come from the `ProductionsLayout` component.
* The H1 "database" heading (`<h1>...</h1>`), episode navigation bar (`<nav>...</nav>`), and trademark information (`<div>...</div>`) come from the `DoctorWhoLayout` component.
* The H2 "episodes" heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.

```html
<header>
    ...
</header>

<nav>
    <a href="main-production-list">Main Production List</a>
    <a href="production-search">Search</a>
    <a href="new-production">Add Production</a>
</nav>

<h1>...</h1>

<nav>
    <a href="main-episode-list">Main Episode List</a>
    <a href="episode-search">Search</a>
    <a href="new-episode">Add Episode</a>
</nav>

<h2>...</h2>

<ul>
    <li>...</li>
    <li>...</li>
    <li>...</li>
</ul>

<div>
    ...
</div>

<footer>
    ...
</footer>
```

## Share a Razor Pages layout with integrated components

When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components. For more information, see <xref:blazor/components/integration>.

:::moniker range=">= aspnetcore-8.0"

## Sections

To control the content in a layout from a child Razor component, see <xref:blazor/components/sections>.

:::moniker-end

## Additional resources

* <xref:mvc/views/layout>
* [Blazor samples GitHub repository (`dotnet/blazor-samples`)](https://github.com/dotnet/blazor-samples) ([how to download](xref:blazor/fundamentals/index#sample-apps))
