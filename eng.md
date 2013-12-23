*By [Ryan Morr][1]*

When discussing primitive data types in JavaScript, most people are aware of
the basics, starting with*String*, *Number*, and *Boolean*. These primitives
are fairly straightforward and function as you might expect. This article,
however, will be focusing on the more unique primitive data types known as
*Null* and *Undefined*; what makes them similar, dissimilar, and overall
unusual.

## Understanding Null and Undefined

In JavaScript, `null` is a literal and language keyword that represents no
discernible object value. In other words, it’s an indication of “no value.”
While similar,`undefined` actually represents the non-existence of a value.
Both are completely immutable, have no properties or methods, and are incapable
of property assignment. In fact, attempting to access or define a property will
raise a`TypeError`. They are, as their names suggest, completely void of value.

The absence of value lends to their characterization as *falsy* values, meaning
they evaluate to false when employed in the context of a conditional, such as an
`if` statement. Comparing the two with other falsy values using an *equality
operator* (`==`) reveals that they are not equal to anything except themselves:

    null == 0; // false
    undefined == ""; // false
    null == false; // false
    undefined == false; // false
    null == undefined; // true

Despite this and the other similarities, `null` and `undefined` are not
equivalent. Each serve as the sole member of its own distinct type;`undefined`
is of type*Undefined* and `null` is of type *Null*. Comparing the two with a
*strict equality operator* (`===`), which equates both value and type, is enough
to prove that:

    undefined === null; // false

This is an important distinction; both serve different purposes and justifiably
so. To distinguish between the two, you may want to think of `undefined` as
representing an unexpected absence of value and`null` as representing an
expected absence of value.

## Yielding Undefined

There are a number of ways of yielding an `undefined` value in code. It is
typically encountered when attempting to access a value where no value exists.
In this case, JavaScript, being the dynamic and weakly-typed language it is,
will merely default to an`undefined` value rather than raise an error.

Any declared variable that has not been provided an initial value will have a
default value of `undefined`:

    var foo; // defaults to undefined

An `undefined` value is issued when attempting to access an object property or
an array item that does not exist:

    var array = [1, 2, 3];
    var foo = array.foo; // foo property doesn't exist, returns undefined
    var item = array[4]; // no item exists in the array at index 5, returns undefined

If a return statement is omitted from a function, `undefined` is returned
instead:

    var value = (function(){})(); // returns undefined

An argument not supplied to a function invocation will result in an `undefined`

    (function(undefined){
        // parameter is undefined
    })();

The `void` operator can also be used to return an `undefined` value. Libraries
such as [Underscore][1] employ it as a bulletproof means of type checking
because it is immutable and can be relied upon to return `undefined` in any
context:

    function isUndefined(obj){
        return obj === void 0;
    }

Lastly, `undefined` is a predefined global variable (not a keyword like `null`
`undefined` value:

    'undefined' in window; // true

As of ECMAScript 5, this variable is [read-only][2], which was not the case
previously.

## The Use Case for Null

The use case for `null` is the primary aspect that sets it apart, because
unlike `undefined`, `null` is regarded as more of an assignment value. This is
precisely why the `typeof` operator returns *“object”* when employed in
conjunction with a `null` value. The original rationale for this is that `null`
was, and still is, commonly used as an empty reference where an object is
expected, much like a placeholder. This behaviour associated with `typeof` has
since been identified as a bug, and although a correction has been
[proposed][3].

This is why the JavaScript environment never sets a value to `null`; it must be
done programmatically. As the [documentation on MDN][4] puts it:

> In APIs, `null` is often retrieved in place where an object can be expected
> but no object is relevant.

This is true for the DOM, which is language-independent and does not fall
within the scope of the ECMAScript specification. Because it is an external API,
attempting to retrieve a non-existent element returns a`null` value rather than
`undefined`.

Generally, if you need to assign a non-value to a variable or property, pass it
to a function, or return it from a function,`null` is almost always the best
option. To put it simply, JavaScript uses `undefined` and programmers *should*
use `null`.

Another viable use case for `null` that is also considered good practice is an
explicit variable nullification (`object = null`) when a reference is no longer
required. By assigning a `null`, you are effectively purging the reference, and
assuming the object is not referenced elsewhere in code, designating it for
garbage collection, thus reclaiming memory.

## Digging Deeper

What makes `null` and `undefined` such black holes is not just in their
behavior, but also in the manner in which they are handled internally within the
JavaScript environment. They do not seem to possess the same characteristics
typically associated with other primitives or built-in objects.

As of ES5, the `Object.prototype.toString` method, which has become the de-
facto standard for type checking, proves to be consistent in this regard for
both `null` and `undefined`:

    Object.prototype.toString.call(null); // [object Null]
    Object.prototype.toString.call(undefined); // [object Undefined]

However, the `Object.prototype.toString` method is not actually retrieving the
internal`[[Class]]` property of `null` or `undefined` to expose the
constructors. According to the [documentation][5], the following steps are taken
upon invocation:

> 1.  If the this value is `undefined`, return `"[object Undefined]"`.
> 2.  If the this value is `null`, return `"[object Null]"`.
> 3.  Let O be the result of calling `ToObject` passing the `this` value as the
>     argument.
> 4.  Let class be the value of the `[[Class]]` internal property of O.
> 5.  Return the String value that is the result of concatenating the three
>     Strings
>    `"[object "`, class, and `"]"`.

The method performs a simple string return if it detects `null` or `undefined`
merely to uniform functionality with other objects. This is common throughout
the specification, as most methods contain a simple catch and return when they
encounter`null` and `undefined` values. In fact, there is no indication that
they even contain any of the [internal properties][6] typically associated with
every native object. It is as if they are not objects at all? I’m curious to
know if an explicit programmatic definition actually exists internally to the
JavaScript environment for either primitive? Perhaps someone more familiar with
an implementation could weigh in.

## Conclusion

No matter how unusual these primitives appear to be, understanding `null` and
`undefined`, the difference between them, and their distinct roles in
JavaScript are fundamental to the language. It may not exactly make or break
your application, however generally speaking, it can only prove beneficial in
the long run for both development and debugging.

 [1]: https://github.com/jashkenas/underscore/blob/master/underscore.js#L1052
 [2]: http://es5.github.io/#x15.1.1.3
 [3]: http://wiki.ecmascript.org/doku.php?id=proposals:typeof
 [4]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null
 [5]: http://es5.github.io/#x15.2.4.2
 [6]: http://es5.github.io/#x8.6.2