---
title: Mybatis-Note
date: 2018-02-06 19:03:56
tags:
category: Mybatis
---
- mybatis 中批量可选择性的 `update` 不同 `table` 中的 `data`
    - 方法:
        1. 前端传入表名 和 `List<LinkedHashMap>` 类型的 `valueList`<!-- more -->
        ```
        private String category;
        private List<LinkedHashMap> itemList;
        ```
        2. 中台根据不同的表名调用不同 `mapper` 类的 `update` 语句
        3. 在`mappers`包中的对应`xml`中编写`update`语句模板
        ```
        <update id="updateListSelective" parameterType="java.util.List" >
            <foreach collection="listOfUpdate" index="index" item="item" separator=";">
            update counter_item
            <set>
                <if test="item.price != null">
                price = #{item.price,jdbcType=DECIMAL},
                </if>
                <if test="item.count != null">
                count = #{item.count,jdbcType=INTEGER},
                </if>
                <if test="item.advisePrice != null">
                advise_price = #{item.advisePrice,jdbcType=DECIMAL},
                </if>
            </set>
            where
            counter_id = #{item.counterId,jdbcType=INTEGER}
            and
            barcode = #{item.barcode,jdbcType=VARCHAR}
            </foreach>
        </update>
        ```
        注意: `parameterType`为`List`, `<foreach>`标签中的字段前面要加列表内容名(即`item`), `mybatis`会自动识别`List<LinkedHashMap>`中的字段
- Mybatis 中 collection 中 List 的默认值为: list