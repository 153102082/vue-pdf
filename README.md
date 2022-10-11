# VuePDF

VuePDF is a **Vue 3** wrapper for pdf.js that enable you to display pdf pages in your project.

## Compatibility

This package is for Vue 3 and supports the same browsers as Vue 3.
If you want a package for Vue 2 or older browsers check this project from FranckFreiburger: [vue-pdf](https://github.com/FranckFreiburger/vue-pdf).

## Content

* [**Install**](#install)
* [**Live Demo**](#live-demo)
* [**Basic Usage**](#basic-usage)
* [**usePDF Composable**](#pdfproxy-composable)
  * [**Params**](#params)
  * [**Returns**](#returns)
* [**VuePDF Component**](#vuepdf-component)
  * [**Props**](#props)
  * [**Events**](#events)

## Install

```console
npm i @tato30/vue-pdf
```

## Live Demo

You can find a [live demo](https://tato30.github.io/VuePDF/) to check a few examples to use the component.

## Basic Usage

```vue
<template>
  <VuePDF :pdf="pdf" :page="1" />
</template>

<script>
import {usePDF, VuePDF} from 'VuePDF'

export default {
  components: {
    VuePDF
  },
  setup(){
    const { pdf, pages, info } = usePDF("document.pdf")

    console.log(`Document has ${pages} pages`)
    console.log(`Document info: ${info}`)

    return {
      pdf
    }
  }
}
</script>
```

## usePDF Composable

This function is the pdf loader, let you get the basic information and properties about pdf document.

### **Params**

#### **src**

Type: `string | URL | TypedArray` <br/>
Required: `True`

This param is the same `src`  of pdf.js

```js
const { pdf, pages, info } = usePDF("document.pdf")
```

#### **options**

Type: `object`

an object with the optional properties:

- `onPassword`: Callback function to request the document password if wrong or no passwrod provider.
- `onProgress`: Callback function to enable progress monitor.

```js
const onPassword = (updatePassword, reason) => {
  console.log(`Reason for callback: ${reason}`)
  updatePassword('documentpassword1234')
}

const onProgress = ({loaded, total}) => {
  console.log(`${loaded / total * 100}% Loaded`);
}

const { pdf, pages, info } = usePDF("document.pdf", {
  onPassword: onPassword,
  onProgress: onProgress
})
```

### **Returns**

#### **pdf**

Type: `PDFDocumentLoadingTask`

The loading task of document, see [PDFDocumentLoadingTask]([https://](https://mozilla.github.io/pdf.js/api/draft/module-pdfjsLib-PDFDocumentLoadingTask.html)) for more details

#### **pages**

Type: `int`

Pages number of document

#### **info**

Type: `object`

Info object about document

---

## VuePDF Component

This is the component to render a pdf page.

### **Props**

#### **:pdf**

Type: `PDFDocumentLoadingTask` <br/>
Required: `True`

The PDFDocumentLoadingTask obtained from [PDFProxy](#pdf)

```html
<VuePDF :pdf="pdf" />
```

#### **:page**

Type: `int` <br/>
Default: `1`

Page to render, this prop must be the page number starting at 1

```html
<VuePDF :pdf="pdf" :page="2" />
```

#### **:scale**

Type: `int` <br />
Default: `1`

Scale to render page

```html
<VuePDF :pdf="pdf" :page="1" :scale="0.5" />
```

#### **:rotation**

Type: `int` <br />
Default: `Document Default`

Rotate the page in 90° multiples eg. (`90`, `180`, `270`)

```html
<VuePDF :pdf="pdf" :page="1" :rotation="90" />
```

#### **:text-layer**

Type: `boolean` <br />
Default: `false`

Enable text selection in page

```html
<VuePDF :pdf="pdf" :page="1" text-layer />
```

#### **:annotation-layer**

Type: `boolean` <br />
Default: `false`

Enable document annotations like links, popups, etc.

```html
<VuePDF :pdf="pdf" :page="1" annotation-layer />
```

### **Events**

#### **@loaded** -> `object`

Emitted when page has finishes rendering in view

```vue
<template>
  <VuePDF :pdf="pdf" :page="2"  @loaded="loadedEvent"  />
</template>

const loadedEvent = (value) => {
  console.log(value);
},

```

#### **@annotation** -> `object`

Emitted when user has interaction with any annotation in document view.

```vue
<template>
  <VuePDF :pdf="pdf" :page="2"  @annotation="annotationEvent"  />
</template>

const annotationEvent = (value) => {
  console.log(value);
},
```

Annotations values has the following struct:
| Property | Value                                                                                                                                  |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `type`   | Annotation type, posible values: `internal-link`, `link`, `file-attachment`, `form-text`, `form-select`, `form-checkbox`, `form-radio` |
| `data`   | Annotation associated data                                                                                                             |

#### **EXAMPLES**:
#### **internal-link**
`internal-link` fires when user clicks a link that redirects to other content within the document
```json
{
  "type": "internal-link",
  "data": {
    "referencedPage": 3,
    "offset": { 
      "left": 82,
      "bottom": 716
    }
  }
}
```
#### **link**
`link` fires when user clicks an external link 
```json
{
  "type": "link",
  "data": {
    "url": "mailto:aor@testmail.com",
    "unsafeUrl": "mailto:aor@testmail.com"
  }
}
```
#### **file-attachment**
`file-attachment` fires when user double-clicks an attachment link 
```json
{
  "type": "file-attachment",
  "data": {
    "filename": "utf8test.txt",
    "content": [ 83, 101, 110, ... ] // Uint8Array
  }
}
```
#### **form-text**
`form-text` fires when user inputs a value in an textfield element 
```json
{
  "type": "form-text",
  "data": {
    "fieldName": "firstname",
    "value": "Aldo Hernandez"
  }
}
```
#### **form-select**
`form-text` fires when user inputs a value in an one-select or multi-select element 
```json
{
  "type": "form-select",
  "data": {
    "fieldName": "gender",
    "value": [
      {
        "value": "M",
        "label": "Male"
      }
    ],
    "options": [
      {
        "value": "",
        "label": "-"
      },
      {
        "value": "M",
        "label": "Male"
      },
      {
        "value": "F",
        "label": "Female"
      }
    ]
  }
}
```
#### **form-checkbox**
`form-checkbox` fires when user changes a checkbox field 
```json
{
  "type": "form-checkbox",
  "data": {
    "fieldName": "newsletter",
    "checked": true
  }
}
```
#### **form-radio**
`form-radio` fires when user changes a radio field 
```json
{
  "type": "form-radio",
  "data": {
    "fieldName": "drink",
    "value": "Wine",
    "defaultValue": "Beer",
    "options": [ "Water", "Beer", "Wine", "Milk" ]
  }
}
```







<!-- 
{ 
  "viewBox": [0, 0, 595.276, 841.89],
  "scale": 1,
  "rotation": 90,
  "offsetX": 0,
  "offsetY": 0,
  "transform": [0, 1, 1, 0, 0, 0],
  "width": 841.89,
  "height": 595.276
}
 -->