# Creating a New Pattern

Custom patterns define what information you want to extract and verify from emails while maintaining privacy. This guide will walk you through creating a pattern that reveals partial email information and verifies specific content.

## Accessing the ZK Regex Registry

1. Navigate to the [ZK Regex Registry](https://registry-dev.zkregex.com/)
2. Click on "Submit a new pattern" to begin creating a new pattern.

## Pattern Details

### Pattern Title

A concise, descriptive name that clearly indicates the pattern's purpose.

{% hint style="info" %}
**Example**: "Partial Email and Content Verification"
{% endhint %}

### Description

A brief but comprehensive explanation of your pattern's functionality.

{% hint style="info" %}
**Example**: "Verifies partial sender email and specific content within the email body"
{% endhint %}

### Slug

A unique identifier for your pattern.

* Use lowercase letters
* Replace spaces with hyphens
* Start with your name or organization for uniqueness
* Keep it relevant to the pattern's function

{% hint style="info" %}
**Example**: "your-name/partial-email-content-verify"
{% endhint %}

### Email Query

Uses Gmail search syntax to identify which emails the pattern should be applied to.

{% hint style="info" %}
**Example**: "from:example@gmail.com"
{% endhint %}

<details>

<summary>More Email Query Examples</summary>

* Selects emails with a specific subject: `subject:"Important Notice"`
* Targets emails within a date range: `after:2023/01/01 before:2023/12/31`
* Selects emails with attachments: `has:attachment`
* Combined query: `from:example@gmail.com subject:"Verification Code"`

</details>

### Circuit Name

The name of the generated circuit.

* Use CamelCase
* Make it descriptive of the pattern's function
* Ensure it's unique within your patterns

{% hint style="info" %}
**Example**: "PartialEmailContentVerifier"
{% endhint %}

## Defining Fields to Extract

You can define multiple fields to extract different pieces of information from the email. Each field has the following properties:

### Field Name

A unique identifier for this field within your pattern. Use CamelCase and make it descriptive of the data it extracts.

{% hint style="info" %}
**Example**: "SenderEmail"
{% endhint %}

### Data Location

Specifies where in the email to look for this information:

* "header": For information in the email headers (From, To, Subject, etc.)
* "body": For information in the main content of the email

### Parts

An array of objects that define the regex pattern for extracting information. Each part has two properties:

#### **is\_public**

Boolean (true/false)

* `true`: This part of the matched text will be revealed in the proof
* `false`: This part will be kept private

#### **regex\_def**

A regular expression that defines what to match.

<details>

<summary>Sender Email Field Example</summary>

```json
{
  "field_name": "SenderEmail",
  "data_location": "header",
  "parts": [
    {
      "is_public": false,
      "regex_def": "From: "
    },
    {
      "is_public": true,
      "regex_def": "[a-zA-Z]{3}"
    },
    {
      "is_public": false,
      "regex_def": "[a-zA-Z0-9._%+-]+"
    },
    {
      "is_public": true,
      "regex_def": "@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}"
    }
  ]
}
```

This field:

1. Looks in the header for the "From:" line
2. Reveals the first 3 letters of the email address
3. Keeps the rest of the local part private
4. Reveals the domain (everything after and including the @)

For an email "alice@example.com", this would reveal "ali@example.com".

</details>

<details>

<summary>Email Content Field Example</summary>

```json
{
  "field_name": "EmailContent",
  "data_location": "body",
  "parts": [
    {
      "is_public": false,
      "regex_def": ".*"
    },
    {
      "is_public": true,
      "regex_def": "Verification code: [A-Z0-9]{6}"
    },
    {
      "is_public": false,
      "regex_def": ".*"
    }
  ]
}
```

This field:

1. Looks in the email body
2. Keeps everything before the verification code private
3. Reveals a 6-character alphanumeric verification code
4. Keeps everything after the verification code private

For an email body "Your verification code: ABC123. Keep this private.", this would reveal only "Verification code: ABC123".

</details>

## Advanced Regex Tips

* Use `.*` to match any character (except newline) any number of times
* Use `\s*` to match any whitespace characters
* Use `?` after a character or group to make it optional
* Use parentheses `()` to create capture groups for more complex matches
* Use `\b` for word boundaries to ensure you're matching whole words

<details>

<summary>Advanced Regex Example</summary>

```json
{
  "is_public": true,
  "regex_def": "\\bBalance:\\s*\\$[0-9,]+(\\.[0-9]{2})?"
}
```

This would match and reveal a balance like "Balance: $1,234.56" in an email.

</details>

### Best Practices

1. **Privacy First**: Always consider what information needs to be public and what should remain private. Reveal only what's absolutely necessary.
2. **Be Specific**: The more specific your regex, the more efficient and secure your pattern will be. Avoid overly broad patterns that might accidentally reveal too much.
3. **Test Thoroughly**: Before submitting, test your regex patterns with sample emails to ensure they work as expected. Use online regex testers for quick iterations.
4. **Document Well**: Use clear names and descriptions to make your pattern understandable to others (and your future self).
5. **Version Control**: If you're updating an existing pattern, consider creating a new version rather than modifying the original. This helps maintain backwards compatibility.

### After Submission

1. The system will process your submission and generate the necessary circuits.
2. This may take a few minutes, depending on the complexity of your pattern.
3. Once processed, your pattern will be available for use in the ZK Email SDK.
4. You can test your pattern using the SDK to ensure it works as expected.

### Example Use Cases

<details>

<summary>Transaction Verification</summary>

Extract and verify transaction amounts and recipient addresses from bank notification emails.

</details>

<details>

<summary>Age Verification</summary>

Confirm a user's age from a government ID email without revealing their exact birthdate.

</details>

<details>

<summary>Membership Validation</summary>

Verify a user's membership status from an organization's email without revealing other personal details.

</details>

### Next Steps

Now that you've created your custom pattern, you're ready to implement it in your application. You can use the ZK Email SDK to:

1. Generate inputs from emails based on your pattern
2. Create zero-knowledge proofs of the extracted information
3. Verify these proofs in your application logic

Remember, the power of ZK Email lies in its ability to verify information while maintaining privacy. Your custom patterns are the key to unlocking this potential for your specific use cases.
