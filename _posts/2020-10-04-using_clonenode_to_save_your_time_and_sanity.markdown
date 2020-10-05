---
layout: post
title:      "Using cloneNode to save your time and sanity"
date:       2020-10-04 21:16:05 -0400
permalink:  using_clonenode_to_save_your_time_and_sanity
---


When I was working on my JavaScript and Rails Project I was trying to build an interactive app, that will present information on the screen only when it was prompted to do so by the user.

In HTML or Rails, we would just build a form that will be present on the page and we will use it when we need to, but JavaScript opened new possibilities to manipulate the DOM and present information when it's needed.

JavaScript gives a lot of functionality, but it does require lots of code to make some things work. For example, to add this HTML code to your document: 

**HTML**

```
<li class="menu" id="menu_3">
    <a href="http://localhost:3000/menus/3" class="menuLink">Apple Menu </a>
    <button class="deleteMenu" id="3" style="">✘</button>
    <button class="editMenu" id="3" style="">✎</button>
</li>`
```
You will need that much more JavaScript Code:

**JavaScript**

```
  displayMenu(){
    let li = document.createElement('li')
    li.className = 'menu'
    li.id = `menu_${this.id}`

    let link = document.createElement('a')
    link.setAttribute('href',`${MENUS_URL}/${this.id}`)
    link.innerText=`${this.name} `
    link.className = 'menuLink'

    let deleteButton = document.createElement('button')
    deleteButton.className = `deleteMenu`
    deleteButton.id = this.id
    deleteButton.innerText = '✘'
    deleteButton.style ="align: right;"

    let editButton = document.createElement('button')
    editButton.className = `editMenu`
    editButton.id = this.id
    editButton.innerText = '✎'
    editButton.style ="right;"

    li.appendChild(link)

    li.appendChild(deleteButton)
    li.appendChild(editButton)
    return li;
  }
```

Well, good news you don't need to recreate this line each time you need to add another `<li>` element. In JavaScript, you can use Classes, and they serve as object templates. You need to declare class using the following convention:

```
class Menu {

  static all_menus = []

  constructor(name,id, items) {
    this.name = name;
    this.id = id;
    this.items = items
    Menu.all_menus.push(this)
  }

}
```

`Constructor()` method is used to create and initialize this class objects. Also, the code above includes static method `all_menus` it will be used in the class to keep all of the class Objects, when a class object is created, it gets pushed into `all_menus`.  As you can see it's prepended by `static` keyword, which makes it a class method and it's called on entire class i.e Menu.all_menus, comparing to previously mentioned `displayMenu()` method, it's an instance method that will be called on an instance of the class. So if we would like to build `li` element for each instance of our Menu class, we can do use both of our methods:

```
Menu.all_menus.forEach((item) => {
  item.displayMenu()
});
```

So the beauty of it, that once you build a method, you can reuse it and make your code nice and clean, but `display menu()` is a relatively small piece of code. When I was working on building forms with JavaScript I lost it, because of the number of elements that I needed to add to each DOM Element (input, labels, values, etc.).

I was really worried that my code will have a lot of errors, and it will be hard to manage and then I came across `cloneNode`.

> The [Node.cloneNode()](https://developer.mozilla.org/en-US/docs/Web/API/Node/cloneNode) method returns a duplicate of the node on which this method was called.

I decided that it will be easier to build a form in HTML, hide it with CSS by (` display: none;`) and then clone the form when I needed it, change ids, classes, etc, and append it to the necessary element.


```
let editMenuForm = addNewMenuForm.cloneNode(true)
const menusColumn=document.querySelector('#menusColumn')
editMenuForm.className= 'editMenuForm'
editMenuForm.id = `editMenu_${this.id}
menusColumn.appendChild(editMenuForm)
```
 
Understanding how to build elements and manipulate DOM with JavaScript is crucial to be working with JavaScript. Even though you can build each element step by step, I found using `cloneNode` very useful, specifically when working with forms.


