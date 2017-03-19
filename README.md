# node-MySQL
## 一、整体思路
分为三个方面

* 数据库
* 服务器
* 客户端



## 1.数据库
使用mySql数据库 进行文件存储
SQL 代码：

      DROP TABLE IF EXISTS `news`;
         CREATE TABLE `news` (
           `id` int(11) NOT NULL AUTO_INCREMENT,
           `title` varchar(255) DEFAULT NULL,
           `time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
           `content` text,
           `click` int(255) DEFAULT NULL,
           `title_img` varchar(255) DEFAULT NULL,
           PRIMARY KEY (`id`)
         ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

   1. 建立数据库news ·

    > |       key       |   value       |
      | ----------------|:-------------:|
      | Source Server   | mysql         |
      | Source Server V | 100119        |
      | Source Host     | localhost:3306|
      | Source Database | news          |

2. 建立表news

     > 含义|字段|类型|长度|其他属性
       ------|------|----|-----|--------
       ID|id|int|11|非空、主键、自动递增
       标题|title|varchar|255|无
       发布时间|time|datetime|0|无
       内容|content|text|0|无
       点击量|click|int|255|无
       标题图片|title_img|varchar|255|无

## 2.服务器
服务器分为几个模块（整体思路从数据库反向推导）

* 将连接信息保存为单独文件（config.js文件)

        module.exports = {
            mysql: {
            host: '127.0.0.1',
            user: 'root',
            password: '',
            database:'news', // 数据库
            port: 3306
            }
        };

* 数据库语句模块（对新闻表的增删改查）

        var user = {
        insert:'INSERT INTO news (title, content, title_img) VALUES ( ?, ?, ?)',
        update:'update news set title=?, content=?, title_img=? where id=?',
        update_click:'update news set click=click+1 where id=?',
        delete: 'delete from news where id=?',
        queryById: 'select * from news where id=?',
        queryAll: 'select * from news'
        };

* dao模块 数据库连接模块（用数据库连接池进行连接）


        var pool  = mysql.createPool($config.mysql);
        pool.getConnection(function(err,conn){
                    if(err){
                        console.log(err);

                    }else{
                        var data=['新闻','neirong',''];
                        conn.query($sql.insert,data,function(err,vals){
                            console.log(err);
                            console.log(vals);

                            >//some

                            //释放连接
                            conn.release();
                        });
                    }
                });

* api路由设置

         addNews、allNews，delNews、updateNews、updateClick、selNewById

        router.get('/addNews', function(req, res, next) {
            userDao.addNews(req, res, next);
            // res.send('respond with a resource');
        });

* 后台页面路由

    > app.use('/admin',admin);

    后台页面模版使用jade，利用bootstrap生成

## 3.客户端

* 后台客户端
