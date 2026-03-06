# Blind SQLi Conditional Response -Step by Step Guide

STEP 1: Target Selection
Browse the target site, click on any category (e.g., Gifts), and locate the request in Burp Suite's HTTP History. Send the request to Repeater.

STEP 2: Locating the Vulnerability
Identify the Cookie: TrackingId header. The vulnerability lies within this cookie value. We will modify the text immediately after the = sign and before the semicolon ;.

STEP 3: Tautology Testing (True/False)
Inject a boolean condition after the TrackingId value to test the server's response:

TRUE Test: xyz' OR '1'='1'--

FALSE Test: xyz' OR '1'='2'--
Check the response body for the specific string "Welcome back". If it appears only during the TRUE test, the "Oracle" is confirmed.

STEP 4: Determining Password Length
Revert to the original TrackingId and append the following payload to find the password length:
' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a'--
Crucial: Highlight the payload, right-click -> Convert selection -> URL -> URL-encode all characters. Increment the number (e.g., >1, >2, etc.) until "Welcome back" disappears to find the exact length.

STEP 5: Testing Character Positions
To find the actual characters, use the SUBSTRING function:
' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a'--
URL-encode the payload again. If "Welcome back" appears, the first character is 'a'.

STEP 6: Transitioning to Automation
Manual guessing is inefficient. Right-click anywhere in the request and select Send to Intruder.

STEP 7: Setting Payload Positions
In the Intruder > Positions tab, use the § markers to define the variables for the attack:
SUBSTRING(password,§1§,1)='§a§'--
(Set the Attack Type to Cluster Bomb).

STEP 8: Configuring Payloads
Go to the Payloads tab:

Payload Set 1 (Position): Select type Numbers, set from 1 to 20, step 1.
Payload Set 2 (Character): Select type Simple List, add a-z and 0-9.

STEP 9: Defining the Success Oracle (Grep - Match)
Go to Settings > Grep - Match. Click Clear, then Add the string: Welcome back. This instructs Burp to flag every request that returns a successful character match.

STEP 10: Execution and Reconstruction
Click Start Attack. In the results window, click the "Welcome back" column header to sort by successful hits. Match each character from Payload 2 with its position in Payload 1. Assemble all 20 characters and log in as administrator to solve the lab.


`#CyberSecurity` `#PenetrationTesting` `#SQLi` `#BurpSuite` `#EthicalHacking`
