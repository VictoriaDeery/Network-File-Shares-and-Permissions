
<h1>Network-File-Shares-and-Permissions</h1>

 ### [YouTube Demonstration-available upon request]
 
<h2>Description</h2>
In this lab, we will share out resources over the network and create File Shares to allow Read, Write, or Deny access to individual users or groups. We'll make use of the previous Active Directory implementation explained in my Active Directory repositories, as well as at least one of the 1,000 users we made.
<p>
<br />

<h2>Requirements</h2>

- <b>Active Directory must be running in Azure on VM dc-1</b> 
- <b>a client virtual machine must be running in Azure (client-1) and joined to the domain</b>
- <b> both of these requirements have step by step details in my repository ActiveLabDirectory<b/>

<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Azure</b>
- <b>Active Directory</b>
- <b>remote desktop</b>
- <b>Active Directory Users and Computers</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)

<h2>Outline:</h2>

- A. Create some sample file shares with various permissions
- B. Attempt to access file shares as a normal user
- C. Create an “ACCOUNTANTS” Security Group, assign permissions, and test access
<p>
</p>

<h2>Program walk-through:</h2>

<p align="center">

<img src="https://github.com/user-attachments/assets/30f43066-78a7-448f-9a22-eef5090994a6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Overview: A file share is a folder that lives on a computer somewhere on the network that you are able to access. You can connect it to your own computer to make it look like it is on your actual computer. But the distinction is, you are accessing anothers computers' folder over the network. Here, we will create folders, assign permissions, and verify the set permissions of the files.

 <p>
 <br />
- A. Create some sample file shares with various permissions
   <p>
     1. Log into dc-1 with jand doe (mydomain.com\jane_admin). Go to start -> Administrative Tools -> Active Directory Users and Computers -> mydomain.com -> _EMPLOYEES -> select a user, I'm choosing "ban.qos" and log into client-1 using this user (mydomain.com\ban.qos), recall that we made all user passwords "Password1" unless manually changed.
     
   </p>
   2. On DC-1, on the C:\ drive, create 4 folders: “read-access”, “write-access”, “no-access”, “accounting” (note these are just names, words chosen, and just because a folder is named "read access" doesn't mean that it has read access. click start -> file explorer ->this PC -> Windows (C:) -> right click in the space to create "new" -> "folder" and make each of them. To set access, right-click the folder -> properties -> "sharing" tab -> share -> type "domain users" -> "add" -> they will be populated below with "read" and a drop-down which you can select to change -> share -> done
   <p>
<img src="https://github.com/user-attachments/assets/6a710725-c3d9-4e09-9329-05e9269575d7" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p></p>
     3. Set the following permissions (share the folder)
Folder: “read-access”, Group: “Domain Users”, Permission: “Read”
Folder: “write-access”,  Group: “Domain Users”, Permissions: “Read/Write”
Folder: “no-access”, Group: “Domain Admins”, “Permissions: “Read/Write”
(Skip accounting for now)
<p>
- B. Attempt to access file shares as a normal user in client-1
<p>
<img src="https://github.com/user-attachments/assets/907ef5de-d83c-4785-bcb0-14c583ac3362" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
  1. I am using the user"ban.qos" in client-1, which is a member of the "domain users" group. Open File Explorer and in the search bar at the top, type "\\dc-1." You will see the "read access," "write access," and "no-access" folders but not the accounting folder because we have not configured its access. Click to the "read-access" folder or file, and you'll see if you try to make a new folder or edit, you are unable. Test the other folder to make sure they respond appropriately (can make a document in "write-access", cannot open "no-access" because we are not logged in as an admin.
</p>

- C. Create an “ACCOUNTANTS” Security Group, assign permissions, and test access
<p>
<img src="https://github.com/user-attachments/assets/0e620093-e20f-428e-8a1f-e35a55f68603" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p></p>
1. Go back to dc-1. In Active Directory, create a security group called “ACCOUNTANTS” via active directory users and computers -> right-click "mydomain.com"-> "new" -> "organizational Unit" -> name it "_GROUPS." Now open "_GROUPS," right-clcik in the space ->"new"-> "group" -> name it “ACCOUNTANTS” -> leave presets and select "ok"
<p>
<img src="https://github.com/user-attachments/assets/ed40d73c-f794-48f1-96d0-dbae72121da0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p></p>
2. Using dc-1, set the following permissions on the “accounting” folder created earlier (right-click start to get to file explorer -> Windows (C:) -> "accounting". right-click the "accounting" folder -> properties -> "sharing" tab -> share -> type "ACCOUNTANTS" -> "add" -> they will be populated below with "read" and a drop down, select to change to "read/write" -> share -> done ->ok -> ok):
Folder: “accounting”, Group: “ACCOUNTANTS”, Permissions: “Read/Write”
<p>
  (before <img src="https://github.com/user-attachments/assets/f19c0f1a-2b31-4699-8830-29bea677cde9)
now seeing the accounting folder as a regular user within the accounting group, but no access:
<img src="https://github.com/user-attachments/assets/3ce1e2f7-6802-43c9-b7eb-b64f1d584856" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/user-attachments/assets/20f3ba4f-9fee-4fae-a047-f645f55ad795" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/user-attachments/assets/de67c2d6-d073-4435-9018-08f0ac46a8ae" height="80%" width="80%" alt="Disk Sanitization Steps"/>

3. On Client-1, as a <regularuser> ( in my case "ban.qos"), try to access the accountants folder by re-opening File Explorer and typing "\\dc-1," which is how you map to a computer on the network. Since "accountants" is shared out now, the regular user will now see the accounting folder, however, it should fail to open because they are not yet a member of the accounting group. Let's change that. Now, log out of Client-1 as <regularuser>. Then on dc-1, make <regularuser> a member of the “ACCOUNTANTS”  Security Group by going to mydomain.com -> "_GROUPS" -> double click  "ACCOUNTANTS" ->members -> add-> enter the name -> check name ->add ->ok ->  apply -> ok. Note, if you wanted to, you could add other groups to this group and give them access at once, for example typing "domain users" instead of one domain user's name to give access to all in the domain users group. Now lastly, sign back into Client-1 as <regularuser> restart, and then open file Expolere and map to dc-1 using \\dc-1 and double click the "accounting" folder which you should now be able to open. Right-click in the space -> new -> text document to create a new text document in this folder. Open the file and edit it. It should all work now.

</p>
We created a Security Group in Active Directory called "ACCOUNTANTS." We shared out the folder and gave the security group we created, permission to read and write. We tested access to the folder with our user without being a member of the group yet, and it failed. We logged out and restarted. Then, we added that user to the accountant security group member list. We logged back in as that user and successfully accessed the folder.
<p>

<img src="https://github.com/user-attachments/assets/412f0e44-cf11-4ae9-87e0-3cf97abe8377" height="80%" width="80%" alt="Disk Sanitization Steps"/>



