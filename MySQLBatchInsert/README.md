# Mybatis 批处理
在使用 Mybatis 大量数据插入到 MySQL 的时候，以前采用的 sql 拼接 foreach 的方式进行的插入 ，防止 SQL 拼接过大，每次都执行 1000 条数据，会发现耗时比较久。
在批量插入的时候，发现 mybatis 中存在 batch 方法，比 sql 的 foreach 更快的方式

# 方法
1. jdbc 增加 rewriteBatchedStatements=true
2. 通过 sqlSessionFactory.openSession(ExecutorType.BATCH, false) 打开批处理， false 表示需要手动提交
3. 每次执行都是调用的单条数据插入语句
4. 有了 1000 条数据后，执行 sqlSession.commit();
5. 在执行完成后，执行 sqlSession.close();

# 数据比较：

|               | 100条          | 1000条            | 10000条              | 100000条             | 1000000条               |
|---------------|---------------|------------------|---------------------|---------------------|------------------------|
| 代码for循环插入     | 238/209/200ms | 1715/1686/1633ms | 17963/17135/16966ms | 太慢了，没测试   | 太慢了，没测试                |
| sql 拼接 foreach | 53/36/30ms    | 167/128/128ms    | 1710/1228/1456ms    | 10539/10337/11625ms | 103400/103634/103802ms |
| mybaits batch | 24/19/9ms     | 57/40/41ms       | 629/432/388ms      | 3485/3198/3578ms    | 32725/35179/47818ms    |

> 以上 foreach 和 batch 都是1000条数据插入一次