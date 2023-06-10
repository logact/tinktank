sudo docker run \
--name mysql8 \
-d \
-p 3306:3306 \
--restart unless-stopped \
-v /mydata/mysql8/log:/var/log/mysql \
-v /mydata/mysql8/data:/var/lib/mysql \
-v /mydata/mysql8/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
