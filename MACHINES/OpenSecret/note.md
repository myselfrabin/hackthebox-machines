## CHALLENGE NAME: OpenSecret
## DIFFICULTY: VERY EASY
## PLATFORM: HACKTHEBOX

## Challenge Scenario: 
- A simple help desk portal where users can submit support tickets. The application uses JWT tokens for session management, but something seems off about how they're implemented. Can you find the security flaw?



## OPENING THE WEBSITE: 

- Like this hackthebox challeng palying first time, kind of nervous
!![alt text](./images/homepage.png)


- When i try to submitting a token, there shows a **no session token provided![alt text](./images/nosessiontoken.png)**
    - Quick test with:
    ```
    name: test {this shows as yourname}
    email: abc@gmail.com
    description: <u>issue is here
    ```
- And only name and description is shown as **json POST body**
![POSTbODY](./images/postBody.png)




- Now reading the source we got to know that the way to provide session is by giving variable name:  
```
session_token
```
![alt text](./images/cookieName.png)
- The way to provide cookie is by giving: **session_token=<ACTUAL TOKEN HERE>**


## SO QUESTION COMES HERE, HOW DO WE PROVIDE TOKEN: 
- We get the secret in the source code is that the token?? --> let's see 
![secret key](./images/secretKey.png)
- Let's give this in burp as: 
```
Cookie: session_token= HTB{0p3n_s3cr3ts_ar3_n0t_s3cr3ts}
```
![alt text](./images/thisisjustasecretkey.png)
 - As we know every `jwt` needed to be signin so this is that signin key not a token itself.

## Let's review source code again if we get any lead here: 
![this source code clears everything](./images/clearsSourcecode.png)
- Ok looking at this source code clears everthing: 
```javascript
 // Generate a JWT session token for the user
            async function generateJWT() {
                // Check if user already has a token
                const existingToken = document.cookie
                    .split("; ")
                    .find((row) => row.startsWith("session_token="));

                if (existingToken) {
                    console.log("Session token already exists");
                    return;
                }

                // Create a random guest username
                const username = "guest_" + Math.floor(Math.random() * 10000);

                // JWT Header
                const header = { alg: "HS256", typ: "JWT" };

                // JWT Payload
                const payload = { username: username };

```
    - This source code is for generating the jwt token every `jwt` token have three part i.e `header,payload and signature`
    - The header for this jwt token is: `HS256` and it's having only one payload as: `username`{remember this we will tempring after sometime when generating token} and other part is signature we have already discovered it.

## NOW THE ACTUAL PAYLOAD GENERATION WORKS BEGINS:
- We go to the [jwt.io](https://www.jwt.io) and started playing around it to generate token.
- 
![alt text](./images/genrate_the_token.png)
- Here, we by giving the header,payload and signature we successfully generated the payload.
- We used the username `test` here because when I was trying at beginnig I have used test so I used test you can you anything.
- So let's go to the burp and quick test it.
- Our work on this lab is to submit the token by modifying and looking or security hole in jwt token hence we did it.



- solved 

![alt text](./images/Ticket_submitted_successfully.png)





