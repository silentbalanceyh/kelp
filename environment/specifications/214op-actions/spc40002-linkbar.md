# SPC40002 - DataList中的编辑和删除按钮专用配置

## 1. Example



## 2. Tree Map

## 3. Comments

1. LinkBar专用配置：LinkBar目前仅用于内置DataList中，不配置ui.column
2. LinkBar配置信息
3. ```
   # ui.control -> （内置配置）
   "spec":{
       "tables":{
           "data":...
           "roomlist":[
               ...
               {
                   // Required & Shared
                   "type":"cell.LinkBar",
                   "cid":"colRowIndex",

                   // Required
                   "field":"$INDEX$",
                   "remove":{
                       "form":"fmForm",
                       "field":"orderItems"
                   },
                   "edit":{
                       "form":"fmRoomForm"
                   }
               }
           ]
       }
   }
   ```

   * Required & Shared：可在ui.column中使用的配置，一般不为Column必须配置
   * Required：remove和edit分别对应两个按钮，如果不配置则表示不显示这两个按钮
     * remove：删除按钮，form表示删除的form信息，field则表示删除的form上对应的字段信息
     * edit：编辑按钮，form表示点击了编辑过后当前记录数据需要填充到哪个form
     * field：索引字段，索引字段只能为$INDEX$这个值，不可以是其他值，这个会影响两个按钮的行为，必须填写

## 4. Development



