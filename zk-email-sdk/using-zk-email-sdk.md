# Using ZK Email SDK

## Understanding Patterns

Before we dive into using the SDK, it's crucial to understand the concept of "patterns" in the context of ZK Email.

### What is a Pattern?

A pattern is a template that defines:

1. What information you want to extract from an email
2. How to locate this information within the email structure
3. Which parts of this information should be made public and which should remain private

Think of a pattern as a set of instructions for the ZK Email system to follow when processing an email. It tells the system:

* Where to look in the email (header, body, etc.)
* What to look for (sender email, subject line, specific content, etc.)
* What to reveal and what to keep hidden

### Why Create a Pattern?

Creating a pattern is essential because:

1. **Customization**: It allows you to tailor the email verification process to your specific needs.
2. **Privacy**: You can control what information is revealed and what remains private.
3. **Efficiency**: By defining exactly what you need, you optimize the proof generation process.
4. **Reusability**: Once created, a pattern can be used multiple times across different applications.

## Creating a New Pattern

Now that we understand what patterns are and why they're important, let's create one.

1. **Visit the ZK Regex Registry**: Go to [https://registry-dev.zkregex.com/](https://registry-dev.zkregex.com/)
2. **Click on "Submit"** to create a new pattern.
3. **Fill in the Pattern Details**:
   * **Pattern Title**: A descriptive name (e.g., "Email Verification")
   * **Description**: Explain what your pattern verifies (e.g., "Verifies the sender and subject of an email")
   * **Slug**: A unique identifier (e.g., "your-name/email-verification")
   * **Email Query**: Define how to find relevant emails (e.g., "from:example@gmail.com")
   * **Circuit Name**: Name for the generated circuit (e.g., "EmailVerifier")
4. **Define Fields to Extract**: For each piece of information you want to extract, add a field:
   * **Field Name**: Name of the data you're extracting (e.g., "SenderEmail", "Subject")
   * **Data Location**: Choose whether the data is in the email header or body
   *   **Parts**: Define the regex pattern to match the data. For example:

       ```json
       {
         "parts": [
           {
             "is_public": false,
             "regex_def": "From: "
           },
           {
             "is_public": true,
             "regex_def": "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}"
           }
         ]
       }
       ```
5. **Submit Your Pattern**: Click "Submit" to register your pattern.
6. **Wait for Processing**: The system will process your pattern and generate the necessary circuits. This may take a few minutes.

### Implementing the Pattern in Your Application

Once your pattern is registered and processed, you can use it in your application with the ZK Email SDK.

#### Setting Up Your Project

First, ensure you've installed the ZK Email SDK as described in the Installation Guide.

#### Creating a Component

Here's an example of a React component that uses the ZK Email SDK to verify an email based on your custom pattern:

```tsx
import React, { useState, useEffect } from 'react';
import { useGoogleAuth, useZkEmailSDK } from "@zk-email/zk-email-sdk";

const EmailVerifier: React.FC = () => {
  const [emailContent, setEmailContent] = useState<string>('');
  const [verificationResult, setVerificationResult] = useState<string>('');

  const {
    isGoogleAuthed,
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

  useEffect(() => {
    if (isGoogleAuthed) {
      createInputWorker("your-name/email-verification");
    }
  }, [isGoogleAuthed, createInputWorker]);

  const handleVerifyEmail = async () => {
    if (!isGoogleAuthed) {
      setVerificationResult('Please log in with Google first.');
      return;
    }

    if (!inputWorkers["your-name/email-verification"]) {
      setVerificationResult('Input worker not ready. Please try again.');
      return;
    }

    try {
      setVerificationResult('Verifying email...');
      const input = await generateInputFromEmail("your-name/email-verification", emailContent);
      const proofResult = await generateProofRemotely("your-name/email-verification", input);
      
      // The proofResult contains the verified data from your pattern
      const verifiedSender = proofResult.publicInputs.SenderEmail;
      const verifiedSubject = proofResult.publicInputs.Subject;
      
      setVerificationResult(`Email verified! Sender: ${verifiedSender}, Subject: ${verifiedSubject}`);
    } catch (error) {
      setVerificationResult(`Error verifying email: ${error.message}`);
    }
  };

  return (
    <div>
      <h2>Email Verifier</h2>
      {isGoogleAuthed ? (
        <>
          <button onClick={googleLogOut}>Log Out</button>
          <textarea
            value={emailContent}
            onChange={(e) => setEmailContent(e.target.value)}
            placeholder="Paste your email content here"
          />
          <button onClick={handleVerifyEmail}>Verify Email</button>
          <p>Result: {verificationResult}</p>
        </>
      ) : (
        <button onClick={googleLogIn}>Log In with Google</button>
      )}
    </div>
  );
};

export default EmailVerifier;
```

#### How It Works

1. **Authentication**: The component uses Google authentication to ensure secure access.
2. **Input Worker Creation**: An input worker is created for your custom pattern. This worker processes emails according to your pattern.
3. **Email Verification Process**:
   * The user inputs an email.
   * The component generates input for the circuit based on your pattern.
   * It sends this input to a remote prover to generate a zero-knowledge proof.
   * The proof result contains the verified data extracted from the email.
4. **Result Display**: The component shows the verified information without revealing the full email content.

### Integration in Your App

To use this component in your app:

1. Import the `EmailVerifier` component in your main app file.
2. Wrap your app with the `ZkEmailSDKProvider`.
3. Place the `EmailVerifier` component where needed.

Example:

```tsx
import React from 'react';
import { ZkEmailSDKProvider } from "@zk-email/zk-email-sdk";
import EmailVerifier from './components/EmailVerifier';

function App() {
  return (
    <ZkEmailSDKProvider 
      clientId={process.env.GOOGLE_OAUTH_CLIENT_ID}
      zkEmailSDKRegistryUrl='https://registry-dev.zkregex.com'
    >
      <div className="App">
        <h1>My Email Verification App</h1>
        <EmailVerifier />
      </div>
    </ZkEmailSDKProvider>
  );
}

export default App;
```

### Best Practices

* **Error Handling**: Implement robust error handling for various scenarios.
* **User Feedback**: Provide clear feedback during the verification process.
* **Security**: Only use the verified data extracted by your pattern, never expose full email contents.
* **Performance**: Consider caching proof results for repeated verifications.

### Next Steps

* Experiment with different patterns to suit various use cases.
* Explore advanced features of the ZK Email SDK in our API Reference.
* Join our community to share your experiences and get help from other developers.

By following this guide, you've learned how to create custom email verification patterns and implement them in your application using the ZK Email SDK. This powerful tool allows you to verify email information while maintaining privacy and security.

##
