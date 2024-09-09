---
layout: post
title: "Bypassing Fotor Paywall"
tags: ["engineering"]
---

Recently, I needed to upscale my passport-sized photo, which was blurry, and I didn't have time to get a new picture taken for some document that I didn’t care much about. After 5 seconds of googling, I stumbled upon Fotor, which seemed like a good tool for the job, and it’s true. When the unblurring was done, I could see the result right in front of my eyes, but unfortunately, I had to pay in order to download the enhanced image. Since it was just a one-time thing, I didn’t want to pay. Well, I could have taken a screenshot and saved it, but where’s the fun in that?

As I don’t get views on my blog, and I’m grateful for that, I’m going to write about it without worrying that someone’s going to misuse this information. Anyway, here’s how you can handle things when you’re done editing your photo on Fotor and it requires you to pay.

__Step 0: Click on Download__

It's important that you click on the <button class="btn" type="">Download</button> button, which is on the top right corner of the canvas. There, you'll find something similar to -

<img src="{{site.baseurl}}/assets/images/posts/fotor-one.png" alt="">

Sample Image. The original image size was 3.5 MB.
{: .center}

__Step 1: Inspect the Image Element__

Locate the image element in the DOM. The highlighted element will look something like this:

```html
<img class="_2weKW" src="data:image/png;base64,...">
```

<img src="{{site.baseurl}}/assets/images/posts/fotor-two.png" alt="">

__Step 2: Extract the Base64 String__

Now that we have located the image element, the next step is to extract the base64 string from the `src` attribute. This base64 string represents the image data -

```
let base64String = document.getElementsByClassName('borderImg')[0].src
// or
let base64String = document.querySelector('.BBrpF ._2kUSF ._2weKW>img').src;
```

<img src="{{site.baseurl}}/assets/images/posts/fotor-three.png" alt="">

>-  __Show More (4.7 MB) Copy__

__Step 3: Check the Size of the Base64 String__

Even though Chrome indicated that the string was too large to display in the console directly, I clicked on the Show More button to see the entire string and copied it. 🔥 Smart move. After that, my system slowed down, and when I pasted it in a new tab, only half of the image was visible. It got corrupted. There are multiple reasons for this, and I'll write about them later.

This step is not required, but it helps you understand what happens when you deal with large strings, as large base64 strings can be impractical to handle manually.

```js
console.log(`Base64 string size: ${base64String.length} characters`);

// Base64 string size: 4685630 characters
```

Now, we can convert this into a human-readable format -

\\[ \text{MB} = \frac{\text{characters (bytes)}}{1,000,000} \\]

\\[ \text{MB} = \frac{4685630 \text{ bytes}}{1,000,000} \approx 4.686 \text{ MB} \\]


When you convert a base64-encoded string to its original binary format, the size of the data will change. Base64 encoding increases the size of the data by approximately 33%

- The encoding represents binary data using ASCII characters, and every 3 bytes of binary data are encoded into 4 characters of base64.
- This means that the base64 string is approximately 4/3 (or 1.33) times the size of the original binary data, so we need to calculate the size of the image accordingly.

\\[ \text{Original binary size (in bytes)} = \frac{\text{Base64 size (in bytes)}}{1.33} \\]

\\[ \text{Base64 size (in bytes)} = 4.686 \times 1,000,000 \approx 4,686,000 \text{ bytes} \\]

\\[ \text{Original binary size (in bytes)} = \frac{4,686,000 \text{ bytes}}{1.33} \approx 3,525,563.91 \text{ bytes} \\]

\\[ \text{Original binary size (in MB)} = \frac{3,525,563.91 \text{ bytes}}{1,000,000} \approx 3.526 \text{ MB} \\]

It’s almost equal to the size of the original image on Fotor, so the calculation is accurate.

__Step 4: Convert the Base64 String to a Blob__

A base64 string is not directly downloadable, so we need to convert it to a Blob (Binary Large Object). A Blob represents binary data and can be treated like a file.

- Define a function to handle the conversion:

