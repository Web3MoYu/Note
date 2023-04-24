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
public class DBConnection {
    static Connection con = null;

    /**获取数据库连接*/
    public static Connection getConnection() {
        if (con == null) {
            try { //加载驱动程序
                Class.forName("com.mysql.cj.jdbc.Driver");
            } catch (Exception e) {
                e.printStackTrace();
                System.out.println("加载MYSQL数据库驱动失败！");
            }
            try { //通过驱动程序管理器获取数据库连接
                //mysql如果你的字符集没有设置，或者没有设置好的话，默认是latin1
                con = DriverManager.getConnection("jdbc:mysql://localhost:3306/db_student?" +
                        "useUnicode=true&characterEncoding=UTF-8&serverTimezone=GMT%2B8", "root", "root");
            } catch (Exception e) {
                e.printStackTrace();
                System.out.println("获取数据库连接失败！");
            }        }
        return con; 
    }

    /** 关闭数据库的操作 */
    public static void closeConnection() {
        if (con != null)
            try {
                con.close();
                con = null;
            } catch (SQLException e) {
                e.printStackTrace();
                System.out.println("关闭con对象失败！");
            }
    }
}
```

# JQuery发请求

````html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Test</title>
    <script src="./js/jquery-1.11.3.min.js"></script>
</head>

<body>
    <form id="fromTest" action="./submit" method="post">
        <input type="text" id="username" name="username">
        <br />
        <input type="password" id="password" name="password">
    </form>
    <button onclick="test()">点击发送请求</button>
    <br />
    <button onclick="test2()">点击提交表单数据</button>
</body>
<script>
    const test = () => {
        $.post(
            "./post/",
            $("#fromTest").serialize(),
            function (e) {
                console.log(e);
            }
        );
        $.get(
            "./get/",
            $("#fromTest").serialize(),
            function (e) {
                console.log(e);
            }
        );
        $.ajax({
            url: "./ajaxPost/",
            type: "POST",
            data: {
                id: 1,
                form: $("#fromTest").serialize()
            },
            async: false,
            success: (data) => {
                console.log(data);
            },
            error: (error) => {
                console.log(error);
                console.log("error");
            }
        })
    }


    const test2 = () => {
        // 省略验证
        $("#fromTest").submit();
    }
</script>

</html>
````

# 实验三登录

## UserDB

```java
public class UserDB {

    public User getUserByName(String userName) {
        User user = null;
        PreparedStatement pStmt = null;
        ResultSet rs = null;
        try {
            Connection connection = DBConnection.getConnection();
            pStmt = connection.prepareStatement("select * from t_user where VC_LOGIN_NAME = ?");
            pStmt.setString(1, userName);
            rs = pStmt.executeQuery();
            if (rs.next()) {
                user = new User();
                user.setUserId(rs.getInt("N_USER_ID"));
                user.setUsername(rs.getString("VC_LOGIN_NAME"));
                user.setPassword(rs.getString("VC_PASSWORD"));

            }
            rs.close();
            pStmt.close();
        } catch (Exception e) {
            System.out.println("获取指定用户信息失败！");
        } finally {
            DBConnection.closeConnection();
        }
        return user;
    }

