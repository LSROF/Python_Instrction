# python



## -创建cmd小网盘

> 利用socketsever，实现多客户端连接
>
> - 用户可以进行注册
> - 用户可以进行登录
> - 用户上传文件
> - 用户下载他自己的文件



## -服务器的设计

> 网络通信模块：socketserver
>
> 接收到客户端的链接之后，客户端会发送数据过来

> 通信：
>
> ​		客户端发送数据给服务器
>
> ​		服务器解析数据要干什么以及它的参数是什么，例如：login, username, password
>
> ​		服务器处理登录逻辑
>
> ​		服务器反馈给客户端一个结果



## -运用

> **为了处理事务，使用了三层架构**



## -server_main.py

```python
import struct
import socketserver
import json
import os

from util.service import UserService


# 通过json使接受和发送消息处理的是字典
def server_recv(conn):  # 接受消息
    len_bs = conn.recv(4)
    # 解包返回的是元组，数据放在[0]中
    len_data = struct.unpack("i", len_bs)[0]  # 获取数据包的长度
    msg = conn.recv(len_data)
    result = msg.decode("utf-8")
    return json.loads(result)   # 将数据处理成字典


def server_send(conn, dic):  # 发送消息
    msg = json.dumps(dic)
    msg_bs = msg.encode("utf-8")
    conn.send(struct.pack("i", len(msg_bs)))
    conn.send(msg_bs)


class MySocketSever(socketserver.BaseRequestHandler):
    def handle(self) -> None:
        conn = self.request
        while 1:
            try:
                dic = server_recv(conn)  # 客户端发送请求过来
                result = handle(dic, conn)  # 处理客户端的述求
                server_send(conn, result)  # 服务器反馈给客户端处理结果
            except Exception:
                print("客户端已断开")
                break


def handle(dic, conn):    # 处理用户的述求, 直接处理字典
    # 区分请求api
    api = dic.get("api")
    data = dic.get("data")

    # 路由逻辑
    if api == "login":
        return login(data)
    elif api == "register":
        return register(data)
    elif api == "upload":
        return upload(data, conn)
    elif api == "check":
        return check(data)
    elif api == "download":
        return download(data, conn)


def login(data):
    username = data.get("username")
    password = data.get("password")
    if not all([username, password, "test"]):
        return {"code": 3, "msg": "用户参数错误", "data": None}
    # 开始接收用户名和密码
    print("接收到客户端的：", username, password)
    # 准备验证用户名和密码
    # 需要数据库操作-db
    result = UserService().login(username, password)
    if result:
        return {"code": 1, "msg": "用户登录成功", "data": result}
    else:
        return {"code": 2, "msg": "用户登录失败", "data": None}


def register(data):
    # 校验数据，为了服务器的安全需在服务器内部进行一次校验
    username = data.get("username")
    password = data.get("password")
    real_name = data.get("real_name")

    if not all([username, password, real_name, "test"]):
        return {"code": 2, "msg": "参数不正确", "data": {}}

    # 判断用户名是否已经使用
    result = UserService().select_user_by_username(username)
    if result:
        return {"code": 3, "msg": "用户名重复，请输入其他用户名", "data": {}}

    # 注册
    user_data = UserService().register(data)

    # 返回用户信息
    if user_data:
        return {"code": 1, "msg": "注册成功", "data": user_data}
    else:
        return {"code": 4, "msg": "执行操作失败，请联系管理员", "data": {}}


def upload(data, conn):
    username = data.get("username")
    file_name = data.get("file_name")
    file_size = data.get("file_size")

    # 数据校验
    if not all([username, file_name, file_size, "test"]):
        return {"code": 3, "msg": "文件上传失败, 参数错误", "data": None}

    if not os.path.exists(username):
        os.makedirs(username)   # 创建文件夹

    # 判断客户端上传的文件是否重名
    if os.path.exists(f"{username}/{file_name}"):
        return {"code": 2, "msg": "文件名重复", "data": None}

    # 传输文件校验
    server_send(conn, {"code": 99999})

    # 传输数据
    with open(f"{username}/{file_name}", mode="wb") as f:
        # 从conn(连接)中获取信息
        while file_size > 0:
            file_data = conn.recv(2048)  # 每次接受2048个字节
            file_size -= len(file_data)
            f.write(file_data)
    return {"code": 1, "msg": "文件上传成功", "data": None}


def check(data):
    username = data.get("username")
    # 是否存在该用户
    if not username:
        return {"code": 4, "msg": "用户不存在", "data": None}
    user_info = UserService().select_user_by_username(username)
    if not user_info:
        return {"code": 3, "msg": "参数不正确", "data": None}

    # 校验数据
    if not os.path.exists(f"{username}"):
        return {"code": 2, "msg": "用户无文件", "data": None}

    file_dir = os.listdir(f"{username}")
    if not file_dir:
        return {"code": 2, "msg": "用户无文件", "data": None}

    # 获取文件大小
    file_data = []
    file_dic = {}
    for ls in range(len(file_dir)):
        size = os.path.getsize(f"{username}\\{file_dir[ls]}")
        file_dic["name"] = file_dir[ls]
        file_dic["size"] = size
        file_data.append(file_dic)
        file_dic = {}

    # 返回所有的文件列表
    return {"code": 1, "msg": "查看文件成功", "data": file_data}


def download(data, conn):
    username = data.get("username")
    file_name = data.get("file_name")
    file_size = data.get("file_size")
    path = f"{username}\\{file_name}"
    # 判断
    if not all([username, file_name, file_size, "test"]):
        print("====")
        return {"code": 2, "msg": "参数不正确", "data": None}

    if not UserService().select_user_by_username(username):
        return {"code": 3, "msg": "用户不存在", "data": None}

    if not os.path.exists(path):
        return {"code": 4, "msg": "文件不存在", "data": None}

    # 告诉客户端可以开始接收文件
    server_send(conn, {"code": 99999})
    # 文件传输
    with open(path, mode="rb") as f:
        print("给客户端发送文件")
        while file_size > 0:
            file_data = f.read(2048)
            file_size -= len(file_data)
            conn.send(file_data)
        server_recv(conn)
    return {"code": 1, "msg": "文件下载成功", "data": None}


def main():
    # 启动网络通信逻辑
    server = socketserver.ThreadingTCPServer(("127.0.0.1", 1111), MySocketSever)
    server.serve_forever()  # 启动


if __name__ == '__main__':
    main()

```



