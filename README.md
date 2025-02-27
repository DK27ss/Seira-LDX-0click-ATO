# Seira-LDX-Silent-0click-ATO

## Overview
During the first week of the new academic year in September 2024, I became aware of an e-learning application used by the campus I was on at the time. 

I'll show you how, out of curiosity and much to my surprise, I was able to discover a remote account takeover without any user interaction, which led to an privilege escalation and exfiltration of the Azure user database.

By simply looking at the requests exchanged between me and the server with burpsuite I was able to discover that I was receiving the `ConnectedUserIds` from the Websockets History.

![websocket](https://github.com/user-attachments/assets/5148235e-e661-47fa-9ddb-d18a81ace935)

With this discovery I went looking for other `endpoints` that could potentially expose the `UserId`.

I was already aware that the `SSO` `LDAP` `Authentication` request when the web page was refreshed was exposing my `UserId`. So all I had to do was try to copy an exposed `ConnectedUserIds` from the WebSocket History and copy it instead of my own in the `LDAP` `SSO` `Authentication` request.

![sso_ldap](https://github.com/user-attachments/assets/2c486908-f4d0-4a2f-a8e4-8178e1f35cdc)

So I went looking for an `Administrator` `UserId` and it was when I went to one of the two learning modules available to me that I discovered in the server response that my `coach` `UserId` was displayed in clear text (in addition to the UserId's of my mates in the same group).

![module](https://github.com/user-attachments/assets/414bfa6b-8d1f-4188-9239-977cced1aa15)

![group](https://github.com/user-attachments/assets/b2d8568d-e927-4bf6-9a6b-0c65ac345702)

Why not now test with my `coach` `UserId`, assuming that he has more advantages than a standard user? And bingooo!!! He has `Administrator` rights.

![0click](https://github.com/user-attachments/assets/4dedd1fc-7481-4e66-a9c4-5802e59281dd)

After performing an `privilege` `escalation` to an `Administrator` account, I initially observed that connection to the account was `instantaneous` and `totally` `transparent`.

![nologs](https://github.com/user-attachments/assets/b83d7d35-1d29-4c2c-b123-2788942fe67d)

I then looked into how to obtain the full `UserId` and ended up with full access to the `Azure` `user` `database`.

![ldapall](https://github.com/user-attachments/assets/f4ca1b51-820b-4a51-8a62-f5ef43905275)

Using the query `/server/api/users/ldap/all` and defining a `display` `limit` of `10000`, I was able to extract the `entire` `database`.

![total](https://github.com/user-attachments/assets/6d914c40-cd4e-458d-9939-e440667b7396)

Finally, I could connect to any `Administrator` account, filtering by `privilege` / `last name` / `first name` and pivot to accounts `without` ever being `detected` in the connection `logs`, and without `any interaction` on the part of the `user`.

![keys](https://github.com/user-attachments/assets/70b5748a-273c-4da0-b872-691996e65f94)

Here's a quick debrief of my first 0click ATO in the wild, the flaw has now been fixed and all traffic is now `encrypted`. :)


