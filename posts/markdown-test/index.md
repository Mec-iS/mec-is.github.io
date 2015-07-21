<!-- 
.. title: Markdown TEST
.. slug: markdown-test
.. date: 2015-07-21 20:19:22 UTC+02:00
.. tags: Python, Functional, Decorators
.. category: 
.. link: 
.. description: 
.. type: text
-->

# Python Decorators (Classes over Functions?)
 Taken From Bruce Eckel's Blog. 
 All rights reserved http://bruceeckel.github.io
<br/>
<br/>
NOTE: the Author states that using classes as decorators instead of functions improve readability and code refactoring, and also conceptually it is a much better solution in his opinion. I think it can be a good practice, I would like to know what a functional programmer thinks about.
<br/>
## Starting example
"The only constraint upon the object returned by the decorator is that it can be used as a function -- which basically means it must be callable. Thus, any classes we use as decorators must implement `__call__`."
<br/>
<br/>


    class myDecorator(object):
        '''
        Typically, you'll capture the function object in the constructor and later use it in the __call__() method
        '''
        def __init__(self, f):
            print("inside myDecorator.__init__()")
            f() # Prove that function definition has completed
    
        def __call__(self):
            print("inside myDecorator.__call__()")
    
    @myDecorator
    def aFunction():
        print("inside aFunction()")
    
    print("Finished decorating aFunction()")
    
    aFunction()
    

    inside myDecorator.__init__()
    inside aFunction()
    Finished decorating aFunction()
    inside myDecorator.__call__()
    

<br/>
<br/>
"When `aFunction()` is called after it has been decorated, we get completely different behavior; the `myDecorator.__call__()` method is called instead of the original code. That's because the act of decoration replaces the original function object with the result of the decoration -- in our case, the myDecorator object replaces `aFunction`."
<br/>
<br/>


    # Very basic example with Class
    
    class entryExit(object):
    
        def __init__(self, f):
            self.f = f
    
        def __call__(self):
            print("Entering", self.f.__name__)
            self.f()
            print("Exited", self.f.__name__)
    
    @entryExit
    def func1():
        print("inside func1()")
    
    @entryExit
    def func2():
        print("inside func2()")
    
    func1()
    func2()

    Entering func1
    inside func1()
    Exited func1
    Entering func2
    inside func2()
    Exited func2
    


    # Turned into a Function
    
    def entryExit(f):
        def new_f(): 
            """ Wrapping functio, closure """
            print("Entering", f.__name__)
            f()
            print("Exited", f.__name__)
        return new_f
    
    @entryExit
    def func1():
        print("inside func1()")
    
    @entryExit
    def func2():
        print("inside func2()")
        
    func1()
    func2()
    print(func1.__name__)  # it prints the name of the closure function (to correct this you can use @wraps from functools library)

    Entering func1
    inside func1()
    Exited func1
    Entering func2
    inside func2()
    Exited func2
    new_f
    

<br/>
<br/>
# Decorators with arguments

"Without arguments the function to be decorated is passed to the constructor, and the `__call__()` method is called whenever the decorated function is invoked."
<br/>
<br/>


    # see what happens when we add arguments to the decorator:
    
    class decoratorWithArguments(object):
    
        def __init__(self, arg1, arg2, arg3):
            """
            If there are decorator arguments, the function
            to be decorated is not passed to the constructor!
            """
            print("Inside __init__()")
            self.arg1 = arg1
            self.arg2 = arg2
            self.arg3 = arg3
    
        def __call__(self, f):
            """
            If there are decorator arguments, __call__() is only called
            once, as part of the decoration process! You can only give
            it a single argument, which is the function object.
            """
            print("Inside __call__()")
            def wrapped_f(*args): # closure that takes decorators' arguments
                print("Inside wrapped_f()")
                print("Decorator arguments:", self.arg1, self.arg2, self.arg3)
                f(*args)
                print("After f(*args)")
            return wrapped_f # .__call__ returns the closure
    
    @decoratorWithArguments("hello", "world", 42)  # arguments passed to the decorator
    def sayHello(a1, a2, a3, a4):
        print('sayHello arguments:', a1, a2, a3, a4)
    
    print("After decoration")
    
    print("Preparing to call sayHello()")
    sayHello("say", "hello", "argument", "list")
    print("after first sayHello() call")
    sayHello("a", "different", "set of", "arguments")
    print("after second sayHello() call")

    Inside __init__()
    Inside __call__()
    After decoration
    Preparing to call sayHello()
    Inside wrapped_f()
    Decorator arguments: hello world 42
    sayHello arguments: say hello argument list
    After f(*args)
    after first sayHello() call
    Inside wrapped_f()
    Decorator arguments: hello world 42
    sayHello arguments: a different set of arguments
    After f(*args)
    after second sayHello() call
    

<br/>
<br/>
NOTE: instead of using `.__call__` to run the decorated function, "you must it to perform the decoration -- it is nonetheless surprising the first time you see it because it's acting so much differently than the no-argument case, and you must code the decorator very differently from the no-argument case."
<br/>
<br/>


    
