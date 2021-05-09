# upload

## Requirements

1. Primary document owner should be able to upload a document to the service using signeasy app [P0]
2. Permitted users should be able to view/download the document using the app [P0]
3. Each user should be able to add a signature and upload the modified document [P0]
4. Users associated with the document should be able to view/download the modified version [P0]
5. Users associated with the document should be notified of changes [P1]
6. Primary document owner should be able to upload via mail, or provide a reference to file in alternate cloud storage [P1]

## Basic MVP
![upload_basic_mvp](https://user-images.githubusercontent.com/34787500/117580673-b3456b00-b116-11eb-8815-b835d4e07301.png)

- Upload service

  receives stream of file data to be uploaded
  
- Download service

  client pulls the stream of file data from the download service
  
- Object store
-
  maintains uploaded files (cannot modify)
 
- Metadata store

  maintains information about file upload location, primary user, participating user 

#### Advantages:
  Simple framework - could horizontally scale upload and download service
  typically could be used when number of participants for a document is very less, say 2 to 3.
#### Disadvantages:
  Uploading/Downloading medium to large files could take a long time (improvements could be made using multi-part upload and partial download/streaming)
  To avoid conflicts users should download latest copy before uploading modification

 
  
  


