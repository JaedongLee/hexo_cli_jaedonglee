---
title: Java-Note-Mybatis-resultSet中的数据映射到collection中有相同字段的resultMap中出现的问题
date: 2018-04-20 19:42:27
tags: Mybatis
category: Java
---
- 原因: 当resultMap中包含collection时, 如果collection中的字段的名称与resultMap中的相同且未对字段名用as重命名时, resultSet映射到resultMap上时, 会同时映射到resultMap和collection中相同的字段
- 解决方法: 改column的名称
- 例:<!-- more -->
```
原:         
  <resultMap id="BaseResultMap" type="com.jeecms.cms.entity.CustomForm">
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="gmt_create" jdbcType="TIMESTAMP" property="gmtCreate" />
    <result column="gmt_modify" jdbcType="TIMESTAMP" property="gmtModify" />
    <result column="is_active" jdbcType="BIT" property="isActive" />
    <result column="sender_id" jdbcType="INTEGER" property="senderId" />
    <result column="receiver_id" jdbcType="INTEGER" property="receiverId" />
    <result column="type" jdbcType="INTEGER" property="type" />
    <result column="is_agreed" jdbcType="BIT" property="isAgreed" />
    <result column="process_url" jdbcType="VARCHAR" property="processUrl" />
    <result column="process_reason" jdbcType="VARCHAR" property="processReason" />
    <collection property="customFormFields" ofType="com.jeecms.cms.entity.CustomFormField">
      <id column="id" jdbcType="INTEGER" property="id"/>
      <result column="gmt_create" jdbcType="TIMESTAMP" property="gmtCreate" />
      <result column="gmt_modify" jdbcType="TIMESTAMP" property="gmtModify" />
      <result column="is_active" jdbcType="BIT" property="isActive" />
      <result column="form_id" jdbcType="INTEGER" property="formId" />
      <result column="field_name" jdbcType="VARCHAR" property="fieldName" />
      <result column="field_value" jdbcType="VARCHAR" property="fieldValue" />
    </collection>
  </resultMap>          
现在:
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
- 注意: include标签中引用的sql标签也要用as更改
