# Report Writing

{% hint style="danger" %}
**treat your customer like an clueless guy that does not know anything technical** and they only **want to know what could go wrong with their business and how could they should ask their “IT” to fix** your discovery and continue to operate.
{% endhint %}



## Report Main sections

### Summary Section

The summary plays a critical role in **helping readers quickly understand the results of your assessment** without needing to dive into the technical details. It connects your work to real-world business and security impact. The summary typically appears at the start of the report and should allow a reader, even someone without a technical background, to answer these questions:

* What did we test?
* What did we find?
* What does it mean for our business or system?
* What should we do next?

Sometimes, one summary isn't enough for both business and security teams. In these cases, split the summary into two parts:

* **Executive Summary** - Written for business stakeholders, this version skips technical jargon and highlights business risks. It clarifies the security posture and priorities, helping stakeholders grasp the impact and urgent actions needed.
* **Findings & Recommendations** - This section for the security team identifies common vulnerability themes and attack chains. Risk ratings are assessed in isolation, but combinations of lower-risk vulnerabilities can have significant business impacts. Highlight these to help security stakeholders reprioritize remediation efforts. Identify systemic issues to guide developers in avoiding future errors.

#### Summary section elements

Regardless of whether you split the summary into two or keep it as one, a good summary should cover the following:

* **Overview** - What was tested? What type of system or application is it? What were the goals of the assessment? What was the scope, and how much coverage could be achieved?
* **Results** - What did the assessment uncover? Was the system secure? If not, what categories of issues were found?
* **Impact** - What is the real-world impact if the issues remain unaddressed? How could the system be exploited by a real-life threat actor?
* **Remediation Direction** - At a high level, what actions should the organisation take next? Does this require major investment, or are the issues mostly quick fixes?

### Vulnerability Write-up Section

The largest section of your report will be the vulnerability write-ups. **Each write-up should explain what the vulnerability is, where it was found, how it was discovered**, and most importantly, **how it should be remediated**.&#x20;

This section is primarily written for the stakeholders who are going to fix the issues, such as developers or system administrators.

#### Structure of a Good Write-Up

To make your write-ups clear and actionable, you should follow a consistent structure for each one. Here's a format that works well:

* **Title** - A short, descriptive heading (e.g. "Unauthenticated SQL Injection in Login Form")
* **Risk Rating** - A risk rating for the discovered vulnerability. Vulnerabilities should always be rated in isolation, as if all other vulnerabilities did not exist and should either use the client's risk rating matrix or a public one, such as **CVSS**.
* **Summary** - A **brief explanation** of the vulnerability and **its potential impact** in plain language.
* **Background** - Provide additional context to **explain the vulnerability and why it matters**. This is especially important if the reader is unfamiliar with it. Remember that the developers who will fix the vulnerability are potentially not security experts, so more guidance to help them understand the root cause of the vulnerability will aid them in remediating the issue accurately.
* **Technical Details & Evidence** - **Where** and **how** the **issue was found**. Include requests, responses, payloads, and screenshots or code snippets if needed.
* **Impact** - **What an attacker could realistically do with this vulnerability**. This shows that you are not just providing the vulnerability without thinking about how a real threat actor could leverage it in the specific system or application where you found it. For example, it is common to say with XSS that the threat actor would steal the user's cookie to perform session hijacking. But what if the application uses tokens instead? Does that now mean that the impact is lower? Make sure to contextualise the impact to the specific system that you are testing.
* **Remediation Advice** - **Clear, actionable steps to resolve the issue**. It is critical to ensure that your remediation advice will address the root cause of the vulnerability. While you may want to provide additional measures that will aid in further mitigation, **your first recommendation should address the vulnerability at its core**. Consider, for example, SQL Injection. While sanitisation and input validation can help mitigate the vulnerability and make it harder to exploit, parameterisation is required to address the vulnerability at its core. This ensures that regardless of the input, there can be no confusion between the SQL command and user-supplied input. **Always ensure that your recommendation will fully resolve the vulnerability**, not just mitigate its impact. If you wish to provide further defence-in-depth controls, make sure to mention that these cannot be implemented in isolation.
* **References** - (Optional) Links to relevant vendor documentation or guidance to support the fix.



<details>

<summary>The Golden Thread</summary>

Although we are giving explicit sections here, at a certain point, reporting becomes so natural that you are able to create the entire write-up without requiring these exact headings or sections. Once you understand the structure of a good vulnerability write-up, you will be able to have a golden thread flow through your write-up meaning that readers will be able to follow, understand, and know what to do next without requiring explicit headings and sections. In certain cases, this can greatly help with the flow of your writing. However, in other cases, for example when your report will be ingested by vulnerability management software, having discrete sections is often still beneficial. Ideally, you should tailor your report structure to the specific client's needs.

</details>

#### Context Matters

Your report should never feel like it was copied from a textbook or another client’s report. The most valuable reports are those that **explain vulnerabilities in the context of the specific system you tested**. This means your write-up should answer questions such as:

* **Where exactly was this vulnerability found?** - Be specific by including the endpoint, parameter, or feature where the issue occurred.
* **What assumptions are required for the vulnerability to be exploited?** - Does the attacker need credentials? Does it only affect admin users? Is it only exploitable during a certain workflow?
* **How does this affect&#x20;**_**this**_**&#x20;client’s environment?** - If it’s a hospital system, could it leak patient data? If it’s an e-commerce site, could it affect transactions?
* **What steps should&#x20;**_**this**_**&#x20;client take to fix it?** - Go beyond generic fixes. If you know they’re using a specific tech stack, tailor your advice. If you know that they are using C# with MS SQL, show an example of how to fix SQL Injection specifically in C# for MS SQL database connections.



#### Example Scenario

<details>

