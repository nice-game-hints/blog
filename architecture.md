---
title: Architecture of Nice Game Hints
author: Juho Rutila
released: 2020-06-27
---

Nice Game Hints is built with the premise that the contents of the guides don't really change after publishing. The guide author will write the guide and after that the guide pretty much just stands there waiting for readers.

For this kind of premise a static served web page makes most sense. This means that the pages for the site are generated beforehand and when the reader loads the page the page is served as is. There is no backend server at all as there is no need to authenticat the reader or load the guide from a database.

Guides are stored as markdown files in a Azure Blob Storage. When the guide is released an Azure Function webhook is called and the zipball is downloaded from Github and extracted into the blob storage. There are other functions that are triggered from the new blobs. One function reads the markdown file and creates a json file that has more information about a single guide file. For example the subguides and their titles are precalculated into the json file at this point. This way the frontend can read only this one json file to show everything that is needed to present to the reader.

The frontend is built with a Vue-based Nuxt framework. Nuxt is nice as it can work with a server side rendering but also with client rendering modes. This is taken advantage of by pre-rendering the pages. When a new guide is released a prerender function is triggered and it will create the statically served html file of the each guide page. These prerender pages are then served as static website from the blob storage.

The whole frontend works without the prerender htmls and json files. Just putting the markdown files into the correct place a call to the sites index.html can render the guide correctly. It is just a little bit slower.

The problem, of course, arises when the application is changed. The application is prerendered to all of those hundreds of guide pages. This means that changes to the application cause all of these pages to be prerendered again. With current amount of guide pages this is not a problem. When there are guides for thousands of games some optimization about the prerendering needs to be done.
