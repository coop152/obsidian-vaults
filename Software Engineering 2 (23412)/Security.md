## Broken Authentication
Authentication is the process of confirming a user's identity. Good authentication will not be susceptible to spoofed identity attacks. On a basic level, authentication involves comparing some **credentials** given by the user to those stored in a secure user database. These credentials can be based on **who we are**, **what we know** or **what we have**. For example, Biometrics, Passwords and OTP authentication apps, respectively.

The rest was completely trivial password hashing recap, including a repeat of the misinformation from year 1 where they said that hashing on the front-end is secure (LMAO).

## Authorisation
Authorisation is the process of specifying access rights and privileges to certain resources. Good authorisation protects against privilege escalation attacks. You need authorisation to protect many resources, e.g:
- System resources (files, services, other running programs)
- Customer data
- Application features (adding/deleting records)

Some privileges may be:
- Admin - can access everything
- HR Staff -  Can access employee records and certain protected documents
- Staff - Can access own records
- Guest - Can access little to nothing; may not require authentication

## Injection
General solution: never trust user input.
#### SQL Injection
a five year old could give a definition of sql injection

#### Cross-site Request Forgery (CRSF)

#### Cross-site Scripting (XSS)