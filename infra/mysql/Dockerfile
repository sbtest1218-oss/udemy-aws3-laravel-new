# 公式イメージ（library/mysql）のLTS版を使用。
# 旧: mysql/mysql-server:8.0 は 2023年1月を最後に更新が止まっており（Oracleが公式イメージに一本化）、
# セキュリティ修正が入らないため公式の mysql:8.4 に移行。
FROM mysql:8.4

ENV TZ=Asia/Tokyo

COPY ./my.cnf /etc/my.cnf
RUN chmod 644 /etc/my.cnf
