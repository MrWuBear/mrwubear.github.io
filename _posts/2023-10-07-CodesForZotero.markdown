---
layout: post
title: Codes For Zotero
date: 2023-10-07
description:  # Add post description (optional)
img: zoteroImg.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Zotero,js]
---


- **即时删除rss**
    - ```
        ZoteroPane.getSelectedItems()[0].eraseTx({ skipEditCheck: true })`
    ```

- **删除超时**
    - ```
        // 获取所有feeds
        var feeds = Zotero.Feeds.getAll();
        // 获取现在时间
        var nowTimeStamp = new Date();
        for (let feed of feeds){
            // feed最新更新时间
            var feedLastTime = feed["lastUpdate"];
            var feedLastTimeStamp = new Date(feedLastTime)
            // feed下所有item
            var s = new Zotero.Search();
            s.libraryID = feed.libraryID;
            var ids = await s.search();
            var items = await Zotero.Items.getAsync(ids);
            
            for (let item of items){
                // item 时间
                var itemTimeStamp = new Date(item.dateAdded)
                var gap = nowTimeStamp - itemTimeStamp
                // 如果 item 距现在时间大于一天，且不是最新更新的则剔除
                if (gap>3600*1000*24 & itemTimeStamp<feedLastTimeStamp){
                    item.eraseTx({ skipEditCheck: true })
                }
            }
        }
    ```
- 
- **即时删除所有rss**
    - ```
        var feeds = ZoteroPane.getSelectedItems();
        for (let feed of feeds){
        await feed.eraseTx({ skipEditCheck: true });
        }
    ```


- **即时取消订阅所有RSS**
    - ```
        var feeds = Zotero.Feeds.getAll();
        for (let feed of feeds){
            await feed.eraseTx({ skipEditCheck: true });
        }
    ```

- **批量添加网页**
    - ```
        // Open
        C:\Users\User Name\AppData\Local\Google\Chrome\User Data\Default\Bookmarks
        // Edit And Save
        {"type":"url","name":"Name","url":"URL"},
        // Open Chrome And Export BookMarks To Html
        // Open Zotero And Import
    ```

- **选定项目更新网页快照**
    - 参考资料
        - https://gist.github.com/jryans/f2631cf3b2c500817cdf79a791209bca
        - https://forums.zotero.org/discussion/85656/script-to-convert-old-web-snapshots-to-new-format
    - ```
        // Delete Existed Attachments
        async function deleteAttach(item){
            try {
                var oldAttachmentID = item.getAttachments();
                await Zotero.Items.trashTx(oldAttachmentID); 
            } catch (e) {
                // Zotero.debug(e);
            }
        }

        // Recapture Webpage
        async function ReCapture(item){
            var libraryID = item.libraryID;
            var parentItemID = item.id;
            var url = item.getField("url");
            try {
                await Zotero.Attachments.importFromURL({
                    libraryID,
                    parentItemID,
                    url,
                    title: "Snapshot",
                    contentType: "text/html",
                });
                // Zotero.debug(`Snapshot done: ${item.getField('title')}`)
            } catch (e) {
                // Zotero.debug(e);
            }
        }

        // Obtain Selected Items
        var items = Zotero.getActiveZoteroPane().getSelectedItems();

        // Get Selected item's id
        var itemIdLists = [];
        for (let item of items){
            var itemId = item.id;
            itemIdLists.push(itemId);
        }

        // Obtain Items
        var items = await Zotero.Items.getAsync(itemIdLists);

        // Processing
        for (let item of items) {
            await deleteAttach(item);
            await ReCapture(item);
        }
    ```




