# Why bother to use Set-Variable?

Something pretty early on in your scripting experience you will have encountered the assignment of a value to a variable. Most languages are 
pretty flexible in this and will allow some simple syntax like ```var = value``` to set a value that you want to reuse into memory and you access
it again my making reference to the variable name that you chose. PowerShell is no different, running the command ```$Var = "Monday"``` will 
set the value *Monday* into memory and it can be recalled and used at will by using the variable name *$Var1*.
