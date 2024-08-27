# Installing ZK Email SDK

## Prerequisites

{% hint style="info" %}
**Before You Begin:**

Make sure you have the following tools installed:

* **Node.js** (version 14 or later)
* **npm** or **yarn** (package manager)
* A **Google OAuth Client ID** (for Gmail API access)
{% endhint %}

To obtain a Google OAuth Client ID:

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Enable the Gmail API for your project
4. Create credentials (OAuth client ID) for a Web application
5. Note down your Client ID for later use

## Project Setup and Installation

Choose your framework below for specific setup and installation instructions.

{% tabs %}
{% tab title="React (Vite)" %}
#### 1. Create a new React project with Vite

For npm users:

```bash
npm create vite@latest my-zk-email-app -- --template react-ts
```

For yarn users:

```bash
yarn create vite my-zk-email-app --template react-ts
```

This guide will walk you through the complete process of setting up a project, configuring the environment, and installing the ZK Email SDK, with specific instructions for React and different Next.js approaches.

#### 2. Navigate to the project directory

```bash
cd my-zk-email-app
```

#### 3. Install dependencies

For npm users:

```bash
npm install
```

For yarn users:

```bash
yarn
```

#### 4. Install the ZK Email SDK

For npm users:

```bash
npm install @zk-email/zk-email-sdk
```

For yarn users:

```bash
yarn add @zk-email/zk-email-sdk
```

#### 5. Create an environment file

Create a new file named `.env` in the root of your project:

```bash
touch .env
```

Add your Google OAuth Client ID to the `.env` file:

```
VITE_GOOGLE_OAUTH_CLIENT_ID=your_client_id_here
```

#### 6. Configuration

Update your `src/App.tsx` file:

```tsx
import { ZkEmailSDKProvider } from "@zk-email/zk-email-sdk";

function App() {
  return (
    <ZkEmailSDKProvider 
      clientId={import.meta.env.VITE_GOOGLE_OAUTH_CLIENT_ID}
      zkEmailSDKRegistryUrl='https://registry-dev.zkregex.com'
    >
      {/* Your app components */}
    </ZkEmailSDKProvider>
  );
}

export default App;
```

#### 7. Usage

Create a new component, e.g., `src/components/EmailComponent.tsx`:

```tsx
import React from 'react';
import { useGoogleAuth, useZkEmailSDK } from "@zk-email/zk-email-sdk";

function EmailComponent() {
  const {
    googleAuthToken,
    isGoogleAuthed,
    loggedInGmail,
    googleLogIn,
    googleLogOut,
  } = useGoogleAuth();

  const {
    createInputWorker,
    generateInputFromEmail,
    generateProofRemotely,
    proofStatus,
    inputWorkers
  } = useZkEmailSDK();

  // Your component logic here
}

export default EmailComponent;
```
{% endtab %}

{% tab title="Next.js (App Router)" %}
#### 1. Create a new Next.js project

For npm users:

```bash
npx create-next-app@latest my-zk-email-app
```

For yarn users:

```bash
yarn create next-app my-zk-email-app
```

Choose the following options:

* Would you like to use TypeScript? › Yes
* Would you like to use ESLint? › Yes
* Would you like to use Tailwind CSS? › No (or Yes, if you prefer)
* Would you like to use `src/` directory? › Yes
* Would you like to use App Router? › Yes
* Would you like to customize the default import alias? › No

#### 2. Navigate to the project directory

```bash
cd my-zk-email-app
```

#### 3. Install the ZK Email SDK

For npm users:

```bash
npm install @zk-email/zk-email-sdk
```

For yarn users:

```bash
yarn add @zk-email/zk-email-sdk
```

#### 4. Create an environment file

Create a new file named `.env.local` in the root of your project:

```bash
touch .env.local
```

Add your Google OAuth Client ID to the `.env.local` file:

```
NEXT_PUBLIC_GOOGLE_OAUTH_CLIENT_ID=your_client_id_here
```

#### 5. Configuration

