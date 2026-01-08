# IDOR - Santa's Little IDOR 

## Learning Objectives

- Understand the concept of authentication and authorization
- Learn how to spot potential opportunities for Insecure Direct Object References (IDORs)
- Exploit IDOR to perform horizontal privilege escalation
- Learn how to turn IDOR into SDOR (Secure Direct Object Reference)

- **Vertical privilege escalation:** This refers to privilege escalation where you gain access to more features. For example, you may be a normal user on the application, but can perform actions that should be restricted for an administrator.
- **Horizontal privilege escalation:** This refers to privilege escalation where you use a feature you are authorized to use, but gain access to data that you are not allowed to access. For example, you should only be able to see your accounts, not someone else's accounts.


**Q1) What does IDOR stand for?** 

- `Insecure Direct Object Reference` 


**Q2)** **What type of privilege escalation are most IDOR cases?**

- `Horizontal`


**Q3)** **Exploiting the `IDOR` found in the `view_accounts` parameter, what is the `user_id` of the parent that has 10 children?**

- I kept changing the `user_id` until I found the one which had 10 children. 
