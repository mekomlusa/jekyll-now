---
layout: post
title: Hey you! Hit Database less!
---

It's almost at the conclusion of my web development internship, so I'm in a bittersweet mode right now. I thought that I was meant to be a data scientist (and yes I still love data munging, that never changes), but I had so much fun with web backend (aka [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)) as well.

This post won't contain any technical meat (sorry guys... and gals :P) but I do have a series of tech posts to share in the near future. Nonetheless, I should be reminded that every time when querying the database, think of a way to hit it less. Just moved a ticket today to "in review" and hopefully it could finally be approved. Basically, we need to send a JSON to another team in-house, and for photo attachments, we just send the attachment links in the blob. But the team would like us to provide thumbnails as well. (Yeah, I don't know why I always end up with photo thumbnails LOL) Right now the mobile request does not contain thumbnails links; however, it seems to me that every time when we save the attachment to our database, a thumbnail link is automatically created (could be a by-product of Azure storage I guess - no code forensic was performed though). So my solution was natural: ** For each attachment with mime type = photo, check if there is a thumbnail URL in the database**.

However...

What we really care is just a list of items that may contain photo attachments. Each item is associated with a list of attachments. Right now I'm looping through all the attachment of all the items one by one. I could have looped through all items, retrieve the attachment list of each item, then do some operations in memory.

In other words - consider the following case:

* I have 5 items, each item has 2 photo attachments.

My previous approach (looping through each attachment by ID): will hit the database 5 * 2 = 10 times.

The alternative approach (looping through each item, retrieve the attachment list and do something in a separate thread): will hit the database 5 times, since each time I get a list of attachments of the item.

See? a 50% performance gain!

Let's hope that the senior developer won't discover this and will approve my PR :D
