---
title: About function pointer in C
mathjax: false
copyright: true
comment: true
date: 2019-04-06 09:55:45
tags:
- C
- Functional Programming
- Express in English
categories: 
- Back-End Development
- C

---

{% note primary %}
"About C function pointer in C" expressed in English.
{% endnote %}

<!-- more -->

---


## Forword

I have coded by using the functional programming in JavaScript and Java.I also have learned the C function pointer which can also be used to program functionally.However,I have seldom or never used it.The article will be writen to interpret the function pointer in C.

## Music

> ***Not dyed*** by **He Tu**

<iframe frameborder="no" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=533181327&auto=1&height=66"></iframe>

## Body

There are two main themes in the part:

- C function pointer
- *left-value* & *right-value*

### How to use C function pointer

The function pointer can be declared in this way:

```c
typedef int (*func_ptr)(int,int);
```

In this way,we can declare a function pointer which points to a  class of functions using two *int* parameters and *int* return-values.

While in JavaScript,We need not be careful about it.

In the underlying architecture,the reasons why a function pointer can only point to a class functions which have the same parameters and return-value are as the following:

- the compiler need to ensure the number and type of parameters to allocate the memory space for it,and to perform the correct operation.
- the compiler need to know the return-value type of the function.

Thus,the function pointer must be designated the return-value type and the parameter type.It's the same as the data pointer which has definite specification.

### An example

We declare the function pointer which is as follows:

```c
typedef int(*func_ptr)(int,int);
```


Then we define a function named *maximum*:

```c
int maximum(int a,int b){
    return a>b?a:b;
}
```

Then in the main-function,we use the function pointer in this way:

```c

int main(){
    func_ptr p=&maximum;// '&' can be ignored.
    //func_ptr p=max;
    int a=1,b=2,c=3,d=4;
    printf("Maximum of four numbers is %d!\n",p(a,p(b,p(c,d))));
    return 0;
}

// As what you think,the result is 4.

```

### Write callback function by using functional programming in C

You maybe have used the callback function in C GUI programming such as ***Win32 API*** and ***MFC***.But how does it make it? 

***Callback*** can be implemented by using function pointer.And function pointer is always used as callback function.

We have declared the function pointer `func_ptr` before,and `maximum` function.Then we declare another function and implement a callback function:

```c
typedef int(*func_ptr)(int,int);

int maximum(int a,int b){
    return a>b?a:b;
}

int minimum(int a,int b){
    return a<b?a:b;
}

int max_min(int a,int b,int c,int d,func_ptr max_min_func_ptr){

    //Do something here!

    //As the following: revoke the callback function.
    return max_min_func_ptr(a,max_min_func_ptr(b,max_min_func_ptr(c,d)));
}

int main(){
    func_ptr pmax = maximum;
    func_ptr pmin = minimum;
    int a=1,b=2,c=3,d=4;
    printf("Maximum of four numbers is %d!\n",max_min(a,b,c,d,pmax));
    printf("Minimum of four numbers is %d!\n",max_min(a,b,c,d,pmin));
}

```

**Result:**
```dos
Maximum of four numbers is 4!
Minimum of four numbers is 1!
Press any key to continue . . .
```

In this way,we use these two functions named `maximum` and `minimum` as the callback functions.

### What is *left-value* and *right-value*

The crucial different between both in ***C++***:

{% note primary no-icon %}
- left-value: Can get its address in the memory.
> int a,int a[2],char a,struct {} a,function a...
- right-value: Cannot get its address.
> 2,3.1...
{% endnote %}

**Instead of being assignable or not**

As before,we can conclude that **the function designator**(function name) is a *left-value*.Of course,that is a *left-value* in C++.
While in C,it's neither a *left-value* nor a *right-value*.

## Conclusion

In this article,we are familiar with *c function point*,*callback* and *lr values*.

That's all for the time being.Thanks for your reading!

See you later.