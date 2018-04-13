# Two Factor Authentication

For business that require extra security for their customer, Freshcom allow you to use two factor authentication \(TFA\). If you are selling a secret weapon, you probably want to use this to be double sure the customer is who they say there are.

{% hint style="info" %}
In order to actually send the verification code needed for TFA to the customer's email or phone number you must create a notification trigger for the appropriate event. Please see our guide on [notification](../notification/) for sending email and text message.
{% endhint %}



## Enable TFA

TFA is enabled on a user specific level, meaning you can have one user using TFA and another not using TFA. There are two method of TFA:

* SMS TFA - The one time password is sent using text messages, this method require the user have a verified phone number.
* Email TFA - The one time password is sent using email, this method require the user to have a verified email address.

You can also make TFA as the default method for your entire account by changing the default authentication method settings through the Dashboard. Note that changing this will not effect existing user's it will only effect new user.

You can enable a TFA for a user through the Dashboard by changing the authentication method of the user or customer. Note that when you are changing a customer's authentication method through the Dashboard you are actually changing the associated user's authentication method of that customer. If you take a look at our API reference you will notice the customer resource itself does not actually have a authentication method field.

You can also prompt your customer to enable TFA in your client side application. If the customer already have all the required information verified, then this can be enable directly updating the authentication method of their associated user resource. 

If they do not have the require information, then depending on the TFA method there are a few extra step needed to get their information verified.

1. Prompt the customer to enter the required information, phone for SMS TFA or email for Email TFA.
2. Create a phone verification code or a email verification code.
3. Prompt the customer to enter the verification code.
4. Submit the code together with the updated information.

```javascript
import freshcom from 'freshcom-sdk'

// Assuming user logged in and using user access token.
freshcom.createAndSetAccessToken({
  username: 'joe@example.com',
  password: 'supersecurepassword',
  scope: 'aid:aid-test-74b901b2-32f9-4e3b-8d4d-4eca2360550c',
  grant_type: 'password'
})

// 1. Prompt the user to enter the required information.
let phoneNumebr = '...'
 
// 2. Create a verification code, note the actual code is not returned.
return freshcom.createPhoneVerificationCode({
  phoneNumber: phoneNumber
})

// 3. Prompt the user to enter the verification code.
let verificationCode = '...'

// 4. Submit the code and update the user information.
freshcom.updateUser({
  authMethod: 'tfa_phone',
  phoneNumber: phoneNumber,
  phoneVerificationCode: verificationCode
})
```



## Register a Customer with TFA

Registering a customer with TFA require a few extra steps compare to registering a customer without TFA.

1. Prompt the customer to enter the required information, phone for SMS TFA or email for Email TFA.
2. Create a phone verification code or a email verification code.
3. Prompt the customer to enter the verification code.
4. Submit the code together with the all the other information.
5. _\(optional\)_ Login the customer.

```javascript
import freshcom from 'freshcom-sdk'

freshcom.createAndSetAccessToken({
  fresh_token: 'prt-test-aba1d92c-defb-478e-a031-40b5c27171c7',
  grant_type: 'refresh_token'
})

// 1. Prompt the user to enter the required information.
let customer = {
  username: '...',
  password: '...',
  phoneNumber: '...'
}
 
// 2. Create a verification code, note the actual code is not returned.
return freshcom.createPhoneVerificationCode({
  phoneNumber: customer.phoneNumber
})

// 3. Prompt the user to enter the verification code.
let customer.phoneVerificationCode = '...'

// 4. Submit the code and the user information.
freshcom.createCustomer(customer).then(function (response) {
  // 5. (optional) Login the customer by creating its access token.
  return freshcom.createAndSetAccessToken({
    username: customer.username,
    password: customer.password,
    otp: customer.phoneVerificationCode,
    grant_type: 'password'
  })
})
```

Note the one time password is the same a the phone verification code only creating a registered customer or when updating a guest customer to a registered customer.

## Login a Customer with TFA

Login a customer with TFA is a 3 step process:

1. Prompt user to enter their username and password.
2. Attempt to create a access token to trigger a creation of one time password.
3. Prompt the user to enter the one time password.
4. Create access token using the username, password and one time password.

```javascript
import freshcom from 'freshcom-sdk'

// 1. Prompt user to enter their username and password.
let username = '...'
let password = '...'

// 2. Attempt to create access token to trigger a creation and sending of OTP.
freshcom.createAndSetAccessToken({
  username: username,
  password: password,
  grant_type: 'password'
}).catch(function (response) {
  // Create a access token for a user with TFA without providing a OTP will 
  // always cause an error, this is intentional the creation and sending of OTP
  // will still be triggered.
})

// 3. Prompt user to enter the OTP.
let otp = '...'

// 4. Create access token using the all the information
freshcom.createAndSetAcccessToken({
  username: username,
  password: password,
  otp: otp,
  grant_type: 'password'
})
```

## Next Step

