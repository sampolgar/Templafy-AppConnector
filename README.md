# Templafy App Connector Template

## What is the App Connector?

The App Connector is a mechanism to create a document/presentation and push data from a webpage e.g. CRM into a template.

https://user-images.githubusercontent.com/39210767/194206135-3cda8d01-d57f-46c7-85cf-e34b0c5c0634.mp4

## TL|DR
1. Host the static HTML file in Azure blog or an S3 bucket, change the tenant URL details inside the HTML file
2. Upload [this word](https://templafydownload.blob.core.windows.net/delivery/Integrations/AppConnector-HTML/index.html) document to your Templafy
3. Open the HTML file on Azure blob or S3 - it should be on HTTPS. Take the URL i.e. if the URL is https://s3.aws.com/appconnector/index.html copy s3.aws
4. As an admin, create an app connector integration in Templafy. Add the domian i.e. s3.aws.com to the domain settings (don't add a / at the end)
5. Use the pop-open Templafy button on the app connector and select the template you uploaded in step 2

## Architecture

- all communication to Templafy must use HTTPS - if you're developing on your computer, please use ngrok
- the button or alternate action on the webpage calls Templafy via the pop-up and listens to event response

## Key Instructions

### Templafy configuration

1. Configure an app connector with the domain that will contact Templafy. This needs to be the exact domain, as Templafy will only accept the domain. Instructions available here: https://support.templafy.com/hc/en-us/articles/360018358678-How-to-enable-a-Custom-App-Connector-
2. Contact support if you can't access this page
   ![image](https://user-images.githubusercontent.com/39210767/193812874-51ccdee3-caf7-4ed9-9156-c17d35482bc5.png)

### Tenant name

1. Your tenant name is available in your URL - you'll need this
   ![image](https://user-images.githubusercontent.com/39210767/193813042-2b67abb3-af2b-42b0-b1c4-df120221d573.png)

### Features

Features configure the pop-up window, you can change these

```
"menubar=no,location=no,resizable=no,scrollbars=no,status=no,titlebar=no,toolbar=no,width=1500,height=1000";
```

### Library navigation

#### default navigation path

Configuring library navigation will expedite the document creation process.
With the default library navigation - `/library/` - your document creation process looks like this

https://user-images.githubusercontent.com/39210767/194207571-b902ca5b-bd4e-470c-b380-77e3683b6139.mp4

Notice the number of clicks you need to get to the end-document.

#### navigation path

You can skip this by accessing Templafy as an end-user, then navigating to the end folder, document or presentation you need to pop-open, copy the URL path to use as the library navigation path.

https://user-images.githubusercontent.com/39210767/194209819-f9ac379d-d67c-496a-ad34-179d972c04cd.mp4

Then use the navigation path `/library/documents/Global/sales/crm/_appconnector-prefilledform-with-image`

#### navigation path with asset id

The library path uses the Template name. If the template name changes, the library path will be invalid. A solution is to use the documentId in the library path.
To find the ID, as an admin, navigate to the document library, click on the settings and copy the assetid.

https://user-images.githubusercontent.com/39210767/194210627-d43dbde5-b0ce-4cbb-a576-f8059c6ad5bb.mp4

Then use the navigation path `/library/id/{{documentId}}`

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

If this happens, 
1. check your browser logs for any issues 
2. check your current browser location is included in the settings in the Templafy admin portal
3. ensure you open Templafy from HTTPS
4. ensure your user is authenticated to Templafy
5. double check the above - there's probably something wrong

# Javascript functions

## Templafy sends event type = ready
Templafy will let the Window know when its ready for the JSON content, don't send it beforehand or it will be lost.

```
if (event.data.type == "ready") {
    console.log(jsonContent)
    openedPopup.postMessage({
        type: "content",
        content: jsonContent,
    },
        tenantOrigin);
}
```

## Templafy sends event type = document

When the end-user presses "Save back" function, Templafy will send the finished document back to users window.

![image](https://user-images.githubusercontent.com/39210767/194216772-29da9511-c7b4-4215-a0c3-972c4ff5347c.png)

Use the function to do what you need e.g. download or save to an in-house system

```
 if (event.data.type == "document") {
       window.addEventListener('message', console.log)
       let documentURL = event.data.documentUrl;
       openedPopup.close();
       //file is downloaded
       window.location.href = documentURL;
    }
```



## How to use this template?

Templafy won't support non-HTTPS. You can deploy to an HTTPS server, or you can use ngrok for testing locally, or host the static HTML file in the cloud e.g. Azure blob.

### Local testing

- ensure you have nodejs
- cd to the folder you want to clone the project into
- run git clone https://github.com/sampolgar/Templafy-AppConnector.git
- run `node index.js`
- open http://localhost:4000/

`You won't be able to open Templafy - see ngrok details below for to open your local machine to HTTPS`

### Setup ngrok

1. install [ngrok](https://ngrok.com/)
2. run setup instructions from [ngrok](https://ngrok.com/)
3. run `ngrok http 4000 --host-header=localhost:4000`
4. ngrok will provide an HTTPS URL e.g. https://50bf-2001-8003-3096-3101-6c05-f840-8e0e-8a29.au.ngrok.io => you'll need to add this to your App Connector configuration in Templafy

# Configuring Templates to receive data from the App Connector
1. Data sent to Templafy must be in JSON format
2. Bind data in the document with HostSystem bindings https://support.templafy.com/hc/en-us/articles/360018415558-HostSystem-binding-

## Pre-filling Template form with JSON data

https://user-images.githubusercontent.com/39210767/194212231-97ecd614-20b2-48cd-8eb7-60076780a7b7.mp4

## Using JSON data in the document

### Simple text binding

![image](https://user-images.githubusercontent.com/39210767/194214603-3e4db59a-4c9a-4b1b-9483-46bc58372b65.png)

### Json array binding with images
1. Ensure the image is a public image
2. Use an image binding, details [here](https://support.templafy.com/hc/en-us/articles/6066298674205-How-to-insert-custom-image-bindings-to-accommodate-complex-use-cases-) 
3. If multiple images, use repeating groups, details [here](https://support.templafy.com/hc/en-us/articles/360018397457-Adaptive-sections-Repeating-Group-binding) 

https://user-images.githubusercontent.com/39210767/194213926-9bd44f00-6268-4667-b865-2d02dcffe227.mp4
