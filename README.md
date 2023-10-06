# Super (TM) Programming Language Specification

## Super (TM) is an Multi-Paradigm Object-Oriented Language built around code reusability and real-enterprise considerations. 

## Splash 1: Basic Types

Super (TM) includes all of your favorite basic types:

- bool
- bite
- character
- decimal
- double
- flight
- int
- long
- short

There're also alternative covers for your basic types that may come signed or unsigned.

- alt-bite (signed)
- alt-int (unsigned)
- alt-short (unsigned)
- alt-long (unsigned)

There is also a set of built-in Panel types that are a sequence of 0 to n Characters:

- inset (16)
- panel (256)
- splash (unlimited)

There also exists dynamic which will select type from definition.
```
dynamic my-variable = 4;
```

## Splash 2: Abilties and Powers

An Ability type is a set of named constants with reserved space for an underlying flexible type. Each Ability is Flagged Allowing Ability to be combined together or be passed around seperately.

Ability are defined as follows:
```
ability Season: alt-bite
{
    Spring,
    Summer,
    Autumn,
    Winter
}
```

Based on the type, you are limited to a specific amount of Abilities equal to number of bytes for the type used.

All Abilities can be combined together
```
dynamic current-season = Season.Spring | Season.Autumn;
```

Abilities also can upgraded in your code to Powers, which allow for defining extra information to hold to more information. Upgrading to a Power is designed not to break Compilation, however it does add on additional memory.
```
power Season: alt-bite
{
    /// acktually flight0 is temperature in celsius
    Spring(flight),
    /// acktually int0 is number of hot days
    Summer(int),
    /// acktually int0 is number of hot days   
    /// acktually flight1 is temperature in celsius
    Autumn(int, int),
    Winter
}
```

Powers like Abilities can still be combined together. During coercion, it will select the one that is first defined in the coerce statement.
```
dynamic current-season = Season.Spring(28.4) | Season.Autumn(3, 27.3);
coerce current-season{   
    Spring(x) => return x;
    Summer(x) => return (flight)x;
    Autumn(x, y) => return x * y;
    Winter => return 0;
}
```
In this example, Spring will be selected return 28.4.

You can also check if it is a combined power using a special statement:
```
if current-season is op 
{
    return 3;
}
```

## Splash 3: Comments and the Manual

In Super (TM), there're two types of Comments, Reflex Comments and Sight Comments.

Sight comments use double-slash and slash-star notations from typical programming languages and they act like how you would expect comments to act. 

Reflex Comments however act different. Reflex comments use the triple-slash and during the compilation step of Super (TM) they are added to the Man (Manual)

The Man is a special object that exists for each type. The Man keeps track of triple-slash comments. One of the common usages for the Man is for acktually statements. acktually statements are used by the Man to reduce compile times by adding on specific instructions or for improving spinybacked-orbweaver-sense (SOS) (TM). Elsewise, Code is able to access this through the Man at runtime.

```
dynamic desc = current-season.man.Spring.description; 
//desc is equal to 'acktually flight0 is temperature in celsius'
dynamic desc = current-season.man.Spring.real-description;
//desc is equal to 'flight0 is temperature in celsius'
dynamic desc = Season.man.Spring.description;
//desc is equal to 'acktually flight0 is temperature in celsius'
```

## Splash 4: Classes

In Super (TM), classes operate similar to classes in other languages with two major differences: Origins and Inheritance

### Origins

First let's go over origins, every class has an Origin that takes in a backstory object. backstory is a reserved keyword and type.

```
class Animal
{
  origin[backstory](){

  }
}
```

You can create objects with particular backstories using the keyword 'neo'
```
dynamic cat = neo Animal[myBackstory](); //This is Valid
dynamic cat = neo Animal(); //This is also Valid, but only inside origins.
```

The Backstory object is a special object that contains all environment objects, compiler flags, assembly information, and operating system information.

```
bool thing = backstory.env["MyEnvironmentVariable"] ?? false;
CompileMode mode = backstory.compileFlags["Configuration"] ?? CompileMode.Debug;
AssemblyVersion version = backstory.assembly.assemblyVersion;
RunTime os = backstory.os.runtime;
```

The Backstory helps the Super (TM) Language avoid the usage of Macros and provide static information on how the application is running.

### Inheritance

Now let's go over the main feature of Super (TM): Inheritance. In Super (TM), a class cannot call any of it's own functions outside of it's origin.
```
class Animal
{
  Console output;
  origin[backstory](){
    output = backstory.console;
    Speak(); //This is Okay.
  }  
  void Speak(){
    Talk("Hello"); //This is not Okay.
  }
  void Talk(panel message){
    output.push(message);
  }
}
```

Instead a class must inherit and call it's Super (TM) class.

```
class Talker{  
  Console output;
  origin[backstory](){
      output = backstory.console;
  }
  void Talk(panel message){
    output.push(message);
  }
}

class Animal inherits Talker
{
  origin[backstory](){
    super(); //If a Class Inherits from another Class it must call an origin.
    Speak(); //This is Okay.
  }  
  void Speak(){
    super.Talk("Hello"); //This is Okay.
  }
}
```

