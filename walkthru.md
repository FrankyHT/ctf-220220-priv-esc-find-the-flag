# Privilege Escalation - Find the Flag

## Walkthru


### Login screen
Administrator account, no password known
•	Option to switch accounts and enter in username/password combo

Try and use common usernames/accounts
•	Username: user
•	Password: password


### Enumeration
Looking for accounting data– where to look?
•	Notes/documents – Word docs, text files, excel spreadsheets
•	Databases/datasets – access databases, excel spreadsheets
•	Account tracking system – Installed program, web apps

Checkout Program Files, My Documents, ProgramData
•	Directory in Program Files called ‘Accounting Systems United’ – Good chance this will have something

### Access the directory C:\program files\ Accounting Systems United\
•	Opening the directory in Explorer doesn’t work

•	Right click ‘context menu’ disabled (can’t view properties)

Let’s try via command prompt
•	Access denied

See what privs the current user has
•	whoami /priv
•	SeTakeOwnershipPrivilege property for current user

### About SeTakeOwnershipPrivilege
•	This policy setting determines which users can take ownership of any securable object in the device, including Active Directory objects, NTFS files and folders, printers, registry keys, services, processes, and threads.

•	By default, the owner is the person who or the process which created the object. Owners can always change permissions to objects, even when they are denied all access to the object.

Ownership can be taken by:
•	An administrator. By default, the Administrators group is given the Take ownership of files or other objects user right.
•	Anyone or any group who has the Take ownership user right on the object.
•	A user who has the Restore files and directories user right.

### Back to accessing the C:\program files\ Accounting Systems United\ directory
SeTakeOwnershipPrivilege granted for user

See who owns the directory
•	dir /q
•	/q = Display the owner of the file/directory

Owned by Administrator group
•	Use ‘takeown’ to change ownership of directory (and files within)
•	takeown /f “C:\program files\Battle Tracker United” /r
•	/f = Filename or directory
•	/r = Recursive

Now we access to the directory
•	“dir” to checkout the contents
•	#fl_4g#.jpg present.
•	Attempt to open will return ‘denied access’


### Modify the access control list
SETakeOwnershipPrivilege – Owns the file, but still yet to be granted permission to read/write/execute

Use ‘icacls’ to grant current user access to open the file
•	icacls {file} /grant user:F
•	user:F = {username}:{Full access}
•	See: https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/icacls

Can open the file now.
•	Use Explorer to open
•	Or via Command Prompt
•	mspaint {file}


### Rick-rolled image?
Not what we wanted, so what now?
•	Confirm if nothing else attached/affiliated with the #fl_4g#.jpg

How about Alternate Data Stream?
•	Can be used to hide files attached to visible ones
•	See: https://blog.malwarebytes.com/101/2015/07/introduction-to-alternate-data-streams/

Let’s check:
•	dir /r
•	/r = Display alternate data streams of the file
•	Can see ADS is present in on the file “hidden”

### ADS discovered with the flag
So let’s open it up.

In command prompt
•	mspaint #fl_4g#.jpg:hidden
•	Flag found
