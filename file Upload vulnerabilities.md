# Overwriting Existing Files.
====================================
![4a79e61655944c8100520fac5cad6599.png](:/c284408382004e1599236c638481cacc)

Let's download another image from the internet and call it `spaniel.jpg`. We'll then upload it to the site and see if we can overwrite the existing image:

![208602da0b8daf4c2f6b9a3d0c6994d3.png](:/c67a26d60a354cac96ea228fa09225c7)

Check wheather if you can overwrite the existing file.


# Trying for RCE
========================
Upload file with malicious content and execute to check if you can get remote
code execution.

# Bypassing Filtering
===========================
First up, let's discuss the differences between client-side filtering and server-side filtering.

When we talk about a script being "Client-Side", in the context of web applications, we mean that it's running in the user's browser as opposed to on the web server itself. JavaScript is pretty much ubiquitous as the client-side scripting language, although alternatives do exist.  Regardless of the language being used, a client-side script will be run in your web browser. In the context of file-uploads, this means that the filtering occurs before the file is even uploaded to the server. Theoretically, this would seem like a good thing, right? In an ideal world, it would be; however, because the filtering is happening on our computer, it is trivially easy to bypass. As such client-side filtering by itself is a highly insecure method of verifying that an uploaded file is not malicious.

Conversely, as you may have guessed, a server-side script will be run on the server. Traditionally PHP was the predominant server-side language (with Microsoft's ASP for IIS coming in close second); however, in recent years, other options (C#, Node.js, Python, Ruby on Rails, and a variety of others) have become more widely used. Server-side filtering tends to be more difficult to bypass, as you don't have the code in front of you. As the code is executed on the server, in most cases it will also be impossible to bypass the filter completely; instead we have to form a payload which conforms to the filters in place, but still allows us to execute our code.

### Different Kinds of filtering
**1. Extension Validation**

**2. File Type Filtering:**
![0c19d3d4900ca1acf5c404e532b5ec34.png](:/3d2475fa5e1a401fbc19812515be247f)

**3. Magic Number validation**
![028dd6259c80d4f2a51d8a9666f399fc.png](:/bb04acb323e74be19e36d2cfe4b53295)

**4. File Length Filtering:**
File length filters are used to prevent huge files from being uploaded to the server via an upload form (as this can potentially starve the server of resources). In most cases this will not cause us any issues when we upload shells; however, it's worth bearing in mind that if an upload form only expects a very small file to be uploaded, there may be a length filter in place to ensure that the file length requirement is adhered to. As an example, our fully fledged PHP reverse shell from the previous task is 5.4Kb big -- relatively tiny, but if the form expects a maximum of 2Kb then we would need to find an alternative shell to upload.

**5. File Name Filtering:**

As touched upon previously, files uploaded to a server should be unique. Usually this would mean adding a random aspect to the file name, however, an alternative strategy would be to check if a file with the same name already exists on the server, and give the user an error if so. Additionally, file names should be sanitised on upload to ensure that they don't contain any "bad characters", which could potentially cause problems on the file system when uploaded (e.g. null bytes or forward slashes on Linux, as well as control characters such as ; and potentially unicode characters). What this means for us is that, on a well administered system, our uploaded files are unlikely to have the same name we gave them before uploading, so be aware that you may have to go hunting for your shell in the event that you manage to bypass the content filtering.

**6. File Content Filtering:**

More complicated filtering systems may scan the full contents of an uploaded file to ensure that it's not spoofing its extension, MIME type and Magic Number. This is a significantly more complex process than the majority of basic filtration systems employ, and thus will not be covered in this room.


# Bypassing Magic Number check
=============================================
magic numbers are used as a more accurate identifier of files. The magic number of a file is a string of hex digits, and is always the very first thing in a file. Knowing this, it's possible to use magic numbers to validate file uploads, simply by reading those first few bytes and comparing them against either a whitelist or a blacklist.

1. Add Random 4 characters to the file to upload, so that we can change that using hexeditor.
	![fda8d1f595f8212ce5b053bfc9c5b3ab.png](:/ac642e1199c34e358173fa1fd431d209)
	
2. Open that file in hex-editor
![24f9d7a458f2a8f5ef41b52f936af436.png](:/4231d9f52b5c42b8b3f011e24768a4db)

3. Change this to the magic number for files that server allowed to upload
![261e20e017c23b7eeaed06565e4aef8f.png](:/763f4d08c2364aea952033640179645b)
