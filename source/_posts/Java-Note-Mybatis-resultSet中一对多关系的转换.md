---
title: Java-Note-Mybatis-resultSet中一对多关系的转换
date: 2018-04-19 18:34:15
tags: Mybatis
category: Java
---
- 使用collection来将一对多的result set转换为实体类最后转化为json
- 例子:
    - result set:
    ```
    Columns: id, gmt_create, gmt_modify, is_active, sender_id, receiver_id, type, is_agreed, process_url, process_reason, id, gmt_create, gmt_modify, is_active, form_id, field_name, field_value
    Row: 1, 2018-04-18 18:58:06.0, 2018-04-18 18:58:06.0, 1, 19, 18, 0, null, null, null, 1, 2018-04-18 19:01:52.0, 2018-04-18 19:05:15.0, 1, 1, name, 德玛西亚
    Row: 1, 2018-04-18 18:58:06.0, 2018-04-18 18:58:06.0, 1, 19, 18, 0, null, null, null, 2, 2018-04-18 19:02:04.0, 2018-04-18 19:05:02.0, 1, 1, gender, 男
    Row: 1, 2018-04-18 18:58:06.0, 2018-04-18 18:58:06.0, 1, 19, 18, 0, null, null, null, 3, 2018-04-18 19:02:10.0, 2018-04-18 19:04:58.0, 1, 1, nation, 中国
    Row: 1, 2018-04-18 18:58:06.0, 2018-04-18 18:58:06.0, 1, 19, 18, 0, null, null, null, 4, 2018-04-18 19:02:16.0, 2018-04-18 19:04:54.0, 1, 1, birth, 19910526
    ```
    - 转换后的json:<!-- more -->
    ```
    [
        {
            "id": 2,
            "gmtCreate": 1524050347000,
            "gmtModify": 1524050347000,
            "isActive": true,
            "senderId": 18,
            "receiverId": 20,
            "type": 1,
            "isAgreed": true,
            "processUrl": null,
            "processReason": null,
            "customFormFields": [
                {
                    "id": 18,
                    "gmtCreate": 1524050438000,
                    "gmtModify": 1524050438000,
                    "isActive": true,
                    "formId": 2,
                    "fieldName": "社会组织名称",
                    "fieldValue": "2"
                },
                {
                    "id": 19,
                    "gmtCreate": 1524050472000,
                    "gmtModify": 1524050472000,
                    "isActive": true,
                    "formId": 2,
                    "fieldName": "法人登记证",
                    "fieldValue": "2"
                },
                {
                    "id": 20,
                    "gmtCreate": 1524050481000,
                    "gmtModify": 1524050481000,
                    "isActive": true,
                    "formId": 2,
                    "fieldName": "单位地址",
                    "fieldValue": "2"
                },
                {
                    "id": 21,
                    "gmtCreate": 1524050489000,
                    "gmtModify": 1524050489000,
                    "isActive": true,
                    "formId": 2,
                    "fieldName": "成立日期",
                    "fieldValue": "2"
                }
            ]
        }
    ]
    ```
- 方法: 在resultMap中使用collection, 
    - 参考资料: 官方文档 http://www.mybatis.org/mybatis-3/sqlmap-xml.html
    - 官方文档中的原话: `collection – a collection of complex types
nested result mappings – collections are resultMaps themselves, or can refer to one`
    - 实践: 
        - 其中的property填入一对多中"一"这一方(称之为A, 多称为B)的实体类中的代表B的列表的成员变量名, 在本例中为`customFormFields`
        - ofType填B的完全限定名, 本例中为`com.jeecms.cms.entity.CustomFormField`
        - 下一层填id和result
    - 例:
    ```
    <resultMap id="JoinFormFieldResultMap" type="com.jeecms.cms.entity.CustomForm">
        <id column="custom_form_id" jdbcType="INTEGER" property="id" />
        <result column="custom_form_gmt_create" jdbcType="TIMESTAMP" property="gmtCreate" />
        <result column="custom_form_gmt_create" jdbcType="TIMESTAMP" property="gmtModify" />
        <result column="custom_form_is_active" jdbcType="BIT" property="isActive" />
        <result column="custom_form_sender_id" jdbcType="INTEGER" property="senderId" />
        <result column="custom_form_receiver_id" jdbcType="INTEGER" property="receiverId" />
        <result column="custom_form_type" jdbcType="INTEGER" property="type" />
        <result column="custom_form_type" jdbcType="BIT" property="isAgreed" />
        <result column="custom_form_process_url" jdbcType="VARCHAR" property="processUrl" />
        <result column="custom_form_process_reason" jdbcType="VARCHAR" property="processReason" />
        <collection property="customFormFields" ofType="com.jeecms.cms.entity.CustomFormField">
        <id column="custom_form_field_id" jdbcType="INTEGER" property="id"/>
        <result column="custom_form_field_gmt_create" jdbcType="TIMESTAMP" property="gmtCreate" />
        <result column="custom_form_field_gmt_modify" jdbcType="TIMESTAMP" property="gmtModify" />
        <result column="custom_form_field_is_active" jdbcType="BIT" property="isActive" />
        <result column="custom_form_field_form_id" jdbcType="INTEGER" property="formId" />
        <result column="custom_form_field_field_name" jdbcType="VARCHAR" property="fieldName" />
        <result column="custom_form_field_field_value" jdbcType="VARCHAR" property="fieldValue" />
        </collection>
    </resultMap>
    ```
    - 注意: 字段名(包括column中)要用`as`做替换, 否则result set取出来为多行, 最后mybatis转换出来只有一行!!!
        - 替换示例:   
        ```
        <sql id="Form_Column_List">
            custom_form.id as custom_form_id, custom_form.gmt_create as custom_form_gmt_create,custom_form.gmt_modify as custom_form_gmt_modify,custom_form.is_active as custom_form_is_active,custom_form.sender_id as custom_form_sender_id, custom_form.receiver_id as custom_form_receiver_id,
            custom_form.type as custom_form_type, custom_form.is_agreed as custom_form_is_agreed, custom_form.process_url as custom_form_process_url, custom_form.process_reason as custom_form_process_reason
        </sql>
        ```