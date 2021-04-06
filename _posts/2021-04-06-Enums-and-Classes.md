# About enums and classes

## So what's an enum? 

A distinct type that has a set of named constants.
An enumerator is a data type that has preset values (constants). When we set a variable to an enum value it will always be one of our predefined values

## How do we create an Enum then?

we define an enum a bit like a hash table of values we want to work with

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


($var1 -eq $var2)

$var2.tostring()

$var2 += [Animals]::tiger

$var2 -eq 'fish'
$var2 -eq 'tiger'

$var2 -like 'tiger'

$var2 -eq 'fish, tiger'
$var2


[Flags()] enum FileAttributes {
    Archive = 1
    Compressed = 2
    Device = 4
    Directory = 8
    Encrypted = 16
    Hidden = 32
}

[FileAttributes]$file1 = [FileAttributes]::Archive
[FileAttributes]$file1 +=[FileAttributes]::Compressed
[FileAttributes]$file1 +=  [FileAttributes]::Device
"file1 attributes are: $file1"

[FileAttributes]$file2 = [FileAttributes]28 ## => 16 + 8 + 4
"file2 attributes are: $file2"

####################### Classes ##################################

# PowerShell Classes

#requires -version 5

<#
 "a blueprint of an object used to defines a type of object"
#>
Class Television {
    [string]$Manufacturer
    [string]$Model
    [int]$Screensize_CM
    [int]$Channel
}

$TV = [Television]::new()

$TV

$TV.Manufacturer = 'Sony'
$tv.Model = "TV123"


Class Television2 {
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

# test out the new methods
$tv.ChannelUp()
$tv.ChannelDown()
# because both methods return an INT we see the new Channel setting

$tv.ChannelDown

# calling the ToString method gets all of the object properties in the string format that we specified 
$tv.ToString()

################
# something for the reader to do: add a Volume property and methods to control the TV volume selection
################

# Classes can have properties that are themselves classes, or Enums
enum TVChannel{
    BBC1 = 1
    BBC2 = 2
    ITV = 3
    Channel4 = 4
    Channel5 = 5
}

[system.enum]::GetNames([TVChannel])

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
        return ("The {0} {1} has a screen size of {2}cm" -f $this.Manufacturer, $this.Model, $this.Screensize_CM)
    }
}


# define a new object of our lagest type of TV
$TV = [television3]::new()
# set some basic property values
$TV.Manufacturer = 'Toshiba'
$tv.Model = "t200"
$tv.Screensize_CM = 200

# set the new Channel property.
# we dont get any response from this method as we set it to [void]
$tv.SetChannel(4)

# check out the object
$tv

# test out the new methods
$tv.ChannelUp()
$tv.ChannelDown()
# because both  methods return an INT we see the new Channel setting

$tv.ChannelDown

# calling the ToString method gets all of the object properties in the string format that we specified 
$tv.ToString()


# Enums allow for compact storage of yes/no options
[flags()]enum TVFeature{
    HDMIInput = 1
    SmartTV = 2
    USB = 4
    TVGuide = 8
    WallMount = 16
    UHD = 32
}

[system.enum]::GetNames([TVFeature])

Class Television4 {
    [string]$Manufacturer
    [string]$Model
    [TVFeature]$Features

    [string]ToString() {
        return ("The {0} {1} has these features: {2}" -f $this.Manufacturer, $this.Model, $this.Features)
    }
}


# define a new object of our latest type of TV
$TV = [television4]::new()
# set some basic property values
$TV.Manufacturer = 'Panasonic'
$tv.Model = "P9000"

# check out the object
$tv

# so lets confirm we have HDMIInout for this TV
$tv.Features = [tvfeature]::HDMIInput

$tv

# but what if we also have one of the other features as well ?
$tv.Features += [tvfeature]::UHD

$tv

# calling the ToString method gets all of the object properties in the string format that we specified 
$tv.ToString()

# adding and removing features is simple with += and -=
$tv.Features += [TVFeature]::SmartTV
$tv.Features += [TVFeature]::USB
$tv.Features -= [TVFeature]::HDMIInput

# check out our TV object now
$tv
$tv.ToString()

# so, if we have a TV, how do we tell what features it has and how can we control script flow?

# by using Bitwise operators
# usinf -band to find features
# "In a bitwise AND operation, the resulting bit is set to 1 only when both input bits are 1.""
$tv.Features -band [tvfeature]::HDMIInput

$tv.Features -band [tvfeature]::SmartTV

$tv.Features -bnot [TVFeature]::USB

if ($tv.Features -band [tvfeature]::SmartTV) {
    "This TV has SmartTV feature"
}

switch ($tv.Features) {
    { $_ -band [TVFeature]::HDMIInput } { "We have HDMIInput feature" }
    { $_ -band [tvfeature]::SmartTV } { "We have SmartTV feature" }
    { $_ -band [tvfeature]::USB } { "We have USB feature" }
    { $_ -band [tvfeature]::TVGuide } { "We have TVGuide feature" }
    { $_ -band [tvfeature]::WallMount } { "We have WallMount feature" }
    { $_ -band [tvfeature]::UHD } { "We have UHD feature" }
}

# want to buy a TV with 'this' or 'that' feature?
# is we want a TV that has UHD and TVGuide or SmartTV
if (($tv.Features -band [tvfeature]::UHD) -band (($tv.Features -band [tvfeature]::TVGuide) -bor ($tv.Features -band [tvfeature]::SmartTV))) {
    Write-Output ("The {0} TV has the features you want" -f ($tv.Manufacturer))
}
else {
    Write-Output ("Sadly the {0} TV doesnt have the features you want" -f ($tv.Manufacturer))
}

# want to add multiple features at once?
# Lets get a new TV

# define a new object of our lagest type of TV
$TV = [television4]::new()
# set some basic property values
$TV.Manufacturer = 'LG'
$TV.Model = "HAL100"

# what do we get if we add enums together?
[int]([TVFeature]::SmartTV + [TVFeature]::UHD)

#can we set the TV Features like that?
$tv.Features = [int]([TVFeature]::SmartTV + [TVFeature]::UHD)

$TV.Features
