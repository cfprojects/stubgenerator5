File: CFCStubGenerator.cfc
http://cfcstub.riaforge.org
Author: Brian Kotek
Site: http://www.briankotek.com/blog

Captivate presentations about this tool:

http://experts.acrobat.com/p61249254/
http://experts.acrobat.com/p54208808/
(longer version of UML creation): http://experts.acrobat.com/p85142436/


Usage:
The Stub Generator can run using two types of files:

----------------------------------------------------
Simple text file:

This CFC can use a simple text file to create stubs for CFCs and CFUnit/CFCUnit unit test files. Just create an instance of the component, and call the createStubs() method. The easiest way it to run the index.cfm configuration screen included in this zip file. The CFC and CFUnit files will all be created in the same directory as the text file. Any CFC paths or folder paths will be created using the path to the text file directory as well.

The format of the text file is:

User
getUserName
getAddress

Invoice
calculateTotal
addLineItem

This is just thie list of component names and the names of the methods that you want stubbed out. Separate the components with 2 carriage returns.

-----------------------------------------------------
XMI file:

This Stub Generator can generate components based on an XMI data file. XMI is a standard XML format for describing UML diagrams. I have only tested this with Poseidon UML, but as far as I know any UML tool that can export to XMI should work. Note that the location of the XMI file used in the generation will be used as the base location for the test suite files (the test suite files will be placed in a subdirectory called 'testsuite' beneath the folder containing the XMI file).

Built-in ColdFusion data types: The cfctemplate.zuml file already has the necessary data types created to represent most of the built-in ColdFusion data types like query, struct, etc. It also has some Java data types defined since Posiedon will try to create these and they can interfere with the CFC generation. It is crucial that you type all argument and return types using these types, even if you pick the 'any' data type. So you will probably want to start with this template, save it with a new name, and work from there.

Packages: Poseidon supports the creation of packages and I suggest using them. The generator will use the package paths for any CFC or interface components and use these when adding argument types, return types, extends, and implements. It also uses the package paths to create the ColdSpring XML, test suites, and ANT build XML files. The package paths are determined relative to the web root. Thus, if you have a package in your UML that resolves to 'myapp.components.beans', the CFCs in that package will be generated to the web root-relative path '/myapp/components/beans'.

CFCs: CFCOMPONENT stubs will be generated for all objects in the UML diagram. The name of each component corresponds to the object name given in the UML diagram. Any text included in the Poseidon 'documentation' tab for that object will be inserted into the 'hint' attribute of the CFCOMPONENT tag.

CFC Init Methods: Init methods are ignored when generating test methods in the unit test files. Init methods should be given a return type matching the CFC type.

CFC Methods: CFFUNCTION stubs will be generated for all methods in the UML diagram. The 'returnType' attribute will be populated with the return type specified in the UML diagram (including concrete CFCs, abstract CFCs, or interfaces). Any text included in the Poseidon 'documentation' tab for that method will either be inserted into the 'hint' attribute of the CFFUNCTION tag or placed into the body of the CFFUNCTION tag set as a CFML comment. This option is set on the Stub Generator configuration screen.

CFC Method Arguments: CFARGUMENT tags will be generated for all method parameters specified in the UML diagram (including concrete CFCs, abstract CFCs, or interfaces). Any text included in the Poseidon 'documentation' tab for that parameter will be inserted into the 'hint' attribute of the CFARGUMENT tag.

Getter and Setter Generation: This option is set on the Stub Generator configuration screen. If a method starts with "get", the generator will add in code to return an instance variable with the name of the method. i.e., if you have a UML method called "getName", the generator will add the code <cfreturn variables.instance.name />. Along the same line, if a method starts with "set" and has one argument, the generator will add in code to set the variable. i.e., if you have a UML method called "setName" with a single argument called "name", the generator will add the code <cfset variables.instance.name = arguments.name />. I realize this won't always be correct, but I figured it wouldn't hurt much since it does eliminate a lot of mundane typing and if it incorrectly adds code for a method that you didn't really mean to be a getter or setter, it's very easy to delete that code. 

Abstract Methods: Abstract methods are generated with a body containing a CFTHROW tag which specifies that the method is abstract and must be overridden by a subclass. Also, abstract methods are ignored when generating test methods in the unit test files.

Inheritance: Inheritance is included in the generated CFCs by applying the 'extends' attribute of CFCOMPONENT or CFINTERFACE.

Interfaces: If you incorporate interfaces in your UML, CFINTERFACE files will be created for each interface. If an object in your UML implements one or more interfaces, those interfaces will be added to the 'implements' attribute of the generated CFCOMPONENT tag as a comma-delimited list. If an interface extends one or more interfaces, those interfaces wil be added to the 'extends' attribute of the generated CFINTERFACE tag as a comma-delimited list. Any text included in the Poseidon 'documentation' tab for that interface will be inserted into the 'hint' attribute of the CFINTERFACE tag. Note that as of this writing, interfaces are only supported by ColdFusion 8. 

Composition and Aggregation: The tool does detect aggregation or composition between components and inserts a custom attribute to the CFCOMPONENT tag called "aggregates" and lists aggregated components in a comma-delimited list. This option is set on the Stub Generator configuration screen. This isn't used by ColdFusion, but in the event that I create a tool that can read CFCs and generate the UML diagram, this attribute would allow the UML to be aware of composition and aggregation in your CFCs. Other than this, I was not sure how to actually use this information. This is because any composed objects have to be passed into the object either in the constructor or a setter. I really can't try to generate these automatically since I would have no way of knowing whether to use constructor or setter injection. If anyone has any other ideas on how knowing about composition might be helpful in the generated code please let me know. I suppose I could have it generate a hint or comment in the CFC documenting the relationships?

Unit Tests: Unit test methods will be created for any public methods in the corresponding CFCs. Unit test files will be created in a 'test' subdirectory of each package, and will contain tests for the CFCs in that package. 

Test Suites: CFCUnit/CFUnit test runners, ColdSpring XML, and ANT build XML files will be created in a 'testsuite' subdirectory of the directory where the XMI file used to generate the CFCs was read from.

Updated 11/14/07:
The tool now supports generating private and public properties if you define these in the UML. Public properties are set in the THIS scope, and associated cfproperty tags are created. The tool also supports existing CFCs now. It will not overwrite existing components, but will still generate the other files (unit tests, ColdSpring, etc.).

You can also set up hierarchies in the tool to define paths to CFCs that are outside of your project (for example, Transfer), and mark a CFC as "active" in the UML tool to instruct the generator to use this path as opposed to the root path you set up in the package hierarchy. You must set up the alternate package path and set the base package for the alternate packages as "root" in the UML tool. In this way, the generator will know the proper paths to the external components for typing and ColdSpring path creation. Examples of this setup are included in the bookstore2.zuml/bookstore2.xmi files.





-----------------------------------------------------


This file and related examples are available at my blog at www.briankotek.com/blog.