---
title: Springframework-Note
date: 2018-03-08 18:10:04
tags:
category: springframework
---
- springframework事物回滚注意事项
    - throw 
        - must to throw something out of method, that springfamework can know that it need to rollback.
        - spring will rollback all transactions in the method even though only one transaction is error
        - example:<!-- more -->
        ```
        Item item = new Item();
        item.setBarcode("28");
        item.setTitle("超越无限");
        item.setDescription("身为风帆, 总要拓展");
        List<Item> itemList1 = new ArrayList<>();
        List<Item> itemList2 = new ArrayList<>();
        itemList1.add(item);
        try {
            itemMapper.insertItems(itemList1);
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
        try {
            itemMapper.insertItems(itemList2);
        } catch (Exception e) {```
            e.printStackTrace();
            throw e;
        }
        ```
        spring will rollback both itemList1 and itemList2 even only itemList2 is wrong
        ```
        Item item = new Item();
        item.setBarcode("28");
        item.setTitle("超越无限");
        item.setDescription("身为风帆, 总要拓展");
        List<Item> itemList1 = new ArrayList<>();
        List<Item> itemList2 = new ArrayList<>();
        itemList1.add(item);
        try {
            itemMapper.insertItems(itemList1);
                try {
                    itemMapper.insertItems(itemList2);
                } catch (Exception e) {
                    e.printStackTrace();
                    throw e;
                }
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
        ```
        spring will rollback itemList1 even itemList1 doesn't throw expection, because itemList2 throws