## -server.py

```python
# 处理事务
from util.db import MyMysqlSever


class UserService:

    def login(self, username, password):
        sql = "select * from user where username = %s and password = %s"
        with MyMysqlSever() as client:
            result = client.select(sql, username, password)
            if result:
                # 将用户信息提取出来
                return result
            else:
                return {}

    def register(self, data):
        sql = "insert into user(username, password, real_name, phone, address) values (%s, %s, %s, %s, %s)"
        with MyMysqlSever() as client:
            try:
                client.insert(sql,
                              data["username"],
                              data["password"],
                              data["real_name"],
                              data["phone"],
                              data["address"])
                client.user_commit()

                # as后面跟着别名  last_insert_id()可获得被插入数据id
                sql = "select last_insert_id() as last_id from user"
                last_id = client.select(sql).get("last_id")

                sql = "select * from user where id = %s"
                result = client.select(sql, last_id)
                return result

            except Exception:
                client.user_rollback()
                return None

    def select_user_by_username(self, username):
        sql = "select * from user where username = %s"
        with MyMysqlSever() as client:
            result = client.select(sql, username)
            return result

```



## -db.py

```python
import pymysql

from pymysql.cursors import DictCursor


class MyMysqlSever:
    # 进入with的时候自动调用
    def __enter__(self):
        # 建立连接
        self.conn = pymysql.connect(
            host="127.0.0.1",
            port=3306,
            user="myuser",
            password="mypassword",
            database="network_disk"
        )
        return self     # 一定要返回self,返回值对应的是as后的变量

    # 离开with的时候自动调用
    def __exit__(self, exc_type, exc_val, exc_tb):
        # 关闭连接
        self.conn.close()

    def user_commit(self):
        if self.conn:
            self.conn.commit()

    def user_rollback(self):
        if self.conn:
            self.conn.rollback()

    def select(self, sql, *args):
        cursor = None
        try:
            cursor = self.conn.cursor(DictCursor)
            cursor.execute(sql, args)   # args返回的是一个元组，刚好
            data = cursor.fetchone()
            return data
        except Exception:
            print("查询失败", sql, args)
        finally:    # 一定要执行
            if cursor:
                cursor.close()

    def insert(self, sql, *args):
        cursor = None
        try:
            cursor = self.conn.cursor()  # 新增时没有必要使用DictCursor
            cursor.execute(sql, args)
        except Exception:
            # import traceback
            # print(traceback.format_exc())
            print("异常")
            raise Exception("抛出异常")  # 为了外面可以进行判断
        finally:
            if cursor:
                cursor.close()

    def update(self):
        pass

    def delete(self):
        pass
    
```



