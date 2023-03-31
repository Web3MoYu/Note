# 原型设计
## 列表页面

1.给出图书信息测试数据的JSON定义

```json
[
    {
        "id":1,
        "isbn":"9787040396638",
        "name":"高等数学-上册",
        "author":"同济大学数学系",
        "press":"高等教育",
        "price":"39.8"
    },
    {
        "id":2,
        "isbn":"9787040396621",
        "name":"高等数学-下册",
        "author":"同济大学数学系",
        "press":"高等教育",
        "price":"33.5"
    }
]
```

2.给出使用JSON数据生成图书信息列表的javaScript代码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>BookTable</title>
</head>

<script src="./jquery-1.11.3.min.js"></script>

<body>
    <div>
        <table>
            <tr>
                <td>图书ID</td>
                <td>ISBN</td>
                <td>书名</td>
                <td>作者</td>
                <td>出版社</td>
                <td>单价</td>
            </tr>
        </table>
        <table>
            <div id="booklist"></div>
        </table>
    </div>
</body>
<script>
    const data = [
        {
            "id": 1,
            "isbn": "9787040396638",
            "name": "高等数学-上册",
            "author": "同济大学数学系",
            "press": "高等教育",
            "price": "39.8"
        },
        {
            "id": 2,
            "isbn": "9787040396621",
            "name": "高等数学-下册",
            "author": "同济大学数学系",
            "press": "高等教育",
            "price": "33.5"
        }
    ]
    let strHtml = "";
    for (const book of data) {
        strHtml += "<tr>"
        strHtml += "<td>" + book.id + "</td>"
        strHtml += "<td>" + book.isbn + "</td>"
        strHtml += "<td>" + book.name + "</td>"
        strHtml += "<td>" + book.author + "</td>"
        strHtml += "<td>" + book.press + "</td>"
        strHtml += "<td>" + book.price + "</td>"
        strHtml += "</tr>"
    }
    $("#booklist").append(strHtml)
    console.log(strHtml)
</script>

</html>
```

## 输入页面

1.给出图书信息添加页面的输入表单部分的代码（Javascript数据验证函数名为check，不需要table或者css+div进行布局管理）

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>InputPage</title>
    <script src="./jquery-1.11.3.min.js"></script>
</head>

<body>
    <div>
        <form action="" id="form">
             书号<input type="number" name="isbn" id="isbn">
            </br>
            书名<input type="text" name="name" id="name">
            </br>
            作者<input type="text" name="author" id="author">
            </br>
            出版社<input type="text" name="press" id="press">
            </br>
            价格<input type="text" name="price" id="price">
            </br>
            submit<input type="submit" onclick="check()">
        </form>
    </div>
</body>
</html>
```

2.给出实现提交前数据验证的Javascript函数check的定义代码（只验证书号，书名，单价，书号需要用到的正则表达式）

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>InputPage</title>
    <script src="./jquery-1.11.3.min.js"></script>
</head>

<body>
    <div>
        <form action="" id="form">
            书号<input type="number" name="isbn" id="isbn">
            </br>
            书名<input type="text" name="name" id="name">
            </br>
            作者<input type="text" name="author" id="author">
            </br>
            出版社<input type="text" name="press" id="press">
            </br>
            价格<input type="text" name="price" id="price">
            </br>
            submit<input type="submit" onclick="check()">
        </form>
    </div>
</body>
<script>
    const check = () => {
        re = /^\d{13}$/
        if (!re.test($("#isbn").val())) {
            alert("请输入13位数字形式的书号")
            return
        }
        re = /\S{10,50}/;
        if (!re.test($("#name").val())) {
            console.log($("#name").val())
            alert("请输入10-50个字符的书名")
            return
        }
        re = /^\d+(\.\d+)?$/;
        if (!re.test($("#price").val())) {
            alert("请输入数字形式的价格")
            return
        }

    }
</script>

</html>
```

# JSP技术

1.实现图书信息值bean的所有getter和setter方法

```java
package com.example.server.entity;

/**
 * Author: lsh
 * Date: 2023/3/30 19:37
 * Version: 1.0
 */
