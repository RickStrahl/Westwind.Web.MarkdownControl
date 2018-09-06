# Westwind.Web.MarkdownControl
#### A basic Markdown content rendering control for ASP.NET Web Forms

This simple project provides an ASP.NET Web Forms Server control that allows you to embed Markdown as literal text into a page and expand it as the page renders. This is useful for content pages that contain lots of text and when you don't want to write out HTML in these pages. 

Instead you can add a control like this into the page:

```html
<ww:Markdown runat="server" id="md1">
    ### 1.6.2
    *September 10th, 2017*
    
    * **Keyboard support for Context Menu**  
    You can now pop up the context menu via keyboard using the Windows context menu key (or equivalent). The menu is now cursor navigable. This brings spell checking and various edit operations to keyboard only use.
    
    * **Fix: `UseSingleWindow=false` no longer opens Remembered Documents**   
    When not running in `UseSingleWindow` mode, the `RememberLastDocumentsLength` setting has no effect and no previous windows are re-opened. This is so multiple open windows won't open the same documents all the time. In `UseSingleWindow` mode last documents are remembered and opened when starting MM for the first time.
</ww:Markdown>
```

And the content will be rendered to HTML at runtime.

You can also render Markdown directly via code:

```cs
string html = Markdown.Parse("This is **bold Markdown**.");
```

or

```html
<%= Markdown.Parse("This is **bold Markdown**.") %>
```

or even this in WebPages or MVC:

```html
@Markdown.ParseHtml("This is **bold Markdown**.")
```

