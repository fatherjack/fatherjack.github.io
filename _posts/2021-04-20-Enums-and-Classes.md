# About enums and classes

## So what's an enum? 

A distinct type that has a set of named constants.
An enumerator is a data type that has preset values (constants). When we set a variable to an enum value it will always be one of our predefined values

## How do we create an Enum then?

we define an enum a bit like a hash table of values we want to work with, except there is no Name part, its just a set of values.

````powershell
PS> enum Animals {
    Cat
    Dog
    Fish
    Tiger
}
````

## How do we reference an enum once we have created one then?

You can reference an enumerator with ```[``` and ```]```

```powershell
PS> [Animals]
```

![image](https://user-images.githubusercontent.com/2597535/113761216-1c4c5400-970f-11eb-9b90-997b5fc453b6.png)

```powershell
# does that look familiar?
PS> (get-date).dayofweek
PS> (get-date).dayofweek.gettype()
```

![image](https://user-images.githubusercontent.com/2597535/113761336-3dad4000-970f-11eb-92c6-003ed698fac0.png)


## we can get a specific item from the enumerator with an index value

```powershell
PS> [Animals].GetEnumName(2)
```

![image](https://user-images.githubusercontent.com/2597535/113762255-4fdbae00-9710-11eb-80ce-dd111fc55c7b.png)

# Using enums as values

Setting a variable to a value and setting a variable to an enum are similar in syntax but subtley different in what implications there are. The first 
results in a string variable type whereas the other results in a type that is our enum.

```powershell
$var1 = 'fish'
[animals]$Var2 = [Animals]::Fish

$Var1.gettype().fullname
$Var2.gettype().fullname
```

![image](https://user-images.githubusercontent.com/2597535/113762717-cbd5f600-9710-11eb-9d23-e3eb676c807a.png)

How do these two variables compare?

```powershell
PS> ($var1 -eq $var2)
```

![image](https://user-images.githubusercontent.com/2597535/113769368-d4322f00-9718-11eb-81db-dbb02d451d6c.png)

We can very simply convert our Animals type variable into a string with its built-in ToString() method

```powershell
PS> $var2.tostring()
```

Strange things happen when we add a second value to our [Animals] type variable though

```powershell
PS> $var2 += [Animals]::tiger
PS> $var2
```

![image](https://user-images.githubusercontent.com/2597535/113770982-bd8cd780-971a-11eb-9985-ad06c4bed7f1.png)

The value of our variable has become the sum of the enum object values (Fish is 2 and Tige is 3 in our original declaration of the enum. However, we cant get those values back out very simply. These lines simply return ```False```.

```powershell
PS> $var2 -eq 'fish'
PS> $var2 -eq 'tiger'
```
![image](https://user-images.githubusercontent.com/2597535/113771465-4dcb1c80-971b-11eb-8566-60fc3b46ae1c.png)

Trying other comparisons is similarly doomed

```powershell
PS> $var2 -like 'tiger'
PS> $var2 -eq 'fish, tiger'
PS> $var2
```

![image](https://user-images.githubusercontent.com/2597535/113771718-a6021e80-971b-11eb-9080-ffa872b97234.png)

The value, 5, is also useless to us as both 2+3 and 1+4 could be combinations of values that make up our current value of 5

If we want to work with more than one value in an enum then we need to declare it with the ```[Flags()]``` designation. Looking at the different attributes that a file can have, we could define an enum as follows

```powershell
[Flags()] enum FileAttributes {
    Archive = 1
    Compressed = 2
    Device = 4
    Directory = 8
    Encrypted = 16
    Hidden = 32
}
```
Once that is done we could define ```$file1``` as 

```powershell
[FileAttributes]$file1 = [FileAttributes]::Archive
[FileAttributes]$file1 +=[FileAttributes]::Compressed
[FileAttributes]$file1 +=  [FileAttributes]::Device
```
Having done this, we can see the attributes of our imaginary file with this line

```powershell
"file1 attributes are: $file1"
```

![image](https://user-images.githubusercontent.com/2597535/113772356-83bcd080-971c-11eb-94d9-47c6deb76acd.png)

Equally, we can use the sum of our selected attribute values to set our variable. Encrypted, Directory, and Device are 16 + 8 + 4 respectively, referencing our declaration above. This totals 28 so we can set our $file2 variable as follows

```powershell
[FileAttributes]$file2 = [FileAttributes]28  
"file2 attributes are: $file2"
```
![image](https://user-images.githubusercontent.com/2597535/113772691-ee6e0c00-971c-11eb-8576-4cfae890e255.png)

####################### Classes ##################################


## So what's a class? 

We can consider a PowerShell class to be a blueprint of an object that defines what type of thing it is and how it will behave. For this functionality you will need to be working on PowerShell version 5. We can define our class with syntax layout that is quite similar to the way a function is declared.

```powershell
PS> Class Television {
    [string]$Manufacturer
    [string]$Model
    [int]$Screensize_CM
    [int]$Channel
}
```

With that done, when we are ready in our code we can then declare a variable to have a datatype of our class rather than one of the more common datatypes such as INT or STRING etc and when we inspect that variable we can see the properties that we wanted for our object

```powershell
PS> $TV = [Television]::new()

$TV
```

![image](https://user-images.githubusercontent.com/2597535/114382822-bdf6f980-9b84-11eb-80e7-3cdef55d22b5.png)

Setting values for these properties is very simple, we 'dot reference' the property of our variables and assign the value we need

```powershell
PS> $TV.Manufacturer = 'Sony'
$tv.Model = "TV123"
```

![image](https://user-images.githubusercontent.com/2597535/114383114-162dfb80-9b85-11eb-859b-7e5f24391fe5.png)

With this example Television class some properties are static, the size and manufacturer are not going to change over time but to make things a bit more realisticit would be good if properties like the Channel value can be set and changed at will. If we want to represent this in our code then we can declare a method to do what we need. Within our class defintion we can provide logic for setting the channel property and also to instruct the object to increment or decrement the channel property as if we were pressing the channel up / down button on the remote control. In terms of retrieving property values We can also add a method that will give a string output which describe the object at that point in time - the ToString method that we see on other objects. Let's do this with a new class - Television2.

```powershell
PS> Class Television2 {
    [string]$Manufacturer
    [string]$Model
    [int]$Screensize_CM
    [int]$Channel

    [void]SetChannel([int]$chan) {
        $this.Channel = $chan
    }

    [int]ChannelUp() {
        $this.Channel ++
        return ($this.Channel)
    }
    
    [int]ChannelDown() {
        $this.Channel --
        return ($this.Channel)
    }

    [string]ToString() {
        return ("The {0} {1} has a screen size of {2}cm" -f $this.Manufacturer, $this.Model, $this.Screensize_CM)
    }
}

# define a new object of this new type of TV
$TV = [television2]::new()

# set some basic property values
$TV.Manufacturer = 'Samsung'
$tv.Model = "s46"
$tv.Screensize_CM = 120

# set the new Channel property.
# we dont get any response from this method as we set it to [void]
$tv.SetChannel(4)

# check out the object
$tv
```

![image](https://user-images.githubusercontent.com/2597535/114449043-39c86480-9bcc-11eb-9166-b9ae850ea044.png)

So we can see that our object has the values set by direct assignment (eg ```$TV.Manufacturer = 'Samsung'```) but also using the object method *SetChannel* that we created with the line ```$tv.SetChannel(4)```.

Now we will test the **ChannelUp** and **ChannelDown** methods.

```powershell
# test out the new methods
$tv.ChannelUp()
$tv.ChannelDown()
# because both methods return an INT we see the new Channel setting

# calling the ToString method gets all of the object properties in the string format that we specified 
$tv.ToString()
```

The ChannelUp and ChannelDown methods were written to do two things - the first (*$this.Channel ++*) is to change the channel up or down by one and then the second (*return ($this.Channel)*) is to return the new channel value.
![image](https://user-images.githubusercontent.com/2597535/114449348-8c098580-9bcc-11eb-9a02-de216ff77c75.png)



``` # something for the reader to do: add a Volume property and methods to control the TV volume selection```


## Classes can have properties that are themselves classes, or Enums

If we create an enum called TVChannel that represents some of the TV channels here in the UK,  we can then use that in a class definition as the data type for the TVChannel property of our planned objects.

```powershell
enum TVChannel{
    BBC1 = 1
    BBC2 = 2
    ITV = 3
    Channel4 = 4
    Channel5 = 5
}

Class Television3 {
    [string]$Manufacturer
    [string]$Model
    [int]$Screensize_CM
    [TVChannel]$Channel

    [void]SetChannel([int]$chan) {
        $this.Channel = $chan
    }
    [int]ChannelUp() {
        $this.Channel ++
        return ($this.Channel)
    }    
    [int]ChannelDown() {
        $this.Channel --
        return ($this.Channel)
    }

    [string]ToString() {
        return ("The {0} {1} has a screen size of {2}cm. It's currently set to show channel {3}" -f $this.Manufacturer, $this.Model, $this.Screensize_CM, $this.Channel)
    }
}
```

## Great but what advantage does this give us?

If we do this then the way that our script objects act changes subtely. Using the enum and class defined above, let's create a new TV object and check out the object and it's .ToString() output.

```powershell
# define a new object of our largest type of TV
$TV = [television3]::new()
# set some basic property values
$TV.Manufacturer = 'Toshiba'
$tv.Model = "t200"
$tv.Screensize_CM = 200

# set the new Channel property - note we dont get any response from this method as we set it to [void]
$tv.SetChannel(4)

# check out the object
$tv
```
![image](https://user-images.githubusercontent.com/2597535/115143295-5c300700-a03e-11eb-80f8-7ac72da94692.png)

Instead of seeing the channel number as before, using the enum for the TVChannel gives us the channel name in output.

Enums have another benefit to us, if we are up to doing some comparisons using binary operators. They allow for compact storage of yes/no options. TVs have a lot of features nowadays so it could be really handy to keep all features in one property rather than adding hundreds of individual properties to our object. We need to set up an enum with each of our features first and the add a Features property to our class. 

```powershell
[flags()]enum TVFeature{
    HDMIInput = 1
    SmartTV = 2
    USB = 4
    TVGuide = 8
    WallMount = 16
    UHD = 32
}

Class Television4 {
    [string]$Manufacturer
    [string]$Model
    [TVFeature]$Features

    [string]ToString() {
        return ("The {0} {1} has these features: {2}" -f $this.Manufacturer, $this.Model, $this.Features)
    }
}
```

Notice the slight twist to the enum declaration - it has a ```[flags]``` prefix and the integers rise a double of the previous value.

```powershell
# define a new object of our latest type of TV
$TV = [television4]::new()
# set some basic property values
$TV.Manufacturer = 'Panasonic'
$tv.Model = "P9000"

# check out the object
$tv
```
![image](https://user-images.githubusercontent.com/2597535/115143746-0f99fb00-a041-11eb-97ac-ff1d19b6fe41.png)

```powershell
# so lets confirm we have HDMIInout for this TV
$tv.Features = [tvfeature]::HDMIInput

$tv
```
![image](https://user-images.githubusercontent.com/2597535/115143759-1e80ad80-a041-11eb-8b9d-6364c0522bca.png)

```powershell
# but what if we also have one of the other features as well ?
$tv.Features += [tvfeature]::UHD

$tv
```
![image](https://user-images.githubusercontent.com/2597535/115143775-35270480-a041-11eb-9ecf-30a8cb2fbceb.png)

```powershell
# calling the ToString method gets all of the object properties in the string format that we specified 
$tv.ToString()
```
![image](https://user-images.githubusercontent.com/2597535/115143782-45d77a80-a041-11eb-977b-31626bbae718.png)

It's pretty trivial to add or remove features to our objects using ```+=``` and ```-=```.
```powershell
# adding and removing features is simple with += and -=
$tv.Features += [TVFeature]::SmartTV
$tv.Features += [TVFeature]::USB
$tv.Features -= [TVFeature]::HDMIInput

# check out our TV object now
$tv; $tv.ToString()
```
![image](https://user-images.githubusercontent.com/2597535/115143864-95b64180-a041-11eb-910e-6f6f72e3a3c7.png)
![image](https://user-images.githubusercontent.com/2597535/115143886-a070d680-a041-11eb-8377-b727a48763bb.png)

## Does this help us tell what features a TV has?

Great question - yes it does! We need to start using lesser known bitwise comparison operators ```-band``` and ```-bnot``` to do this. We will take a look at ```-band``` first this does a bitwise comparison on two objects and returns true if the comparison is equivalent. As help says 

> "In a bitwise AND operation, the resulting bit is set to 1 only when both input bits are 1."

```powershell
# using -band to find features
$tv.Features -band [tvfeature]::HDMIInput

$tv.Features -band [tvfeature]::SmartTV
```
![image](https://user-images.githubusercontent.com/2597535/115144868-e3817880-a046-11eb-9259-b17290ee3760.png)

```powershell
if ($tv.Features -band [tvfeature]::SmartTV) {
    "This TV has SmartTV feature"
}
```
![image](https://user-images.githubusercontent.com/2597535/115144883-f3995800-a046-11eb-8b73-ecc6bba6d2a6.png)

```powershell
switch ($tv.Features) {
    { $_ -band [TVFeature]::HDMIInput } { "We have HDMIInput feature" }
    { $_ -band [tvfeature]::SmartTV } { "We have SmartTV feature" }
    { $_ -band [tvfeature]::USB } { "We have USB feature" }
    { $_ -band [tvfeature]::TVGuide } { "We have TVGuide feature" }
    { $_ -band [tvfeature]::WallMount } { "We have WallMount feature" }
    { $_ -band [tvfeature]::UHD } { "We have UHD feature" }
}
```
Making use of the switch operators feature of evaluating all comparisons regardless of making a match in any we can get this output
![image](https://user-images.githubusercontent.com/2597535/115144938-2e02f500-a047-11eb-82c9-9592f410877c.png)

With some crafty bitwise comparisons we can use our script to check which TVs hve what features and are therefore ones to consider buying
```powershell
# want to buy a TV with 'this' or 'that' feature?
# is we want a TV that has UHD and TVGuide or SmartTV
if (($tv.Features -band [tvfeature]::UHD) -band (($tv.Features -band [tvfeature]::TVGuide) -bor ($tv.Features -band [tvfeature]::SmartTV))) {
    Write-Output ("The {0} TV has the features you want" -f ($tv.Manufacturer))
}
else {
    Write-Output ("Sadly the {0} TV doesnt have the features you want" -f ($tv.Manufacturer))
}
```
![image](https://user-images.githubusercontent.com/2597535/115145003-791d0800-a047-11eb-9b9b-5cc5c4084be9.png)

With all the features in one property it is very simple to add a lot of them in one update to the object
```powershell
# want to add multiple features at once?
# Lets get a new TV

# define a new object of our lagest type of TV
$TV = [television4]::new()
# set some basic property values
$TV.Manufacturer = 'LG'
$TV.Model = "HAL100"

# what do we get if we add enums together?
[int]([TVFeature]::SmartTV + [TVFeature]::UHD)
```
![image](https://user-images.githubusercontent.com/2597535/115145061-b5e8ff00-a047-11eb-98c0-bbc88b0047f0.png)
```powershell
#can we set the TV Features like that?
$tv.Features = 34

$TV.Features
```
![image](https://user-images.githubusercontent.com/2597535/115145086-d3b66400-a047-11eb-90cf-0722764fa6c1.png)

So there we have it, Enums, Classes and Bitwise operators. I hope you found something in this thats useful to you.