    public List<User> getUser(String userName) {
        User user = null;
        PreparedStatement pStmt = null;
        ResultSet rs = null;
        List<User> users = new ArrayList<>();
        try {
            Connection connection = DBConnection.getConnection();
            pStmt = connection.prepareStatement("select * from t_user where VC_LOGIN_NAME != ?");
            pStmt.setString(1, userName);
            rs = pStmt.executeQuery();
            while (rs.next()) {
                user = new User();
                user.setUserId(rs.getInt("N_USER_ID"));
                user.setUsername(rs.getString("VC_LOGIN_NAME"));
                user.setPassword(rs.getString("VC_PASSWORD"));
                users.add(user);
            }
            //TODO 4、释放对象
            rs.close();
            pStmt.close();
        } catch (Exception e) {
            System.out.println("获取指定用户信息失败！");
        } finally {
            DBConnection.closeConnection();
        }
        if ("admin".equals(userName)){
            return users;
        }
        return null;
    }
}
```

## UserAction

```java
public class UserAction extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        HttpSession session = req.getSession();
        UserDB userDB = new UserDB();
        // 获取用户
        User userByName = userDB.getUserByName(username);
        int flag = 0;

        if (userByName == null) {
            flag = 1;
        } else {
            if (!password.equals(userByName.getPassword())) {
                flag = 2;
            }
        }
        session.setAttribute("username", username);
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter writer = resp.getWriter();
        if (flag == 0) {
            session.setAttribute("success", new Object());
            writer.print(flag);
            //resp.sendRedirect(path + "/login/user_info.jsp");
        } else {
            writer.print(flag);
            //resp.sendRedirect(path + "/login/login.jsp?loginflag=" + flag);
        }

    }
}
```

## Login.jsp

```jsp
<%@ page import="java.io.PrintWriter" %><%--
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>学生信息管理系统登录</title>
        <link rel="stylesheet" type="text/css"
              href="../css/student.css">
        <script type="text/javascript" src="../js/jquery-1.11.3.min.js"></script>
    </head>
    <body>
        <div class="container">
            <div class="son">
                <form name="frm" id="studentFrm" method="post" action="../UserAction">
                    <input type="hidden" name="id" id="id" value="">
                    <table border=1>
                        <tr class="td_header">
                            <td colspan="2"><span id="frmTitle">用户登录信息录入</span>，<span class="star">*</span>为必填项
                            </td>
                        </tr>
                        <tr>
                            <td>用户名</td>
                            <td><input type="text" name="username" id="username"><span class="star">*</span></td>
                        </tr>
                        <td>密码</td>
                        <td><input type="password" name="password" id="password"><span class="star">*</span></td>
                        </tr>
                        <tr>
                            <div>
                                <td colspan="2" align="center"><input type="button" value="提交" onclick="checkFrom()">
                                    <input type="button" value="重置" onclick=""></td>
                            </div>
                        </tr>
                    </table>
                </form>
            </div>
        </div>
    </body>
    <script>

        function checkFrom() {
            if ($("#username").val() === '' || $("#username").val() === null) {
                alert("用户名不能为空");
                $("#username").focus();
                return;
            }
            if ($("#password").val() === '' || $("#password").val() === null) {
                alert("密码不能为空");
                $("#password").focus();
                return;
            }
            $.ajax({
                // cache: true,
                type: "POST",
                url: "../UserAction",
                data: $("#studentFrm").serialize(),
                async: false,
                success: function (data) {
                    const pathName = window.document.location.pathname;
                    const path = pathName.substring(0, pathName.substr(1).indexOf('/') + 1);
                    // 究极大坑
                    if (data == '0') {
                        window.location = path + '/login/user_info.jsp';
                    } else {
                        window.location = path + '/login/login.jsp?loginflag=' + data;
                    }
                }
            });
        }

        <%
            String loginFlag=request.getParameter("loginflag");
            String userName = (String) request.getSession().getAttribute("username");
            if(loginFlag==null){ //无登录失败标识，是正常访问登录页 面
                loginFlag="";
            }
            PrintWriter writer = response.getWriter();
            if(loginFlag.equals("1")){
                writer.println("<script>alert('用户【"+userName+"】不存在！')</script>");
            }else if(loginFlag.equals("2")){
                writer.println("<script>alert('密码错误！')</script>");
            }
        %>


    </script>

</html>
```

## UserInfo.jsp

```jsp
<%@ page import="java.io.PrintWriter" %>
<%@ page import="java.util.List" %>
<%@ page import="entity.User" %>
<%@ page import="service.UserDB" %><%--
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <%
            String username = (String) request.getSession().getAttribute("username");
            Object o = request.getSession().getAttribute("success");
            PrintWriter writer = response.getWriter();
            if (username != null && o != null) {
                writer.println("<h3>" + username + "登录成功</h3>");  
            }
            List<User> users = new UserDB().getUser(username);
            if (users != null && users.size() > 0) {
                writer.println("<table border=\"1\">\n" +
                        "  <tr>\n" +
                        "    <th>Username</th>\n" +
                        "    <th>Password</th>\n" +
                        "  </tr>");
                for (User user : users) {
                    writer.println("<tr>");
                    writer.println("<td>" + user.getUsername() + "</td>");
                    writer.println("<td>" + user.getPassword() + "</td>");
                    writer.println("</tr>");
                }
                writer.println("</table>");
            }
        %>
    </body>
