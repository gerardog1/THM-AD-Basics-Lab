# Hands On Active Directory
I establish an RDP connection to the machine with the given IP and credentials provided by the room.
## Active Directory Structure
Active Directory is accessed on this admin account, the structure is as shows:
<img width="650" height="451" alt="image" src="https://github.com/user-attachments/assets/c538bea6-e586-4ef1-a132-430a821d98fe" />
## Deleting extra OU
To delete extra OU's in AD, Advanced Features must be enabled in the View tab, OUs are protected against accidental deletion by default. 
<img width="400" height="472" alt="image" src="https://github.com/user-attachments/assets/6eb3500d-9cca-4546-8222-50d3811b67dc" />

Unchecking this box lets you delete the specifc OU selected.
## Delegation 
In the case of needing to delegate permissions to a user, I right click the department and click Delegate.

<img width="300" height="372" alt="image" src="https://github.com/user-attachments/assets/f45a52b6-8857-433e-9f89-e1e6029e63e9" />

This opens a new window where it asks for the users whom you want to delegate control to.
<img width="500" height="350" alt="image" src="https://github.com/user-attachments/assets/98199e6a-e054-451e-a34b-76758f689fdd" />

In this lab I delegate control to Phillip. I do so by typing his name and click "check names", click OK.

Next it will ask for the tasked to be delegated.

<img width="450" height="414" alt="image" src="https://github.com/user-attachments/assets/e0aba5f9-091b-4ce0-902f-9f40319d8d33" />

Phillip can now reset passwords in this specific OU. He however can't access AD like we did, he must use the following command in powershell:
```bash
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```
> Note: Sophie is in the department that we delegated control to Phillip.

In this lab, I changed Sophie's password as Phillp. Since we we wouldn't want Sophie to keep on using a password we know, I force a password reset at the next logon for security best practice, with the following command:
```bash
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```
## Managing Computers
By default, all the machines that join a domain (except for the DCs) will be put in the container called "Computers".

<img width="600" height="417" alt="image" src="https://github.com/user-attachments/assets/7423e833-7ddb-4c53-82ce-6bac28a4c1d3" />

There is not only machines here, but also servers. We would very likely want the machines used daily to have different policies from the servers. Let's seperate them by creating seperate OUs; Workstations, Servers, & Domain Controllers (auto created by Windows)

<img width="600" height="366" alt="image" src="https://github.com/user-attachments/assets/3d0748fb-ac5f-4253-b967-733f1fb5a81b" />

Now we move the corresponding machines to our newly made OUs.
## Group Policies
To configure GPOs, we use the Group Policy Management tool, available from the start menu.

Upon launching the tool, the complete OU hierarchy will be shown. To configure Group Policies, you first create a GPO under Group Policy Objects and then link it to the OU where you want the policies to apply. 

<img width="500" height="577" alt="image" src="https://github.com/user-attachments/assets/0c4fae1d-441a-4666-b148-def3507be56d" />

Let's edit the minimum password length policy. This setting is found within the Default Domain Policy:

<img width="618" height="374" alt="image" src="https://github.com/user-attachments/assets/4ec39cea-ca57-4b77-9b32-f84503f84bff" />

This will open a new window where we can navigate and edit all the available configurations. To change the minimum password length, go to Computer Configurations -> Policies -> Windows Setting -> Security Settings -> Account Policies -> Password Policy and change the required policy value:

<img width="600" height="565" alt="image" src="https://github.com/user-attachments/assets/3ee08853-78fd-44e7-955c-f630cbc22c08" />

Next I created a new OU to restrict access to the control panel across all machines to only the users that are part of the IT department.

<img width="600" height="565" alt="image" src="https://github.com/user-attachments/assets/3905158c-6285-4188-946b-7195c69d298f" />

Upon opening this newly created GPO for editing, I navigated to the shown path to get to "Prohibit access to Control Panel and PC settings". And change it to Enabled.

Once the GPO is configured, it needs to be linked to all of the OUs corresponding to users who shouldn't have access to the Control Panel of their PCs. In this case, I linked the Marketing, Management and Sales OUs by dragging the GPO to each of them:

<img width="600" height="619" alt="image" src="https://github.com/user-attachments/assets/43debb13-4297-4344-8857-12ccb7a7d577" />

I next created one more GPO to auto lock the screen after a certain amount of time. I created the GPO and navigated to the following route:

<img width="700" height="565" alt="image" src="https://github.com/user-attachments/assets/ebf4393b-834a-48bc-bd16-46ce2abd2a92" />

I set the inactivity limit to 5 minutes so that computers get locked automatically if any user leaves their session open. I then linked the GPO to the root domain by dragging the GPO to it:

<img width="500" height="579" alt="image" src="https://github.com/user-attachments/assets/30d72961-0be0-4d9b-8dd2-568df1aae835" />

I then RDP'd into user "Mark" to confirm the settings. I then got a message indicating the operation is blocked and to contact the admin, confirming it worked.

