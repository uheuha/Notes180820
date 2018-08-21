
===180820 i updated/added this line in eheuha after <<forking it from jrgithubb; i also inserted a date 180820 in the next line below;
===180820 <<c#<<pass<<by<<reference<<by<<value
=== <<java<<pass<<by<<value only coz what is passed is the value of the reference:
https://stackoverflow.com/questions/40480/is-java-pass-by-reference-or-pass-by-value

Java is always pass-by-value. Unfortunately, they decided to call the location of an object a "reference".  When we pass the value of an object, we are passing the reference to it.  This is confusing to beginners.

It goes like this:

public static void main(String[] args) {
    Dog aDog = new Dog("Max");
    // we pass the object to foo
    foo(aDog);
    // aDog variable is still pointing to the "Max" dog when foo(...) returns
    aDog.getName().equals("Max"); // true
    aDog.getName().equals("Fifi"); // false 
}

public static void foo(Dog d) {
    d.getName().equals("Max"); // true
    // change d inside of foo() to point to a new Dog instance "Fifi"
    d = new Dog("Fifi");
    d.getName().equals("Fifi"); // true
}

In the example above aDog.getName() will still return "Max". The value aDog within main is not changed in the function foo with the Dog "Fifi" as the object reference is passed by value. If it were passed by reference, then the aDog.getName() in main would return "Fifi" after the call to foo.

Likewise:

public static void main(String[] args) {
    Dog aDog = new Dog("Max");
    foo(aDog);
    // when foo(...) returns, the name of the dog has been changed to "Fifi"
    aDog.getName().equals("Fifi"); // true
}

public static void foo(Dog d) {
    d.getName().equals("Max"); // true
    // this changes the name of d to be "Fifi"
    d.setName("Fifi");
}


In the above example, Fifi is the dog's name after call to foo(aDog) because the object's name was set inside of foo(...). Any operations that foo performs on d are such that, for all practical purposes, they are performed on aDog itself (except when d is changed to point to a different Dog instance like d = new Dog("Boxer")).


I just noticed you referenced my article.

The Java Spec says that everything in Java is pass-by-value. There is no such thing as "pass-by-reference" in Java.

The key to understanding this is that something like

Dog myDog;

is not a Dog; it's actually a pointer to a Dog.

What that means, is when you have

Dog myDog = new Dog("Rover");
foo(myDog);

you're essentially passing the address of the created Dog object to the foo method.

(I say essentially because Java pointers aren't direct addresses, but it's easiest to think of them that way)

Suppose the Dog object resides at memory address 42. This means we pass 42 to the method.

if the Method were defined as

public void foo(Dog someDog) {
    someDog.setName("Max");     // AAA
    someDog = new Dog("Fifi");  // BBB
    someDog.setName("Rowlf");   // CCC
}

let's look at what's happening.

the parameter someDog is set to the value 42

at line "AAA" someDog is followed to the Dog it points to (the Dog object at address 42)
that Dog (the one at address 42) is asked to change his name to Max
at line "BBB" a new Dog is created. Let's say he's at address 74
we assign the parameter someDog to 74 
at line "CCC" someDog is followed to the Dog it points to (the Dog object at address 74)
that Dog (the one at address 74) is asked to change his name to Rowlf
then, we return
Now let's think about what happens outside the method:

Did myDog change?

There's the key.

Keeping in mind that myDog is a pointer, and not an actual Dog, the answer is NO. myDog still has the value 42; it's still pointing to the original Dog (but note that because of line "AAA", its name is now "Max" - still the same Dog; myDog's value has not changed.)

It's perfectly valid to follow an address and change what's at the end of it; that does not change the variable, however.

Java works exactly like C. You can assign a pointer, pass the pointer to a method, follow the pointer in the method and change the data that was pointed to. However, you cannot change where that pointer points.


In C++, Ada, Pascal and other languages that support pass-by-reference, you can actually change the variable that was passed.

If Java had pass-by-reference semantics, the foo method we defined above would have changed where myDog was pointing when it assigned someDog on line BBB.

Think of reference parameters as being aliases for the variable passed in. When that alias is assigned, so is the variable that was passed in.


Java always passes arguments by value NOT by reference.

Let me explain this through an example: 

public class Main{
     public static void main(String[] args){
          Foo f = new Foo("f");
          changeReference(f); // It won't change the reference!
          modifyReference(f); // It will modify the object that the reference variable "f" refers to!
     }
     public static void changeReference(Foo a){
          Foo b = new Foo("b");
          a = b;
     }
     public static void modifyReference(Foo c){
          c.setAttribute("c");
     }
}

I will explain this in steps:

Declaring a reference named f of type Foo and assign it to a new object of type Foo with an attribute "f".

Foo f = new Foo("f");

.
.
.
.

This will give you some insights of how Java really works to the point that in your next discussion about Java passing by reference or passing by value you'll just smile :-)

Step one please erase from your mind that word that starts with 'p' "_ _ _ _ _ _ _", especially if you come from other programming languages. Java and 'p' cannot be written in the same book, forum, or even txt.

Step two remember that when you pass an Object into a method you're passing the Object reference and not the Object itself.

Student: Master, does this mean that Java is pass-by-reference?
Master: Grasshopper, No.
Now think of what an Object's reference/variable does/is:

A variable holds the bits that tell the JVM how to get to the referenced Object in memory (Heap).
When passing arguments to a method you ARE NOT passing the reference variable, but a copy of the bits in the reference variable. Something like this: 3bad086a. 3bad086a represents a way to get to the passed object.
So you're just passing 3bad086a that it's the value of the reference.
You're passing the value of the reference and not the reference itself (and not the object).
This value is actually COPIED and given to the method. 
In the following (please don't try to compile/execute this...):

1. Person person;
2. person = new Person("Tom");
3. changeName(person);
4.
5. //I didn't use Person person below as an argument to be nice
6. static void changeName(Person anotherReferenceToTheSamePersonObject) {
7.     anotherReferenceToTheSamePersonObject.setName("Jerry");
8. }

What happens?

The variable person is created in line #1 and it's null at the beginning.
A new Person Object is created in line #2, stored in memory, and the variable person is given the reference to the Person object. That is, its address. Let's say 3bad086a.
The variable person holding the address of the Object is passed to the function in line #3.
In line #4 you can listen to the sound of silence 
Check the comment on line #5
A method local variable -anotherReferenceToTheSamePersonObject- is created and then comes the magic in line #6: The variable/reference person is copied bit-by-bit and passed to anotherReferenceToTheSamePersonObject inside the function.
No new instances of Person are created.
Both "person" and "anotherReferenceToTheSamePersonObject" hold the same value of 3bad086a.
Don't try this but person==anotherReferenceToTheSamePersonObject would be true.
Both variables have IDENTICAL COPIES of the reference and they both refer to the same Person Object, the SAME Object on the Heap and NOT A COPY.
A picture is worth a thousand words:

