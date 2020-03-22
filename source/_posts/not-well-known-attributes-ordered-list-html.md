---
title: Not well known attributes for ordered list in HTML
date: 2020-03-23 17:00:00
---

In html exist the **ol** tag that allow you to show an ordered list.

```html
<ol>
    <li>Banana</li>
    <li>Strawberry</li>
    <li>Peach</li>
    <li>Orange</li>
    <li>Lemon</li>
</ol
```

and for the above code the result is the following:

![Ordered list showing name of fruits from 1 to 5](https://eperedo-blog.s3.amazonaws.com/html/ol/ol-1.PNG)

But what about if you want to change the order number? You can use the value attribute for the **li** tag.

```html
<ol>
    <li value="5">Banana</li>
    <li value="4">Strawberry</li>
    <li value="3">Peach</li>
    <li value="2">Orange</li>
    <li value="1">Lemon</li>
</ol
```

and the result will be:

![Ordered list showing name of fruits from 5 to 1](https://eperedo-blog.s3.amazonaws.com/html/ol/ol-value-li.PNG)

Great, but there is another attribute that may be useful and give you the same result with less code. I am talking about the **reversed** attribute

```html
<ol reversed>
    <li>Banana</li>
    <li>Strawberry</li>
    <li>Peach</li>
    <li>Orange</li>
    <li>Lemon</li>
</ol
```

much better because of the "less code" part!

![Ordered list showing name of fruits from 5 to 1 using the reverse attribute](https://eperedo-blog.s3.amazonaws.com/html/ol/ol-reversed.PNG)

Ok but now you have a section where you need to show the **Top Ten Fruits**. I know! You are thinking in an easy solution, just mix the **reversed** attribute with the **value** attribute, right?

```html
<ol reversed>
    <li value="10">Banana</li>
    <li>Strawberry</li>
    <li>Peach</li>
    <li>Orange</li>
    <li>Lemon</li>
</ol
```

![Ordered list showing name of fruits from 10 to 5 using the reverse and value attribute](https://eperedo-blog.s3.amazonaws.com/html/ol/ol-reversed-value-li.PNG)

Sure, that get the job done, but in this case there is other way using the **start** attribute. 

```html
<ol reversed start="10">
    <li>Banana</li>
    <li>Strawberry</li>
    <li>Peach</li>
    <li>Orange</li>
    <li>Lemon</li>
</ol
```

Good now you can have the same behavior but using only attributes for the **ol** tag.

![Ordered list showing name of fruits from 10 to 5 using the reverse and start atrribute](https://eperedo-blog.s3.amazonaws.com/html/ol/ol-reversed-start.PNG)

### Browser Support

All these examples will work fine in basically every browser, but worth mentioning a couple of things:

- **start** attribute for the **ol** tag was deprecated in html 4, but still supported in html 5.
- **value** attribute for the **li** tag was deprecated in html 4, but still supported in html 5.

Hope it helps!