### Get it from NuGet
To use the control you can install from [Nuget](https://www.nuget.org/packages/Westwind.Web.MarkdownControl/):

```ps
PS> install-package Westwind.Web.MarkdownControl
```

### Features
The control provides these features:

* ASP.NET Server Control `<ww:Markdown></ww:Markdown>`
* Ability to normalize leading spacing
* Includes a static method to parse Markdown
* Uses the awesome [MarkDig Markdown parser](https://github.com/lunet-io/markdig) to parse Markdown

### Control Usage and Syntax
To use the control add it to your page like any other server control.

First add a reference to the control assembly either on the page or in Web.config:

At the top of the page:
```html
<%@ Register TagPrefix="ww" Namespace="Westwind.Web.MarkdownControl" Assembly="Westwind.Web.MarkdownControl" %>
```

or in `web.config` globally:

```xml
<configuration>
    <system.web>
        <pages>
            <controls>
                <add assembly="Westwind.Web.MarkdownControl" 
                     namespace="Westwind.Web.MarkdownControl" 
                     tagPrefix="ww" />
            </controls>
        </pages>
    </system.web>
</configuration
```      

Then embed the control into the page where you want the markdown to appear:

```xml
<ww:Markdown runat="server" id="md2" NormalizeWhiteSpace="True">
    # Markdown Monster Change Log 
    [download latest version](https://markdownmonster.west-wind.com/download.aspx) &bull; 
    [install from Chocolatey](https://chocolatey.org/packages/MarkdownMonster) &bull; 
    [Web Site](https://markdownmonster.west-wind.com)
</ww:Markdown>
```

#### NormalizeWhiteSpace
This property is true by default and if the control starts with a line that is indented it will strip the same indentation from all lines following. This allows text like this to render properly as Markdown:

```html
<div class="container">
    <ww:Markdown runat="server" id="md1">
        ### 1.6.2
        *September 10th, 2017*
        
        * **Keyboard support for Context Menu**  
        You can now pop up the context menu via keyboard using the Windows context menu key (or equivalent). The menu is now cursor navigable. This brings spell checking and various edit operations to keyboard only use.
        
        * **Fix: `UseSingleWindow=false` no longer opens Remembered Documents**   
        When not running in `UseSingleWindow` mode, the `RememberLastDocumentsLength` setting has no effect and no previous windows are re-opened. This is so multiple open windows won't open the same documents all the time. In `UseSingleWindow` mode last documents are remembered and opened when starting MM for the first time.
    </ww:Markdown>
</div>
```

Notice the indentation of the markdown.  With `NormalizeWhiteSpace` off you need to explicitly left align the embedded Markdown Content:

```xml
<ww:Markdown runat="server" id="md2" NormalizeWhiteSpace="False">
# Markdown Monster Change Log 
[download latest version](https://markdownmonster.west-wind.com/download.aspx) &bull; 
[install from Chocolatey](https://chocolatey.org/packages/MarkdownMonster) &bull; 
[Web Site](https://markdownmonster.west-wind.com)
</ww:Markdown>
```

If not normalized the entire markdown block would render as code (more than 4 white space characters which is a code block). When `NormalizeWhiteSpace` is true the leading space is stripped of the entire block.

> #### First Line Determines Whitespace to strip
> The first line of the Markdown block determines what white space is stripped from all other lines.

Note the default is `True` - if you have funky behavior due to indentation I'd recommend you left justify your markdown and set this value to `False`.

#### SanitizeHtml
Markdown is essentially a superset of HTML as you can embed any HTML into Markdown. Markdown itself doesn't have any rules about what HTML can be embedded and it's entirely possible to embed script code inside of markdown.

If you capture Markdown text from users it's important **you treat input Markdown just as you would raw HTML user input**.

To help with this, the Markdown control has a `SanitizeHtml` property which is set to `True` by default, which performs rudimentary script sanitation. It removes `<script>`, `<iframe>`, `<form>` and a few other elements, removes `javascript:` and `data:` attribute content, and removes `onXXX` event handlers from HTML input.

If you rather render your Markdown *as is* set `SantizeHtml` to `False`.

### Static Markdown Rendering
The control also includes static Markdown rendering that you can use in your Web Application inside of pages or your Web code.

```cs
string html = Markdown.Parse("This is **bold Markdown**");
```

You can also embed Markdown into pages like this:

```html
<div class="container" id="ChangeLogText">
    <%= Markdown.Parse(Model.ChangelogMarkdownText) %>
</div>    
```

or in WebPages or MVC:


```html
<div class="well well-sm">
    @Markdown.ParseHtml("This is **bold Markdown**.")
</div>
```

#### sanitizeHtml Parameter
By default the Parse method applies HTML sanitation via a `sanitzeHtml` parameter, which defaults to `true`. If you would like to get the raw unsanitized HTML returned or you want to do your own HTML Sanitation post parsing, set `sanitizeHtml: false` in the method call.

### Adding Code Highlighting
If you'd like to highlight your code snippets with syntax highlighting I recommend [Highlight.js](https://highlightjs.org/). Using this easy to use library you can add the following to a page to get syntax coloring for code snippets:

```html
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/dracula.min.css" />
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/highlight.min.js"></script>

<script>
    function highlightCode() {
        var pres = document.querySelectorAll("pre>code");
        for (var i = 0; i < pres.length; i++) {
            hljs.highlightBlock(pres[i]);
        }
    }
    highlightCode();
</script>
```

## License
The Westwind.Web.MarkdownControl library is an open source product licensed under **[MIT license](http://opensource.org/licenses/MIT)**, and there's no charge to use, integrate or modify the code for this project. You are free to use it in personal, commercial, government and any other type of application. Commercial licenses are also available.

All source code is copyright West Wind Technologies, regardless of changes made to them. Any source code modifications must leave the original copyright code headers intact.

## Acknowledgements
This library uses the following excellent components:

* [MarkDig Markdown Parser](https://github.com/lunet-io/markdig)

---

<div style="margin-top: 30px;font-size: 0.8em;
            border-top: 1px solid #eee;padding-top: 8px;">
    <img src="https://markdownmonster.west-wind.com/favicon.png"
         style="height: 20px;float: left; margin-right: 10px;" height="20" width="20" />
    content created and published with 
    <a href="https://markdownmonster.west-wind.com"
       target="top">Markdown Monster</a>
</div>

