# 1.mysql 连接

```java
package com;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class LoginServlet
 */
@SuppressWarnings("serial")
public class LoginServlet extends HttpServlet {
static final String url="jdbc:mysql://localhost:3306/javaweb?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC";
			
	static final String user="root";
	static final String pwd="123666";
	static final String driver="com.mysql.cj.jdbc.Driver";
	
	public ResultSet executeQuery(String sql) {
		ResultSet rs=null;
		try {
			Class.forName(driver);
			Connection con = DriverManager.getConnection(url, user, pwd);
			PreparedStatement psm=con.prepareStatement(sql);
			rs=psm.executeQuery();
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} 
		
		return rs;
	}
    public LoginServlet() {
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//设置字符编码
				response.setCharacterEncoding("UTF-8");
				response.setContentType("text/html;charset=UTF-8");
				request.setCharacterEncoding("UTF-8");
				
				//获取用户输入的信息
				String user=request.getParameter("user");
				String pwd=request.getParameter("pwd");
				
				PrintWriter out =response.getWriter();
				String sql="select * from t_user where name='"+user+"';";
				System.out.println(sql);
				String pwd2="";
				try {
					ResultSet rs=executeQuery(sql);
					if(rs.next()) {
						pwd2=rs.getString("pwd");
					}
					System.out.println(pwd2);
					if(pwd2.equals(pwd)) {
						System.out.println("可以");
//						request.getRequestDispatcher("login.jsp").include(request, response);
					request.getRequestDispatcher("login.jsp").include(request, response);;
					}else {
						out.write("用户名或密码不正确！");
					}
					rs.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				out.close();
	}

}

```

# 2.sql连接语句

```sql
jdbc:mysql://localhost:3307/javaweb?useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf-8
```

