<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
<br />

<h1>Group Policy and Account Management with Active Directory in Azure</h1>


<h2>Description</h2>
In this guide, I will show off how to configure Remote Desktop for non-administrative users, automate user creation using PowerShell, and manage group policies. I'll also show you about account lockouts and log monitoring-- two things you will most likely see in a real-life IT work environment.  <br/>
<br />

This is a continuation of the [Active Directory: Deploying Active Directory in Azure](https://github.com/cbhylgz/Azure-AD-Deployment) guide. Check that one out if you want to follow along! <br />
<br />
You can find the Powershell script I run in this project [here.](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) 
<br />


<h2>Environments and Utilities Used</h2>

- <b>Microsoft Azure</b>
- <b>Virtual Machines</b>
- <b>Remote Desktop Connection</b>
- <b>Active Directory</b>

<h2>Operating Systems Used </h2>

- <b>Windows Server </b>
- <b>Windows 10</b>

<h2>Steps to Take:</h2>

<p align="center">
Begin by logging into the client VM, client-1, using the domain admin's login info (jane_admin). The aim is to allow non-administration users to use Remote Desktop. Right-click on the start button, then click System and then click "Remote Desktop" on the right-side, and then "Select users that can remotely access this PC": <br/>
<img src="https://i.imgur.com/2eOpNGQ.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Put in "Domain Users", then click Check Names, then "OK" twice. This allows all domain users to access this client with Remote Desktop.  <br/>
<img src="https://i.imgur.com/UYtCJAV.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Next up is to create a bunch of users in Powershell. To start, log into the DC (Domain Controller) as our admin (jane_admin): <br/>
<img src="https://i.imgur.com/jw8nITi.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
After logging in, open Powershell ISE as an admin and start a new script:  <br/>
<img src="https://i.imgur.com/TVXw1D9.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Use Ctrl + S and save. This pop up should appear:  <br/>
<img src="https://i.imgur.com/by8Q2SP.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Copy and paste the user creation script in the top section and click "Run Script" which has a green play symbol located in the top bar:  <br/>
<img src="https://i.imgur.com/7Optc0J.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Powershell will then run the user creation script and create random users and place them in the OU (Organizational Unit) we created in the last guide, named "_EMPLOYEES". Search "Active Directory Users and Computers" > mydomain.com > _EMPLOYEES, and from there we will pick lucky username to log into client-1 with along with the password, "Password1" (all of the users have this password, as designated by the script):  <br/>
<img src="https://i.imgur.com/sM0V1kX.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Next up, log out of Jane's account on client-1. Afterwards, log back in with one of the users created by the script, still using "mydomain.com/[username]": <br/>
<img src="https://i.imgur.com/vVS10D0.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
After logging in, check the user folder by opening up File Explorer and clicking C: > Users. We can also see the folder for the other users that have signed into this client. Because we don't have permissions, however, we cannot access them:  <br/>
<img src="https://i.imgur.com/jNpzEDX.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Log out of this user. Next, I'll be showing how to set a group policy to lock out an account. Go back to the DC VM and right click Start, then select Run and type "gpmc.msc" in its search bar to bring up the Group Policy Management console: <br/>
<img src="https://i.imgur.com/SHFW4cv.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Expand Domains > mydomain.com > select Default Domain Policy:  <br/>
<img src="https://i.imgur.com/frFH9sZ.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Under Computer Configuration, expand Policies > Window Settings > Security Settings, then select Account Lockout Policy:  <br/>
<img src="https://i.imgur.com/iUbYHWx.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Click "Account lockout threshhold" and choose to make the account lockout after 5 invalid attempts, then click Apply:  <br/>
<img src="https://i.imgur.com/uwLbHqq.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
The policy will update automatically, but it will take some time. However, force-updating is possible by signing into client-1 as our admin and running "gpupdate /force" in the command line. Sign out, and then sign back in with the wrong info for your chosen user 5 times. If done correctly, this lockout pop-up should appear:  <br/>
<img src="https://i.imgur.com/EgNRJCY.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
You'll need to unlock it in order to try again, so return to the DC > Active Directory Users and Computers > mydomain.com > _EMPLOYEES > double-click on the locked out user > Account tab > check the "Unlock account" box:  <br/>
<img src="https://i.imgur.com/TuV49xo.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
(Alternatively you could right-click on the user > Reset Password and there will be a "Unlock users account" option. That way you can change the password at the same time you unlock it):  <br/>
<img src="https://i.imgur.com/WCVBR8E.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Now, we should be able to sign back into client-1 again:  <br/>
<img src="https://i.imgur.com/n1yvvW7.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
To disable users, go to the DC and in Active Directory Users and Computers, click mydomain.com > _EMPLOYEES > right-click on the user you want to disable > Disable:  <br/>
<img src="https://i.imgur.com/xZpGQlu.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Signing out of the client-1 VM and signing back in with the disabled account will result in this message:  <br/>
<img src="https://i.imgur.com/lJgtSEV.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
To re-enable the user, go back to the DC and right-click again on the user and select Enable:  <br/>
<img src="https://i.imgur.com/LHjsljR.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
The user can now log into client-1. After we log back in, check out the event viewer log. Search for "Event Viewer" in the start search bar > Windows Logs > Security. It will show that we can't due to being denied access:  <br/>
<img src="https://i.imgur.com/KcEYMc6.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
Only a user with admin rights is able to check the Security section. Instead of signing out of the client VM and logging back in as jane_admin, all we need to do is close the window, right-click on Event Viewer through the search bar again, and click "Run as Administrator". From there, a pop-up will appear asking for admin credentials. Once that's done, check Security again (Windows Logs > Security). From here, we can see a bunch of different information regarding login attempts, whether they worked or not, who logged in and what their IP is, and even the date and time:  <br/>
<img src="https://i.imgur.com/mpCGlEK.png" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />
<br />
<h2>Active Directory: Creating Users, Group Policy, and Managing Accounts in Azure: Finished! </h2>
