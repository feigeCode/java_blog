# 1、需求分析和数据库设计

![image-20200213124158980](.\img\image-20200213124158980.png)

~~~sql
CREATE DATABASE myblog CHARACTER SET utf8;

DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `username` varchar(10) NOT NULL,
  `email` varchar(20) NOT NULL,
  `password` varchar(18) NOT NULL,
  `sex` tinyint(1) NOT NULL DEFAULT '1',
  `time` datetime NOT NULL,
  PRIMARY KEY (`username`),
  UNIQUE KEY `email` (`email`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert into user values('feige1','18356987756@qq.com','123456789','1','2020-2-13 8:30:45'),
('feige2','18356987751@qq.com','123456789','1','2020-2-13 8:30:45'),
('feige3','18356987752@qq.com','123456789','0','2020-2-13 8:30:45'),
('feige4','18356987753@qq.com','123456789','1','2020-2-13 8:30:45'),
('feige5','18356987754@qq.com','123456789','0','2020-2-13 8:30:45'),
('feige6','18356987755@qq.com','123456789','0','2020-2-13 8:30:45');
~~~

![image-20200218103612089](.\img\image-20200218103612089.png)