</html>
```



# 实验室4课程的增删改查

## CourseDB

```java
public class CourseDB {

    /* 获取所有学生信息 */
    public List<Course> getAllCourse() {
        ResultSet rs;
        Statement sql;
        List<Course> courseList = new ArrayList<>();
        try {
            Connection con = DBConnection.getConnection();
            sql = con.createStatement();
            rs = sql.executeQuery("SELECT * from t_course");
            while (rs.next()) {
                Course course = new Course();
                setCourse(rs, course);
                courseList.add(course);
            }
            rs.close();
            sql.close();

        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("获取所有学生信息失败！");
        } finally {
            DBConnection.closeConnection();
        }
        return courseList;
    }

    public Course getCourseById(int id) {
        ResultSet rs;
        PreparedStatement pStmt = null;
        Course course = new Course();
        try {
            Connection con = DBConnection.getConnection();
            pStmt = con.prepareStatement("SELECT * FROM t_course where N_COURSE_ID=?");
            pStmt.setInt(1, id);
            rs = pStmt.executeQuery();
            if (rs.next()) {
                setCourse(rs, course);
            }
            rs.close();
            pStmt.close();

        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("获取指定课程信息失败！");
        } finally {
            DBConnection.closeConnection();
        }
        return course;
    }

    private void setCourse(ResultSet rs, Course course) throws SQLException {
        course.setId(rs.getInt("N_COURSE_ID"));
        course.setName(rs.getString("VC_COURSE_NAME"));
        course.setType(rs.getInt("N_TYPE"));
        course.setCredit(rs.getFloat("F_CREDIT"));
        course.setGrade(rs.getInt("N_GRADE"));
        course.setMajor(rs.getInt("N_MAJOR"));
        course.setDetail(rs.getString("VC_DETAIL"));
    }

    public int updateStudent(Course course) {
        PreparedStatement pStmt;
        int count = 0;
        try {
            Connection con = DBConnection.getConnection();
            pStmt = con.prepareStatement("update t_course" +
                    " set VC_COURSE_NAME = ?, N_TYPE = ?, F_CREDIT = ?, N_GRADE = ?, N_MAJOR = ?, VC_DETAIL = ? " +
                    "where N_COURSE_ID = ?");
            pStmt.setString(1, course.getName());
            pStmt.setInt(2, course.getType());
            pStmt.setFloat(3, course.getCredit());
            pStmt.setInt(4, course.getGrade());
            pStmt.setInt(5, course.getMajor());
            pStmt.setString(6, course.getDetail());
            pStmt.setInt(7, course.getId());
            count = pStmt.executeUpdate();
            pStmt.close();

        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("修改课程信息失败！");
        } finally {
            DBConnection.closeConnection();
        }
        return count;
    }

    public int deleteStudent(int id) {
        PreparedStatement pStmt = null;
        int count = 0;
        try {
            Connection con = DBConnection.getConnection();
            pStmt = con.prepareStatement("delete from  t_course  where N_COURSE_ID = ?");
            pStmt.setInt(1, id);
            count = pStmt.executeUpdate();
            pStmt.close();
        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("删除课程信息失败！");
        } finally {
            DBConnection.closeConnection();
        }
        return count;
    }

    public int insertStudent(Course course) {
        PreparedStatement pStmt = null;
        int count = 0;
        try {
            Connection con = DBConnection.getConnection();
            pStmt = con.prepareStatement("insert into " +
                    "t_course(vc_course_name, n_type, f_credit, n_grade, n_major, vc_detail) VALUES(?,?,?,?,?,?) ");
            pStmt.setString(1, course.getName());
            pStmt.setInt(2, course.getType());
            pStmt.setFloat(3, course.getGrade());
            pStmt.setInt(4, course.getGrade());
            pStmt.setInt(5, course.getMajor());
            pStmt.setString(6, course.getDetail());
            count = pStmt.executeUpdate();
            pStmt.close();
        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("添加课程失败！");
        } finally {
            DBConnection.closeConnection();
        }
        return count;
    }
}

