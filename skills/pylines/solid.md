# SOLID Guide

> ⚠️ **Warning:** This guide is almost ready, but it may still contain
> some rough edges.

-   [SOLID Guide](#solid-guide)
-   [Introduction and FAQ](#introduction-and-faq)
    -   [What is SOLID?](#what-is-solid)
    -   [Why SOLID?](#why-solid)
    -   [Why write our own guide when there are already so
        many?](#why-write-our-own-guide-when-there-are-already-so-many)
    -   [Classic problems with SOLID, or the "yes, but"
        situation](#classic-problems-with-solid-or-the-yes-but-situation)
    -   [Conventions and notes](#conventions-and-notes)
-   [SOLID Principles](#solid-principles)
    -   [Single Responsibility Principle
        (SRP)](#single-responsibility-principle-srp)
    -   [Open/Closed Principle (OCP)](#openclosed-principle-ocp)
        -   [Example](#example)
        -   [Summary](#summary)
    -   [Liskov Substitution Principle
        (LSP)](#liskov-substitution-principle-lsp)
    -   [Interface Segregation Principle
        (ISP)](#interface-segregation-principle-isp)
    -   [Dependency Inversion Principle
        (DIP)](#dependency-inversion-principle-dip)
        -   [Dependency Injection](#dependency-injection)
        -   [DI Containers](#di-containers)

This guide will help developers understand the SOLID principles and
apply them in real-world projects. We will walk through code examples,
show what problems can arise when these principles are violated, and
suggest ways to address them.

# Introduction and FAQ

### What is SOLID?

SOLID is a set of principles introduced by Robert Martin. Robert Martin
is a software consultant rather than a software developer, so his books
are not particularly easy for developers to read. Nevertheless, many
people in the industry consider them highly authoritative. There is also
an opinion that Robert Martin is an "infobusinessman." We will not draw
any conclusions about him, but we do consider his SOLID principles
useful.

### Why SOLID?

In our experience, the SOLID principles work well (that is, we have
validated this empirically for ourselves: we have applied them and, in
our opinion, they have proved useful):

-   Code is easier to understand.
-   Code is easier to test.
-   It is easier to onboard people onto the team.

Some disadvantages are that the code becomes a little more verbose and
there is slightly more of it.

### Why write our own guide when there are already so many?

Most guides on the internet suffer from several problems:

-   They present the principles dryly, without explaining their essence.
-   They repeat the same information over and over without adding
    anything new.
-   They ignore the original texts or quote them inaccurately.
-   After reading the guide, you still do not understand how to apply
    the principles in practice.
-   They contain useless text and claims such as "you must apply this,"
    "this is fundamental," "everyone should know this," "YOU JUST DON'T
    UNDERSTAND," and other vague statements such as "there are pitfalls
    everywhere" or "do it correctly; don't do it incorrectly."

That is why SOLID principles are so difficult to apply in practice, and
why almost everyone gives a vague answer when asked about them.

We are writing our own guide so that readers can actually apply the
principles in practice. To do that, you will need to read our guide and
follow our conventions.

### Classic problems with SOLID, or the "yes, but" situation

Almost every interview discussion---or discussion of SOLID in
general---often boils down to the following statement: "SOLID is
good/great/excellent/should be applied/is necessary for everyone/is
fundamental."

The next question we usually ask is: "And how have you implemented
SOLID?" This is where phrases that every one of our interviewers has
heard many times start to appear:

-   "I have an intuitive feel for SOLID."
-   "SOLID happens naturally if you use common sense."
-   "SOLID is obvious."
-   "SOLID is not applicable everywhere."
-   "We apply it where necessary" (and, obviously, do not apply it where
    it is not necessary).

In fact, the SOLID principles themselves are described in the 2003 book
*Agile Software Development: Principles, Patterns, and Practices*. They
take up about 30 pages and are complex enough that applying them
requires thought and reflection---certainly not merely "intuition,"
since you were not born with an innate sense of SOLID. We will gradually
explain where this supposed "intuition" comes from in this guide.

Incidentally, the original publication is ignored by almost all guides,
and it is often impossible to find even a link to it in those guides.
That is why, when asked about the source of their knowledge of SOLID,
people often answer with something like "Wikipedia" or "Habr."

In our guide, however, we rely on the primary source and try to
supplement it with our own experience.

The approach "it is not applicable everywhere" or "you should apply it
where necessary" does not work, because we have not even started doing
anything yet, but have already found plenty of excuses for why nothing
worked and why it will not work. First, you need to make an effort to
establish the SOLID principles in your codebase. Then, once you have
some practical experience, you can discuss exceptions. Otherwise, these
are just excuses for not getting started. To take this to an absurd
extreme: "Let's introduce the principle 'do not steal'! Great, wonderful
principle, but, you know, it is not always applicable..."

### Conventions and notes

-   SOLID should be applied consciously, and you need to make an effort
    to do so. There is no "automatic" or "intuitive" path. It does not
    implement itself.
-   To achieve SOLID, use mypy (or ty, once it is ready) and cover 100%
    of the code with type annotations, except where mypy infers the
    types itself. This helps achieve the LSP principle: when mypy scans
    your code and finds annotations, it can "catch" these situations.
    LSP, in turn, is an "enabler" for the OCP principle. This also makes
    your code more reliable.
-   An interface is either a set of public methods without leading
    underscores, an `abc`, or a `typing.Protocol`. We prefer the latter
    because it works at the static-analysis stage, does not require
    running the code, adds no runtime overhead, and works perfectly with
    duck typing.
-   We interpret SOLID as being about OOP, and apply it to OOP. However,
    we do not deny that some of the principles also apply outside OOP.
    For example, SRP works perfectly well for functions.
-   There is an opinion that Martin rewrote SOLID in 2017 with the
    release of *Clean Architecture*. That is not quite true. He applies
    SOLID to his own clean architecture, but this does not invalidate
    the 2003 interpretation. The principles are simply interpreted more
    broadly. Why do we not use the interpretations from *Clean
    Architecture*? Because they become even more complex and vague. For
    example, SRP gains the concept of an "actor." And we should be
    honest: it would be enough for us to spend the next 20+ years
    figuring out what a "reason" for change actually is. That alone
    would be sufficient to understand the principle. Therefore, this
    guide focuses on the original formulations from the 2003 book, which
    have not been rewritten, forgotten, or revoked.

# SOLID Principles

The following sections present our interpretation of the SOLID
principles. As our primary source, we use Robert Martin's [*Agile
Software Development: Principles, Patterns, and
Practices*](https://dl.ebooksworld.ir/motoman/Pearson.Agile.Software.Development.Principles.Patterns.and.Practices.www.EBooksWorld.ir.pdf)
by [Robert
Martin](https://www.google.com/search?q=robert+martin+in+the+bathrobe&sca_esv=e98085266670db2f&rlz=1C5GCEM_enRU1156RU1162&udm=2&biw=1728&bih=958&sxsrf=AE3TifPGpbG0VjeuheuWgJOeImA0eGKkmw%3A1751981495217&ei=tx1taM-EDZK4wPAP6eyc2Q0&ved=0ahUKEwiPvLPVr62OAxUSHBAIHWk2J9sQ4dUDCBA&uact=5&oq=robert+martin+in+the+bathrobe&gs_lp=EgNpbWciHXJvYmVydCBtYXJ0aW4gaW4gdGhlIGJhdGhyb2JlSKVmUI0OWLVlcAp4AJABAZgBlwOgAfEbqgEIMzcuMS40LTG4AQPIAQD4AQGYAgygAuIGwgIGEAAYBxgewgIIEAAYBxgKGB7CAggQABgHGAgYHsICBxAjGCcYyQLCAgUQABiABMICBxAAGIAEGBPCAgoQABiABBgTGMcDwgIGEAAYExgewgIIEAAYExgIGB6YAwCIBgGSBwIxMqAHlDyyBwIxMLgH4AbCBwUyLjcuM8gHGw&sclient=img#vhid=ZrTVGSeCQAXVKM&vssid=mosaic).

## Single Responsibility Principle (SRP)

> A class should have only one reason to change.

A rough translation into other words might be: "a class should have one
reason to change." But what is a reason? The word "reason" has caused
confusion for many years, so in 2014 Martin [wrote an article explaining
this
word](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html).
The problem is that even this article gives us only approximate
guidelines. And we write actual code. Taking everything together, let us
formulate the ideas we arrived at while applying this principle:

-   The core of SRP is the idea of separation of responsibilities. Keep
    it in mind when writing code. In other words, it is better to split
    code into pieces, each of which implements a very specific and small
    part of the logic. The idea of separation of responsibilities seems
    to come from human psychology, specifically Miller's law of the
    "magical number." A person can keep only a small number of things in
    their head (7 ± 2), so the principle of separation of
    responsibilities helps people read code.

-   The word "reason" at the heart of SRP is something your team needs
    to agree on. Within your team, you must agree on what constitutes a
    reason for change (we also consider the word "responsibility" to be
    a close synonym for expressing this idea). The argument for doing
    this can be illustrated with code for working with a modem:

    ``` python
    class Modem(Modem):
        def dial(self, phone_number):
            ...

        def hangup(self):
            ...

        def send(self, data):
            ...

        def receive(self):
            ...
    ```

    We have four methods, and programmers split into several groups over
    how to interpret them. Some believe there is a single
    responsibility: after all, we are working with a modem. Working with
    a modem is the responsibility and the reason for change. Others
    believe that working with the communication channel is one reason
    for change, while working with data transmission is another.

    For example, what would the code look like if we decided that there
    are two "reasons" for change here?

    ``` python
    # Responsibility for the connection
    class Connection:
        def dial(self, phone_number):
            ...

        def hangup(self):
            ...


    # Responsibility for data transmission
    class DataChannel:
        def send(self, data):
            ...

        def receive(self):
            ...
    ```

    So what should we do? How can we universally determine the reasons
    for change in your code? Usually, this is decided empirically, based
    on the code you see and on your team. You need to agree on the
    reasons for change (the responsibilities of classes) and check them
    during code review. If you find this difficult, come to the
    community and we will help. In addition to asking others to review
    your code, you can also consider whether a particular piece of code
    is likely to change in the future. For example, you are writing this
    very code for working with a modem and realize that the data-sending
    code will change frequently. In that case, splitting it into two
    responsibilities is appropriate.

Incidentally, this principle is a contender for first place among the
two principles that make programmers "intuitively feel SOLID." Its
deceptive simplicity creates this false sense of intuition.

Let's look at another example of code that violates SRP:

``` python
@dataclasses.dataclass
class UserService:
    database_host: str
    database_port: str
    kafka_topic: str
    kafka_host: str

    async def create_user(self, user_data: UserData) -> None:
        database_connection = asyncpg.connect(self.database_host, self.database_port)
        await database_connection.execute("INSERT INTO users ...")

        kafka_connection = aiokafka.connect(self.kafka_host)
        await kafka_connection.send(user_data, self.kafka_topic)

    async def delete_user(self, user_id: str) -> None:
        database_connection = asyncpg.connect(self.database_host, self.database_port)
        await database_connection.execute("DELETE FROM users WHERE ...")
        ...
        kafka_connection = aiokafka.connect(self.kafka_host)
        await kafka_connection.send(user_data, self.kafka_topic)
```

This class is responsible not only for working with users, but also
contains the connections to Kafka and Postgres. It therefore takes
responsibility for connections to these infrastructure components, even
though its main task is to work with users. We draw this conclusion from
the semantics of the code.

The disadvantages are that this code is fragile and difficult to test,
because you will have to create many mocks and, in particular, patch
things. This is bad because, instead of checking that our code behaves
as required (creating and deleting users), we will be forced to patch
the Kafka and Postgres connections somehow in our tests. These are
magical actions with unknown consequences. We have lost the cleanliness
of the codebase in two places at once, introduced "magic" actions, and
made the code more fragile. In other words, any changes in the libraries
or patching mechanisms can cause errors that have nothing to do with
what `UserService` actually does.

In this small example, it may seem like "what's the big deal? I'll just
patch it," and that is a reasonable comment. Of course, nothing terrible
will happen here. The code will be a little worse and more fragile, but
none of this seems critical. The problem is that this example becomes
much worse at scale. When this becomes the main approach in your
codebase, everything starts becoming fragile and unstable.

What can we improve here? Stop instantiating connections inside the
class and pass them to it as arguments instead.

So, to comply with SRP, we would write something like this:

``` python
import dataclasses
import asyncpg
import aiokafka

@dataclasses.dataclass
class UserService:
    database_connection: asyncpg.Connection
    kafka_producer: aiokafka.AIOKafkaProducer
    kafka_topic: str

    async def create_user(self, user_data: "UserData") -> None:
        await self.database_connection.execute("INSERT INTO users ...", user_data)
        await self.kafka_producer.send_and_wait(self.kafka_topic, user_data)

    async def delete_user(self, user_id: str) -> None:
        await self.database_connection.execute("DELETE FROM users WHERE id = $1", user_id)
        ...
        await self.kafka_producer.send(user_data, self.kafka_topic)
```

## Open/Closed Principle (OCP)

> Software entities (classes, modules, functions, etc.) should be open
> for extension, but closed for modification.

This is the second "one of those principles" that creates the false
impression that SOLID is intuitive (as we mentioned at the beginning of
the article). This principle is formulated more complexly than SRP, but
its essence is much closer to the basic ideas every programmer is
taught. Therefore, it too can "trick" someone who is encountering SOLID
for the first time.

Translated into Russian, the principle would mean something like:
"software entities (classes, modules, functions, and so on) should be
open for extension, but closed for modification." The problem with this
wording is that it is unclear what "extension" and "modification" mean.
Even after reading the book, things do not become much clearer.

So here is our interpretation.

Essentially, the principle describes the following idea: write code so
that you can add new functionality without rewriting the old code, by
writing new code instead. In other words, if you write code that you
will need to "refactor" (read: delete) in the very next pull request,
you are most likely violating OCP.

The idea itself is practically obvious to all programmers. Essentially,
from the very beginning we are taught that when we need to write a
function that adds 2 + 3, we write
`sum_two_numbers = lambda a,b: a + b, sum_two_numbers(2, 3)` rather than
simply putting "2 + 3" in the code. This is a basic programming skill:
generalizing tasks. OCP is very close to this idea. We generalize
functionality sufficiently, think about the future, and allow our future
selves or colleagues to add new features to the product without
rewriting our code.

However, all of this becomes much less clear when we apply the principle
to the sprawling OOP code we have to write. Is it clear how to write a
class that can be extended but not rewritten? What features will the
business want tomorrow? How can we make sure that a module we write for
today's task will not need to be renamed and rewritten tomorrow? There
are no direct answers to these questions. And since we are not paid to
philosophize, let's turn this into a practical guide to action:

-   Our task is to think through how OOP code can be extended so that it
    will not need to be rewritten tomorrow.
-   Since this is difficult, you can look for violations of the
    principle: if you notice that you or your colleague keep rewriting
    the same code, consider it an OCP problem.
-   If you have an OCP problem, your task is to rewrite the code so that
    you will not need to rewrite it again next time.
-   If you frequently see pull requests in which a lot of code is
    "deleted" each time, this is a potential sign of an OCP violation.
-   There is no need to follow the principle fanatically. If you have
    had to rewrite the same code, fixed the problem, but still
    occasionally make small changes to it, that is probably not a major
    issue. In my opinion, you should decide for yourself what counts as
    "small." I would consider a change of around 10 lines or fewer to be
    small, without taking things to extremes. That is, even 12 lines
    would still be a small change.

#### Example

We are writing code where `run_command` executes a command by name. It
is the entry point for CLI logic:

``` python
def run_command(command_name, **options):
    if command_name == "list-pods":
        return list_pods(**options)
    elif command_name == "scale":
        return scale(**options)
    elif command_name == "--help":
        print("Available commands: list-pods, scale")
        return
    raise SystemExit(f"Unknown command: {command_name}")
```

Let's add a new command, `delete-pod`:

``` python
def run_command(command_name, **options):
    if command_name == "list-pods":
        return list_pods(**options)
    elif command_name == "scale":
        return scale(**options)
    elif command_name == "delete-pod":
        return delete_pod(**options)
    elif command_name == "--help":
        print("Available commands: list-pods, scale, delete-pod")
        return
    raise SystemExit(f"Seriously, who is '{command_name}'?")
```

When adding new commands, it is easy to accidentally affect the behavior
of other commands and forget to update `--help`. To fix this, we should
make the code more generic. We can use a mapping from command names to
handlers:

``` python
AVAILABLE_COMMANDS = {
    "list-pods": list_pods,
    "scale": scale,
    "delete-pod": delete_pod,  # if you have a command, add it here
}

def run_command(command_name, **options):
    if command_name == "--help":
        print(f"Available commands: {', '.join(AVAILABLE_COMMANDS)}")
        return

    if command_handler := AVAILABLE_COMMANDS.get(command_name):
        return command_handler(**options)

    raise SystemExit(f"What are you doing, [NAME]? There is no such command: '{command_name}'")
```

Now, adding a new command only requires changing `AVAILABLE_COMMANDS`:

``` python
AVAILABLE_COMMANDS = {
    "list-pods": list_pods,
    "scale": scale,
    "delete-pod": delete_pod,
}
```

At some point in the evolution of the code, the `run_command` function
may become too complex, and we may want to test the command-execution
logic separately from the commands themselves. In that case, we can
create a `CommandRunner` class that receives the available commands
during initialization:

``` python
class CommandRunner:
    def __init__(self, available_commands):
        self.available_commands = available_commands

    def run_command(self, command_name, **options):
        if command_name == "--help":
            print(f"Available commands: {', '.join(self.available_commands)}")
            return

        if command_handler := self.available_commands.get(command_name):
            return command_handler(**options)

        raise SystemExit(f"Hey, '{command_name}' is not on the list.")
```

When designing software, we should aim to minimize the need to refactor
existing code in the future and instead make it possible to add new
code.

#### Summary

Let's formulate some rules for identifying OCP violations:

-   Look for candidates for generalization: repetitive code and chains
    of `if` statements.
-   Look at your pull requests. If code is deleted as often as it is
    added, that is a warning sign. In particular, if you delete old code
    when adding new functionality, this already highlights a problem:
    you are violating OCP.
-   The same code or section of code is frequently changed or rewritten.

## Liskov Substitution Principle (LSP)

> Subtypes must be substitutable for their base types.

People sometimes say in interviews that "Python has no types," which is
usually considered an incorrect answer. However, it is always clear
where this idea comes from: Python's typing works at runtime and is not
particularly visible to developers. Python developers do not need to
think about types most of the time, so some developers act as if these
types do not exist. Type annotations, which are often called "typing,"
add to the confusion. The existence of statically typed, compiled
languages makes the situation even more confusing.

Therefore, when we read the original formulation of the LSP principle,
the question arises: "Does this apply to Python?" Here I would recall
that SOLID is primarily about OOP (in our interpretation). So, in our
own usage, we replace the word "types" with "classes." The principle
"types must be replaceable with subtypes without breaking the program"
(which is not a very precise translation) can therefore be rephrased for
Python as "classes must be replaceable with subclasses without breaking
the program."

Our approach to this principle is as follows: to achieve LSP, the first
thing we need to do is cover the code completely with type annotations.
Unfortunately, even then, **not all** cases are covered by annotations.

To avoid making you wait, let's start with an example of a case that
mypy does not catch:

``` python
class ParentShape:
    ...

class Circle(ParentShape):
    def draw_shape(self) -> None:
        ...

class Rectangle(ParentShape):
    def draw_shape(self) -> None:
        ...

# The ParentShape annotation is the source of the LSP violation.
def draw_some_shape(one_shape: ParentShape) -> None:
    # OCP violation.
    if isinstance(one_shape, Circle) or isinstance(one_shape, Rectangle):
        one_shape.draw_shape()
```

This violates both LSP and OCP at the same time. OCP is violated because
every time we want to add a new shape, we will have to change the
`draw_some_shape` code. LSP is violated because the `ParentShape` base
class does not have a `draw_shape` method. This means that if we want to
replace `ParentShape` with `Circle` in the program, for example, the LSP
principle will be violated, since this cannot be done without breaking
the program.

At the same time, we understand that code using `isinstance` like this
is bad, but that is not obvious to every developer; this example is
somewhat contrived. There is also an issue with `ParentShape`. We
understand that it is some kind of "base" class whose direct instance we
are unlikely to encounter in the code. However, knowing that this
violates LSP pushes us toward one of several options: abandon
`ParentShape` in favor of a `Protocol`, add the `draw_shape` method to
it, specify a union of types (`Circle | Rectangle`), or use `Any` (which
is not recommended). I would choose `Protocol`; then the only remaining
problem in the code would be the OCP violation. Of course, real
programmers rarely write code like this, but it does happen.

An important additional conclusion: an LSP violation does not occur when
we write classes, but when we use them.

Now let's look at a more straightforward example. It shows how the
program breaks if we replace the `Animal` class with the `Dog` class
throughout our code:

``` python
class Animal:
    def make_sound(self):
        return "generic sound"

class Dog(Animal):
    def make_sound(self):
        return ["woof", "woof"]

def listen(animal: Animal):
    print(animal.make_sound().upper())


listen(Animal())
listen(Dog())  # Error: 'list' object has no attribute 'upper'
```

The LSP violation occurred because we wrote the `Dog` class and its
`make_sound` method in such a way that the returned type is completely
different from the base class's return type. How can we fix this code?
Easily: add type annotations, and mypy will not let us make this
mistake.

Here is another example of an LSP violation:

``` python
class Bird:
    def fly(self):
        ...
    ...  # other common methods

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("...")
```

mypy will not be able to "catch" this violation either, even though here
we violate both LSP and ISP at the same time. LSP is violated because if
we replace `Bird` with `Penguin` in the program, calls to the `fly`
method will not expect an error at all. We also violate ISP because the
"consumer" of `Penguin` does not need the `fly` method, but gets it
anyway. It is quite obvious that in this case `Penguin` should not be a
"descendant" of `Bird`; then our problems disappear. This example is
very "cardboard-like" (oversimplified), and real-world cases are usually
more complicated, but it illustrates the problems easily.

How would we write "bird" code that follows LSP? For example:

``` python
class GenericBird:  # Common methods from Bird have moved here.
    ...  # other common methods

class FlyingBird(GenericBird):  # Bird has become this class.
    def fly(self):
        ...

class Penguin(GenericBird):
    ...
```

As we can see, LSP is easy to follow, but it is also easy to violate by
specifying incorrect types or even organizing inheritance incorrectly.
mypy helps you a lot here, but sometimes even it cannot determine
whether there is a violation. In those cases, you should rely on your
own attention and the code-review process.

Arguments in favor of following LSP:

-   It helps make polymorphism work reliably.
-   It helps achieve a cleaner OOP design, making code more readable.
-   It makes your code safer. As the codebase grows, we are less likely
    to break existing code.
-   It makes code more extensible because LSP is an enabler for OCP.

## Interface Segregation Principle (ISP)

> Clients should not be forced to depend on methods that they do not
> use.

Our interpretation of this principle is adapted for Python:
[interfaces](#conventions-and-notes) should not contain methods that
their clients will not use; instead, create separate, small interfaces
for different clients based on their needs.

In other words, if you are writing an interface for use in some part of
the code and that part needs three methods, write three methods. Do not
add 10 or 15 methods "just in case." The original formulation expresses
the idea that a client should not depend on methods it does not use. But
in Python, thanks to duck typing, it does not seem that we "depend"
directly on methods we do not use. Consider this case:

``` python
import typing

class SmartHomeDevice(typing.Protocol):
    def turn_on(self) -> None: ...
    def turn_off(self) -> None: ...
    def set_temperature(self, value: float) -> None: ...
    def play_music(self, track: str) -> None: ...

class SmartLight:
    def turn_on(self) -> None:
        print("Light on")

    def turn_off(self) -> None:
        print("Light off")

    # The light cannot control temperature or play music,
    # but the type checker requires it to implement all methods:
    def set_temperature(self, value: float) -> None:
        raise NotImplementedError("Light can't set temperature")

    def play_music(self, track: str) -> None:
        raise NotImplementedError("Light can't play music")

def activate(device: SmartHomeDevice):
    device.turn_on()

lamp = SmartLight()
activate(lamp)
```

The light is forced to implement temperature and music methods that it
does not need.

If we add a new action (for example, "open the curtains"), all classes
will have to add yet another stub.

mypy will complain if the methods are not implemented, but at runtime,
`NotImplementedError` exceptions may appear unexpectedly.

What should we do to avoid this problem? We should split the
`SmartHomeDevice` interface into smaller ones. One will contain the
interface for "lights" (or similar objects), while the others will be
for devices that can handle temperature and music.

Let's look at a more understandable example. Imagine that we are
developing a system for office devices. We have an all-in-one device
that can print, scan, and send faxes. We will not use `Protocol` here so
as not to make the example code "bloated":

``` python
# Problematic approach: one class with all capabilities.
class AllInOneDevice:
    def print_document(self, document) -> None:
        print(f"Printing document: {document.content}")

    def scan_document(self):
        print("Scanning document")
        return {"content": "Scanned document contents"}

    def send_fax(self, document, recipient: str) -> None:
        print(f"Sending fax '{document.content}' to {recipient}")

# A function that only wants to scan and print.
def copy_document(device) -> None:
    # This function uses only 2 of the device's 3 methods.
    document = device.scan_document()  # Used.
    device.print_document(document)    # Used.
    # device.send_fax(...) — this method is not needed, but it is already implemented.

# Usage.
all_in_one = AllInOneDevice()
copy_document(all_in_one)
```

The problem is that `copy_document` depends on the `send_fax` method,
which it does not need. If we want to use a device that can only scan
and print (but cannot send faxes), we will not be able to do so.

The solution is to split the interfaces according to the ISP:

``` python
# Concrete device implementations.
class SimplePrinter:
    def print_document(self, document):
        print(f"Printing document: {document.content}")

class SimpleScanner:
    def scan_document(self):
        print("Scanning document")
        return {"content": "Scanned document contents"}

class OfficeAllInOne:
    def print_document(self, document):
        print(f"Printing document: {document.content}")

    def scan_document(self):
        print("Scanning document")
        return {"content": "Scanned document contents"}

    def send_fax(self, document, recipient: str):
        print(f"Sending fax '{document.content}' to {recipient}")

# Now copy_document depends only on the interfaces it needs.
def copy_document(scanner, printer):
    document = scanner.scan_document()
    printer.print_document(document)

# Now we can use different combinations of devices.
# 1. Separate devices.
scanner = SimpleScanner()
printer = SimplePrinter()
copy_document(scanner, printer)

# 2. A function that uses fax (only implementations that support this interface).
def send_document_via_fax(fax_machine, ...):
    ...

# Only OfficeAllInOne can send faxes; SimplePrinter and SimpleScanner cannot.
send_document_via_fax(OfficeAllInOne(), ...)
```

Why is this better?

-   The client function `copy_document` depends only on the interfaces
    it needs (`scan_document` and `print_document`), not on an
    unnecessary one (`send_fax`).
-   We reduce coupling: the function does not know about or depend on
    methods it does not need.
-   Flexibility: different devices can be combined for different tasks.
-   Extensibility: new types of devices can easily be added without
    changing existing code.
-   Clarity: each interface has a clear purpose.

## Dependency Inversion Principle (DIP)

> a.  High-level modules should not depend on low-level modules. Both
>     should depend on abstractions.
>
> b.  Abstractions should not depend on details. Details should depend
>     on abstractions.

In a loose translation, the principle says that high-level modules
should depend on abstractions rather than being tied to concrete
implementations. It can be rephrased as: "rely on abstractions rather
than concrete implementations as dependencies."

This raises some questions. First, what is an abstraction (an OOP term,
an abstract class, a protocol, just some class)? By "abstraction," we
specifically mean a contract, also known as an
["interface"](#conventions-and-notes)---and Martin seems to be heading
in the same direction. What is a dependency? This is a little more
complicated, but we assume that if one part of the code needs another
part of the code to perform its tasks, then the latter is a dependency
of the former. For example, if we have class A and a `get_some` method,
and that method needs class B and its `get_another` method to work, then
class A depends on class B.

### Dependency Injection

And now we come to the DI pattern. Its name may sound very similar to
the DIP principle, although they are, in fact, two different things (DI
is a pattern, while DIP is a principle). Let's try to understand how
they are related. First, let's clarify what the DI pattern is. Here is a
clear example of the DI pattern:

``` python
# Bad:
class CrmClient:
    def fetch_user_balance_from_crm(self, user_uuid: str) -> decimal.Decimal:
        httpx_connection: httpx.Client = httpx.Client(...)
        ...

# “Manual” DI: we expect the dependency to be provided from outside.
class CrmClient:
    def fetch_user_balance_from_crm(
        self,
        httpx_connection: httpx.Client,
        user_uuid: str,
    ) -> decimal.Decimal:
        ...

# “Manual” DI can be done better:
@dataclasses.dataclass(kw_only=True, slots=True, frozen=True)  # avoids boilerplate and reduces the number of errors
class CrmClient:
    # CrmClient can now be placed in a DI framework and receive httpx.Client from outside.
    # Or you can simply instantiate it manually if you do not want to use a DI framework.
    httpx_connection: httpx.Client

    def fetch_user_balance_from_crm(self, user_uuid: str) -> decimal.Decimal:
        ...
```

The DI pattern is an everyday tool for us, and we believe it makes code
simpler, cleaner, and easier to test. But how is it related to the DIP
principle (coming back to SOLID)? There is no direct dependency between
them, but we believe that the DI pattern is an approach that helps put
DIP into practice.

There are a number of experts who believe that implementing DI cannot
achieve DIP, because the definition says that we should rely on
abstractions rather than concrete implementations, whereas with DI we
often use concrete implementations. However, remembering duck typing in
Python, we can be confident that in tests we can replace these concrete
implementations with mocks that implement only the methods we need.

Why are we writing about this? In our opinion, Martin recommends relying
on abstractions precisely because of testability requirements; this
problem clearly comes from the world of statically typed languages. In
our opinion, Python "solves" this through dynamic, duck (and somewhat
structural) typing. Recall what duck typing means: what matters to us is
not what a given object inherits from, but which methods it implements.
If the required methods are present, we consider it to be of the
required type.

Therefore, although some people formally believe that implementing DI
does not achieve DIP, we think otherwise. Can we say that we are
unequivocally right? Probably not, but we prefer to believe that the
formula "DI + duck typing (Python) == DIP" is correct.

Here is an example of duck typing:

``` python
import typing

class PaymentProcessor(typing.Protocol):
    def process_payment(self, amount: float) -> bool:
        ...

class StripePaymentService:
    def process_payment(self, amount: float) -> bool:
        print(f"Processing ${amount} via Stripe")
        return True

class OrderService:
    def __init__(self, payment_processor: PaymentProcessor):
        self.payment_processor = payment_processor

    def complete_order(self, amount: float) -> None:
        if self.payment_processor.process_payment(amount):
            print("Order completed successfully")

# In production, we use the real implementation.
stripe_service = StripePaymentService()
order_service = OrderService(payment_processor=stripe_service)
order_service.complete_order(100.0)

# In tests, we use a mock that implements only the required methods.
class MockPaymentProcessor:
    def process_payment(self, amount: float) -> bool:
        return True  # Payments always succeed in tests.

mock_processor = MockPaymentProcessor()
test_order_service = OrderService(payment_processor=mock_processor)
test_order_service.complete_order(50.0)
```

Let's set duck typing aside and look at another example.
`OrderProcessor` uses `MailNotifier` to send emails about orders:

``` python
class MailNotifier:
    def __init__(self, login: str, password: str) -> None:
        pass

    def notify(self, message: str) -> None:
        print("sending an email...")

class OrderProcessor:
    def __init__(self, notifier: MailNotifier) -> None:
        self.notifier = notifier

    def process_order(self, items: list[Item]) -> None:
        order = Order(items=items)
        self.notifier.notify(f"Your order {order.id} will be shipped soon!")


notifier = MailNotifier(settings.email_login, settings.email_password)
processor = OrderProcessor(notifier=notifier)
processor.process_order(items=...)
```

Suppose the requirements change, and now we want to send SMS messages
instead of emails. To do this, we will replace `MailNotifier` with
`SMSNotifier`:

``` python
class Notifier:
    def notify(self, message: str) -> None:
        ...

class MailNotifier(Notifier):
    def __init__(self, login: str, password: str) -> None:
        pass

    def notify(self, message: str) -> None:
        print("sending an email...")

class SMSNotifier(Notifier):
    def __init__(self, api_key: str) -> None:
        pass

    def notify(self, message: str) -> None:
        print("sending an SMS...")

class OrderProcessor:
    def __init__(self, notifier: Notifier) -> None:
        self.notifier = notifier

    def process_order(self, items: list[Item]) -> None:
        order = Order(items=items)
        self.notifier.notify(f"Your order {order.id} will be shipped soon!")


notifier = SMSNotifier(settings.sms_api_key)
processor = OrderProcessor(notifier=notifier)
processor.process_order(items=...)
```

To avoid having to change `OrderProcessor` every time the notification
method changes, we should create a `Notifier` interface and pass an
object conforming to this interface to `OrderProcessor.__init__`:

``` python
import typing

class Notifier(typing.Protocol):
    def notify(self, message: str) -> None: ...

class OrderProcessor:
    def __init__(self, notifier: Notifier) -> None:
        self.notifier = notifier

    def process_order(self, items: list[Item]) -> None:
        order = Order(items=items)
        self.notifier.notify(f"Your order {order.id} will be shipped soon!")

class EmailNotifier(Notifier):
    def __init__(self, login: str, password: str) -> None:
        pass

    def notify(self, message: str) -> None:
        print("sending an email...")

class SMSNotifier(Notifier):
    def __init__(self, sms_service_api_key: str) -> None:
        pass

    def notify(self, message: str) -> None:
        print("sending an SMS...")


OrderProcessor(notifier=SMSNotifier(settings.sms_service_api_key)).process_order(items=...)
```

In this example, we also moved the initialization of `Notifier` out of
`OrderProcessor` and into the client code. This makes `OrderProcessor`
more flexible and resilient to change. Dependency Injection (DI) often
helps achieve the SRP (Single Responsibility Principle) and DIP
(Dependency Inversion Principle).

### DI Containers

The next stage in the evolution of the DI pattern is when the DI pattern
is combined with a DI framework. Somewhere on the horizon, the term
IoC---Inversion of Control---starts to appear. What is terrible about
this term? First, it gets confused with DIP. Second, there is no
consensus about what it means, and in some Russian-language Telegram
chats you will be ground into dust if you bring up the topic of IoC.

We understand IoC as the use of DI "containers" (we also call them IoC
containers). This is when the required dependencies are assembled into
one or more objects, where they are defined as declarative attributes
that reference one another---a kind of declarative dependency graph for
your application. Here is an example:

``` python
from that_depends import BaseContainer, providers, inject


def create_db_session(config):
    return f"DB Session with {config}"

@dataclass
class Config:
    db = ...
    users = ...

@dataclass
class UserRepository:
    users_config = ...
    session = ...

    def get_user(self, user_id):
        ...

# Here is our container.
class Container(BaseContainer):
    my_local_config = providers.Singleton(Config)
    db_session = providers.Factory(create_db_session, config=my_local_config.db)
    user_repository = providers.Factory(
        UserRepository, my_local_config.users, session=db_session
    )

@inject
def usage_example(user_repo=Provide[Container.user_repository]):
    print(user_repo.get_user(123))
```

If this looks complicated and/or overloaded, that is because it is. But
this complexity is introduced into projects so that the rest of the code
can become cleaner.

This probably raises a philosophical question: how do we distinguish
what should go into a DI container and what should not? One approach is
to use the following rule. If most (not necessarily all) of these
statements are true:

-   It is an object (a class).
-   It depends on other objects or is itself a dependency of other
    objects.
-   It is used in several places.
-   It needs to be instantiated regularly.

Then it makes sense to put that "thing" into a DI container. A database
connection is a classic example.
