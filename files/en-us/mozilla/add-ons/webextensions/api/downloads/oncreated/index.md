---
title: downloads.onCreated
slug: Mozilla/Add-ons/WebExtensions/API/downloads/onCreated
page-type: webextension-api-event
browser-compat: webextensions.api.downloads.onCreated
sidebar: addonsidebar
---

The **`onCreated()`** event of the {{WebExtAPIRef("downloads")}} API fires when a download begins, i.e., when {{WebExtAPIRef("downloads.download()")}} is successfully invoked.

The listener is passed the {{WebExtAPIRef('downloads.DownloadItem')}} object in question as a parameter.

## Syntax

```js-nolint
browser.downloads.onCreated.addListener(listener)
browser.downloads.onCreated.removeListener(listener)
browser.downloads.onCreated.hasListener(listener)
```

Events have three functions:

- `addListener(listener)`
  - : Adds a listener to this event.
- `removeListener(listener)`
  - : Stop listening to this event. The `listener` argument is the listener to remove.
- `hasListener(listener)`
  - : Check whether a given `listener` is registered for this event. Returns `true` if it is listening, `false` otherwise.

## addListener syntax

### Parameters

- `function`
  - : The function called when this event occurs. This function is passed this argument:
    - `downloadItem`
      - : The {{WebExtAPIRef('downloads.DownloadItem')}} object in question.

## Examples

Log the URL of items as they are downloaded:

```js
function handleCreated(item) {
  console.log(item.url);
}

browser.downloads.onCreated.addListener(handleCreated);
```

{{WebExtExamples}}

## Browser compatibility

{{Compat}}

> [!NOTE]
> This API is based on Chromium's [`chrome.downloads`](https://developer.chrome.com/docs/extensions/reference/api/downloads#event-onCreated) API.

<!--
// Copyright 2015 The Chromium Authors. All rights reserved.
//
// Redistribution and use in source and binary forms, with or without
// modification, are permitted provided that the following conditions are
// met:
//
//    * Redistributions of source code must retain the above copyright
// notice, this list of conditions and the following disclaimer.
//    * Redistributions in binary form must reproduce the above
// copyright notice, this list of conditions and the following disclaimer
// in the documentation and/or other materials provided with the
// distribution.
//    * Neither the name of Google Inc. nor the names of its
// contributors may be used to endorse or promote products derived from
// this software without specific prior written permission.
//
// THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
// "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
// LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
// A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
// OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
// SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
// LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
// DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
// THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
// (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
// OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
-->