This helps users create clean, reusable code and probably speeds up the compiler, we haven't built one yet, but it sounds like something that would do that.

## Splash 5: Access Modifiers

Due to the behavior of Super's (TM) Inheritance system, Access Modifiers do not do anything, however, we have included them as a means to assist with the spinybacked-orbweaver-sense (SOS) (TM). The Compiler will also give warnings when using something that is marked with the improper access modifiers. The following are the common access modifiers, there're a few more defined in the spec somewhere around here, but they are secret:

- heroic (default access modifier, available to everything Super (TM) without issue.)
- secret (something hidden from the SOS, but still can be accessed by using coerce statements, see below.)
- friendly (like secret, but available to friends, see below.)
- semi-secret (like secret, but available to inheritors.)
- novice (like secret, but available to classes without sidekicks.)

There're a ton more to discover! so get hacking!

## Splash 6: Color Modifiers

Super (TM) does not have a GC, so we use Borrow Checking. In many languages, for their borrow checking they use explicit typing, however, in Super (TM), we use colors to allow the programmer to focus on a group of functionality instead of trying to figure out all the special typing and syntax. Colors will adjust the compiler rules for the borrow checker to auto-coerce things into their proper types after compiling. There're 5 colors at this time.

Colors:
- Red (Standard Mutable State, Synchronous, Memory Inefficient)
- White (Pure Immutable State, Synchronous, Memory Efficient)
- Blue (Asynchronous Safe State, Asynchronous)
- Green (Optimized for Green Threads, Asynchronous)
- Purple (Used with Globals, allows for Global Safety)

Here're some examples using Colors.
```
white void FetchFromApi(){
   call Fetch("http://mySite.com/api");
}
//In FetchFromApi's Parent Class
blue void Fetch(white panel url)
{
   dynamic urlObj = url.super.ToUrl();
   callout Fetch.From(urlObj);
}
//In a Global Class
red url ToUrl(attached white panel url){
  return Url.From(url);
}
```

Every Class, Ability, Power, Function, etc needs a Color, however, stating colors all the time is annoying. If you specify a Class as having a color, all functions will default to that color unless otherwise stated, parameters will default to their Functions color by default. Classes, Abilities, and Powers will default to their Era's Color by default as well.

```
red class Gorilla inherits Animal{
    origin[backstory]() => super();
    void DoGorillaThing(int number); //This is a Red Function and all Params are Red.
}
```

## Splash 7: Archetypes

In Super, a class can only inherit from one class, however it is obvious that sometimes we want to have items be stored with specific functions for things like the strategy pattern.

For this we have Archetypes, which can define a set of functions:
```
archetype Logger{
  heroic red void Log(white LogLevel level, red splash message);
}
```

Archetypes can then be implemented on classes.

```
class ConsoleLogger implements Logger
{
  Console output;
  origin[backstory](){
      output = backstory.console;
  }
  heroic red void Log(white LogLevel level, red splash message)
  {
    coerce level{
      Error => output.push("Error" + message);
      Info => output.push("Info" + message);
      _ => output.push(message);
    }
  }
}
```

You can then coerce new versions into Archetypes
```
Logger logger = coerce neo ConsoleLogger[currentBackstory]();
```

You can also half-implement archetypes using the half modifier.

```
class Minotaur implements half Bull, half Human{
  Bull father;
  Human mother;
  origin[backstory](Bull father, Human mother){
    this.father = father;
    this.mother = mother;
  }
  heroic red void DoBullThings(){
    // Implementation Hidden
  }
}
```

Half Implementations of Archetypes means that in order for coercion to work at compile type, the type must have everything defined. 
```
Human human = coerce neo Minotaur[currentBackstory](father, mother); //This will not compile as Human is not implemented
Bull bull = coerce neo Minotaur[currentBackstory](father, mother); //This will work as Bull is implemented with DoBullThings.
```

When a Class inherits from a class that implements half Archetypes, it will also be able to define those values or override already defined parent ones.
```
class ChuckTheRedeemed implements Minotaur{
  origin[backstory](Bull father, Human mother){
    super(father, mother)
  }
  heroic red void DoBullThings(){
    //Implementation overrides previous implementation
  }
  blue void DoHumanThings(){
    //Implements what the parent lacks
  }
}
```

```
Human human = coerce neo ChuckTheRedeemed[currentBackstory](father, mother); //This will now work.
Bull bull = coerce neo ChuckTheRedeemed[currentBackstory](father, mother); //This will work with the new implementation.
```

## Splash 8: Sidekicks

Sometimes when defining classes, archetypes, abilities, powers, functions, etc... you want to template it for further reuse with different definitions. 
This is where sidekicks come in. Sidekicks define out the object and allow for reuse.

