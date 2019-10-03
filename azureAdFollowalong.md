#Thursday, security module

logged onto portal.azure.com as chrismvc@outlook.com ( incognito )
Click "Azure AD"
Click : "Overview"
mine said : chrismvcoutlookcom.onmicrosoft.com

new asp.net core web application 
Project Name = DemoADSecurity

alow https
work or school accounts
Cloud - Single organisation
Domain=chrismvcoutlookcom.onmicrosoft.com ( as noted above but defaulted anyway, probably because logged onto Visual Studio as this account in order to get VS running )

Check "read directory data"

trust IIS express cert ? YES

running 

url = contains clientid

Now

I can now see my app on Azure portal
Click "Default Directory", "App registrations"

Display name
DemoADSecurity

Application (client) ID
613ebe65-1def-419b-a2fe-775a36c43336

Directory (tenant) ID
1f3228e6-9a84-4d20-bed6-8fc763d502e5

Object ID
4808d85c-2419-4eee-bc9a-a83ed247cae5

Supported account types
My organization only

Redirect URIs
1 web, 0 public client

Application ID URI
https://chrismvcoutlookcom.onmicrosoft.com/DemoADSecurity

Managed application in local directory
DemoADSecurity

---

Theres "View API Permissions"