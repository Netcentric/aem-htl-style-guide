# AEM Sightly Style Guide
A style guide for the Sightly, the HTML templating system from Adobe Experience Manager (AEM).

## Table of Contents

  1. [HTML](#html)
  2. [Comments](#comments)
  3. [Expression language](#expression-language)

## 1. HTML <a name='html'></a>

  - [1.1](#1.1) <a name='1.1'></a> **Always self close HTML void elements.**
    ```html
    <!--/* Bad */-->
    <input type="text" name="name">
    <img src="smiley.gif" alt="Smiley face" height="42" width="42">
    <meta name="author" content="Erik Grijzen">
    <link rel="stylesheet" type="text/css" href="styles.css">
     
    <!--/* Good */-->
    <input type="text" name="name" />
    <img src="smiley.gif" alt="Smiley face" height="42" width="42" />
    <meta name="author" content="Erik Grijzen" />
    <link rel="stylesheet" type="text/css" href="styles.css" />
    ```

**[⬆ back to top](#table-of-contents)**

## 2. Comments <a name='comments'></a>

  - [2.1](#2.1) <a name='2.1'></a> **Always use Sightly comments.**
  
    Normal HTML comments get rendered to the final markup. To keep the DOM clean, always use Sightly comments over normal HTML comments.

    ```html
    <!-- Never use HTML comments -->
 
    <!--/* Always use Sightly comments */-->
    ```

**[⬆ back to top](#table-of-contents)**

## 3. Expression language <a name='expression-language'></a>

  - [3.1](#3.1) <a name='3.1'></a> **Always use the safest display context as possible.**
  
    See here a list of all the available <a href="https://github.com/Adobe-Marketing-Cloud/sightly-spec/blob/master/SPECIFICATION.md#121-display-context" target="_blank">display contexts</a>.

    ```html
    <!--/* Bad */-->
    <h2>${teaser.headline @ context = 'html'}</h2>
 
    <!--/* Good */-->
    <h2>${teaser.headline @ context = 'text'}</h2>
    ```

**[⬆ back to top](#table-of-contents)**

# };