```js
function base64ToBlob(base64, mime) {
    let byteString = atob(base64.split(',')[1]); // Decode the base64 string
    let ab = new ArrayBuffer(byteString.length); // Create an ArrayBuffer
    let ia = new Uint8Array(ab); // Create a typed array view
    for (let i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i); // Convert each character to a byte
    }
    return new Blob([ab], { type: mime }); // Create a Blob with the array buffer and MIME type
}
```

- Extract the MIME type from the base64 string and convert it to a Blob:

```js
let mimeType = base64String.match(/data:([^;]+);/)[1];
let blob = base64ToBlob(base64String, mimeType);
```

__Step 5: Create a Download Link__

With the Blob ready, the next step is to create a download link that allows to save the image.

- Create an anchor element and set its href attribute to a URL representing the Blob:

```js
let link = document.createElement('a');
link.href = URL.createObjectURL(blob);
```

- Set the download attribute to specify the file name:

```js
link.download = 'meowtograph.png';
```

3. Append the link to the document, click it programmatically to trigger the download, and then remove the link:

```js
document.body.appendChild(link);
link.click();
document.body.removeChild(link);
```

__Full Script__

Here's the complete script which you can paste in case you edit your photo by using Fotor's premium tools and need to download it -

```js
let base64String = document.querySelector('.BBrpF ._2kUSF ._2weKW>img').src;

function base64ToBlob(base64, mime) {
    let byteString = atob(base64.split(',')[1]);
    let ab = new ArrayBuffer(byteString.length);
    let ia = new Uint8Array(ab);
    for (let i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
    }
    return new Blob([ab], { type: mime });
}

let mimeType = base64String.match(/data:([^;]+);/)[1];
let blob = base64ToBlob(base64String, mimeType);
let link = document.createElement('a');
link.href = URL.createObjectURL(blob);
link.download = 'upscaled_image.png';
document.body.appendChild(link);
link.click();
document.body.removeChild(link);
```

__Alternatively__

You can open the Network tab and switch between JPG and PNG, and you'd find -

<img src="{{site.baseurl}}/assets/images/posts/fotor-four.png" alt="">

LMAO
{: .center}

Now you can either open the `data:image/jpeg` in a new tab, and it will get saved as the image, so you don't have to go through all of that written above. 🧌



### 💭 Coming Back To 🚶

- __Understanding why the system slows down or an application crashes during a large copy-paste operation__

When we copy data to the clipboard, the OS needs to allocate memory to store that data. A large Base64 string requires a significant amount of memory, which leads to high memory usage. If the system has limited RAM (in my case, 8 GiB), this can cause it to swap data to disk, which is much slower than RAM, resulting in an overall system slowdown. I had other applications up and running.

Clipboard operations often involve inter-process communication (IPC) between the application you’re copying from, the OS’s clipboard manager, and the application you’re pasting into. Large data transfers cause significant delays during this IPC. Additionally, on Linux, shared memory segments are used for large data transfers, requiring synchronization mechanisms (like semaphores) to coordinate access, which adds overhead, especially if you have a less powerful machine.

Applications freeze because they need to allocate memory, serialize data, and communicate with the clipboard manager. This is often done on the main UI thread, blocking other operations and making the application seem unresponsive.

- __Corrupted Image__

1. When you copy the Base64 string to the clipboard, the browser serializes the string data into a format suitable for clipboard storage. This process involves converting the string into a sequence of bytes. For large strings, this can be a complex process.

2. When you paste the Base64 string into a new tab, the browser then deserializes the data from the clipboard back into a string. If there is any issue during the deserialization process, such as an incomplete or corrupted data transfer, the resulting string will be truncated or malformed.

3. Now, any corruption in the serialized data can cause issues during deserialization, which can result in an incomplete or incorrect image. So, corruption can occur at various stages, including during serialization, clipboard storage, or deserialization.

🛺 __I my case, I__ ---

- Open the JavaScript console in your browser.
- Execute code to get the Base64 string of an image.
- Manually copy the entire Base64 string from the console output or from the img.src.
- The system slows down, but I paste it into Sublime Text, and the editor freezes.
- Wait for a while.
- Open a new tab in Chrome.
- Paste the Base64 string you copied.
- The browser attempts to render the image from the Base64 string.
- The string is incomplete or corrupted, and the browser can only render part of the image, leading to a partially loaded image.
