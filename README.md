# AEM HTL Style Guide
A style guide for the [HTML Template Language](https://docs.adobe.com/docs/en/htl.html) (HTL), formerly known as Sightly, the HTML templating system from Adobe Experience Manager (AEM).

## Table of Contents

  1. [HTML](#html)
  2. [Comments](#comments)
  3. [Expression language](#expression-language)
  4. [Block statements](#block-statements)

<a name='html'></a>
## 1. HTML

  - [1.1](#1.1) <a name='1.1'></a> **Avoid inline JavaScript or CSS.**
  
  In order to encourage keeping a clean separation of concerns, HTL has by design some limitations for inline JavaScript or CSS. First, because HTL doesn't parse JavaScript or CSS, and therefore cannot automatically define the corresponding escaping, all expressions written there must provide an explicit `context` option. Then, because the HTML grammar ignores elements located inside a `<script>` or `<style>` elements, no block statement can be used within them.
  
  Therefore JavaScript and CSS code should instead be placed into corresponding `.js` and `.css` files. Data attributes are the easiest way to communicate values to JavaScript, and class names are the best way to trigger specific styles.

    ```html
    <!--/* Bad */-->
    <section class="teaser" data-sly-use.teaser="com.example.TeaserComponent">
         <h2 class="teaser__title">${teaser.title}</h2>
         <script>
             var teaserConfig = {
                 skin: "${teaser.skin @ context='scriptString'}",
                 animationSpeed: ${teaser.animationSpeed @ context='number'}
             };
        </script>
        <style>
            .teaser__title {
                font-size: ${teaser.titleFontSize @ context='styleToken'}
            }
        </style>
    </section>
    
    <!--/* Good */-->
    <section class="teaser" data-sly-use.teaser="com.example.TeaserComponent" data-teaser-config="${teaser.jsonConfig}">
        <h2 class="teaser__title teaser__title--font-${teaser.titleFontClass}">${teaser.title}</h2>
    </section>
    ```

**[⬆ back to top](#table-of-contents)**

<a name='comments'></a>
## 2. Comments

  - [2.1](#2.1) <a name='2.1'></a> **Always use HTL comments.**
  
    Normal HTML comments get rendered to the final markup. To keep the DOM clean, always use HTL comments over normal HTML comments.

    ```html
    <!-- Never use HTML comments -->
 
    <!--/* Always use HTL comments */-->
    ```

**[⬆ back to top](#table-of-contents)**

<a name='expression-language'></a>
## 3. Expression language

  - [3.1](#3.1) <a name='3.1'></a> **Only set a display context if necessary**
  
  In most cases you can leave out the display context, because it is determined automatically.

    ```html
    <!--/* Bad */-->
    <a href="${teaser.link @ context = 'uri'}"></a>
 
    <!--/* Good */-->
    <a href="${teaser.link}"></a>
    ```

  - [3.2](#3.2) <a name='3.2'></a> **Always use the safest possible display context.**

  From the following list of contexts, always choose the one closest to the top that fits your needs:  
  `number`: For whole numbers (in HTML, JS or CSS)  
  `uri`: For links and paths (in HTML, JS or CSS, applied by default for `src` and `href` attributes)  
  `elementName`: For HTML element names (applied by default by `data-sly-element`)  
  `attributeName`: For HTML attribute names (applied by default by `data-sly-attribute` for attribute names)  
  `scriptToken`: For JavaScript identifiers and keywords  
  `styleToken`: For CSS identifiers and keywords  
  `scriptString`: For text within JavaScript strings  
  `styleString`: For text within CSS strings  
  `attribute`: For HTML attribute values (applied by default for attribute values)  
  `text`: For HTML text content (applied by default for any content)  
  `html`: For HTML markup (it filters out all elements and attributes that could be dangerous)  
  `unsafe`: Unescaped and unfiltered direct output  

    ```html
    <!--/* Bad */-->
    <section class="teaser" data-sly-use.teaser="com.example.TeaserComponent">
        <h4 onclick="${teaser.clickHandler @ context='unsafe'}">${teaser.title}</h4>
        <div style="color: ${teaser.color @ context='unsafe'};">
            ${teaser.htmlContent @ context='unsafe'}
        </div>
    </section>
 
    <!--/* Good */-->
    <section class="teaser" data-sly-use.teaser="com.example.TeaserComponent">
        <h4 onclick="${teaser.clickHandler @ context='scriptToken'}">${teaser.title}</h4>
        <div style="color: ${teaser.color @ context='styleToken'};">
            ${teaser.htmlContent @ context='html'}
        </div>
    </section>
    ```

  - [3.3](#3.3) <a name='3.3'></a> **Don't write unnecessary expressions for literals.**
  
  It might sound obvious, but an expression with just a string literal inside equals just that string literal.

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.clientlib="${'/libs/granite/sightly/templates/clientlib.html'}">
        ...
    </sly>
 
    <!--/* Good */-->
    <sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
        ...
    </sly>
    ```
  
**[⬆ back to top](#table-of-contents)**

<a name='block-statements'></a>
## 4. Block statements

  - [4.1](#4.1) <a name='4.1'></a> **Use the SLY tag name for all elements that are not part of the markup.**
  
    HTML elements with the tag name SLY are automatically getting unwrapped and will not be part of the final markup.

    ```html
    <!--/* Bad */-->
    <div data-sly-include="content.html" data-sly-unwrap></div>
     
    <!--/* Bad */-->
    <div data-sly-resource="${item @ selectors='event'}" data-sly-unwrap></div>
     
    <!--/* Bad */-->
    <div data-sly-test="${event.hasDate}" data-sly-unwrap>
        ...
    </div>
     
    <!--/* Good */-->
    <sly data-sly-include="content.html"></sly>
     
    <!--/* Good */-->
    <sly data-sly-resource="${item @ selectors = 'event'}"></sly>
     
    <!--/* Good */-->
    <sly data-sly-test="${event.hasDate}">
        ...
    </sly>
    ```
    
    **IMPORTANT** - The SLY element will not automatically unwrap itself if you use HTL 1.0 (AEM 6.0). In that case, you still have to add the "data-sly-unwrap" attribute.
    
    ```html
    <!--/* Bad - HTL 1.0 */-->
    <sly data-sly-include="content.html"></sly>
     
    <!--/* Good - HTL 1.0 */-->
    <sly data-sly-include="content.html" data-sly-unwrap></sly>
    ```
    
  - [4.2](#4.2) <a name='4.2'></a> **Try to place use data-sly-use statements only on top-level elements.**
    
    Since data-sly-use identifiers are always global (https://docs.adobe.com/docs/en/htl/docs/use-api/java.html#Local%20identifier), these attributes should only be placed in the top-level element. That way one can easily see name clashes and also it prevents initializing the same object twice.
    
     ```html
    <!--/* Bad */-->
    <section class="teaser">
        <h3 data-sly-use.teaser="com.example.TeaserComponent">${teaser.title}</h3>
    </section>
     
    <!--/* Good */-->
    <section class="teaser" data-sly-use.teaser="com.example.TeaserComponent">
        <h3>${teaser.title}</h3>
    </section>
    ```
    
  - [4.3](#4.3) <a name='4.3'></a> **Use meaningful identifier names.**
  
    This will enhance the readability of your HTL scripts and and makes it easier for others to understand.

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.comp="com.example.TeaserComponent">
        ...
    </sly>
     
    <!--/* Good */-->
    <sly data-sly-use.teaser="com.example.TeaserComponent">
        ...
    </sly>
    ```
    
  - [4.4](#4.4) <a name='4.4'></a> **Use camelcase for identifier names.**
  
    Using camelCase will help to increase the readability of your identifiers. Notice though that
    HTL will internally only use (and log) lowercase identifiers. Also dashes are not allowed for identifiers.

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.mediagallery="com.example.MediaGallery">
        ...
    </sly>
     
    <!--/* Good */-->
    <sly data-sly-use.mediaGallery="com.example.MediaGallery">
        ...
    </sly>
    ```
    
  - [4.5](#4.5) <a name='4.5'></a> **Always cache test block statement results in an identifier if it repeats itself.**

    ```html
    <!--/* Bad */-->
    <section class="teaser" data-sly-test="${!teaser.empty}">
        ...
    </section>
     
    <div class="cq-placeholder" data-sly-test="${teaser.empty}"></div>
     
    <!--/* Good */-->
    <section class="teaser" data-sly-test.hasContent="${!teaser.empty}">
        ...
    </section>
     
    <div class="cq-placeholder" data-sly-test="${!hasContent}"></div>
    ```
    
  - [4.6](#4.6) <a name='4.6'></a> **Always use identifiers instead of the default “item” variable for list block statements.**

    ```html
    <!--/* Bad */-->
    <ul class="tagList" data-sly-list="${tagList.tags}">
        <li class="tagList__tag">
            <a class="tagList__button" href="${item.url}">${item.title}</a>
        </li>
    </ul>
     
    <!--/* Good */-->
    <ul class="tagList" data-sly-list.tag="${tagList.tags}">
        <li class="tagList__tag">
            <a class="tagList__button" href="${tag.url}">${tag.title}</a>
        </li>
    </ul>
    ```
    
  - [4.7](#4.7) <a name='4.7'></a> **Always place block statements after the normal HTML attributes.**

    ```html
    <!--/* Bad */-->
    <p data-sly-test="${teaser.text}" class="teaser__text"></p>
     
    <!--/* Good */-->
    <p class="teaser__text" data-sly-test="${teaser.text}"></p>
    ```
    
  - [4.8](#4.8) <a name='4.8'></a> **Always use existing HTML elements for your block statements if possible.**

    ```html
    <!--/* Bad */-->
    <sly data-sly-test="${!teaser.active}">
        <section class="teaser">
            …
        </section>
    </sly>
     
    <!--/* Good */-->
    <section class="teaser" data-sly-test="${!teaser.active}">
        …
    </section>
    ```
    
  - [4.9](#4.9) <a name='4.9'></a> **Try to avoid the element, attribute and text block statements.**
  
    It's a lot cleaner and explicit writing your HTL scripts without these block statements.

    ```html
    <!--/* Bad */-->
    <div data-sly-element="${headlineElement}">${event.year}</div>
    <a href="#" data-sly-attribute.href="${event.link}"></a>
    <p class="event__year" data-sly-text="${event.year}"></p>
     
    <!--/* Good */-->
    <h2>${event.year}</h2>
    <a href="${event.link}"></a>
    <p class="event__year">${event.year}</p>
    ```
    
  - [4.10](#4.10) <a name='4.10'></a> **Always define your templates in a separate file.**
  
    It's cleaner to create separate files for your template markup, so your HTL scripts will not get cluttered. 

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.teaser="com.example.TeaserComponent">
      <sly data-sly-template.teaserSmall="${@ title, text}">
        <h2>${title}</h2>
        <p>${text}</p>
      </sly>
      
      <sly data-sly-call="${teaserSmall @ title=teaser.title, text=teaser.text}"></sly>
    </sly>
    
    <!--/* Good - Separate template file: "teaser-templates.html" */-->
    <sly data-sly-template.teaserSmall="${@ teaserModel}">
      <h2>${teaserModel.title}</h2>
      <p>${teaserModel.text}</p>
    </sly>
    
    <!--/* Good - HTL script */-->
    <sly data-sly-use.teaser="com.example.TeaserComponent" data-sly-use.teaserTemplates="teaser-templates.html">
      <sly data-sly-call="${teaserTemplates.teaserSmall @ teaserModel=teaser}"></sly>
    </sly>
    ```
    
**[⬆ back to top](#table-of-contents)**


## License

The MIT License (MIT)

Copyright (c) 2015 Netcentric

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

**[⬆ back to top](#table-of-contents)**
