# HelloWorldApp: Glossary

The domain words this Product uses: what each means, and nothing else. A defined term is written with capital initials wherever it is used in its defined sense.

## Language

### What the Product produces

**Greeting**:
The exact text `Hello World`: capital H and capital W, one space between the words, no punctuation.
Not the Output, which is the Greeting together with its trailing line feed, the empty error stream and the exit code.
_Avoid_: message

**Output**:
Everything a Run produces: the Greeting followed by a single line feed character on standard output, nothing on standard error, and an exit code of 0. The Output is the same bytes on every operating system; it never ends in a carriage return and line feed pair.
Not the Greeting alone, which is only the text.

**Run**:
One start of the Product, from launch until it exits, producing exactly one Output. A Run takes no input; any arguments passed to it are ignored and do not change the Output.
Not a build or a test session, which may start many Runs.

### Who uses it

**Developer**:
A person who starts a Run and reads its Output; the Product has no other kind of user.
Not a Factory agent, which builds and tests the Product but is not its user.
_Avoid_: user