<summary>SQL Injection Write-Up</summary>

**Title**: Unauthenticated SQL Injection in Login Form

**Risk Rating**: High (CVSS 3.1 Base Score: 8.6)

**Summary:** An unauthenticated SQL injection vulnerability was identified in the login form of the TryBankMe application. This could allow an attacker to bypass authentication or extract sensitive customer information.

**Background:**

SQL Injection occurs when user-supplied input is unsafely included in SQL queries without proper parameterisation. The SQL interpreter is unable to discern SQL commands from the user-supplied input, leading to confusion that allows a threat actor to inject directly into the SQL command itself. This vulnerability is commonly exploited to bypass authentication or extract sensitive data.

**Technical Details & Evidence:**

The login form at `/login` was vulnerable to SQL Injection via the `username` field. Using Burp Suite, the following HTTP request was intercepted and modified:

```http
POST /login HTTP/1.1
Host: trybankme.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 45
username=' OR 1=1--&password=randomvalue
```

The application responded with a 302 Found redirect to the authenticated user dashboard:

```http
HTTP/1.1 302 Found
Location: /dashboard
Set-Cookie: sessionid=abc123xyz456; Path=/; HttpOnly
```

This confirmed that authentication was bypassed using a classic SQL Injection payload.

**Impact:**

An attacker could log in as any user without valid credentials. Furthermore, even though the injection points were a blind-injection, a threat actor could leverage error-based injections to enumerate the information from the database.

**Remediation Advice:**

Parameterised queries should be used for all database operations involving user input. For example, in a .NET application using MS SQL as the database, the login query should be altered to look like this:

```csharp
using (SqlConnection connection = new SqlConnection(connectionString))
{
  string query = "SELECT * FROM Users WHERE Username = @username AND Password = @password";
  SqlCommand command = new SqlCommand(query, connection);
  command.Parameters.AddWithValue("@username", inputUsername);
  command.Parameters.AddWithValue("@password", inputPassword);
  connection.Open();
  SqlDataReader reader = command.ExecuteReader();
  if (reader.HasRows)
  {
      // Login successful
  }
}
```

As shown in the example above, the user input is distinctly split from the SQL command, ensuring that the SQL engine cannot be confused.

**References:**

* [https://tryhackme.com/room/sqlinjectionlm](https://tryhackme.com/room/sqlinjectionlm)

</details>



### Appendices Section

Appendices help security stakeholders and testers verify details, check the scope, or follow up after fixes. There's no set format for appendices; it can change from project to project. Still, you should aim to include two main appendices in your report.

#### Assessment Scope

The assessment scope appendix should be used to establish how close the assessment was to what was originally scoped in the Rules of Engagement document. It may be that changes were made during the project or that it was impossible to gain coverage of the entire scope for various reasons. The assessment scope appendix is the perfect place to provide this information which can help security stakeholders understand the next steps. For example, if you were only able to gain coverage of 80% of the scope, a complete reassessment for the remaining 20% will probably be required, depending on how many vulnerabilities were discovered during the initial test.

#### Assessment Artefacts

The assessment artefacts appendix provides you with an opportunity to list out any changes that you may have made during your testing. While you should always aim to perform your own cleanup, it is often impossible to fully remove all artefacts created due to security testing. This is crucial information as some of these artefacts may be potentially malicious. For example, you may have uploaded a webshell by leveraging an unrestricted file upload vulnerability. In the worst possible case, two years later everyone forgets about the file and the pentest, only to rediscover it and raise it as an actual security incident! This appendix allows you to provide these artefacts and recommendations on which of these artefacts need to be removed and how they should be removed.



## Styling Guides and Report QA

<details>

<summary>Writing Clearly</summary>

Clarity is one of the most important qualities of a good report. You should always aim to avoid ambiguity by using simple and direct language that makes your point obvious. Your writing should be understandable even to readers who do not have deep knowledge of the system. If your meaning is unclear or buried under unnecessary complexity, your findings are less likely to be taken seriously, or even fixed.

</details>

<details>

<summary>Professional Writing</summary>

A pentest report is a formal document. Use the same tone and style that you would in any other business-critical communication. This means:

* **Be objective** - Stick to the facts. Avoid exaggeration, emotional language, or assumptions about intent.
* **Avoid informal writing** - No slang, no jokes, and no "we pwned the login page."
* **Be consistent** - Use the same terminology, formatting, and structure throughout the report. Avoid switching between different spellings or terms for the same thing.

</details>

<details>

<summary>General Best Practices</summary>

Follow these simple rules to improve the professionalism and readability of your writing:

* **Write in past tense** -E.g. "The vulnerability was discovered during authentication testing."
* **Do not use first-person language** - Avoid "I," "we," "our," and "us." Write as a neutral observer.
* **Mask sensitive information** - Never include real passwords or other private data unless explicitly authorised. If you have a screenshot to show impact of a vulnerability, make sure to blur any sensitive information.
* **Use clean, formal phrasing** - Avoid contractions and overly casual terms. "The attacker gained unauthorised access" is better than "we broke in."

</details>

<details>

<summary>Quality Assurance (QA) Process</summary>

Even experienced testers can make mistakes. This is why every report should go through a proper review process:

* **Read your own work** - Step away from the report and come back to it later with fresh eyes. Look for unclear sentences, inconsistencies, or missing context. It can usually help to read your writing out loud for yourself.
* **Get someone else to read your work** - A peer reviewer should check that your findings are understandable, actionable, and professionally written.&#x20;

QA isn’t just about fixing typos. It’s about making sure the report reflects well on both you and your pentesting team.

You can have the best technical findings in the world, but if your report is sloppy, emotional, inconsistent, or unclear, it will not have the impact it should. Good writing helps good security work get the attention it deserves.

</details>





