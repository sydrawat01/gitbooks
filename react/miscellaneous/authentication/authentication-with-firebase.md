---
description: Using Firebase REST API for authentication basics.
---

# 🔐 Authentication with Firebase

## Creating a Firebase Project

We need to create a Firebase project, but we will be using the `Authentication` feature instead of the `Realtime database` now. If you already have a Firebase project set up, you can use the `Authentication` feature for that project.

### Sign-in providers

Firebase offers a range of sign-in providers. You can select any one from these.

![Firebase sign-in providers.](<../../.gitbook/assets/Screenshot 2021-05-07 at 13.01.16.png>)

Here, we will be using the generic authentication flow using the `Email/Password` sign-in provider. We need to `enable` this sign-in provider by clicking on it and changing the configuration to enable it. We are using this sign-in provider to mimic our own Authentication API.

![Enable email/password sign-in provider.](<../../.gitbook/assets/Screenshot 2021-05-07 at 13.04.04.png>)

### Firebase REST API

Now, we need to connect the REST API to handle adding new users and allowing existing users to log in to our web application. This can be done by referring to the docs [here](https://firebase.google.com/docs/reference/rest/auth).

## Registering New Users

With our Firebase authentication set up, we can now add users to our application, and give them appropriate authentication tokens to access protected resources within our web app.

I'll be using a custom hook to set up a fetch request to the Firebase API to add new users via authentication. Here is the custom hook:

{% code title="use-http.js" %}
```jsx
import { useState, useCallback } from 'react';

export const useHttp = () => {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const sendRequest = useCallback(async (reqConfig) => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch(reqConfig.url, {
        method: reqConfig.method ? reqConfig.method : 'GET',
        headers: reqConfig.headers ? reqConfig.headers : {},
        body: reqConfig.body ? JSON.stringify(reqConfig.body) : null,
      });
      const data = await response.json();
      if (!response.ok) {
        if(data.error) {
          throw new Error(data.error.message);
        }
      }
    } catch (error) {
      setError(error.message);
    }
    setIsLoading(false);
  }, []);

  return {
    isLoading,
    error,
    sendRequest,
  };
};

```
{% endcode %}

Next, we will use this custom hook in our `AuthForm` component to register a user to our app.

### Sign up with email / password

To use the **Sign-up** REST API from Firebase, we need to use [these docs](https://firebase.google.com/docs/reference/rest/auth#section-create-email-password).

![Firebase REST API for sign-up authentication.](<../../.gitbook/assets/Screenshot 2021-05-07 at 15.03.47.png>)

{% code title="AuthForm.js" %}
```jsx
import {useState, useRef} from 'react';
import {useHttp} from '../../hooks/use-http';
const API_KEY = 'xxx_API_KEY_xxx';

const AuthForm = () => {
  const {isLoading, error, sendRequest: userAuth} = useHttp();
  const [isLogin, setIsLogin] = useState(true);
  const emailInputRef = useRef();
  const pwdInputRef = useRef();
  const switchAuthModeHandler = () => {
    setIsLogin(prevState => !prevState);
  }
  const submitFormHandler = (event) => {
    event.preventDefault();
    const enteredEmail = emailInputRef.current.value();
    const enteredPwd = pwdInputRef.current.value();
    if(isLogin) {
    }else {
      userAuth{
        url: `https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=${API_KEY}`,
        method: 'POST,
        headers: {
          'Content-Type': 'application/json',
        },
        body: {
          email: enteredEmail,
          password: enteredPwd,
          returnSecureToken: true,
        },
      })
    }
  }
  return (
    <section>
      <h1>{isLogin ? 'Login' : 'Sign Up'}</h1>
      <form onSubmit={submitFormHandler}>
        <div>
          <label htmlFor="email">Your Email</label>
          <input type="email" id="email" ref={emailInputRef} required />
        </div>
        <div>
          <label htmlFor="password">Your Password</label>
          <input type="password" id="password" ref={pwdInputRef} required />
        </div>
        <div>
          <button>{isLogin ? 'Login' : 'Create Account'}</button>
          <button
            type="button"
            onClick={switchAuthModeHandler}
          >
            {isLogin ? 'Create new account' : 'Login with existing account'}
          </button>
        </div>
      </form>
    </section>
  );
}
```
{% endcode %}

After we successfully register a new user to our app, we can see the use with email and password added to our Firebase console:

![Firebase Authentication console.](<../../.gitbook/assets/Screenshot 2021-05-07 at 15.06.43.png>)

Firebase automatically creates the `Authentication token` for each user, which is under the `User UID` tab.

## Existing User Login

After successfully registering a user to our Firebase back-end API, it is time we add the logic to allow already registered users to gain access to the protected resources in our app. We would need to refer [these docs](https://firebase.google.com/docs/reference/rest/auth#section-sign-in-email-password).

### Sign in with email / password

We will use the same custom HTTP hook that we created before to issue a `POST` request to verify the password at the `verifyPassword` API endpoint.

**Method:** POST

**Content-Type:** application/json

**Endpoint:**&#x20;

```
https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=[API_KEY]
```

![Request and Response payloads for sign in API endpoint.](<../../.gitbook/assets/Screenshot 2021-05-07 at 15.36.46.png>)

Logic for sign-in of a user and sending a `POST` request to the API endpoint:

```jsx
const API_KEY = 'xxx_API_KEY_xxx';
const SIGN_UP_URL = `https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=${API_KEY}`;
const SIGN_IN_URL = `https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=${API_KEY}`
//...
//...
  const submitFormHandler = (event) => {
    //...
    //...
    if(isLogin){
      url = SIGN_IN_URL;
    } else {
      url = SIGN_UP_URL;
    }
    userAuth({
        url,
        method: 'POST,
        headers: {
          'Content-Type': 'application/json',
        },
        body: {
          email: enteredEmail,
          password: enteredPwd,
          returnSecureToken: true,
        },
      })
    
  }
```
