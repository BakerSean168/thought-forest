---
tags:
  - tech/ops/database
  - type/howto
  - status/growing
description: Mysql
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[数据库 MOC]] | [[后端开发 MOC]]

---


# Mysql 

## 命令速查

*mysql 语句末尾都要加 `;`，表示结束*

| 命令                                       | 说明      | 示例                                             |
| ---------------------------------------- | ------- | ---------------------------------------------- |
| `mysql -u <用户名> -p`                      | 进入mysql | - mysql -u root -p<br>- mysql -u root -p123456 |
| `show databases`                         |         |                                                |
| `show tables`                            |         |                                                |
| `select <column-name> from <table-name>` |         | - `select * from users`                        |


docker exec -i b099172ab8e2 mysql -u root -p123456 ruoyi-vue-pro < d:\workProgram\ddcc-cloud\sql\mysql\migration_add_missing_columns_to_system_users.sql

