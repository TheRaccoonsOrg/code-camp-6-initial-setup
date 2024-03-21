# The Raccoons Code Camp 6 - Javascript and React

## Lets get our project setup

### Check dependencies

```bash
node -v  // Check Node.js installation
npm -v   // Check Node.js installation
git -v   // Check Node.js installation
```

### Clone repository with initial setup

```bash
git clone https://github.com/mihaildanilov/code-camp-6-initial-setup.git // Clone the repository
```

### Run the project

```bash
cd code-camp-6-initial-setup  // Navigate to the project directory
npm install                   // Install project dependencies
npm run dev                   // Start the development server
```

## Let's start coding

React component structure:
Parent -> Child -> Grand Child -> ...

Entery point of our page is `main.jsx`. It receives App component that renders our content. \
Go to `src/App.jsx` file, there you can see the page content.

While running the development server try editing App.jsx file and see changes in your browser.

### Install all of the necessary components.

During the implementation of the project we will use Shadcn/ui UI library. \
To install shadcn UI library run this command in your terminal:

```bash
npx shadcn-ui@latest add alert button card form input
```

### Go to App.jsx file and create Card component

```jsx
import { Card, CardContent, CardHeader } from './components/ui/card';

const App = () => {
  return (
    <main className="h-screen w-full">
      <div className="h-full flex flex-col items-center justify-center">
        <Card>
          <CardHeader>{/* CardTitle and CardDescription components goes here */}</CardHeader>
          <CardContent>{/* GuessForm and AlertBox components goes here */}</CardContent>
        </Card>
      </div>
    </main>
  );
};

export default App;
```

### Let's add title and description to our card

Here we will add CardTitle and CardDescription components that will go to CardHeader component:

```jsx
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from './components/ui/card';

const App = () => {
  return (
    <main className="h-screen w-full">
      <div className="h-full flex flex-col items-center justify-center">
        <Card>
          <CardHeader>
            <CardTitle>The Higher Lower Game</CardTitle>
            <CardDescription>
              Think of a number, and I&apos;ll tell you if my guess is higher or lower.
            </CardDescription>
          </CardHeader>
          <CardContent>{/* GuessForm and AlertBox components goes here */}</CardContent>
        </Card>
      </div>
    </main>
  );
};

export default App;
```

#### Create getRandomNumber function

```jsx
const MAX_NUMBER = 1000;

const getRandomNumber = () => Math.floor(Math.random(0) * MAX_NUMBER);
```

Place MAX_NUMBER constant outside of App function and getRandomNumber function inside your App function.

**In the next steps place everything within your App function.**

#### Now we have to create useState hook, it will let us temporarly store our generated number

Short definition of hooks: \
Hooks allow function components to have access to state and other React features.Because of this, class components are generally no longer needed.

```jsx
import { useState } from 'react';

const [guessedNumber, setGuessedNumber] = useState();
```

How Does useState() Work? The useState() hook works by handling and managing state in your applications. The useState() hook takes the first (initial) value of the state variable as its argument. The second value then sets your state, which is why it's always initiated as setState .

In our example we want to set intitial guessed number to random number that we are generating using getRandomNumber function.

To generate random number on page load will use another hook called `useEffect`.