public class BookInfo {
    private int id;
    private String isbn;
    private String name;
    private String author;
    private String press;
    private float price;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getIsbn() {
        return isbn;
    }

    public void setIsbn(String isbn) {
        this.isbn = isbn;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public String getPress() {
        return press;
    }

    public void setPress(String press) {
        this.press = press;
    }

    public float getPrice() {
        return price;
    }

    public void setPrice(float price) {
        this.price = price;
    }
}

```

2.实现业务Bean中修改图书信息的方法，只需要修改书名，作者，出版社，单价

```java
 public int UpdateBook(BookInfo book) {
	Connection connection = DBConnection.getConnection();
	int count = 0;
	try {
		PreparedStatement statement = connection.prepareStatement("update T_BOOK set BOOKNAME = ?,AUTHOR = ?, PRESS = ?, PRICE = ? where BOOKID = ?");
		statement.setString(1, book.getName());
        statement.setString(2, book.getAuthor());
        statement.setString(3, book.getPress());
        statement.setFloat(4, book.getPrice());
        statement.setInt(5, book.getId());
        count = statement.executeUpdate();
    } catch (SQLException e) {
        throw new RuntimeException(e);
    }
     return count;
 }
```

3.实现Servlet中修改图书信息的方法



```java
public int UpdateBook(BookInfo book) {
    Connection connection = DBConnection.getConnection();
    int count = 0;
    try {
        PreparedStatement statement = connection.prepareStatement("update T_BOOK set BOOKNAME = ?,AUTHOR = ?, PRESS = ?, PRICE = ? where BOOKID = ?");
        statement.setString(1, book.getName());
        statement.setString(2, book.getAuthor());
        statement.setString(3, book.getPress());
        statement.setFloat(4, book.getPrice());
        statement.setInt(5, book.getId());
        count = statement.executeUpdate();
        connection.close();
        statement.close();
    } catch (SQLException e) {
        throw new RuntimeException(e);
    }
    return count;
}
```



```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	doPost(req, resp);
}

@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
String action = req.getParameter("action");
switch (action){
	case "BookListAction":
		getAllBooks(req,resp);
		break;
	case "BookDelAction":
        DelBook(resp,resp);
		break;
	case "BookUpdateAction":
		UpdateAction(req, resp);
		break;
	}
}
private void UpdateAction(HttpServletRequest request, HttpServletResponse response) throws IOException {
	BookDB bookDB = new BookDB();
	BookInfo bookInfo = new BookInfo();
	bookInfo.setId(Integer.parseInt(request.getParameter("id")));
	bookInfo.setIsbn(request.getParameter("isbn"));
	bookInfo.setAuthor(request.getParameter("author"));
	bookInfo.setName(request.getParameter("name"));
	bookInfo.setPress(request.getParameter("press"));
	bookInfo.setPrice(Float.parseFloat(request.getParameter("price")));
	int count = bookDB.UpdateBook(bookInfo);
	PrintWriter out = response.getWriter();
	if (count != 0) {
	out.print("1");
	} else {
	out.print("0");
	}
	out.close();
}
```

# Ajax应用

## 列表显示

1.实现Servlet中获取所有图书信息，并采用字符串连接的方式构建JSON数据返回给客户端的方法

```java
public ArrayList<BookInfo> GetAllBooks() {
    Connection connection = DBConnection.getConnection();
    ArrayList<BookInfo> list = new ArrayList<>();
    try{
        PreparedStatement statement = connection.prepareStatement("select  * from T_BOOK");
        ResultSet rs = statement.executeQuery();
        while (rs.next()){
            BookInfo bookInfo = new BookInfo();
            bookInfo.setId(rs.getInt("id"));
            bookInfo.setName(rs.getString("name"));
            bookInfo.setAuthor(rs.getString("author"));
            bookInfo.setIsbn(rs.getString("isbn"));
            bookInfo.setPress(rs.getString("press"));
            bookInfo.setPrice(rs.getFloat("price"));
            list.add(bookInfo);
        }
        rs.close();
        connection.close();
        statement.close();
    } catch (SQLException e) {
        throw new RuntimeException(e);
    }
    return list;

```



```java
private void getAllBooks(HttpServletRequest request, HttpServletResponse response) throws IOException {
	BookDB bookDB = new BookDB();
	ArrayList<BookInfo> list = bookDB.GetAllBooks();
	String str = "";
	for (BookInfo book : list) {
	str += "{ \"id\":" + book.getId() + ",\"isbn\":\"" + book.getIsbn() + "\",\"author\":\"" + book.getAuthor();
	str += "\",\"name\":\"" + book.getName() + "\",\"press\":" + book.getPress() + "\",\"price\"" + book.getPrice();
	str += "\"},";
	}
	str = str.substring(0, str.length() - 1);
	response.getWriter().println("[" + str + "]");
    }
}
```

2.实现客户端采用$.get的Ajax代码，在页面加载时使用Ajax请求获取图书信息列表的JSON数据，并显示在id为booklist的DIV中

```javascript
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>listPage</title>
</head>

