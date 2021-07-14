## C++与MySQL的结合——Linux下使用MySQL++

### 安装MySQL++

- 安装必要的依赖

```shell
sudo apt-get install libmysqlclient-dev
```

- 解压安装包并进入mysql++3.3.0文件夹

```bash
tar -zxvf mysql++-3.3.0.tar.gz
```

- 编译

```bash
sudo make
```

- 安装：这一步骤会将动态库复制到/usr/local/lib下，以及将头文件复制到/usr/local/include/mysql++下。

```bash
sudo make install
```

### 使用

- 因为我直接连接实验室的mysql-server，所以本机没有安装mysql-server，也可以自己安装mysql-server，比较简单，百度一下你就懂了。

- 测试小程序

```c++
#include <mysql++.h>
#include <iostream>
  
using namespace std;
int main(int argc, char* argv[]) {
    if (argc < 4) {
      cout << "请安装以下格式输入：要查询的数据库 mysql服务器地址 用户名 密码" << endl;
    }
    const char* db = 0, *server = 0, *user = 0, *password = "";
    db = argv[1];
    server = argv[2];
    user = argv[3];
    password = argv[4];

    mysqlpp::Connection conn(false); 
    if (conn.connect(db, server, user, password)) {// 创建连接
        cout << "connect db succeed. " << endl;
        mysqlpp::Query query = conn.query("SELECT * FROM orders"); // 查询
        if (mysqlpp::StoreQueryResult res = query.store()) // 输出
        {
          cout.setf(ios::left);
          cout << setw(15) << "order_num" <<
          setw(20) << "order_date" <<
          setw(10) << "cust_id" << endl;
 
          mysqlpp::StoreQueryResult::const_iterator it;
          for (it = res.begin(); it != res.end(); ++it) 
          {
            mysqlpp::Row row = *it;
            cout << setw(10) << row[0] << ' ' <<
            setw(23) << row[1] << ' ' <<
            setw(10) << row[2] << ' ' <<
            endl;
          }
        }
    } else {
        cout << "connect db fail. " << endl;
    }
    conn.disconnect();
    return 0;
}
```

- 编译

  - 将动态库位置加入环境变量

  ```bash
  vim ~/.bashrc
  在文件的最后添加字段
  export LD_LIBRARY_PATH=/usr/local/lib
  ```

  - 

  ```bash
  g++ mysql_test.cpp -o query -I /usr/include/mysql -I /usr/local/include/mysql++ -l mysqlpp
  ```

- 运行

```bash
./query crashcourse 10.24.88.23 root 123456
```

- 结果

```c++
connect db succeed. 
order_num      order_date          cust_id   
20005      2005-09-01 00:00:00     10001      
20006      2005-09-12 00:00:00     10003      
20007      2005-09-30 00:00:00     10004      
20008      2005-10-03 00:00:00     10005      
20009      2005-10-08 00:00:00     10001 
```

- MySQL用户手册地址：[点击](https://tangentsoft.com/mysqlpp/doc/html/userman/index.html)

- 后续再更新相关的api...