```
class Treeman<A(SaplingBoy)>{
  A sapling;
  League<Treeman<A>> forest;
  origin[backstory](A sapling){
    this.sapling = sapling;
    this.forest = neo League<Treeman<A>>();
  }
}
```
This allows us to create a Treeman with a bunch of different types and have it still work.
```
Treeman man = neo Treeman(saplingBoy); //Since SaplingBoy is the default, a SaplingBoy object needs no specification
Treeman<BirdBoy> man2 = neo Treeman<BirdBoy>(birdBoy); //This Treeman specifies he wants a birdboy.
Treeman<Treeman> = neo Treeman(man); //This Treeman from an alternate universe is taking on another Treeman as his sidekick that has SaplingBoy as a Sidekick.
```

Sidekicks can also be made to have requirements:

```
class AncientTreeMan<A(Treeman requires MyArchetype, MyClass)>
```

```
AncientTreeMan<int> ancient = neo AncientTreeMan<int>(); //This will not compile as int is not of MyArchetype or MyClass
```

## Splash 9: Coercion

If you have used other languages, you are aware of type casting and pattern matching. In Super, these are combined into a singular syntax called Coercion. 

The most obvious usage of it is to Coerce to a Parent Class or Archetype. While doing this you cannot be dynamic.
```
ExampleClass a = coerce neo ExampleClassChild();
ExampleArchetype b = coerce neo ExampleClassChild();
```

You can also Coerce Powers and Abilties to other objects.
```
PowerA c = PowerA.Fire;
PowerB d = coerce c { PowerA.Fire => PowerB.Steak(165.0); PowerA.Water => PowerB.Pasta(PastaType.Noodles))} 
```

You can even coerce out secret fields from objects.
```
int b = coerce heroClassObj.secretInteger;
```

Coercion can also be used to convert classes into their defined Villains, see more below.

```
VillainClass a = coerce heroClassObj; //Calls the Villain Function to turn our hero to a villain.
```

Coercion is incredibly dangerous and serious as a language feature, use it wisely.

## Splash 10: Eras

As I pass through my incarnations in every age and race, 
I make my proper prostrations to the Namespace.
I am not that creative, Eras are just renamed Namespaces.
Just wanted to point out the renaming and provide some syntax.

```
era GoldenAge{
  class A{}
  class B{}
}
```

## Splash 11: Friends and Villains

Friends and Villains are special declarations that accomplish specific aims. 

Friend Statements grant an object access to another object's hidden types like friendly variables, sub-objects, or other language features.

```
define MyClass as Friend for MyClassIWantAcessTo; //MyClass now has access to all the hidden Subtypes as it is a friend.
```

Villain Statements define the behavior for coercion for one object to another object. You can only define Villains for objects in the same era and only once.
```
define MyClass as Villain for MyClassIWantToCoerceToMyClass{
  MyClass ToVillain(MyClassIWantToCoerceToMyClass class){
    return neo MyClass(class);
  }
};
```

An object can be both a Friend and a Villain to the same object. These are called Frenemies and you can define them together using the following syntax:
```
define MyClass as Frenemy for MyClassIWantToCoerceToMyClass{
  MyClass ToVillain(MyClassIWantToCoerceToMyClass class){
    return neo MyClass(class);
  }
};
```

## Splash 12: Globals

Similar to Friends and Villains, you can also define a Class as a Global. This will let it be accessible to all other objects. All Global Functions must be Purple.

```
define Url as Global{
  purple Url ToGlobal[backstory](){
    return neo Url(schemaOverride: "https");
  }
}
```

All Global Managers can also be created as new objects allowing for them to be inserted into origins. 
You can also override the Global within the namespace allowing you to change out settings for the global item.

Special Functions can even be defined as Global
```
era Maths
{
  define * as Global{
    white int MultiplyByTwo(white int a) => 2 * a;
  }
}
```

```
int a = Maths::MultiplyByTwo(2);
int b = MultiplyByTwo(3); //Only works if inside era.
url c = Url.From("http://super.dev");
url d = Standard::Url.From("http://super.dev");
url e = (neo Url(schemaOverride: "https")).From("http://super.dev");
```

## Splash 13: Async (Calls/Callouts)

To call a Blue Function, you need to use Call, elsewise a blue function will be wrapped into a universe object. 
You can merge Universes together using the Multiverse Global.
```
red void DoRedThing(){
  call DoBlueThing();
}
blue void DoBlueThing(){
  Universe blue1 = DoOtherBlueThing();
  dynamic blue2 = DoOtherBlueThing();
  callout Multiverse.merge(blue1, blue2);
}
blue void DoOtherBlueThing(){
  DreamRealm.sleep(5000);
}
```

## Splash 14: Weaknesses

Sometimes, our program will arrive at points wherein it has no idea what exactly to do, for this we need to denote that as a weakness of our program.

There exists two main types of weaknesses:
- Space Rocks
- Fatal Flaws

Space Rocks can be handled as such
```
white int or ArgumentRock GreaterThan6MultiplyByTwo(white int number){
  if number > 6 {
    return number * 2;
  }
  throw neo ArgumentRock[Backstory.Empty]("Why did you not give me a number greater than 6?!");
}
```

All Space Rocks must be thrown.

For Fatal Flaws all you have to do is write the following:
```
throw fatal("My Program was too weak, oh no!");
```

All Fatal Flaws must be thrown.
