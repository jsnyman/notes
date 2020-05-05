### Modular Programming
* Modular programming refers to the process of breaking a large, unwieldy programming task into separate, smaller, more manageable subtasks or modules. Individual modules can then be cobbled together like building blocks to create a larger application.

### Classes
* Classes provide a means of bundling data and functionality together. Creating a new class creates a new type of object, allowing new instances of that type to be made. Each class instance can have attributes attached to it for maintaining its state. Class instances can also have methods (defined by its class) for modifying its state.
[Classes](https://docs.python.org/3/tutorial/classes.html)

* Classes are blueprints that allow you to create instances with attributes and bound functionality. Classes support inheritance, metaclasses, and descriptors.

#### Module  
* Python has a way to put definitions in a file and use them in a script or in an interactive instance of the interpreter. Such a file is called a module; definitions from a module can be imported into other modules or into the main module.
[Modules](https://docs.python.org/3/tutorial/modules.html)

* Modules are essentially singleton instances of an internal module class, and all their globals are attributes on the module instance.

### Modules vs Classes
So as a general ballpark guideline:

* Use classes as blueprints for objects that model your problem domain.
* Use modules to collect functionality into logical units.

Global state goes in modules (and functions and classes are just as much global state, loaded at the start). Everything else goes into other data structures, including instances of classes.

### Packages
* A project with multiple hierarchical level directories work as a **package** with some attributes.

* When a python scripts need to look **down** into the hierarchical level, the existance of __init__.py file will lead the program flow. Otherwise “package not found error” occurs.


### Python naming conventions
https://realpython.com/python-pep8/

Type	| Naming Convention |	Examples
--- | --- | ---
Function	| Use a lowercase word or words. Separate words by underscores to improve readability. | function, my_function
Variable	| Use a lowercase single letter, word, or words. Separate words with underscores to improve readability. |  x, var, my_variable
Class	| Start each word with a capital letter. Do not separate words with underscores. This style is called camel case.	| Model, MyClass
Method	| Use a lowercase word or words. Separate words with underscores to improve readability. |	class_method, method
Constant	| Use an uppercase single letter, word, or words. Separate words with underscores to improve readability.	| CONSTANT, MY_CONSTANT, MY_LONG_CONSTANT
Module |	Use a short, lowercase word or words. Separate words with underscores to improve readability.	| module.py, my_module.py
Package	| Use a short, lowercase word or
 words. Do not separate words with underscores.	| package, mypackage
