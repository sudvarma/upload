# upload

## Requirements
1. Primary document owner should be able to upload a document to the service using signeasy app [P0]
2. Permitted users should be able to view/download the document using the app [P0]
3. Each user should be able to add a signature and upload the modified document [P0]
4. Users associated with the document should be able to view/download the modified version [P0]
5. Users associated with the document should be notified of changes [P1]
6. Primary document owner should be able to upload via mail, or provide a reference to file in alternate cloud storage [P1]

## Basic MVP
![upload basic mvp](https://user-images.githubusercontent.com/34787500/117582300-ff94a900-b11e-11eb-838b-8b03ad69290e.png)

###### Upload service
  Creates a unique document id, and returns a signed URL for the client to upload the file to object store.
###### Download service
  Provides details of file download URL from the object store 
###### Object store (eg: Amazon S3)
  Maintains uploaded files (cannot modify). An object store (S3) is preferred to block store(EBS) as majority use-case involves read of file. Also a small portion of the file (signature area) alone will get modified.
###### Metadata store (eg: MongoDB)
  Maintains information about file upload location, primary user, participating user. Majority of times, all metadata information w.r.t the document need to be fetched - hence suggest using document store like mongoDB.

###### Advantages:
  - Simple framework - quick to productize - could horizontally scale upload and download service
  - Could be used when number of participants for a document is very less, say 2 to 3.
###### Disadvantages:
  - Uploading/Downloading medium to large files could take a long time (improvements could be made using multi-part upload and partial download/streaming)
  - To avoid conflicts users should download latest copy before uploading modification

 
  
  