What does useEffect do? By using this Hook, you tell React that your component needs to do something after render. React will remember the function you passed (we'll refer to it as our “effect”), and call it later after performing the DOM updates.

#### Let's add useEffect hook to our code

Add `useEffect` hook to imports:

```jsx
import { useEffect, useState } from 'react';
```

Then add it to our App function after `useState` hook

```jsx
useEffect(() => {
  setGuessedNumber(getRandomNumber);
}, []);
```

Now if we will add guessedNumber to our code it will generate new number every time we reload the page.

**So far our code should look like this:**

```jsx
import { useEffect, useState } from 'react';

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from './components/ui/card';

const MAX_NUMBER = 1000;

const App = () => {
  const getRandomNumber = () => Math.floor(Math.random(0) * MAX_NUMBER);
  const [guessedNumber, setGuessedNumber] = useState();

  useEffect(() => {
    setGuessedNumber(getRandomNumber);
  }, []);

  return (
    <main className="h-screen w-full">
      <div className="h-full flex flex-col items-center justify-center">
        <Card>
          <CardHeader>
            <CardTitle>The Higher Lower Game</CardTitle>
            <CardDescription>
              Think of a number, and I&apos;ll tell you if my guess is higher or lower.
            </CardDescription>
          </CardHeader>
          <CardContent>
            {/* GuessForm and AlertBox components goes here */}
            {guessedNumber}
          </CardContent>
        </Card>
      </div>
    </main>
  );
};

export default App;
```

Reload the page to see a new generated number.

#### Let's add our form logic to the App function

Create 3 new `useState` hooks under existing `guessedNumber` state. It will store the part of our game logic.

```jsx
const [guess, setGuess] = useState();
const [guessState, setGuessState] = useState('hidden');
const [attempts, setAttempts] = useState(0);
```

- First hook will be used to store users input.
- Second hook will be used to store guess state.
- Third hook will be used to store count of attempts to guess the generated number.

**Now we will create a function that will generate new number on button click in our form.** \
Place it after `useEffect` hook.

```jsx
const generateNewNumber = () => {
  setGuessedNumber(getRandomNumber);
  setGuess();
  setAttempts(0);
  setGuessState('hidden');
};
```

**Now lets initialize our form logic.**\
We will use `react-hook-form` library to manage states of our forms.\
Also we will set the default values of input to empty string.\
Place it after `generateNewNumber` function.

```jsx
import { useForm } from 'react-hook-form';

const form = useForm({
  defaultValues: { guess: '' },
});
```

**Now create our guessed number check logic.**\
For that we will create new function called `checkGuess`.\
Place it under form initialization.

```jsx
const checkGuess = (guess) => {
  if (guess > guessedNumber) setGuessState('lower');
  else if (guess < guessedNumber) setGuessState('higher');
  else if (guess == guessedNumber) setGuessState('correct');
};
```

**Logic explanation:** \
If guess is lower than our guessed number, change `guessState` to higher.\
If guess is higher than our guessed number, change `guessState` to lower.\
If guess is correct, change `guessState` to correct.

**Create form submit handler function.**

```jsx
const handleSubmit = (data) => {
  setGuess(data.guess);
  checkGuess(data.guess);
  setAttempts(attempts + 1);
  form.reset();
};
```

**So far our code should look like this:**

```jsx
import { useEffect, useState } from 'react';

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from './components/ui/card';
import { useForm } from 'react-hook-form';

const MAX_NUMBER = 1000;

const App = () => {
  const getRandomNumber = () => Math.floor(Math.random(0) * MAX_NUMBER);
  const [guessedNumber, setGuessedNumber] = useState();
  const [guess, setGuess] = useState();
  const [guessState, setGuessState] = useState('hidden');
  const [attempts, setAttempts] = useState(0);

  useEffect(() => {
    setGuessedNumber(getRandomNumber);
  }, []);

  const generateNewNumber = () => {
    setGuessedNumber(getRandomNumber);
    setGuess();
    setAttempts(0);
    setGuessState('hidden');
  };

  const form = useForm({
    defaultValues: { guess: '' },
  });

  const checkGuess = (guess) => {
    if (guess > guessedNumber) setGuessState('lower');
    else if (guess < guessedNumber) setGuessState('higher');
    else if (guess == guessedNumber) setGuessState('correct');
  };

  const handleSubmit = (data) => {
    setGuess(data.guess);
    checkGuess(data.guess);
    setAttempts(attempts + 1);
    form.reset();
  };

  return (
    <main className="h-screen w-full">
      <div className="h-full flex flex-col items-center justify-center">
        <Card>
          <CardHeader>
            <CardTitle>The Higher Lower Game</CardTitle>
            <CardDescription>
              Think of a number, and I&apos;ll tell you if my guess is higher or lower.
            </CardDescription>
          </CardHeader>
          <CardContent>
            {/* GuessForm and AlertBox components goes here */}
            {guessedNumber}
          </CardContent>
        </Card>
      </div>
    </main>
  );
};

export default App;
```

### Now let's create GuessForm component

#### Go to **./src/components** and create a file named **GuessForm.jsx**

We will create a function `GuessForm` that will return our form.

For now lets create test version of the function to see if everything renders correctly on our page.

GuessForm fucntion test code should look like this:

```jsx
const GuessForm = () => {
  return (
    <div>
      <p>GuessForm</p>
    </div>
  );
};

export default GuessForm;
```

#### Go back to App.jsx and place `<GuessForm/>` component inside CardComponent tags

```jsx
import GuessForm from './components/GuessForm';

<CardContent>
  <GuessForm />
  {guessedNumber}
</CardContent>;
```

Now you should see GuessForm writing rendered on the website

#### Moving on to implementation of our form

We will use Shadcn UI libraries components to build our form.\
Importing new components `Form`, `form`, `FormField`, `FormItem`, `FormControl` and `Input` will help us build our form.

GuessForm component explanation:

```jsx
<Form></Form> {/*Defines state provider for our form and lets us extract data from the form to reuse lateron.*/}
<form></form>{/*Default html element for form use goes in between the Form component tags*/}
<FormField
  control={form.control}
  name="guess"
  render={({ field }) => (
    {/* Form */}
  )}
/>{ /*Defines form field for the form html element*/}
<FormItem></FormItem> {/*Defines form item for FormField component*/}
<FormControl></FormControl> {/*Defines form control for the FormItem component*/}
<Input type="number" placeholder="Take a guess" required {...field} /> {/*Defines input field for FormControl component*/}

```

Now that we understand the meaning of the component, let's create the simplest version of the form that we will use in our application.

**The code located in the `GuessForm.jsx` file should look like this:**

```jsx
import { Form, FormControl, FormField, FormItem } from './ui/form';
import { Input } from './ui/input';

const GuessForm = ({ form }) => {
  return (
    <Form {...form}>
      <form className="space-y-8">
        <FormField
          control={form.control}
          name="guess"
          render={({ field }) => (
            <FormItem>
              <FormControl>
                <Input type="number" placeholder="Take a guess" required {...field} />
              </FormControl>
            </FormItem>
          )}
        />
      </form>
    </Form>
  );
};

export default GuessForm;
```

First, let's understand what props are:
Props is a special keyword in React that stands for properties and is used for passing data from one component to another. Data with props is passed in a unidirectional flow from parent to child.

Go back to App.jsx and add form prop to `<GuessForm/>` component, then return back to `GuessForm.jsx` file

```jsx
<CardContent>
  <GuessForm form={form} />
  {guessedNumber}
</CardContent>
```

### Let's add buttons and submission functionality to our form

We will add props for `handleFormSubmit`, `guessState`, and `generateNewNumber`. These props will assist us in submitting the form, changing the input field state, and generating a new number after the user has made the correct guess.

Now, let's add the new props to our function. Inside the rounded brackets, add the necessary props:

```jsx
const GuessForm = ({ form, handleFormSubmit, guessState, generateNewNumber }) => {};
```

- form: This prop represents the current state of the form, including the input values and any other form-related data.

- handleFormSubmit: This prop is a function that will be called when the form is submitted. It handles the submission process, such as validating input and processing the form data.

- guessState: This prop represents the current state of the user's guess. It holds one of the following values: 'lower', 'higher', 'correct', or null if no guess has been made yet.

- generateNewNumber: This prop is a function that generates a new number for the user to guess. It's called after the user has successfully guessed the correct number, triggering a new round of the guessing game.

Let me shortly describe the code that we will create in the next step:

```jsx
{/*The component takes props as mentioned above.*/}

<Form {...form}></Form> {/*Uses the custom Form component, spreading the form prop to pass down form-related props or methods.*/}

<form onSubmit={form.handleSubmit(handleFormSubmit)}></form> {/* A standard HTML form element that handles submission using the handleSubmit method from react-hook-form, calling the handleFormSubmit function upon submission.*/}

<FormField control={form.control} name="guess"> {/*A custom form field component that registers the input with the form's control system, specifically for the "guess" input.*/}

{/*Inside FormField: Utilizes FormItem and FormControl for additional structure and styling.*/}

<Input> {/*A controlled input component for entering the guess. It is set to accept numbers, with a placeholder text "Take a guess". It's required, and it becomes disabled if the guessState is 'correct', preventing further input.*/}

disabled={guessState == 'correct' ? true : false}: { /*Disables the input if the user has guessed correctly.*/}

{/*Export: Exports the GuessForm component for use elsewhere in the application.*/}
```

**For now the code in your GuessForm file should look like this:**

```jsx
import { Form, FormControl, FormField, FormItem } from './ui/form';
import { Input } from './ui/input';

const GuessForm = ({ form, handleFormSubmit, guessState, generateNewNumber }) => {
  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(handleFormSubmit)} className="space-y-8">
        <FormField
          control={form.control}
          name="guess"
          render={({ field }) => (
            <FormItem>
              <FormControl>
                <Input
                  type="number"
                  placeholder="Take a guess"
                  required
                  disabled={guessState == 'correct' ? true : false}
                  {...field}
                />
              </FormControl>
            </FormItem>
          )}
        />
      </form>
    </Form>
  );
};

export default GuessForm;
```

**Let's finalize our component by adding all of the necessary buttons to allow the user to submit their guess and generate a new number by restarting the game.**

We will create two buttons that will be displays a "Generate New Number" if the guess is correct, allowing the user to start a new game. Otherwise, a "Submit" button is shown for submitting guesses. This conditional rendering is based on guessState using a ternary operator.

Add the following code after the `FormField` component in the form HTML element and add `Button` as an import:

```jsx
import { Button } from './ui/button';

<div className="flex flex-row justify-center">
  {guessState == 'correct' ? (
    <Button className="bg-green-600 hover:bg-green-700" onClick={() => generateNewNumber()}>
      Generate New Number
    </Button>
  ) : (
    <Button disabled={guessState == 'correct' ? true : false} type="submit">
      Submit
    </Button>
  )}
</div>;
```

The final version of the file, named `GuessForm.jsx`, should look like this:

```jsx
import { Form, FormControl, FormField, FormItem } from './ui/form';
import { Input } from './ui/input';
import { Button } from './ui/button';

const GuessForm = ({ form, handleFormSubmit, guessState, generateNewNumber }) => {
  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(handleFormSubmit)} className="space-y-8">
        <FormField
          control={form.control}
          name="guess"
          render={({ field }) => (
            <FormItem>
              <FormControl>
                <Input
                  type="number"
                  placeholder="Take a guess"
                  required
                  {...field}
                  disabled={guessState == 'correct' ? true : false}
                />
              </FormControl>
            </FormItem>
          )}
        />
        <div className="flex flex-row justify-center">
          {guessState == 'correct' ? (
            <Button className="bg-green-600 hover:bg-green-700" onClick={() => generateNewNumber()}>
              Generate New Number
            </Button>
          ) : (
            <Button disabled={guessState == 'correct' ? true : false} type="submit">
              Submit
            </Button>
          )}
        </div>
      </form>
    </Form>
  );
};

export default GuessForm;
```

### Head back to the App.jsx file and add necessary props for our form.

Add missing props to the GuessForm component:

```jsx
<GuessForm
  form={form}
  handleFormSubmit={handleSubmit}
  guessState={guessState}
  generateNewNumber={generateNewNumber}
/>
```

### Let's add a section to the form that dynamically displays key information related to the guessing game.

This section will render based on certain conditions:

- If the guess is correct, it will display the guessed number.
- If a guess has been made, it will show the user's guess.
- If attempts have been made, it will indicate the number of attempts.

To implement this, add the following code snippet after the FormField component in the CardContent component:

```jsx
<div className="flex flex-col items-center mt-5">
  {guessState == 'correct' ? <p>Guessed Number: {guessedNumber}</p> : null}
  {guess ? <p>Your Guess: {guess}</p> : null}
  {attempts ? <p>Attempts: {attempts}</p> : null}
</div>
```

Now App.jsx file should look like this:

```jsx
import { useEffect, useState } from 'react';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from './components/ui/card';
import { useForm } from 'react-hook-form';
import GuessForm from './components/GuessForm';

const MAX_NUMBER = 1000;
const App = () => {
  const getRandomNumber = () => Math.floor(Math.random(0) * MAX_NUMBER);

  const [guessedNumber, setGuessedNumber] = useState();
  const [guess, setGuess] = useState();
  const [guessState, setGuessState] = useState('hidden');
  const [attempts, setAttempts] = useState(0);

  useEffect(() => {
    setGuessedNumber(getRandomNumber);
  }, []);

  const generateNewNumber = () => {
    setGuessedNumber(getRandomNumber);
    setGuess();
    setAttempts(0);
    setGuessState('hidden');
  };

  const form = useForm({
    defaultValues: { guess: '' },
  });

  const checkGuess = (guess) => {
    if (guess > guessedNumber) setGuessState('lower');
    else if (guess < guessedNumber) setGuessState('higher');
    else if (guess == guessedNumber) setGuessState('correct');
  };

  const handleSubmit = (data) => {
    setGuess(data.guess);
    checkGuess(data.guess);
    setAttempts(attempts + 1);
    form.reset();
  };

  return (
    <main className="h-screen w-full">
      <div className="h-full flex flex-col items-center justify-center">
        <Card>
          <CardHeader>
            <CardTitle>The Higher Lower Game</CardTitle>
            <CardDescription>
              Think of a number, and I&apos;ll tell you if my guess is higher or lower.
            </CardDescription>
          </CardHeader>
          <CardContent>
            <GuessForm
              form={form}
              handleFormSubmit={handleSubmit}
              guessState={guessState}
              generateNewNumber={generateNewNumber}
            />
            <div className="flex flex-col items-center mt-5">
              {guessState == 'correct' ? <p>Guessed Number: {guessedNumber}</p> : null}
              {guess ? <p>Your Guess: {guess}</p> : null}
              {attempts ? <p>Attempts: {attempts}</p> : null}
            </div>
          </CardContent>
        </Card>
      </div>
    </main>
  );
};

export default App;
```

### Let's create the AlertBox component, which will display different types of alerts based on the alertState prop.

This component will accept a prop named alertState, determining the type of alert to display. Depending on the value of alertState, the component will render different alerts.

- When alertState is set to 'lower', the component will render an alert indicating that the user's guess is lower than the target number. It will display a downward arrow icon, a title indicating "Wrong guess!", and a description advising the user to try guessing lower.

- When alertState is set to 'higher', the component will render an alert indicating that the user's guess is higher than the target number. It will display an upward arrow icon, a title indicating "Wrong guess!", and a description advising the user to try guessing higher.

- When alertState is set to 'correct', the component will render an alert indicating that the user has correctly guessed the number. It will display a checkmark icon, a title indicating "Congratulations!", and a description indicating the successful guess.

- If alertState does not match any of the specified values ('lower', 'higher', 'correct'), the component will not render any alert.

#### Go to **./src/components** and create a file named **AlertBox.jsx**

**Import necessary dependencies:**

```jsx
import { Check, ArrowDown, ArrowUp } from 'lucide-react';
import { Alert, AlertDescription, AlertTitle } from './ui/alert';
```

**Define the functional component AlertBox:**

```jsx
const AlertBox = ({ alertState }) => {
  // Component logic will be added here
};
```

**Implement conditional rendering based on the alertState prop:**

```jsx
const AlertBox = ({ alertState }) => {
  if (alertState === 'lower') {
    return (
      <Alert>
        <ArrowDown className="h-4 w-4" />
        <AlertTitle>Wrong guess!</AlertTitle>
        <AlertDescription>Try to guess lower</AlertDescription>
      </Alert>
    );
  } else if (alertState === 'higher') {
    return (
      <Alert>
        <ArrowUp className="h-4 w-4" />
        <AlertTitle>Wrong guess!</AlertTitle>
        <AlertDescription>Try to guess higher</AlertDescription>
      </Alert>
    );
  } else if (alertState === 'correct') {
    return (
      <Alert>
        <Check className="h-4 w-4" />
        <AlertTitle>Congratulations!</AlertTitle>
        <AlertDescription>You have guessed the number!</AlertDescription>
      </Alert>
    );
  } else {
    return null;
  }
};
```

**Export the AlertBox component:**

```jsx
export default AlertBox;
```

**The final version of the file should look like this:**

```jsx
import { Check, ArrowDown, ArrowUp } from 'lucide-react';
import { Alert, AlertDescription, AlertTitle } from './ui/alert';

const AlertBox = ({ alertState }) => {
  if (alertState == 'lower') {
    return (
      <Alert>
        <ArrowDown className="h-4 w-4" />
        <AlertTitle>Wrong guess!</AlertTitle>
        <AlertDescription>Try to guess lower</AlertDescription>
      </Alert>
    );
  } else if (alertState == 'higher') {
    return (
      <Alert>
        <ArrowUp className="h-4 w-4" />
        <AlertTitle>Wrong guess!</AlertTitle>
        <AlertDescription>Try to guess higher</AlertDescription>
      </Alert>
    );
  } else if (alertState == 'correct') {
    return (
      <Alert>
        <Check className="h-4 w-4" />
        <AlertTitle>Congratulation!</AlertTitle>
        <AlertDescription>You have guessed the number!</AlertDescription>
      </Alert>
    );
  } else {
    return null;
  }
};

export default AlertBox;
```

### Head back to App.jsx file and add AlertBox after key information section of the game

Import AlertBox to the App.jsx file:

```jsx
import AlertBox from './components/AlertBox';
```

Add AlertBox after key information section of the game

```jsx
<AlertBox alertState={guessState} />
```

**The final version of the App.jsx file should look like this:**

```jsx
import { useEffect, useState } from 'react';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from './components/ui/card';
import { useForm } from 'react-hook-form';
import AlertBox from './components/AlertBox';
import GuessForm from './components/GuessForm';

const MAX_NUMBER = 1000;
const App = () => {
  const getRandomNumber = () => Math.floor(Math.random(0) * MAX_NUMBER);

  const [guessedNumber, setGuessedNumber] = useState();
  const [guess, setGuess] = useState();
  const [guessState, setGuessState] = useState('hidden');
  const [attempts, setAttempts] = useState(0);

  useEffect(() => {
    setGuessedNumber(getRandomNumber);
  }, []);

  const generateNewNumber = () => {
    setGuessedNumber(getRandomNumber);
    setGuess();
    setAttempts(0);
    setGuessState('hidden');
  };

  const form = useForm({
    defaultValues: { guess: '' },
  });

  const checkGuess = (guess) => {
    if (guess > guessedNumber) setGuessState('lower');
    else if (guess < guessedNumber) setGuessState('higher');
    else if (guess == guessedNumber) setGuessState('correct');
  };

  const handleSubmit = (data) => {
    setGuess(data.guess);
    checkGuess(data.guess);
    setAttempts(attempts + 1);
    form.reset();
  };

  return (
    <main className="h-screen w-full">
      <div className="h-full flex flex-col items-center justify-center">
        <Card>
          <CardHeader>
            <CardTitle>The Higher Lower Game</CardTitle>
            <CardDescription>
              Think of a number, and I&apos;ll tell you if my guess is higher or lower.
            </CardDescription>
          </CardHeader>
          <CardContent>
            <GuessForm
              form={form}
              handleFormSubmit={handleSubmit}
              guessState={guessState}
              generateNewNumber={generateNewNumber}
            />
            <div className="flex flex-col items-center mt-5">
              {guessState == 'correct' ? <p>Guessed Number: {guessedNumber}</p> : null}
              {guess ? <p>Your Guess: {guess}</p> : null}
              {attempts ? <p>Attempts: {attempts}</p> : null}
            </div>
            <AlertBox alertState={guessState} />
          </CardContent>
        </Card>
      </div>
    </main>
  );
};

export default App;
```

## Congratulations on completing Code Camp 6! 🎉

You've successfully built the application, honing your skills along the way. This achievement marks a significant milestone in your coding journey.

If you have any questions about the application you've built or need further assistance with any aspect of the code, feel free to ask. We're here to support your continued growth as a developer.

Keep exploring, keep coding, and remember, every challenge you conquer brings you one step closer to your goals!

Best regards,\
The Raccoons!
