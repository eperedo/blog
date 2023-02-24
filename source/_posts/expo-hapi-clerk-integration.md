---
title: Validate jwt tokens using Clerk, HapiJs and Expo
date: 2023-02-24 16:30:00
---

I was working on a project that is using [clerk](https://clerk.dev) for its authentication layer and it really looks great, but this particular project was built with [hapi](https://hapi.dev) on the backend and [expo](https://expo.dev) for building the frontend so today I'm going to share a pretty basic way to validate sessions using the clerk sdk with hapi.

## TL;DR

Here's the [repo with the full code](https://github.com/eperedo/hapi-expo-clerk) in case you don't want to spend time reading me.

## Creating the expo app

There's a [Get started with expo](https://clerk.dev/docs/quickstarts/get-started-with-expo) page on the clerk's docs, but to be honest it was kinda confusing and I got a couple of errors so I'm going to describe my personal experience on how I did all the steps.

The easy way to create an expo app is using the [create-expo-app](https://docs.expo.dev/workflow/glossary-of-terms/#create-expo-app) package

```bash
npx create-expo-app myapp
```

You can replace **myapp** with your application's name.

## Installing clerk-expo

In order to install **@clerk/clerk-expo** we need to install react-dom. Yeah weird, right? But this library [has it as a peerDependency](https://github.com/clerkinc/javascript/blob/main/packages/expo/package.json#L48). Maybe kinda makes sense because you can use expo to build web apps as well using [react-native-web](https://necolas.github.io/react-native-web/) and I'm asumming react-dom is a dependency there? But this is actually my first experience with clerk so I don't really know the reason.

```bash
npm i react-dom
```

Ok now you can install the [clerk-expo](https://github.com/clerkinc/javascript/tree/main/packages/expo) library without any problems

```bash
npm install @clerk/clerk-expo
```

## Adding the ClerkProvider

Ok now we can start writing code, first let's wrap our whole app with the **ClerkProvider** component. This provider needs two things: the first one is the public key. You can get it from your dashboard - select your app - look for the **Api Keys** option in the left menu and then copy the **Publishable key**.

```jsx
import React from "react";
import { View, Text } from "react-native";
import { ClerkProvider } from "@clerk/clerk-expo";

function App() {
  return (
    <ClerkProvider publishableKey="pk_test_####">
      <View>
        <Text>My App</Text>
      </View>
    </ClerkProvider>
  );
}
```

Great, but remember we needed **two** things for the clerk provider? The second thing is a prop called **tokenCache** that is used by clerk to save the jwt client once our users are logged in. For this let's just follow the recomendation of the clerk team and use the [securestore](https://docs.expo.dev/versions/latest/sdk/securestore/) library.

```bash
npx expo install expo-secure-store
```

And in the code it looks like this:

```jsx
// the rest of the code is omitted for brevity
import * as SecureStore from "expo-secure-store";

const tokenCache = {
  getToken(key) {
    return SecureStore.getItemAsync(key);
  },
  saveToken(key, value) {
    return SecureStore.setItemAsync(key, value);
  },
};

function App() {
  return (
    <ClerkProvider tokenCache={tokenCache} publishableKey="pk_test_####">
      <View>
        <Text>My App</Text>
      </View>
    </ClerkProvider>
  );
}
```

## Starting the app

Let's start our app first using the expo cli:

```bash
npx expo start
```

## Creating our Login Form

I'm not going to focus on the UI/UX since that's way out of the scope of the post, so yeah it's going to look awful. Our app is composed of basically three actions: Login, fetch user orders (to the hapi API) and logout.

Let's create a new component called **OrdersApp** and inside of it we're going to implement all of the different actions we need starting with our Login Form:

```jsx
import { View, Text, Pressable, TextInput } from "react-native";

function OrdersApp() {
  const [email, setEmail] = React.useState();
  const [pwd, setPwd] = React.useState();

  async function onLogin() {
    console.log("Login Integration with Clerk Here");
  }

  return (
    <View style={{ paddingVertical: 50 }}>
      <View style={{ marginVertical: 10 }}>
        <TextInput
          style={{
            borderWidth: 2,
            borderColor: "gray",
            padding: 16,
          }}
          placeholder="Email"
          onChangeText={setEmail}
          value={email}
        />
      </View>
      <View style={{ marginVertical: 10 }}>
        <TextInput
          style={{
            borderWidth: 2,
            borderColor: "gray",
            padding: 16,
          }}
          placeholder="Password"
          onChangeText={setPwd}
          value={pwd}
          autoComplete="password"
          secureTextEntry
        />
      </View>

      <Pressable
        style={{ backgroundColor: "blue", padding: 16 }}
        onPress={onLogin}
      >
        <Text style={{ color: "#fff", textAlign: "center" }}>Login</Text>
      </Pressable>
    </View>
  );
}
```

Nothing crazy here two TextViews to handle the username and password and the button where we have the part we care about: the login process with clerk. Let's complete the logic inside the **onLogin** function. The first thing to do is importing the **[useSignIn](https://clerk.dev/docs/reference/clerk-react/usesignin)** hook from clerk-expo.

```js
import { ClerkProvider, useSignIn } from "@clerk/clerk-expo";
```

Now let's use our new hook and extract two methods: **signIn** and **setSession**.

```jsx
function OrdersApp() {
  const [email, setEmail] = React.useState();
  const [pwd, setPwd] = React.useState();
  const { signIn, setSession } = useSignIn();
  // rest of the component
}
```

and inside of the **onLogin** function we need to verify the credentials of the user using the **create** method from the **signIn** object. We pass an object with the attributes **identifier** and **password**. For this example the idenfitier is a valid email address.

```js
async function onLogin() {
  const signInResponse = await signIn.create({
    identifier: email,
    password: pwd,
  });
}
```

If the **create** process goes well clerk is giving us an identifier to create a session in the **createdSessionId** attribute and we're going to use it along the **setSession** method.

```js
async function onLogin() {
  const signInResponse = await signIn.create({
    identifier: email,
    password: pwd,
  });

  await setSession(completeSignIn.createdSessionId);
}
```

And let's include this component in our main **App** component.

```jsx
export default function App() {
  return (
    <ClerkProvider tokenCache={tokenCache} publishableKey="pk_test_####">
      <OrdersApp />
    </ClerkProvider>
  );
}
```

Our form looks like this:

![expo login form screen](https://user-images.githubusercontent.com/461124/221272739-7278ed75-002d-4dd3-a44d-23511da3f745.PNG)

## Showing user information after login is successful

The session is where we can get the information about our current user so let's show some user information. But you probably have noticed that we just called the setSession function but we don't save the result in a variable or state and that's on purpose because clerk already give us a hook to access the session information and that's the one we're going to import now **[useSession](https://clerk.dev/docs/reference/clerk-react/usesession)**.

```js
import { ClerkProvider, useSignIn, useSession } from "@clerk/clerk-expo";
```

and this hook give us the **session** object:

```js
function OrdersApp() {
  const { session } = useSession();
}
```

ok now we can make a simple validation: if the session is defined we can show the information of the current user, otherwise we go back to our login form because that means nobody is logged in.

```jsx
function OrdersApp() {
  const { session } = useSession();

  if (session) {
    return (
      <View>
        <Text>Email: {session.user.primaryEmailAddress.emailAddress}</Text>
      </View>
    );
  }

  return (
    // Code for the Login form we just wrote above
  );
}
```

Ok but we really don't have any credentials to test right now, so we need to open the [clerk dahsboard](https://dashboard.clerk.dev/) select the app we're working on and then go to the **Users** option in the left menu and then click on the **Create** button.

![clerk dashboard with the users option open](https://user-images.githubusercontent.com/461124/221275964-cd2ec05b-d77f-42fb-8978-b244162ffaf5.PNG)

This is going to open a modal where we can enter an email and a password. After that we're ready to test our fresh new user.

If we enter the email and password we're going to see a screen with the email of the user.

![expo signed in screen](https://user-images.githubusercontent.com/461124/221272737-7d7ca560-4acf-4762-9057-50fcbd6d9b77.PNG)

Great, we already have a login process working perfectly. But of course our user now is trapped inside this screen.

## Adding our Logout process

Let's fix that by adding a logout button when a session is already present:

```jsx
function OrdersApp() {
  const { session } = useSession();

  function onLogOut() {
    // clerk logout code here
  }

  if (session) {
    return (
      <View>
        <Text>Email: {session.user.primaryEmailAddress.emailAddress}</Text>
        <View style={{ marginVertical: 10 }}>
          <Pressable
            style={{ backgroundColor: "blue", padding: 16 }}
            onPress={onLogOut}
          >
            <Text style={{ color: "#fff" }}>Log Out</Text>
          </Pressable>
        </View>
      </View>
    );
  }
}
```

pretty similar to our login process clerk give us a hook to perform a sign out of our current user, so we need to import that first: **useAuth**

```js
import {
  ClerkProvider,
  useSignIn,
  useSession,
  useAuth,
} from "@clerk/clerk-expo";
```

and now inside our **OrdersApp** component we can get the **signOut** method and simply call it in our **onLogOut** function.

```js
function OrdersApp() {
  // rest of the code omitted for brevity
  const { signOut } = useAuth();

  async function onLogOut() {
    await signOut();
  }
}
```

and just like that if we press the button we're going to see the Login Form again because the **session** object from the **useSession** hook is going to be empty. Aren't these react hooks a beautiful thing, are they?

![expo logout screen](https://user-images.githubusercontent.com/461124/221272740-f0114c33-9b5b-4426-9475-e5e58216c879.PNG)

And the last thing we need to build for our UI is an action that makes the http request and fetch all the orders. This request is going to hit our hapi server that we're going to create later in this post.

```jsx
function OrdersApp() {
  const { session } = useSession();

  function onFetchApi() {
    // fetch request here
  }

  if (session) {
    return (
      <View>
        <Text>Email: {session.user.primaryEmailAddress.emailAddress}</Text>
        <View style={{ marginVertical: 10 }}>
          <Pressable
            style={{ backgroundColor: "blue", padding: 16 }}
            onPress={onFetchApi}
          >
            <Text style={{ color: "#fff" }}>Fetch Data from API</Text>
          </Pressable>
        </View>
        <View style={{ marginVertical: 10 }}>
          <Pressable
            style={{ backgroundColor: "blue", padding: 16 }}
            onPress={onLogOut}
          >
            <Text style={{ color: "#fff" }}>Log Out</Text>
          </Pressable>
        </View>
      </View>
    );
  }
}
```

basically we're copying the logout button and changing its text and the function for the **onPress** prop. Now let's write the code to make the http request using the **[fetch](https://developer.mozilla.org/es/docs/Web/API/Fetch_API/Using_Fetch)** api:

```jsx
function OrdersApp() {
  const [orders, setOrders] = useState([]);
  const { session } = useSession();

  async function onFetchApi() {
    const token = await session.getToken();
    const res = await fetch("http://localhost:3000/orders", {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });
    const json = await res.json();
    setOrders(json);
  }
}
```

First we need the jwt token so the server can verify our session and clerk give us the **getToken** method in the **session** object. This method return a string that represents the jwt token so the rest of the code is a pretty simple http request with fetch and we're including an authorization header with the jwt token we just got from clerk. Once the server has validated everything we can save the response in the state of our component using the **setOrders** method. We expect an array here so let's finish this part by iterating all the orders and show them in the UI.

```jsx
function OrdersApp() {
  const [orders, setOrders] = useState([]);
  const { session } = useSession();

  if (session) {
    return (
      <View>
        <Text>Email: {session.user.primaryEmailAddress.emailAddress}</Text>
        <View style={{ marginVertical: 10 }}>
          <Pressable
            style={{ backgroundColor: "blue", padding: 16 }}
            onPress={onFetchApi}
          >
            <Text style={{ color: "#fff" }}>Fetch Data from API</Text>
          </Pressable>
        </View>
        {orders.map((order) => {
          return (
            <View key={order.orderId} style={{ padding: 16 }}>
              <Text>Order #{order.orderId}</Text>
              <Text>Products purchased: {order.totalProducts}</Text>
              <Text>Total: ${order.amount}</Text>
            </View>
          );
        })}
        <View style={{ marginVertical: 10 }}>
          <Pressable
            style={{ backgroundColor: "blue", padding: 16 }}
            onPress={onLogOut}
          >
            <Text style={{ color: "#fff" }}>Log Out</Text>
          </Pressable>
        </View>
      </View>
    );
  }
}
```

![expo fetch orders screen](https://user-images.githubusercontent.com/461124/221272733-5d558167-d4ef-4414-8476-c57317bee956.PNG)

Ok we have everything done in the UI, of course our fetch api right now is not going to work because we don't have our server yet.

## Creating a HapiJs server

We can start working on the server side now, we're going to have a **/orders** endpoint where we are going to validate the jwt that clerk has generated in our expo app and if the token is valid our endpoint will return a list of orders for the current user.

I'm going to create another folder called **server** just to keep things clear and create a new npm project

```bash
# inside server folder
npm init -f
```

now we need to install our dependencies: hapi and the clerk sdk for nodejs.

```bash
npm i @hapi/hapi @clerk/clerk-sdk-node
```

let's add the code necessary to have our hapi server

```js
// server/server.js
const Hapi = require("@hapi/hapi");

const init = async () => {
  const server = Hapi.server({
    port: 3000,
    host: "localhost",
  });

  await server.start();
  console.log("Server running on %s", server.info.uri);
};

process.on("unhandledRejection", (err) => {
  console.log(err);
  process.exit(1);
});

init();
```

it looks like a lot of code, but it's just the basic code we need to start a hapi server. Now let's include our **/orders** endpoint.

```js
// code omitted for brevity
const server = Hapi.server({
  port: 3000,
  host: "localhost",
});

server.route({
  method: "GET",
  path: "/orders",
  handler: (request, h) => {
    return {
      hello: "world",
    };
  },
});
// code omitted for brevity
```

from here if we can test our server

```bash
node server.js
# and we get
Server running on http://localhost:3000
```

if we do a curl to our server we're going to get a 404 because we don't have anything defined for our main endpoint.

```bash
curl http://localhost:3000
{"statusCode":404,"error":"Not Found","message":"Not Found"}
```

## Validating a clerk token in HapiJs

but we haven't used the clerk sdk yet, so let's change that by calling the library:

```js
const Hapi = require("@hapi/hapi");
const clerk = require("@clerk/clerk-sdk-node");
```

now we need to setup our api key, you can find it in the dashboard in the API KEYS option in the **Secret keys** section, click on **Reveal Key** then copy the value and use it in the code.

```js
const Hapi = require("@hapi/hapi");
const clerk = require("@clerk/clerk-sdk-node");
clerk.setClerkApiKey("sk_test_SECRET_KEY");
```

Great now let's actually validate the session directly in our **/orders** endpoint. Remember how easy was to do things like **signin**, **singout** on the front end side because of all the helpers clerk already give us? Well for the server side is kinda the same story because there's a method called **[verifySession](https://clerk.dev/docs/reference/node/available-methods#verify-session-session-id-session-token)**.

And we need two params for this method: the session id and the jwt token. From our expo app we are sending the jwt token in the **authorization** header so let's begin by getting that value;

```js
server.route({
  method: "GET",
  path: "/",
  handler: async (request, h) => {
    const authorization = request.headers.authorization;
    // let's remove the Bearer and whitespace part from the header
    const clerkToken = authorization.replace("Bearer ", "");
  },
});
```

in hapi you can get the authorization header from the **headers** object inside the **request**. After that we're just removing the **Bearer** word along the empty space and storing that in a **clerkToken** variable. With this we already have one of the params needed for the **verifySession** we talked about.

Now we need the **session id** and [according the docs](https://clerk.dev/docs/request-authentication/customizing-session-tokens) we can get this value from decoding our **clerkToken** variable. This can be done easily because the clerk sdk already contains a **[decodeJwt](https://github.com/clerkinc/javascript/tree/main/packages/backend#decodejwttoken-string)** method.

```js
// code omitted for brevity
handler: async (request, h) => {
  const authorization = request.headers.authorization;
  // let's remove the Bearer and whitespace part from the header
  const clerkToken = authorization.replace("Bearer ", "");
  const decodeInfo = clerk.decodeJwt(clerkToken);
  const sessionId = decodeInfo.payload.sid;
},
```

the **decodeJwt** returns 3 parts, but now we only need the payload object since here's where we can find the **sid** attribute that has the session id and now we have the 2 required params for our **verifySession** method.

```js
// code omitted for brevity
handler: async (request, h) => {
  const authorization = request.headers.authorization;
  // let's remove the Bearer and whitespace part from the header
  const clerkToken = authorization.replace("Bearer ", "");
  const decodeInfo = clerk.decodeJwt(clerkToken);
  const sessionId = decodeInfo.payload.sid;

  const clerkSession = await clerk.sessions.verifySession(sessionId, clerkToken);
  // verify if session is active
  if (clerkSession.status === 'active') {
    return [
      {
        orderId: 1,
        totalProducts: 2,
        amount: 200,
      },
      {
        orderId: 2,
        totalProducts: 2,
        amount: 200,
      }
    ];
  }
},
```

pretty easy to understand we call the method and since this is a promise we use the **await** keyword and if the promise was [fulfilled](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#description) we can start doing our main task that in this case is returning a list of orders. One last thing to do is dealing with the error case, since the **verifySession** method is a promise let's add a try/catch around the whole thing.

```js
// code omitted for brevity
handler: async (request, h) => {
  try {
    const authorization = request.headers.authorization;
    // let's remove the Bearer and whitespace part from the header
    const clerkToken = authorization.replace("Bearer ", "");
    const decodeInfo = clerk.decodeJwt(clerkToken);
    const sessionId = decodeInfo.payload.sid;

    await clerk.sessions.verifySession(sessionId, clerkToken);
    // in a real case scenario you can get this info from a database
    return [
      {
        orderId: 1,
        totalProducts: 2,
        amount: 200,
      },
      {
        orderId: 2,
        totalProducts: 2,
        amount: 200,
      }
    ];
  }
  catch(error) {
    return h
      .response({
        error: error.message,
      })
      .code(401);
  }
},
```

just a try/catch around our function and using the **h** variable to build a response object with a 401 error which is the standard http status code for this situation.

![a cat looking at a door with a no cat sign on it](https://http.cat/401)

we're done, just remember that in order to test this api from your expo app you need to start the server

```bash
node server.js
```

## Trying all the flow

Now we have everything to try our app: login, fetch data from an external api that validates our clerk session and the logout process.

![Expo app video](https://user-images.githubusercontent.com/461124/221290664-3d92a36a-f68d-4405-bed9-7ea05091cf46.gif)

## Final Thoughts

- Clerk is going to give every user an ID and you can save that in your database in order to associate a clerk user with your application's info.

```js
// code omitted for brevity
handler: async (request, h) => {
    const authorization = request.headers.authorization;
    // let's remove the Bearer and whitespace part from the header
    const clerkToken = authorization.replace("Bearer ", "");
    const decodeInfo = clerk.decodeJwt(clerkToken);
    const sessionId = decodeInfo.payload.sid;
    // get the user id from the sub attribute
    const clerkUserId = decodeInfo.payload.sub;

    await clerk.sessions.verifySession(sessionId, clerkToken);
    // simulating getting the info from a database library
    return myDbLibrary.get('select * from orders where user_id = ?', [clerkUserId]);
},
```

- For a better server side validation with hapi you can use the [hapi-auth-jwt2](https://www.npmjs.com/package/hapi-auth-jwt2). This is going to create a hapi strategy so you can validate ALL your routes without repeting the same code for each route. Also this library validates if the token is a jwt valid token (header, payload, signature), check the expiration attribute, etc.

- If you're not using another backend technology take a look to the [API & SDK reference in the docs](https://clerk.dev/docs/reference/overview) and use the official one in case exists.

- If you have problems testing the integration with the hapi server try using your current local IP instead **localhost**

```js
const server = Hapi.server({
  port: 3000,
  host: "192.168.1.60",
});
```

and make the same adjust in your fetch request

```js
const token = await session.getToken();
// you can try with 10.0.2.2 as well if you're using an android emulator
const res = await fetch("http://192.168.1.60:3000", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

- You should **ALWAYS** handle errors for your promises so in a real application take that in mind both in the frontend and backend side.