Create a new file `src/app/providers.tsx`:

```tsx
'use client'

import { ZkEmailSDKProvider } from "@zk-email/zk-email-sdk";

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <ZkEmailSDKProvider 
      clientId={process.env.NEXT_PUBLIC_GOOGLE_OAUTH_CLIENT_ID}
      zkEmailSDKRegistryUrl='https://registry-dev.zkregex.com'
    >
      {children}
    </ZkEmailSDKProvider>
  );
}
```

Update your `src/app/layout.tsx` file:

```tsx
import { Providers } from './providers'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```

#### 6. Usage

Create a new component, e.g., `src/components/EmailComponent.tsx`:

```tsx
'use client'

import { useGoogleAuth, useZkEmailSDK } from "@zk-email/zk-email-sdk";

export default function EmailComponent() {
  const {
    googleAuthToken,
    isGoogleAuthed,
    loggedInGmail,
    googleLogIn,
    googleLogOut,
  } = useGoogleAuth();

  const {
    createInputWorker,
    generateInputFromEmail,
    generateProofRemotely,
    proofStatus,
    inputWorkers
  } = useZkEmailSDK();

  // Your component logic here
}
```
{% endtab %}

{% tab title="Next.js (Pages Router)" %}
#### 1. Create a new Next.js project

For npm users:

```bash
npx create-next-app@latest my-zk-email-app
```

For yarn users:

```bash
yarn create next-app my-zk-email-app
```

Choose the following options:

* Would you like to use TypeScript? › Yes
* Would you like to use ESLint? › Yes
* Would you like to use Tailwind CSS? › No (or Yes, if you prefer)
* Would you like to use `src/` directory? › Yes
* Would you like to use App Router? › No
* Would you like to customize the default import alias? › No

#### 2. Navigate to the project directory

```bash
cd my-zk-email-app
```

#### 3. Install the ZK Email SDK

For npm users:

```bash
npm install @zk-email/zk-email-sdk
```

For yarn users:

```bash
yarn add @zk-email/zk-email-sdk
```

#### 4. Create an environment file

Create a new file named `.env.local` in the root of your project:

```bash
touch .env.local
```

Add your Google OAuth Client ID to the `.env.local` file:

```
NEXT_PUBLIC_GOOGLE_OAUTH_CLIENT_ID=your_client_id_here
```

#### 5. Configuration

Create or update `src/pages/_app.tsx`:

```tsx
import { AppProps } from 'next/app';
import { ZkEmailSDKProvider } from "@zk-email/zk-email-sdk";

function MyApp({ Component, pageProps }: AppProps) {
  return (
    <ZkEmailSDKProvider 
      clientId={process.env.NEXT_PUBLIC_GOOGLE_OAUTH_CLIENT_ID}
      zkEmailSDKRegistryUrl='https://registry-dev.zkregex.com'
    >
      <Component {...pageProps} />
    </ZkEmailSDKProvider>
  );
}

export default MyApp;
```

#### 6. Usage

Create a new component, e.g., `src/components/EmailComponent.tsx`:

```tsx
import { useEffect, useState } from 'react';
import { useGoogleAuth, useZkEmailSDK } from "@zk-email/zk-email-sdk";

export default function EmailComponent() {
  const [isClient, setIsClient] = useState(false);

  useEffect(() => {
    setIsClient(true);
  }, []);

  if (!isClient) return null;

  const {
    googleAuthToken,
    isGoogleAuthed,
    loggedInGmail,
    googleLogIn,
    googleLogOut,
  } = useGoogleAuth();

  const {
    createInputWorker,
    generateInputFromEmail,
    generateProofRemotely,
    proofStatus,
    inputWorkers
  } = useZkEmailSDK();

  // Your component logic here
}
```
{% endtab %}
{% endtabs %}

## Next Steps

Now that you have installed and configured the ZK Email SDK, you're ready to start using it in your application. Check out the Using the SDK guide for detailed instructions on how to leverage the SDK's features in your project.

###
