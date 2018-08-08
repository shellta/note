## MySQL 优化

1. 为查询缓存优化你的查询
2. EXPLAIN 你的 SELECT 查询
3. 当只要一行数据时使用 LIMIT 1
4. 为搜索字段建索引
5. 在Join表的时候使用相当类型的列，并将其索引
6. 千万不要 ORDER BY RAND()
7. 避免 SELECT *
8. 永远为每张表设置一个ID (主键)
9. 使用 ENUM 而不是 VARCHAR
10. 从 PROCEDURE ANALYSE() 取得建议
11. 尽可能的使用 NOT NULL
12. Prepared Statements
13. 无缓冲的查询
14. 把IP地址存成 UNSIGNED INT
15. 固定长度的表会更快
16. 垂直分割
17. 拆分大的 DELETE 或 INSERT 语句

