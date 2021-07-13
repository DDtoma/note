1. 查询时排除pojo类中的指定字段
@TableField(exist = true) 表示该属性不为数据库表字段，但又是必须使用的
2. 常用的pojo类上的注解
@TableName：数据库表相关
@TableId：表主键标识
@TableField：表字段标识
@TableLogic：表字段逻辑处理注解（逻辑删除）