```

## CourseAction

```java
@WebServlet("/CourseAction")
public class CourseAction extends HttpServlet {


    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String actionType = req.getParameter("action");
        switch (actionType) {
            case "list":
                getAllCourse(resp);
                break;
            case "get":
                getCourseById(req, resp);
                break;
            case "update":
                updateCourse(req, resp);
                break;
            case "del":
                deleteById(req, resp);
                break;
        }
    }

    private void deleteById(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        CourseDB courseDB = new CourseDB();
        //1、获取客户端请求
        int id = Integer.parseInt(req.getParameter("id"));        //获取客户端提交数据
        //2、进行数据处理
        int count = courseDB.deleteStudent(id);                //调用业务处理Bean的方法处理数据
        //3、向客户端做出响应
        resp.setContentType("text/html;charset=UTF-8");    //返回text
        PrintWriter out = resp.getWriter();
        if (count != 0) {
            out.print("1");//out.println("1");
        } else {
            out.print("0");
        }
    }

    private void updateCourse(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        CourseDB courseDB = new CourseDB();
        //1、获取客户端请求
        Course course = new Course();
        course.setId(Integer.valueOf(req.getParameter("id")));
        course.setName(req.getParameter("name"));
        course.setType(Integer.valueOf(req.getParameter("type")));
        course.setCredit(Float.valueOf(req.getParameter("credit")));
        course.setGrade(Integer.valueOf(req.getParameter("grade")));
        course.setMajor(Integer.valueOf(req.getParameter("major")));
        course.setDetail(String.valueOf(req.getParameter("detail")));
        //2、进行数据处理
        int count = 0;
        if (course.getId() != -1) {
            count = courseDB.updateStudent(course);
        } else {
            count = courseDB.insertStudent(course);
        }
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();
        if (count != 0) {
            out.print("1");
        } else {
            out.print("0");
        }
        out.close();
    }

    private void getCourseById(HttpServletRequest req, HttpServletResponse resp) {
        int id = Integer.parseInt(req.getParameter("id"));
        Gson gson = new Gson();
        CourseDB courseDB = new CourseDB();
        Course courseById = courseDB.getCourseById(id);
        String s = gson.toJson(courseById);
        try {
            resp.setContentType("application/json;charset=UTF-8");
            resp.getWriter().println(s);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private void getAllCourse(HttpServletResponse resp) {
        Gson gson = new Gson();
        CourseDB courseDB = new CourseDB();
        List<Course> allCourse = courseDB.getAllCourse();
        String s = gson.toJson(allCourse);
        try {
            resp.setContentType("application/json;charset=UTF-8");
            resp.getWriter().println(s);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

    }
}
```

## Course实体类

```java
package entity;
public class Course {
    private Integer id;
    private String name;
    private Integer type;
    private Float credit;
    private Integer grade;
    private Integer major;
    private String detail;

    public Course() {
    }

    public Integer getId() {
        return this.id;
    }

    public String getName() {
        return this.name;
    }

    public Integer getType() {
        return this.type;
    }

    public Float getCredit() {
        return this.credit;
    }

    public Integer getGrade() {
        return this.grade;
    }

    public Integer getMajor() {
        return this.major;
    }

    public String getDetail() {
        return this.detail;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setType(Integer type) {
        this.type = type;
    }

    public void setCredit(Float credit) {
        this.credit = credit;
    }

    public void setGrade(Integer grade) {
        this.grade = grade;
    }

    public void setMajor(Integer major) {
        this.major = major;
    }

    public void setDetail(String detail) {
        this.detail = detail;
    }
}
```

## Course_list.html

```html
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>课程信息列表页面</title>
        <link rel="stylesheet" type="text/css"
              href="../css/student.css">
        <script type="text/javascript" src="../js/jquery-1.11.3.min.js"></script>
    </head>
    <script type="text/javascript">
        $(document).ready(function () {
            getList();
        });

        function getList() {
            $.get("../CourseAction?action=list",					//servlet
                function (data) {
                    WriteList(data);
                },		//JSON数据处理函数
                "JSON");										//JSON
        }

        function WriteList(data) {
            const courses = data;
            let strHtml = "";
            strHtml += "<table width=\"100%\"  border=\"0\" cellpadding=\"0\" cellspacing=\"0\" align=\"center\">	";
            for (let i = 0; i < courses.length; i++) {
                let major = "软件工程";
                if (courses[i]["major"] === 1) {
                    major = "软件工程";
                } else if (courses[i]["major"] === 2) {
                    major = "空间信息";
                } else if (courses[i]["major"] === 3) {
                    major = "大数据";
                }
                let type = "公共基础选修课"
                if (courses[i]["type"] === 1) {
                    type = "公共基础必修课";
                } else if (courses[i]["type"] === 2) {
                    type = "公共选修课";
                } else if (courses[i]["type"] === 3) {
                    type = "学科基础课";
                } else if (courses[i]["type"] === 4) {
                    type = "专业必修课";
                } else if (courses[i]["type"] === 5) {
                    type = "专业选修课";
                } else if (courses[i]["type"] === 6) {
                    type = "集中性实践教学环节";
                }
                strHtml += "<tr class=\"td_" + (i % 2 + 1) + "\">";
                strHtml += "<td width=\"15%\">" + courses[i]["name"] + "</td>";
                strHtml += "<td width=\"15%\">" + type + "</td>";
                strHtml += "<td width=\"15%\">" + courses[i]['credit'] + "</td>";
                strHtml += "<td width=\"15%\">" + courses[i]["grade"] + "</td>";
                strHtml += "<td width=\"15%\">" + major + "</td>";
                strHtml += "<td><a href=\"course_edit.html?id=" + courses[i]['id'] + "\" >编辑</a>";
                strHtml += "<a href=\"JavaScript:delData(" + courses[i]["id"] + ")\">删除</a></td></tr>";
            }
            strHtml += "</table>";

            $("#studentsSpan").html(strHtml);
        }


    </script>
    <body>
        <table width="100%" border="0" cellpadding="0" cellspacing="0">
            <tr>
                <td height="25" colspan="6" class="tb_showall" align="center">
                    课程信息列表
                </td>
            </tr>
            <tr class="td_header">
                <td width="15%">名称</td>
                <td width="15%">性质</td>
                <td width="15%">学分</td>
                <td width="15%">开设年级</td>
                <td width="15%">开设专业</td>
                <td></td>
            </tr>
        </table>

        <div id="studentsSpan"></div>

        <table width="100%" border="0" cellpadding="0" cellspacing="0" align="center">
            <tr>
                <td colspan="4"><a href="./course_edit.html?id=-1">添加课程信息</a>
                <td colspan="4"><a href="JavaScript:getList();">刷新数据列表</a>
            </tr>
        </table>
        <br><br>


    </body>
    <script language="JavaScript">

        function delData(id) {
            $.ajax({
                cache: true,
                type: "POST",
                url: "../CourseAction?action=del",
                data: {"id": id},
                async: false,
                error: function (request) {
                    alert("Connection error");
                },
                success: function (data) {
                    if (data == "0") {
                        alert("数据删除不成功");
                    } else {
                        alert("数据删除成功!");
                        getList();
                    }
                }
            });
        }

    </script>
</html>
```

## Course_edit.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
    <head>
        <meta charset="UTF-8">
        <title>课程信息修改页面</title>
        <link rel="stylesheet" type="text/css"
              href="../css/student.css">
        <script type="text/javascript" src="../js/jquery-1.11.3.min.js"></script>
    </head>
    <script type="text/javascript">
        $(document).ready(function () {
            let url = location.search;
            let id = url.split('=')[1];
            show_input(id);
        });

        function show_input(id) {
            $('#studentsEditDiv').css('display', 'block');
            if (id != -1) {
                $('#frmTitle').text("课程信息修改");
                $.get("../CourseAction?action=get&id=" + id,			//servlet
                    function (data) {
                        ShowData(eval(data));
                    },	//JSON数据处理函数
                    "JSON");									//JSON
            } else {
                $('#frmTitle').text("课程信息添加");
                ClearData();
            }
        }

        function ShowData(course) {
            $("#id").val(course["id"]);
            $("#name").val(course["name"]);
            $("#type").val(course["type"]);
            $("input[type=radio][name=type][value='" + course["type"] + "']").prop("checked", true);
            $("#credit").val(course["credit"]);
            $("#grade").val(course["grade"]);
            $("input[type=radio][name=major][value='" + course["major"] + "']").prop("checked", true);
            $("#detail").val(course["detail"]);
        }

        function ClearData() {
            $("#id").val(-1);
            $("#name").val("");
            $("#type").val("1");
            $("#credit").val("");
            $("#grade").val();
            $("#major").val("");
            $("#detail").val("");
        }

        function submitData() {
            let re = /\S{5,20}/;
            if (!re.test($("#name").val())) {
                alert("请输入5~20个字符的姓名!");
                $("#name").focus();
                return;
            }
            re = /20[0-9]{2}/;
            if (!re.test($("#grade").val())) {
                alert("请输入4位整数年份20xx!");
                $("#grade").focus();
                return;
            }
            re = /^[0-9]+.?[0-9]*$/;
            if (!re.test($("#credit").val())) {
                alert("请输入数值类型的学分");
                $("#credit").focus();
                return;
            }
            let type = $("#type").val();
            if (type == null) {
                alert("请选择课程性质");
                $("#type").focus();
                return;
            }
            $.ajax({
                cache: true,
                type: "POST",
                url: "../CourseAction?action=update",
                data: $("#studentFrm").serialize(),
                async: false,
                error: function (request) {
                    alert("Connection error");
                },
                success: function (data) {
                    if (data == "0") {
                        alert("数据处理不成功");
                    } else {
                        $("#studentsEditDiv").css("display", "none");
                        alert("数据处理成功!");
                        self.location = document.referrer;
                    }
                }
            });


        }
    </script>
    <body>
        <div id="studentsEditDiv" style="display:NONE" class="divcss5">
            <form name="frm" id="studentFrm" method="post" action="../CourseAction">
                <input type="hidden" name="id" id="id" value="">
                <table border=1>
                    <tr class="td_header">
                        <td colspan="2"><span id="frmTitle">课程信息录入</span>，<span class="star">*</span>为必填项</td>
                    </tr>
                    <tr>
                        <td>名称</td>
                        <td><input type="text" name="name" id="name" value="" size="20" maxlength="20"
                                   onchange=""><span class="star">*</span>5~20位字符<span
                                id="codeSpan"></span></td>
                    </tr>
                    <tr>
                        <td>性质</td>
                        <td>
                            <select name="type" id="type" size='1'>
                                <option value='1'>公共基础必修课</option>
                                <option value='2'>公共选修课</option>
                                <option value='3'>学科基础课</option>
                                <option value='4'>专业必修课</option>
                                <option value='5'>专业选修课</option>
                                <option value='6'>集中性实践教学环节</option>
                            </select>
                            <span class="star">*</span>
                        </td>

                    </tr>
                    <tr>
                        <td>学分</td>
                        <td><input type="number" name="credit" id="credit" value="" size="20" maxlength="4"><span
                                class="star">*</span>数值
                        </td>
                    </tr>
                    <tr>
                        <td>开设年级</td>
                        <td><input type="text" name="grade" id="grade" value="" size="20" maxlength="4"><span
                                class="star">*</span>4位数年份
                        </td>
                    </tr>
                    <tr>
                        <td>开设专业</td>
                        <td>
                            <input type="radio" name="major" id="major1" value="1" checked>软件工程
                            <input type="radio" name="major" id="major2" value="2">空间信息
                            <input type="radio" name="major" id="major3" value="3">大数据
                            <span class="star">*</span></td>
                    </tr>
                    <tr>
                        <td>备注</td>
                        <td><TEXTAREA name="detail" id="detail" rows="2" cols="20"
                                      onpropertychange="if(this.value.length>200) {this.value=value.substr(0,200);alert('描述最多200个字符!');}"></TEXTAREA>200个字符以内
                        </td>
                    </tr>
                    <tr>
                        <td colspan="2" align="center"><input type="button" value="提交" onclick="submitData()"></td>
                    </tr>
                </table>
            </form>
        </div>
    </body>
</html>
```

## Boolean类型的getter和setter

```java
public boolean male;//性別
public void setMale(boolean male) {
    this.male = male;
}
public boolean isMale() {
    return this.male;
}
```

