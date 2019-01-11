# Craps!
![Frankie Sinatra and Brando shoot craps!](https://4.bp.blogspot.com/-e1ysy7W69mI/UR2hikXnAPI/AAAAAAAAAwQ/V20M7nNOazA/s1600/Guys4.jpg)

For this exercise you will be building a basic dice game that runs in the
command line. It's a pared down version of the casino classic craps! These are
the rules of craps:

1. The player rolls two dice
2. If the number is 7 or 11 the player wins
3. If the number is 2, 3 or 12 the player loses
4. If The number is any number other than the ones above that number becomes the
'point'
5. The player continues to roll until the point is rolled or a 7 is rolled
first.
6. If the player rolls the point before a 7 they win but if they roll a 7 before
the point they lose.

We are going to try to build a game that allows a player to play craps from the
command line. We will build it in stages with the more advanced behavior being
left as bonus material.

The first
thing to do is to build some functions to model the behavior of two six sided
dice. First check out the [Math.random](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random) method documentation from MDN. Using that let's think about how we could write a
function that returns a random number between 1 and 6. Since we know that
`Math.random()` returns a number between 0 and 1 (including 0 but excluding 1) what
do we need to do to change that to a number between 1 and 6? Let's start by
multiplying `Math.random()` by 6. That will give us a value between 0 and 5.999999.
We next we need to turn those into whole numbers. Let's use another Math
method, [Math.floor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/floor).
Now we're getting a whole number between 0 and 5. The last step is to add 1.
<details>

<summary>The final code looks like this:</summary>

```JavaScript
function rollDie() {
  const roll = Math.floor(Math.random() * 6) + 1;
  return roll;
}
```
</details>
</br>
**Bonus** How would you write this function to be able to model dice of different
sizes?

Great! now we need to write a function that models rolling 2 dice at once. How can we use the function we already wrote to achieve this? What kind of data
structure should we use to return two values instead of just one? The best way
is to call our `rollDie` function twice and store both values in an array that we
then return.
<details>

<summary>This function looks like:</summary>

```JavaScript
function rollDice() {
  const diceRolls = [];
  diceRolls.push(rollDie());
  diceRolls.push(rollDie());
  return diceRolls;
}
```
</details>
</br>
**Turn and talk** Why do we do this instead of returning a single number between
2 and twelve? How could this affect a game that relies on dice?

**Bonus** How would you write this function to return an arbitrary number of
dice?

In our game we are going to want the total of these dice. Let's write a function
that takes an array of two numbers and returns a sum.
<details>

<summary>The function should look like this:</summary>

```JavaScript
function getTotal(rollArray) {
  const rollOne = rollArray[0];
  const rollTwo = rollArray[1];
  return rollOne + rollTwo;
}
```
</details>
</br>

**Bonus** How would you write this function to take an array with an arbitrary
number of rolls? [reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
could help!

Ok, we're close to having enough functions to actually play a game! Let's write a
function that given a total of two dice will return 'win' if the number is 7 or
11, 'lose' if the number is 2, 3 or 12 and the number rolled otherwise. This is
an ideal case for a [switch statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch).
<details>

<summary>The final code looks like:</summary>

```JavaScript
function checkRoll(total) {
  switch (total) {
    case 2:
    case 3:
    case 12:
      return 'lose';
    case 7:
    case 11:
      return 'win';
    default:
      return total;
  }
}
```
</details>
</br>
Now it's time to put it all together. Let's make a function called `playGame`
that rolls two dice and calls `checkRoll` on the total.
<details>

<summary>The final code looks like this:</summary>

```JavaScript
function playGame() {
  const roll = getTotal(rollDice());
  console.log(`you rolled a ${roll}`);
  console.log(checkRoll(roll));
}
```
</details>
</br>

We can check this out by using node to run our file. Try running this in your
terminal until you see all the possible outcomes.

Alright, let's add some more functionality. We want to have our app continue
rolling if a point is established on the initial roll and apply the correct
logic. Here we are going to need to store a global variable. Our app
will need to keep track of the point as it continues to roll. Generally we avoid
using global variables because it can be hard to track changes since they are
available to all the functions in our app. In our case, however, we are going to
use a single global object that stores all the data our app needs to keep track
of. We will refer to this data as `state` and we will store it in an object
called `gameState`. We will start by setting the value of point to `null`. This
will allow us to use conditional logic to see if a point has been established.

Add the following code to the top of your file:

```JavaScript
const gameState = {
  point: null,
};
```

**Turn and talk** What other pieces of state would you think about tracking in a
game? What are some potential pitfalls of using global objects.

Next, Let's write a function that will set the point value in our `gameState`
object in the case that a point is established. This function will directly
mutate our global `gameState` object and `console.log` the point.
<details>

<summary>The code should look like this:</summary>

```JavaScript
function setPoint(total) {
  console.log(`The point is ${total}`);
  gameState.point = total;
}
```
</details>

We can now add this function into our `checkRoll` function. Instead of just
returning total in the case of a point being established we can also call our
`setPoint` function:
<details>

<summary>The relevant changes looks like this:</summary>

```JavaScript
function checkRoll(total) {
  // The code up to here remains unchanged
    default:
      setPoint(total);
      return total;
  }
}
```
</details>
</br>

**Turn and talk** We could have just written the set point behavior into the
switch statement. What are the advantages to writing a separate function?

Let's take a second to test what we just did and make sure it's doing what we
think it's doing. Let's put a `debugger` statement in our `setPoint` function and
see if it's mutating our global variable the way we expect. Make `setPoint` look
like this:

```JavaScript
function setPoint(total) {
  console.log(`The point is ${total}`);
  gameState.point = total;
  debugger;
}
```
At the bottom of our file let's run `setPoint` with 4 by adding:

```JavaScript
setPoint(4);
```
We can now run our file with the inspect flag with the command
`node --inspect-brk <filename>`. After we step into the `setPoint` call we can
see that `gameState.point` is 4 like we would expect. Let's take out the debugger
and the call to `setPoint` and keep going

**Turn and talk** Why is `gameState` in `closure scope` ?


Now that we have some state to keep track of we will need to change some of
our game logic. Specifically our `checkRoll` function needs to behave differently
in the case that a point has been established. We need to add some conditional
logic to our `checkRoll` function that checks whether `gameState.point` is `null`
or if it has a value. If a point has been established we then need to check if
the total equals that point or if the total equals a 7. In the first case we
will return `win` and in the second we will return `lose`. In all other cases we will just
return `gameState.point`.
<details>

<summary>The updated `checkRoll` function should look like this:</summary>

```JavaScript
function checkRoll(total) {
  if (gameState.point) {
    if (total === gameState.point) return 'win';
    if (total === 7) return 'lose';
    return gameState.point;
  }
  // code below remains unchanged
}
```
</details>
</br>
We're almost there! The problem is that our `playGame` function, as written, will
only run once. We need to add some logic that tells our program to run until we
win or lose. There are many ways to go about this. One idea is to notice that
our `checkRoll` function will return a number if there is no win or loss. We can
use that fact to add code to our `playGame` function that will call itself if
`checkRoll` returns a number.
<details>

<summary>Let's make our `playGame` function look like this:</summary>

```JavaScript
function playGame() {
  const roll = getTotal(rollDice());
  console.log(`you rolled a ${roll}`);
  const checkedRoll = checkRoll(roll);
  console.log(checkedRoll);
  if (typeof checkedRoll === 'number') playGame();
}
```
</details>
</br>
**Bonus** How can you refactor `playGame` so that it does not recursively call
itself? What advantages and disadvantages are there to both approaches?

We did it! We built a basic craps game that runs on the command line using
javascript and node. One of the key take aways from this exercise is to see how
we can leverage small, simple functions to make a complex program. We thought
about our game and then modeled it using javascript functions. Hopefully we can
see that when we keep our functions short and centered around one behavior it
makes it easier to use them with other functions and figure out how they behave
together.

## Further exercises:
While we have a working game it leaves a lot to be desired. Below are some
suggestions for features that will improve the overall game play experience.

- Add a function that asks a player's name and stores it in our `gameState` object
- Add a function that asks a player if they want to play again.
- Add a function that keeps track of a player's overall wins and losses.
- Add a function that allows a player to input a bet amount and keeps track of
the player's overall bankroll.
- Use the `chalk` npm packages to add colors to the text output.
