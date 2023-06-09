---
description: >-
  Unique identifier which is a JWT format toke, for unique auth purposes, and
  enhanced security reasons.
---

# 🔐 Using the Auth Token

## Using the Auth Token

We can use the `Authentication Token` received in the previous section, from the app wide state, to then request access to the protected resources in our app, or to the endpoint API or the back-end server. In this section, we will learn how to change the password for the currently logged-in user, using the `Authentication Token` received from the endpoint API and which is stored in the `Redux Store`.

## Changing User Password

We can change a user's password by issuing an HTTP `POST` request to the Auth `setAccountInfo` endpoint. The docs related to this can be found [here](https://firebase.google.com/docs/reference/rest/auth#section-change-password).

&#x20;**Method:** POST

&#x20;**Content-Type:** application/json&#x20;

**Endpoint**

```
https://identitytoolkit.googleapis.com/v1/accounts:update?key=[API_KEY]
```

![Request and Response payloads for changing password endpoint API.](<../../.gitbook/assets/Screenshot 2021-05-07 at 21.30.22.png>)

### Create & Set New Password

We need to get the user input containing the new password from the `ProfileForm` component. Here we will use `useRef` to get the value if the input field. We will then send the `newPassword` to the `UserProfile` component, where we will send a `POST` request to the Firebase end-point API to change the password for the current `token` which is stored in the `Redux Store`.

{% code title="ProfileForm.js" %}
```jsx
import {useRef} from 'react';
//...

const ProfileForm = (props) => {
  const newPasswordInput = useRef();
  
  const submitFormHandler = (event) => {
    event.preventDefault();
    const newPassword = newPasswordInput.current.value;
    // ADD: password validation with regex!
    props.onChangePassword(newPassword);
  }
  
  return(
    <form onSubmit={submitFormHandler}>
      <div>
        <label>Enter New Password</label>
        <input type="password" ref={newPasswordInput} />
      </div>
      <div>
        <button>Change Password</button>
      </div>
    </form>
  );
}

export default ProfileForm;
```
{% endcode %}

{% code title="UserProfile.js" %}
```jsx
//...
import {useHttp} from '../../hooks/use-http';
import {useSelector} from 'react-redux';
const API_KEY = 'xxx_API_KEY_xxx';
//...

const UserProfile = () => {
  const auth = useSelector(state => state.auth);
  const {token, isLoggedIn} = auth;
  const {isLoading, error, sendRequest: changePwd} = useHttp();
  
  const changePasswordHandler = (newPwd) => {
    changePwd({
      url: `https://identitytoolkit.googleapis.com/v1/accounts:update?key=${API_KEY}`,
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: {
        idToken: token,
        password: newPwd,
        returnSecureToken: false,
      }
    });
  }
  
  return(
    //...
    //...
    <ProfileForm onChangePassword={changePasswordHandler} />
  );
}
```
{% endcode %}

### Confirm Password Change

Once the user is logged in, we need to navigate to the `Profile` page component, where we can change the password for the currently logged in user. After successfully changing the password, we can refresh the app, and try to login using the old password. It should return a `Modal / console.log` (based on the logic you have for handling error cases) saying that the password is invalid or something like this : `INVALID_PASSWORD`

This is how we can use the `Authentication Token` for requests to authenticated API end-points. It will depend on the end-point on how the `Token` must be added. Sometimes it may need to be added to the `request body`, or other times as a `query parameter` to the API URL. For some APIs, we may need to add the `Authentication Token` to the `headers` object! In the end, it depends on the API that you are using.

```javascript
//Request body
body: {
  idToken: thisisatoken, 
}

//Query parameter
const url = 'https://api.endpoint/data&token=thisisatoken';

//Headers object
headers: {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer thisisatoken'
}
```
