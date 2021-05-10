# upload

## Requirements
#### Functional Requirements
1. Primary document owner should be able to upload a document to the service using signeasy app [P0]
2. Permitted users should be able to view/download the document using the app [P0]
3. Each user should be able to add a signature and upload the modified document [P0]
4. Users associated with the document should be able to view/download the modified version [P0]
5. Users associated with the document should be notified of changes [P1]
6. Primary document owner should be able to upload via mail, or from alternate cloud storage [P1]

#### Non-functional Requirements
1. Latency to upload / download <= 4 seconds for 5 MB of file size for 95pc of customers
2. scale to a concurrency of 1000 simultaneous uploads / downloads

## Basic MVP
![upload basic mvp (2)](https://user-images.githubusercontent.com/34787500/117582389-65813080-b11f-11eb-9ee6-bbfdeb21b849.png)

#### Upload service [Post API]
  Creates metdata for the document, includes a unique document id, a signed URL for the client to upload the file to object store, associated users, user permissions and file size details. The signed URL returned as part of the metadata details reponse would be used by the client to upload the document to the document store.
#### Download service [Get API]
  Fetches all the document related metadata regarding the document using the unique document id. The returned metadata would include the file download URL from the object store 
#### Object store (eg: Amazon S3)
  Maintains uploaded files (cannot modify). An object store (S3) is preferred to block store(EBS) as majority use-case involves read of file. Also a small portion of the file (signature area) alone will get modified.
#### Metadata store (eg: MongoDB)
  Maintains information about file upload location, primary user, participating user. Majority of times, all metadata information w.r.t a particular document need to be fetched - hence suggest using document store like mongoDB.

#### Advantages:
  - Simple framework - quick to productize - could horizontally scale upload and download service
  - Could be used when number of participants for a document is very less, say 2 to 3.
#### Disadvantages:
  - Uploading/Downloading medium to large files could take a long time (improvements could be made using multi-part upload and partial download/streaming)
  - To avoid conflicts users should download latest copy before uploading modification

## MVP Improvements
#### Breaking files - chunks
![document-chunks (1)](https://user-images.githubusercontent.com/34787500/117602875-54f8a680-b16f-11eb-8892-bf80b5335392.png)

The **most critical portion of the whole process is breaking the files into multiple chunks**, this should effectively reduce the time consumed in upload/download of large files. Below are a few advantages of breaking the files into chunks.
1. Should increase the upload speed as multiple chunks could be uploaded together.
2. Only the modified portion of files needs to be uploaded. (checksum should help identify the modified chunks)
3. Each signature portion being broken into separate chunk 
    - may help in providing better fine-grained access control
    - avoiding complex concurrent merging of chunks
    - small byte transfer and quick synchornization of signature changes (realtime signature streaming)
    - help in separating static and dynamic chunks (static chunks could move to CDN and dynamic chunks could be in cache)
  
#### Concurrent modification 
By having each signature portion in a separate chunk - concurrent modification to the same chunk could be avoided. Incase same chunk needs to be modified by multiple users "operational transformation" or CRDT methodologies needs to be incorporated.

## Clientside App - Chunking
The app plays a key role in breaking the file to mutiple chunks. Below are steps to upload file from the client :
- The primary document owner could mark the signature areas
- The remaining file could be broken into equal chunks
- The metadata information of the file, chunks count, chunk order etc to be send to the upload service
- Upload service responds with the document id and signed urls for each of the chunk to be uploaded to S3
- Further upload service could push the necessary signature area chunks info to the cache server and the static chunks to CDN (optional)
- once upload is completed the upload service could be notified of success and ready state.
  


