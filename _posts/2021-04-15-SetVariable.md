# Why bother to use Set-Variable?

Something pretty early on in your scripting experience you will have encountered is the assignment of a value to a variable. Most languages are pretty flexible in this and will allow some simple syntax like ```var = value```. This sets a value that you want to reuse into memory and you access it again my making reference to the variable name that you chose. PowerShell is no different, running the command ```$Var = "Monday"``` will set the value *Monday* into memory and it can be recalled and used whenever you need it by using the variable name *$Var1* in your script. This is pretty neat, compact, and simple to understand.

With this being so simple and probably suitable for most needs many people dont know that there are functions that work with variables. 
