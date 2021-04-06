# Broken Access Control module

Authorization = What a user can/can't access. In other words, privileges. 
Authentication = Are you who you say you are? 

First, you have to verify that you are who you say you are (authentication). After that, you have to verify that you have the correct permissions to complete your tasks (authorization).

## Broken Access Control

http://sandbox-hackedu.com/account/16 got us into Alice's account. We couldn't get into any accounts without logging in as Alice first. But once we were authenticated as her, we could access http://sandbox-hackedu.com/account/17 (Bob). 

There were not any access checks on Alice's account, so after authentication, she was authorized for everyone. 

## Solution: 

``` 
if (!claims.get("username").equals(user)) {
    con.close();
    throw new Exception("Invalid credentials!");
}
```

Right before inserting things into the new HashMap. 