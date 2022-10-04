# Templafy App Connector Template

## What is the app connector?

The app connector is a mechanism to create a document/presentation and push data from a webpage e.g. CRM into a template.

https://user-images.githubusercontent.com/39210767/193811418-c6b4b9a7-0def-4969-8e97-2c449e1c3633.mp4

## Architecture
- all communication to Templafy must use HTTPS - if you're developing on your computer, please use ngrok
- the button or alternate action on the webpage calls Templafy via the pop-up and listens to event response

## Key Instructions

### Templafy configuration
1. Configure an app connector with the domain that will contact Templafy. This needs to be the exact domain, as Templafy will only accept the domain
2. Contact support if you can't access this page
![image](https://user-images.githubusercontent.com/39210767/193812874-51ccdee3-caf7-4ed9-9156-c17d35482bc5.png)

### Tenant name
1. Your tenant name is available here - you'll need this
![image](https://user-images.githubusercontent.com/39210767/193813042-2b67abb3-af2b-42b0-b1c4-df120221d573.png)

### Features
Features configure the pop-up window, you can change these
```
"menubar=no,location=no,resizable=no,scrollbars=no,status=no,titlebar=no,toolbar=no,width=1500,height=1000";
```

### Library navigation
The Library navigation will expedite the document creation process.
By default, Templafy will pop-up on the base library. Users can select any document/presentation to use, but this isn't the fastest way.
For example, in this video, you see the user pressing documents and swapping between folders.

https://user-images.githubusercontent.com/39210767/193813506-9a5b048b-c72f-4f28-8630-f7f10e25cab5.mp4

Setting the library navigation path will skip past this.
#### How to set the Library navigation
1. Open Templafy as an end-user and navigate to the folder or document you'd like to open
2. e.g. if you're a sales person working on the CRM, you may want all CRM folder docs => https://samsandboxaus2.templafy.com/library/documents/Global/sales/crm
3. use the library navigation path /library/documents/Global/sales/crm
4. if you're creating 1 type of contract and want to open straight on the page, similar to the example at the top of the page, navigate there as a user and copy the URL e.g. https://samsandboxaus2.templafy.com/library/documents/Global/sales/crm/_service-contract
5. use the navigation path /library/documents/Global/sales/crm/_service-contract

### URL
The URL is important to Templafy
```
https://${tenantName}.hive.templafy.com${libraryNav}?externalSystemType=genericAppConnector&origin=${window.location.origin}
```
1. it contains the tenant URL 
2. the library navigation
3. the current browser location
If these 3 are correct, the browser should pop open and you'll be able to browse through the Templafy library

If they aren't correct, you may be able to see a folder structure (if you're authenticated to Templafy), but you won't be able to browse through
e.g. 

https://user-images.githubusercontent.com/39210767/193814749-ad935984-a558-4fb6-9c72-80d4fda1acf1.mp4

## How to use this template?
Templafy won't support non-HTTPS. You can deploy to an HTTPS server or you can use ngrok

### setup ngrok
1. install [ngrok](https://ngrok.com/)
2. run setup instructions from [ngrok](https://ngrok.com/)
3. run `ngrok http 4000 --host-header=localhost:4000`
4. ngrok will provide an HTTPS URL e.g. https://50bf-2001-8003-3096-3101-6c05-f840-8e0e-8a29.au.ngrok.io => you'll need to add this to your App Connector configuration in Templafy


### Locally - can't call Templafy
* cd to the folder you want to clone the project into 
* run git clone https://github.com/sampolgar/Templafy-AppConnector.git
* run `npm install`
* run `node appconnector.js`
* open http://localhost:4000/

### 
* 

Start ngrok with the command `ngrok http 4000 --host-header=localhost:4000`.

# 2 options

1. popping open the Templafy UI in a browser at the base library level
2. popping Templafy UI at a selected document
3. popping Templafy UI at a selected document and pre-populating the document with the data from the page

Adaptive Sections
https://support.templafy.com/hc/en-us/articles/360018397457-Adaptive-sections-Repeating-Group-binding