## -client_main.py

```python
import socket
import struct
import json
import os

# 用户登录状态以及登录信息
login_flag = False
user_info = {}


# 通过json使接受和发送消息处理的是字典
def server_recv(conn):  # 接受消息
    len_bs = conn.recv(4)
    # 解包返回的是元组，数据放在[0]中
    len_data = struct.unpack("i", len_bs)[0]  # 获取数据包的长度
    msg = conn.recv(len_data)
    result = msg.decode("utf-8")
    return json.loads(result)  # 将数据处理成字典


def server_send(conn, dic):  # 发送消息
    msg = json.dumps(dic)
    msg_bs = msg.encode("utf-8")
    conn.send(struct.pack("i", len(msg_bs)))
    conn.send(msg_bs)


# 整合了server_recv()和server_send()
def send_api(conn, api):
    server_send(conn, api)
    return server_recv(conn)  # 服务器响应后的结果


def menu():
    if not login_flag:
        menu_list = ["登录", "注册", "退出"]
        for option in range(len(menu_list)):
            print(option + 1, menu_list[option])
    else:
        menu_list = ["上传文件", "下载文件", "退出登录"]
        for option in range(len(menu_list)):
            print(option + 1, menu_list[option])


def login(conn):
    global login_flag
    global user_info
    username = input("请输入用户名: ")
    password = input("请输入密码: ")
    api_data = {
        "api": "login",
        "data": {
            "username": username,
            "password": password
        }
    }
    # 客户端发送请求，并得到服务器的反馈
    response_data = send_api(conn, api_data)
    # print("服务器返回结果为:", response_data)

    code = response_data.get("code")
    data = response_data.get("data")

    if code == 1:
        login_flag = True
        user_info = data
        print("用户登录成功")
        print(f"欢迎用户: {user_info['real_name']}")
    elif code == 2:
        print("用户登录失败")
    else:
        print("输入不正确，可重试或联系管理员")


def register(conn):
    global user_info
    global login_flag
    username = input("新用户名: "),
    password = input("新密码: "),
    real_name = input("真实姓名: "),
    phone = input("手机号: "),
    address = input("住址: ")

    if not all([username, password, real_name, "test"]):
        print("输入的信息有误，请重新进行输入")
        return None

    api_data = {
        "api": "register",
        "data": {
            "username": username,
            "password": password,
            "real_name": real_name,
            "phone": phone,
            "address": address
        }
    }
    response_data = send_api(conn, api_data)

    code = response_data.get("code")  # 记住是.get(),不是.get[]
    if code == 1:
        print("注册成功")

    elif code == 2:
        print("参数不正确")
    elif code == 3:
        print("用户名重复，请输入其他用户名")
    else:
        print("服务器出现故障，请联系管理员")


def upload(conn):
    file_path = input("请输入上传的文件的路径: ")
    if not (os.path.exists(file_path) and os.path.isfile(file_path)):
        print("文件路径有问题，请重新输入")
        return None
    if not os.path.getsize(file_path) > 0:
        print("上传的文件大小有误")
        return None
    # 发送文件
    api = {
        "api": "upload",
        "data": {
            "username": user_info.get("username"),
            "file_name": os.path.basename(file_path),
            "file_size": os.path.getsize(file_path)
        }}
    server_send(conn, api)

    # 得到code
    act = server_recv(conn)
    code = act.get("code")
    if code == 99999:   # 服务器准备完毕
        # 上传文件
        file_size = os.path.getsize(file_path)
        with open(file_path, mode="rb") as f:
            print("文件正在上传中...")
            while file_size > 0:
                file_data = f.read(2048)
                file_size -= len(file_data)
                conn.send(file_data)
        end = server_recv(conn)
        if end.get("code") == 1:
            print("文件上传成功")
    else:
        # 接受信息
        if code == 2:
            print("文件名重复")
        elif code == 3:
            print("文件上传失败")


def check(conn):
    username = user_info.get("username")
    api_data = {
        "api": "check",
        "data": {
            "username": username
        }
    }
    response_data = send_api(conn, api_data)
    list_data = response_data.get("data")
    file_code = response_data.get("code")
    if file_code == 1:
        while 1:
            print("编号", "\t\t", "文件名", "\t\t", "文件大小")
            for ls in range(len(list_data)):
                print(ls+1, "\t\t", list_data[ls]["name"], "\t\t", list_data[ls]["size"])

            choose = input("请输入你想要下载文件的编号(Q退出): ")

            if choose.upper() == "Q":
                print("已返回上一单元")
                return None

            if not choose.isdigit():
                print("请输入正确的编号")
                continue

            choose = int(choose)
            if choose < 1 or choose > len(list_data):
                print("请输入正确的编号")
                continue
            else:
                number = choose - 1   # 恢复列表中的编号
                download_file = list_data[number]  # 下载的文件信息

                # 开始下载文件
                file_name = download_file["name"]
                file_size = download_file["size"]
                download(username, file_name, file_size, conn)
                break
    elif file_code == 2:
        print("用户无文件")
    elif file_code == 3:
        print("参数不正确")
    elif file_code == 4:
        print("用户不存在")


def download(username, file_name, file_size, conn):
    api_file = {
        "api": "download",
        "data": {
            "username": username,
            "file_name": file_name,
            "file_size": file_size
        }
    }
    response_data = send_api(conn, api_file)
    code = response_data.get("code")

    if code == 99999:
        while 1:
            path = input("请输入文件保存路径: ")

            # 检验
            if not os.path.exists(path):
                print("该路径不存在，请重新输入")
                continue

            if not os.path.isdir(path):
                print("请输入一个有效的路径")
                continue

            with open(f"{path}\\{file_name}", mode="wb") as f:
                print("文件下载中...")
                while file_size > 0:
                    bs = conn.recv(2048)
                    file_size -= len(bs)
                    f.write(bs)
                server_send(conn, "客户端接受完毕")
            response = server_recv(conn)
            if response.get("code") == 1:
                print("文件下载成功")
            else:
                print("下载失败")
            break

    elif code == 2:
        print("参数不正确")
    elif code == 3:
        print("用户不存在")
    elif code == 4:
        print("文件不存在")


def main():
    global login_flag
    global user_info
    sk = socket.socket()
    sk.connect(("127.0.0.1", 1111))
    while 1:
        # 用户使用菜单
        menu()
        if not login_flag:
            try:
                choose = int(input("请输入序号选择功能: "))
                if choose == 1:
                    login(sk)
                if choose == 2:
                    register(sk)
                if choose == 3:
                    print("系统即将退出")
                    import sys
                    sys.exit(0)  # 系统正常退出
            except ValueError:
                print("输入参数错误，请重新输入")
        else:
            try:
                choose = int(input("请输入序号选择功能: "))
                if choose == 1:
                    upload(sk)
                if choose == 2:
                    # 下载文件
                    check(sk)
                if choose == 3:
                    # 退出登录
                    login_flag = False
                    user_info = {}
            except ValueError:
                print("输入参数错误，请重新输入")


if __name__ == '__main__':
    main()

```