<script src="./jquery-1.11.3.min.js"></script>

<body>
    <div>
        <table>
            <tr>
                <td>图书ID</td>
                <td>ISBN</td>
                <td>书名</td>
                <td>作者</td>
                <td>出版社</td>
                <td>单价</td>
            </tr>
        </table>
        <table>
            <div id="booklist"></div>
        </table>
    </div>
</body>
<script>
	let bookData = [];
    //  $.get(url,data,success(response,status,xhr),dataType)  
    $(document).ready(function () {
        $.get("../BookAction?action=BookUpdateAction",
            function (data) {
                this.bookData = data;
            }
        )
    })

    let strHtml = "";
    for (const book of bookData) {
        strHtml += "<tr>"
        strHtml += "<td>" + book.id + "</td>"
        strHtml += "<td>" + book.isbn + "</td>"
        strHtml += "<td>" + book.name + "</td>"
        strHtml += "<td>" + book.author + "</td>"
        strHtml += "<td>" + book.press + "</td>"
        strHtml += "<td>" + book.price + "</td>"
        strHtml += "</tr>"
    }
    $("#booklist").append(strHtml)
</script>

</html>
```

## 删除处理

1.实现Servlet中删除指定id的图书信息，并返回标识字符串("1"表示删除成功，"0"表示删除失败)

```java
public int DelBook(int id) throws SQLException {
    Connection connection = DBConnection.getConnection();
    int count = 0;
    PreparedStatement statement = connection.prepareStatement("delete from T_BOOK where BOOKID = ?");
    statement.setInt(1,id);
    count = statement.executeUpdate();
    connection.close();
    statement.close();
    return count;
}
```



```java
private void DelBook(HttpServletRequest request, HttpServletResponse response) {
	BookDB bookDB = new BookDB();
	String id = request.getParameter("id");
	PrintWriter writer = null;
	int count = 0;
	try {
		count = bookDB.DelBook(Integer.parseInt("id"));
		writer = response.getWriter();
	} catch (Exception e) {
		throw new RuntimeException(e);
	}
	if (count != 0) {
		writer.print("1");
	} else {
		writer.print("0");
    }
}
```



2.客户端采用$.ajax的Ajax代码，实现图书信息的删除处理

```javascript
const delBook = (id) => {
	$.ajax({
		cache: true,
		type: "POST",
		url: "../BookAction?action=BookDelAction",
		data: { "id": id },
		async: false,
		success: function (data) {
			if (data == "0") {
				alert("数据删除不成功");
			} else {
				alert("数据删除成功!");
			}
		}
	})
}
```

# DBConnection

```java
package com.example.server.common;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

/**
 * Author: lsh
 * Date: 2023/3/30 20:06
 * Version: 1.0
 */
public class DBConnection {
    private static Connection connection;

    public static Connection getConnection(){
        if (connection == null){
           try{
               Class.forName("com.mysql.jdbc.Driver");
               connection = DriverManager.getConnection("jdbc://localhost:3306/test", "root", "root");
           }catch (Exception e){
               e.printStackTrace();
           }
        }
        return connection;
    }

    public static void CloseConnection(){
        if (connection != null){
            try {
                connection.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
            connection = null;
        }
    }
}

```

