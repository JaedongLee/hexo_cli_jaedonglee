---
title: Base64-Note
date: 2018-04-23 19:18:09
tags: 
category: Base64
---
- js加密: 用FileReader中的readAsDataURL(file)即可, 但注意, 得到的字符串中, 第一个(也是唯一一个)`,`之前保存的是文件类型和加密类型, 之后保存的才是加密后的字符串
    - FileReader: `The FileReader object lets web applications asynchronously read the contents of files (or raw data buffers) stored on the user's computer, using File or Blob objects to specify the file or data to read.`
        - 异步方法的处理: 当FileReader触发了一些事件后, 会返回相应的数据:
            - onload: 加载完成时触发, 会返回结果
            - onerror: 读取操作发生错误时触发
            - 等等
    - 例子: 
    ```
    var reader = new FileReader();
    reader.readAsDataURL(fileObj);
    reader.onload = function () {
        var encodeString = reader.result;
        console.log(encodeString);
        customFormField1.fieldName = "customFormField1Name";
        customFormField1.fieldValue = "customFormField1Value";
        customFormField0.fieldName = "customFormField0Name";
        customFormField0.fieldValue = "customFormField0Value";
        customFormFileField0.name = "customFormFileField0Name";
        customFormFileField0.encodedContent = encodeString;
        customFormFileField0.postfix = "docx";
        customFormFields[0] = customFormField0;
        customFormFields[1] = customFormField1;
        customFiles[0] = customFormFileField0;
        customForm.customFormFields = customFormFields;
        customForm.type = 0;
        customForm.senderId = 19;
        request.customForm = customForm;
        request.customFiles = customFiles;
        request.integer = 7;
        request.string = "秘书长";
        request = JSON.stringify(request);
        $.ajax({
            type: "POST",
            url: "http://192.168.0.113:8090/form/addForm.jspx",
            data: request,
            dataType: "json",
            contentType: "application/json",
            cache: false,
            processData: false,
            success: function (data) {
                console.log("success: " + data);
            },
            error: function (data) {
                console.log("fail: " + data);
            }
        });
    }
    ```
- java
    - 加密: 
        - 先使用NIO中的Files.readAllBytes读取文件为byte[]
        - 再用java.util中的Base64.getEncoder().encodeToString()加密
    - 解密:
        - 如果加密的string中包含文件类型和加密类型信息, 先截取掉
        - 利用Base64.getDecoder().decode将String解密成byte[]
        - 利用Files.write将byte[]写入指定位置