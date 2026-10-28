# Enterprise Java Practical Exam Solutions
**Environment Setup:** NetBeans IDE 8.0.2 | GlassFish Server 4.1 | Java EE 7

---

## Standard Setup & Execution Steps for NetBeans 8.0.2 & GlassFish Server 4.1

### For Servlets / JSP / Web Applications
1. **Create Web Project:**
   - Open NetBeans IDE 8.0.2.
   - Go to `File` > `New Project...` > Select `Java Web` > `Web Application` > Click `Next`.
   - Set **Project Name** (e.g., `EJ_Practical_App`) and choose location. Click `Next`.
   - Select Server: **GlassFish Server 4.1** and Java EE Version: **Java EE 7 Web**. Click `Finish`.
2. **Creating HTML / JSP Pages:**
   - Right-click `Web Pages` folder > `New` > `HTML Page...` (or `JSP...`).
   - Enter file name (e.g., `index.html`) and click `Finish`.
3. **Creating Servlet Classes:**
   - Right-click project node > `New` > `Servlet...`.
   - Enter **Class Name** (e.g., `CalculatorServlet`) and **Package Name** (e.g., `EJ`). Click `Next`.
   - Ensure "Add information to deployment descriptor (web.xml)" is unchecked if using `@WebServlet` annotations, or checked if configuring via XML. Click `Finish`.
4. **Running the Application:**
   - Right-click project > `Clean and Build`.
   - Right-click project > `Run` (or press `F6`).

---

# SECTION 1: Servlets, JSP, JDBC and Web Forms (47 Questions)

## Q.1 — Unique Questions (A. Servlets)

---

### Question 1: Create a simple calculator application using a Servlet.

#### Steps:
1. Create a Web Application in NetBeans 8.0.2 named `ServletCalculator`.
2. Create `index.html` under `Web Pages`.
3. Create a Servlet named `CalculatorServlet` in package `EJ` with `@WebServlet("/CalculatorServlet")`.
4. Run the project on GlassFish Server 4.1.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head>
    <title>Simple Calculator</title>
</head>
<body>
    <h2>Simple Calculator</h2>
    <form action="CalculatorServlet" method="post">
        Enter Number 1: <input type="text" name="num1" /><br><br>
        Enter Number 2: <input type="text" name="num2" /><br><br>
        Operator:
        <select name="operator">
            <option value="Addition">Addition</option>
            <option value="Subtraction">Subtraction</option>
            <option value="Multiplication">Multiplication</option>
            <option value="Division">Division</option>
        </select><br><br>
        <input type="submit" value="Calculate" />
    </form>
</body>
</html>
```

#### Code: `CalculatorServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/CalculatorServlet")
public class CalculatorServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        try {
            double num1 = Double.parseDouble(request.getParameter("num1"));
            double num2 = Double.parseDouble(request.getParameter("num2"));
            String op = request.getParameter("operator");
            double res = 0;
            if (op.equals("Addition")) res = num1 + num2;
            else if (op.equals("Subtraction")) res = num1 - num2;
            else if (op.equals("Multiplication")) res = num1 * num2;
            else if (op.equals("Division")) res = num1 / num2;

            out.println("<html><body><h2>Result: " + res + "</h2></body></html>");
        } catch (Exception e) {
            out.println("<html><body><h2>Error: " + e.getMessage() + "</h2></body></html>");
        }
    }
}
```

---

### Question 2: Develop a basic calculator using ServletInputStream and ServletOutputStream to perform addition, subtraction, multiplication, division and modulus.

#### Steps:
1. Create a Web Application in NetBeans 8.0.2.
2. Create `index.html` using HTML form submit to `Calculator`.
3. Create Servlet `Servcal.java` reading raw bytes via `ServletInputStream` and writing via `ServletOutputStream`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head>
    <title>Basic Calculator</title>
</head>
<body>
    <h2>Basic Calculator</h2>
    <form action="Calculator" method="post">
        Enter Number 1: <input type="text" name="num1" /><br><br>
        Enter Number 2: <input type="text" name="num2" /><br><br>
        Operator:
        <select name="operator">
            <option value="Addition">Addition</option>
            <option value="Subtraction">Subtraction</option>
            <option value="Multiplication">Multiplication</option>
            <option value="Division">Division</option>
            <option value="Modulus">Modulus</option>
        </select><br><br>
        <input type="submit" value="Calculate" />
    </form>
</body>
</html>
```

#### Code: `Servcal.java`
```java
package EJ;

import java.io.IOException;
import java.net.URLDecoder;
import java.nio.charset.StandardCharsets;
import javax.servlet.ServletException;
import javax.servlet.ServletInputStream;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/Calculator")
public class Servcal extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        ServletInputStream in = request.getInputStream();
        byte[] data = new byte[4096];
        int length = in.read(data);
        ServletOutputStream out = response.getOutputStream();
        try {
            if (length < 0) throw new IllegalArgumentException("No input received");
            String input = new String(data, 0, length, StandardCharsets.UTF_8);
            String num1 = "", num2 = "", operator = "";
            String[] values = input.split("&");
            for (String value : values) {
                String[] pair = value.split("=", 2);
                if (pair.length != 2) continue;
                String key = URLDecoder.decode(pair[0], "UTF-8");
                String val = URLDecoder.decode(pair[1], "UTF-8");
                switch (key) {
                    case "num1": num1 = val; break;
                    case "num2": num2 = val; break;
                    case "operator": operator = val; break;
                }
            }
            int a = Integer.parseInt(num1);
            int b = Integer.parseInt(num2);
            int result = 0;
            switch (operator) {
                case "Addition": result = a + b; break;
                case "Subtraction": result = a - b; break;
                case "Multiplication": result = a * b; break;
                case "Division":
                    if (b == 0) throw new ArithmeticException("Cannot divide by zero");
                    result = a / b;
                    break;
                case "Modulus":
                    if (b == 0) throw new ArithmeticException("Cannot use modulus with zero");
                    result = a % b;
                    break;
            }
            String output = "<html><body><h2>Calculator Result</h2><p>Result: " + result + "</p></body></html>";
            out.write(output.getBytes(StandardCharsets.UTF_8));
        } catch (Exception ex) {
            out.write(("<html><body><p>Error: " + ex.getMessage() + "</p></body></html>").getBytes(StandardCharsets.UTF_8));
        }
    }
}
```

---

### Question 3: Create a Servlet-based login page that displays “Hello” or “Hello [username]” for valid credentials and “login failed” for invalid credentials.

#### Steps:
1. Create `index.html` with username and password input fields.
2. Create `LoginServlet.java` to validate input (e.g., username `admin`, password `123`).

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Login</title></head>
<body>
    <h2>User Login</h2>
    <form action="LoginServlet" method="post">
        Username: <input type="text" name="username"/><br><br>
        Password: <input type="password" name="password"/><br><br>
        <input type="submit" value="Login"/>
    </form>
</body>
</html>
```

#### Code: `LoginServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String u = request.getParameter("username");
        String p = request.getParameter("password");

        if ("admin".equals(u) && "1234".equals(p)) {
            out.println("<h2>Hello " + u + "</h2>");
        } else {
            out.println("<h2>Login Failed</h2>");
        }
    }
}
```

---

### Question 4: Create a Servlet-based login application for an online examination portal.

#### Steps:
1. Create `index.html` with exam portal registration/roll number and password.
2. Create `ExamLoginServlet.java` validating student credentials and redirecting to the exam dashboard.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Exam Portal Login</title></head>
<body>
    <h2>Online Examination Portal Login</h2>
    <form action="ExamLoginServlet" method="post">
        Roll No / ID: <input type="text" name="rollNo"/><br><br>
        Password: <input type="password" name="password"/><br><br>
        <input type="submit" value="Start Exam"/>
    </form>
</body>
</html>
```

#### Code: `ExamLoginServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ExamLoginServlet")
public class ExamLoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String roll = request.getParameter("rollNo");
        String pass = request.getParameter("password");

        if (roll != null && !roll.isEmpty() && "exam123".equals(pass)) {
            out.println("<h2>Welcome Candidate Roll No: " + roll + "</h2>");
            out.println("<p>Your Exam portal is ready. Best of Luck!</p>");
        } else {
            out.println("<h2>Invalid Portal Credentials. Access Denied.</h2>");
        }
    }
}
```

---

### Question 5: Create a registration Servlet using JDBC to accept username, password, email and country from an HTML form and store the details in a database.

#### Steps:
1. Set up Apache Derby or MySQL database with table `users(username, password, email, country)`.
2. Ensure JDBC Driver library is added to NetBeans Project Libraries.
3. Write `index.html` for registration inputs.
4. Write `RegisterServlet.java` executing `PreparedStatement` INSERT.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>User Registration</title></head>
<body>
    <h2>Registration Form</h2>
    <form action="RegisterServlet" method="post">
        Username: <input type="text" name="uname"/><br><br>
        Password: <input type="password" name="pass"/><br><br>
        Email: <input type="email" name="email"/><br><br>
        Country: <input type="text" name="country"/><br><br>
        <input type="submit" value="Register"/>
    </form>
</body>
</html>
```

#### Code: `RegisterServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/RegisterServlet")
public class RegisterServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        String u = request.getParameter("uname");
        String p = request.getParameter("pass");
        String e = request.getParameter("email");
        String c = request.getParameter("country");

        try {
            Class.forName("org.apache.derby.jdbc.ClientDriver");
            Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
            PreparedStatement ps = conn.prepareStatement("INSERT INTO users VALUES (?, ?, ?, ?)");
            ps.setString(1, u);
            ps.setString(2, p);
            ps.setString(3, e);
            ps.setString(4, c);
            int status = ps.executeUpdate();

            if (status > 0) {
                out.println("<h3>User Registered Successfully!</h3>");
            } else {
                out.println("<h3>Registration Failed.</h3>");
            }
        } catch (Exception ex) {
            out.println("<h3>Error: " + ex.getMessage() + "</h3>");
        }
    }
}
```

---

### Question 6: Create a Servlet using RequestDispatcher to validate a password and forward the user to a Welcome Servlet when valid; otherwise, display an error or remain on the index page.

#### Steps:
1. Create `index.html` for password submission.
2. Create `ValidateServlet.java` using `RequestDispatcher` (`forward` if correct, `include` if error).
3. Create `WelcomeServlet.java`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Validate Login</title></head>
<body>
    <h2>Login Page</h2>
    <form action="ValidateServlet" method="post">
        Username: <input type="text" name="uname"/><br><br>
        Password: <input type="password" name="pass"/><br><br>
        <input type="submit" value="Login"/>
    </form>
</body>
</html>
```

#### Code: `ValidateServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ValidateServlet")
public class ValidateServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        String pass = request.getParameter("pass");
        if ("admin123".equals(pass)) {
            RequestDispatcher rd = request.getRequestDispatcher("WelcomeServlet");
            rd.forward(request, response);
        } else {
            out.println("<h3 style='color:red;'>Invalid Password! Try Again.</h3>");
            RequestDispatcher rd = request.getRequestDispatcher("index.html");
            rd.include(request, response);
        }
    }
}
```

#### Code: `WelcomeServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/WelcomeServlet")
public class WelcomeServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String u = request.getParameter("uname");
        out.println("<h2>Welcome, " + u + "! You have logged in successfully.</h2>");
    }
}
```

---

### Question 7: Create a Servlet that uses cookies to store and display the number of times a user has visited the application.

#### Steps:
1. Create `VisitorCounterServlet.java`.
2. Check `request.getCookies()`, retrieve existing counter cookie, increment, and set cookie back using `response.addCookie()`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Cookie Visit Counter</title></head>
<body>
    <h2>Click to track visits:</h2>
    <a href="VisitorCounterServlet">Visit Application</a>
</body>
</html>
```

#### Code: `VisitorCounterServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/VisitorCounterServlet")
public class VisitorCounterServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        Cookie[] cookies = request.getCookies();
        int count = 0;
        if (cookies != null) {
            for (Cookie c : cookies) {
                if ("visitCount".equals(c.getName())) {
                    count = Integer.parseInt(c.getValue());
                }
            }
        }
        count++;
        Cookie visitCookie = new Cookie("visitCount", String.valueOf(count));
        visitCookie.setMaxAge(60 * 60 * 24); // 1 day
        response.addCookie(visitCookie);

        out.println("<h2>Visit Counter</h2>");
        out.println("<h3>You have visited this page " + count + " times.</h3>");
    }
}
```

---

### Question 8: Create a Servlet that demonstrates session creation and destruction and checks whether the user is visiting for the first time or has visited previously.

#### Steps:
1. Create `SessionServlet.java`.
2. Use `request.getSession(true)` and `session.isNew()`.
3. Provide link/button to invalidate session via `session.invalidate()`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Session Demo</title></head>
<body>
    <h2>Session Tracking Demo</h2>
    <a href="SessionServlet?action=visit">Check Session Status</a><br><br>
    <a href="SessionServlet?action=logout">Destroy Session</a>
</body>
</html>
```

#### Code: `SessionServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/SessionServlet")
public class SessionServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String action = request.getParameter("action");

        if ("logout".equals(action)) {
            HttpSession session = request.getSession(false);
            if (session != null) {
                session.invalidate();
            }
            out.println("<h2>Session Destroyed Successfully!</h2>");
            out.println("<a href='index.html'>Back to Home</a>");
            return;
        }

        HttpSession session = request.getSession(true);
        if (session.isNew()) {
            out.println("<h2>Welcome! This is your FIRST visit.</h2>");
        } else {
            out.println("<h2>Welcome back! You have visited previously.</h2>");
        }
        out.println("<p>Session ID: " + session.getId() + "</p>");
        out.println("<a href='SessionServlet?action=logout'>Logout / Invalidate Session</a>");
    }
}
```

---

### Question 9: Create a Servlet application to upload and download files.

#### Steps:
1. Create `index.html` using `enctype="multipart/form-data"` for file uploading.
2. Annotate `FileUploadServlet` with `@MultipartConfig`.
3. Create `FileDownloadServlet` streaming bytes back to client.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>File Transfer</title></head>
<body>
    <h2>File Upload</h2>
    <form action="FileUploadServlet" method="post" enctype="multipart/form-data">
        Select File: <input type="file" name="file"/><br><br>
        <input type="submit" value="Upload File"/>
    </form>
    <hr>
    <h2>File Download</h2>
    <a href="FileDownloadServlet?fileName=test.txt">Download test.txt</a>
</body>
</html>
```

#### Code: `FileUploadServlet.java`
```java
package EJ;

import java.io.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.MultipartConfig;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/FileUploadServlet")
@MultipartConfig
public class FileUploadServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        Part filePart = request.getPart("file");
        String fileName = filePart.getSubmittedFileName();
        String uploadPath = getServletContext().getRealPath("") + File.separator + "uploads";
        File uploadDir = new File(uploadPath);
        if (!uploadDir.exists()) uploadDir.mkdir();

        filePart.write(uploadPath + File.separator + fileName);
        out.println("<h3>File " + fileName + " uploaded successfully to " + uploadPath + "</h3>");
    }
}
```

#### Code: `FileDownloadServlet.java`
```java
package EJ;

import java.io.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/FileDownloadServlet")
public class FileDownloadServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String fileName = request.getParameter("fileName");
        String filePath = getServletContext().getRealPath("") + File.separator + "uploads" + File.separator + fileName;
        File downloadFile = new File(filePath);

        if (!downloadFile.exists()) {
            response.getWriter().println("File not found!");
            return;
        }

        response.setContentType("application/octet-stream");
        response.setHeader("Content-Disposition", "attachment; filename=\"" + fileName + "\"");

        FileInputStream inStream = new FileInputStream(downloadFile);
        OutputStream outStream = response.getOutputStream();
        byte[] buffer = new byte[4096];
        int bytesRead;
        while ((bytesRead = inStream.read(buffer)) != -1) {
            outStream.write(buffer, 0, bytesRead);
        }
        inStream.close();
        outStream.close();
    }
}
```

---

### Question 10: Create a Servlet application demonstrating non-blocking read operations.

#### Steps:
1. Enable `asyncSupported = true` on `@WebServlet`.
2. Create custom `ReadListener` attached to `ServletInputStream`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Non-Blocking Read</title></head>
<body>
    <h2>Non-Blocking Read Test</h2>
    <form action="NonBlockingServlet" method="post">
        Data payload: <input type="text" name="data"/><br><br>
        <input type="submit" value="Send Async"/>
    </form>
</body>
</html>
```

#### Code: `NonBlockingServlet.java`
```java
package EJ;

import java.io.IOException;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet(urlPatterns = "/NonBlockingServlet", asyncSupported = true)
public class NonBlockingServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        AsyncContext asyncCtx = request.startAsync();
        ServletInputStream input = request.getInputStream();
        input.setReadListener(new ReadListener() {
            @Override
            public void onDataAvailable() throws IOException {
                byte[] buffer = new byte[1024];
                while (input.isReady() && !input.isFinished()) {
                    int length = input.read(buffer);
                }
            }

            @Override
            public void onAllDataRead() throws IOException {
                HttpServletResponse resp = (HttpServletResponse) asyncCtx.getResponse();
                resp.getWriter().println("Non-blocking read completed processing.");
                asyncCtx.complete();
            }

            @Override
            public void onError(Throwable t) {
                asyncCtx.complete();
            }
        });
    }
}
```

---

### Question 11: Develop a Servlet-based question-and-answer application that retrieves questions from a database.

#### Steps:
1. Create table `qa_bank(id INT, question VARCHAR(255), answer VARCHAR(255))`.
2. Servlet connects via JDBC, queries all questions, and renders HTML output.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Q&A Portal</title></head>
<body>
    <h2>Question & Answer Repository</h2>
    <a href="QAServlet">View Questions & Answers</a>
</body>
</html>
```

#### Code: `QAServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/QAServlet")
public class QAServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        try {
            Class.forName("org.apache.derby.jdbc.ClientDriver");
            Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery("SELECT * FROM qa_bank");

            out.println("<h2>Question & Answer Portal</h2><ol>");
            while (rs.next()) {
                out.println("<li><b>Q: " + rs.getString("question") + "</b><br>A: " + rs.getString("answer") + "</li><br>");
            }
            out.println("</ol>");
        } catch (Exception ex) {
            out.println("<h3>Error: " + ex.getMessage() + "</h3>");
        }
    }
}
```

---

### Question 12: Develop a web-based multiple-choice quiz with at least five questions, four options per question and one selectable answer per question. Display the number of correct and incorrect answers after submission.

#### Steps:
1. Create `quiz.html` containing 5 questions using radio buttons (`q1`, `q2`, `q3`, `q4`, `q5`).
2. Create `QuizServlet.java` evaluating answers against key and returning total score.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Online Quiz</title></head>
<body>
    <h2>Multiple Choice Quiz</h2>
    <form action="QuizServlet" method="post">
        <p>1. What is Java EE?</p>
        <input type="radio" name="q1" value="A"> A language<br>
        <input type="radio" name="q1" value="B"> Enterprise Edition platform<br>
        <input type="radio" name="q1" value="C"> Database<br>
        <input type="radio" name="q1" value="D"> IDE<br>

        <p>2. Which method initiates a Servlet?</p>
        <input type="radio" name="q2" value="A"> init()<br>
        <input type="radio" name="q2" value="B"> start()<br>
        <input type="radio" name="q2" value="C"> run()<br>
        <input type="radio" name="q2" value="D"> main()<br>

        <p>3. Default port for GlassFish HTTP?</p>
        <input type="radio" name="q3" value="A"> 8080<br>
        <input type="radio" name="q3" value="B"> 3306<br>
        <input type="radio" name="q3" value="C"> 1527<br>
        <input type="radio" name="q3" value="D"> 443<br>

        <p>4. Which scope is valid in JSP?</p>
        <input type="radio" name="q4" value="A"> session<br>
        <input type="radio" name="q4" value="B"> global<br>
        <input type="radio" name="q4" value="C"> system<br>
        <input type="radio" name="q4" value="D"> local<br>

        <p>5. JSTL stands for JavaServer Pages Standard Tag Library?</p>
        <input type="radio" name="q5" value="A"> True<br>
        <input type="radio" name="q5" value="B"> False<br><br>

        <input type="submit" value="Submit Quiz"/>
    </form>
</body>
</html>
```

#### Code: `QuizServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/QuizServlet")
public class QuizServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        int correct = 0, incorrect = 0;
        if ("B".equals(request.getParameter("q1"))) correct++; else incorrect++;
        if ("A".equals(request.getParameter("q2"))) correct++; else incorrect++;
        if ("A".equals(request.getParameter("q3"))) correct++; else incorrect++;
        if ("A".equals(request.getParameter("q4"))) correct++; else incorrect++;
        if ("A".equals(request.getParameter("q5"))) correct++; else incorrect++;

        out.println("<h2>Quiz Results</h2>");
        out.println("<p>Correct Answers: " + correct + "</p>");
        out.println("<p>Incorrect Answers: " + incorrect + "</p>");
        out.println("<p>Total Score: " + correct + " / 5</p>");
    }
}
```

---

### Question 13: Create a Servlet application that accepts two numbers and an arithmetic operation from an HTML form, calculates the result and displays it.

#### Steps:
1. Create `index.html` with two numeric inputs and operation selector.
2. Calculate and present response in `ArithmeticServlet.java`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Arithmetic App</title></head>
<body>
    <form action="ArithmeticServlet" method="post">
        Num 1: <input type="number" name="n1"/><br><br>
        Num 2: <input type="number" name="n2"/><br><br>
        Operation: 
        <select name="op">
            <option value="+">Add</option>
            <option value="-">Subtract</option>
            <option value="*">Multiply</option>
        </select><br><br>
        <input type="submit" value="Compute"/>
    </form>
</body>
</html>
```

#### Code: `ArithmeticServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ArithmeticServlet")
public class ArithmeticServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        double n1 = Double.parseDouble(request.getParameter("n1"));
        double n2 = Double.parseDouble(request.getParameter("n2"));
        String op = request.getParameter("op");
        double result = 0;

        if ("+".equals(op)) result = n1 + n2;
        else if ("-".equals(op)) result = n1 - n2;
        else if ("*".equals(op)) result = n1 * n2;

        out.println("<h2>Calculation Result: " + result + "</h2>");
    }
}
```

---

### Question 14: Create a Servlet application demonstrating simple input validation.

#### Steps:
1. `index.html` collects age and email.
2. `ValidationServlet.java` verifies non-empty input and age >= 18.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Input Validation</title></head>
<body>
    <h2>User Input Validation</h2>
    <form action="ValidationServlet" method="post">
        Age: <input type="text" name="age"/><br><br>
        Email: <input type="text" name="email"/><br><br>
        <input type="submit" value="Validate"/>
    </form>
</body>
</html>
```

#### Code: `ValidationServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ValidationServlet")
public class ValidationServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        String ageStr = request.getParameter("age");
        String email = request.getParameter("email");

        if (ageStr == null || ageStr.trim().isEmpty() || email == null || email.trim().isEmpty()) {
            out.println("<h3 style='color:red;'>Error: Fields cannot be empty!</h3>");
            return;
        }

        try {
            int age = Integer.parseInt(ageStr);
            if (age < 18) {
                out.println("<h3 style='color:red;'>Validation Failed: Must be at least 18 years old.</h3>");
            } else {
                out.println("<h3 style='color:green;'>Validation Passed! Inputs are valid.</h3>");
            }
        } catch (NumberFormatException e) {
            out.println("<h3 style='color:red;'>Error: Age must be a number!</h3>");
        }
    }
}
```

---

### Question 15: Create a simple HTML form that submits a user's name to a Servlet and displays a welcome message containing the name.

#### Steps:
1. Simple input field in `index.html`.
2. Welcome response generated in `GreetingServlet.java`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Welcome Form</title></head>
<body>
    <form action="GreetingServlet" method="get">
        Enter Your Name: <input type="text" name="uname"/>
        <input type="submit" value="Greet Me"/>
    </form>
</body>
</html>
```

#### Code: `GreetingServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/GreetingServlet")
public class GreetingServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String name = request.getParameter("uname");
        out.println("<h1>Welcome, " + name + "! Have a great day.</h1>");
    }
}
```

---

### Question 16: Create a Servlet application for a stationery shop that accepts the prices of two items and displays their sum, difference, product and division.

#### Steps:
1. Accept prices of Item 1 and Item 2 in `index.html`.
2. Process all four operations in `StationeryServlet.java`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Stationery Shop</title></head>
<body>
    <h2>Stationery Shop Billing</h2>
    <form action="StationeryServlet" method="post">
        Pen Price: <input type="text" name="p1"/><br><br>
        Notebook Price: <input type="text" name="p2"/><br><br>
        <input type="submit" value="Calculate Calculations"/>
    </form>
</body>
</html>
```

#### Code: `StationeryServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/StationeryServlet")
public class StationeryServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        double p1 = Double.parseDouble(request.getParameter("p1"));
        double p2 = Double.parseDouble(request.getParameter("p2"));

        out.println("<h2>Stationery Calculations</h2>");
        out.println("<p>Sum: " + (p1 + p2) + "</p>");
        out.println("<p>Difference: " + (p1 - p2) + "</p>");
        out.println("<p>Product: " + (p1 * p2) + "</p>");
        out.println("<p>Division: " + (p2 != 0 ? (p1 / p2) : "N/A") + "</p>");
    }
}
```

---

### Question 17: Create a Servlet application for a canteen that accepts the prices of two food items and displays their total cost.

#### Steps:
1. HTML form collecting food prices.
2. Servlet computes total bill sum.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Canteen Billing</title></head>
<body>
    <h2>Canteen Billing System</h2>
    <form action="CanteenServlet" method="post">
        Item 1 Cost: <input type="text" name="item1"/><br><br>
        Item 2 Cost: <input type="text" name="item2"/><br><br>
        <input type="submit" value="Get Total Cost"/>
    </form>
</body>
</html>
```

#### Code: `CanteenServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/CanteenServlet")
public class CanteenServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        double c1 = Double.parseDouble(request.getParameter("item1"));
        double c2 = Double.parseDouble(request.getParameter("item2"));

        out.println("<h2>Canteen Bill</h2>");
        out.println("<h3>Total Amount Payable: $" + (c1 + c2) + "</h3>");
    }
}
```

---

### Question 18: Create a Servlet application for a college admission portal that validates an application and uses RequestDispatcher to forward it to a confirmation page.

#### Steps:
1. Take applicant details in `index.html`.
2. Validate percentage in `AdmissionServlet.java` and forward to `ConfirmAdmissionServlet`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Admission Portal</title></head>
<body>
    <h2>College Admission Application</h2>
    <form action="AdmissionServlet" method="post">
        Student Name: <input type="text" name="sname"/><br><br>
        12th Percentage: <input type="text" name="marks"/><br><br>
        <input type="submit" value="Submit Application"/>
    </form>
</body>
</html>
```

#### Code: `AdmissionServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/AdmissionServlet")
public class AdmissionServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        double marks = Double.parseDouble(request.getParameter("marks"));
        if (marks >= 50.0) {
            RequestDispatcher rd = request.getRequestDispatcher("ConfirmAdmissionServlet");
            rd.forward(request, response);
        } else {
            response.getWriter().println("<h2>Admission Rejected: Marks below 50% cutoff.</h2>");
        }
    }
}
```

#### Code: `ConfirmAdmissionServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ConfirmAdmissionServlet")
public class ConfirmAdmissionServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String name = request.getParameter("sname");
        out.println("<h2 style='color:green;'>Congratulations " + name + "! Your admission is provisionally confirmed.</h2>");
    }
}
```

---

### Question 19: Create a Servlet application for a bookshop that accepts customer details and stores them in a database using JDBC.

#### Steps:
1. Create table `book_customers(id INT, name VARCHAR(50), phone VARCHAR(15))`.
2. HTML form posts data to `BookshopServlet.java` which performs JDBC `INSERT`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Bookshop Customer Registration</title></head>
<body>
    <h2>Bookshop Customer Register</h2>
    <form action="BookshopServlet" method="post">
        Customer Name: <input type="text" name="cname"/><br><br>
        Phone Number: <input type="text" name="cphone"/><br><br>
        <input type="submit" value="Save Customer"/>
    </form>
</body>
</html>
```

#### Code: `BookshopServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/BookshopServlet")
public class BookshopServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        String name = request.getParameter("cname");
        String phone = request.getParameter("cphone");

        try {
            Class.forName("org.apache.derby.jdbc.ClientDriver");
            Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
            PreparedStatement ps = conn.prepareStatement("INSERT INTO book_customers(name, phone) VALUES (?, ?)");
            ps.setString(1, name);
            ps.setString(2, phone);
            ps.executeUpdate();

            out.println("<h3>Customer recorded in Bookshop DB successfully!</h3>");
        } catch (Exception e) {
            out.println("<h3>DB Error: " + e.getMessage() + "</h3>");
        }
    }
}
```

---

### Question 20: Create a Servlet application for a parcel-tracking system that uses sessions to store and display customer tracking details.

#### Steps:
1. Accept Tracking Number & Status in `index.html`.
2. Store in session in `ParcelServlet.java` and display details.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Parcel Tracking</title></head>
<body>
    <h2>Parcel Tracking System</h2>
    <form action="ParcelServlet" method="post">
        Tracking ID: <input type="text" name="trackId"/><br><br>
        Destination: <input type="text" name="dest"/><br><br>
        <input type="submit" value="Track Parcel"/>
    </form>
</body>
</html>
```

#### Code: `ParcelServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ParcelServlet")
public class ParcelServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        String trackId = request.getParameter("trackId");
        String dest = request.getParameter("dest");

        HttpSession session = request.getSession();
        session.setAttribute("trackId", trackId);
        session.setAttribute("dest", dest);

        out.println("<h2>Parcel Tracked Successfully</h2>");
        out.println("<p>Session Stored Tracking ID: " + session.getAttribute("trackId") + "</p>");
        out.println("<p>Destination: " + session.getAttribute("dest") + "</p>");
    }
}
```

---

### Question 21: Create a Servlet-based online test portal that stores student details in sessions and displays them on another page.

#### Steps:
1. Register student in `TestLoginServlet.java` & save to session.
2. Redirect to `TestDashboardServlet.java` which retrieves session attributes.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Test Portal</title></head>
<body>
    <h2>Online Test Registration</h2>
    <form action="TestLoginServlet" method="post">
        Student Name: <input type="text" name="sname"/><br><br>
        Subject: <input type="text" name="subject"/><br><br>
        <input type="submit" value="Start Test"/>
    </form>
</body>
</html>
```

#### Code: `TestLoginServlet.java`
```java
package EJ;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/TestLoginServlet")
public class TestLoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        HttpSession session = request.getSession();
        session.setAttribute("studentName", request.getParameter("sname"));
        session.setAttribute("testSubject", request.getParameter("subject"));

        response.sendRedirect("TestDashboardServlet");
    }
}
```

#### Code: `TestDashboardServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/TestDashboardServlet")
public class TestDashboardServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        HttpSession session = request.getSession(false);
        if (session != null) {
            String name = (String) session.getAttribute("studentName");
            String subject = (String) session.getAttribute("testSubject");
            out.println("<h2>Test Dashboard</h2>");
            out.println("<p>Student: " + name + "</p>");
            out.println("<p>Exam Subject: " + subject + "</p>");
        } else {
            out.println("<h2>No Active Session Found. Please Log In.</h2>");
        }
    }
}
```

---

### Question 22: Create a Servlet application for a shopping website that uses cookies to remember a visitor's name on subsequent visits.

#### Steps:
1. Read visitor cookie in `ShoppingCookieServlet.java`.
2. If absent, set cookie with value submitted from HTML form.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Shopping Portal</title></head>
<body>
    <h2>Welcome to E-Shop</h2>
    <form action="ShoppingCookieServlet" method="post">
        Enter Name: <input type="text" name="visitorName"/>
        <input type="submit" value="Enter Store"/>
    </form>
</body>
</html>
```

#### Code: `ShoppingCookieServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ShoppingCookieServlet")
public class ShoppingCookieServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        String name = request.getParameter("visitorName");
        if (name != null && !name.isEmpty()) {
            Cookie c = new Cookie("shopVisitor", name);
            c.setMaxAge(3600);
            response.addCookie(c);
        }

        out.println("<h2>Welcome to E-Shop, " + name + "!</h2>");
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        Cookie[] cookies = request.getCookies();
        String visitor = null;
        if (cookies != null) {
            for (Cookie c : cookies) {
                if ("shopVisitor".equals(c.getName())) visitor = c.getValue();
            }
        }

        if (visitor != null) {
            out.println("<h2>Welcome Back to E-Shop, " + visitor + "!</h2>");
        } else {
            out.println("<h2>Welcome First-time Visitor!</h2>");
        }
    }
}
```

---

### Question 23: Create a Servlet application that calculates a water bill based on units consumed.

#### Steps:
1. Form collects units consumed.
2. `WaterBillServlet.java` computes total bill using rate slab ($5 / unit).

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Water Bill Calculator</title></head>
<body>
    <h2>Water Bill Calculator</h2>
    <form action="WaterBillServlet" method="post">
        Units Consumed: <input type="text" name="units"/>
        <input type="submit" value="Calculate Bill"/>
    </form>
</body>
</html>
```

#### Code: `WaterBillServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/WaterBillServlet")
public class WaterBillServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        int units = Integer.parseInt(request.getParameter("units"));
        double bill = 0;

        if (units <= 100) bill = units * 3.0;
        else bill = (100 * 3.0) + ((units - 100) * 5.0);

        out.println("<h2>Water Bill Details</h2>");
        out.println("<p>Units Consumed: " + units + "</p>");
        out.println("<p>Total Payable: $" + bill + "</p>");
    }
}
```

---

### Question 24: Create a Servlet application for a college notice board that allows an administrator to upload PDF notices and students to download them.

#### Steps:
1. `NoticeUploadServlet` handles PDF file uploads.
2. `NoticeDownloadServlet` serves uploaded files.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>College Notice Board</title></head>
<body>
    <h2>Upload Notice (Admin)</h2>
    <form action="NoticeUploadServlet" method="post" enctype="multipart/form-data">
        Select PDF Notice: <input type="file" name="noticePdf"/><br><br>
        <input type="submit" value="Upload Notice"/>
    </form>
    <hr>
    <h2>Student Notice Board</h2>
    <a href="NoticeDownloadServlet?file=exam_schedule.pdf">Download Exam Schedule PDF</a>
</body>
</html>
```

#### Code: `NoticeUploadServlet.java`
```java
package EJ;

import java.io.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.MultipartConfig;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/NoticeUploadServlet")
@MultipartConfig
public class NoticeUploadServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        Part filePart = request.getPart("noticePdf");
        String fileName = filePart.getSubmittedFileName();
        String uploadPath = getServletContext().getRealPath("") + File.separator + "notices";
        File dir = new File(uploadPath);
        if (!dir.exists()) dir.mkdir();

        filePart.write(uploadPath + File.separator + fileName);
        response.getWriter().println("Notice " + fileName + " published successfully.");
    }
}
```

#### Code: `NoticeDownloadServlet.java`
```java
package EJ;

import java.io.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/NoticeDownloadServlet")
public class NoticeDownloadServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String fileName = request.getParameter("file");
        String filePath = getServletContext().getRealPath("") + File.separator + "notices" + File.separator + fileName;
        File downloadFile = new File(filePath);

        if (!downloadFile.exists()) {
            response.getWriter().println("Notice PDF not found!");
            return;
        }

        response.setContentType("application/pdf");
        response.setHeader("Content-Disposition", "inline; filename=\"" + fileName + "\"");

        FileInputStream in = new FileInputStream(downloadFile);
        OutputStream out = response.getOutputStream();
        byte[] buffer = new byte[4096];
        int bytesRead;
        while ((bytesRead = in.read(buffer)) != -1) {
            out.write(buffer, 0, bytesRead);
        }
        in.close();
    }
}
```

---

### Question 25: Create a Servlet application for a library that accepts book details and displays the submitted information on a separate page.

#### Steps:
1. Create `index.html` for inputting Book Title, Author, ISBN.
2. Process and present book details in `LibraryServlet.java`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Library Entry</title></head>
<body>
    <h2>Library Book Entry Form</h2>
    <form action="LibraryServlet" method="post">
        Book Title: <input type="text" name="title"/><br><br>
        Author: <input type="text" name="author"/><br><br>
        ISBN: <input type="text" name="isbn"/><br><br>
        <input type="submit" value="Submit Book"/>
    </form>
</body>
</html>
```

#### Code: `LibraryServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/LibraryServlet")
public class LibraryServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        out.println("<h2>Submitted Book Information</h2>");
        out.println("<p><b>Title:</b> " + request.getParameter("title") + "</p>");
        out.println("<p><b>Author:</b> " + request.getParameter("author") + "</p>");
        out.println("<p><b>ISBN:</b> " + request.getParameter("isbn") + "</p>");
    }
}
```

---

### Question 26: Create a Servlet application for a college event that uses RequestDispatcher to forward a valid registration to a confirmation page.

#### Steps:
1. Accept student event registration details.
2. Validate age >= 18 in `EventServlet.java` and forward to `EventConfirmServlet`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Event Registration</title></head>
<body>
    <h2>College Fest Registration</h2>
    <form action="EventServlet" method="post">
        Name: <input type="text" name="name"/><br><br>
        Age: <input type="text" name="age"/><br><br>
        <input type="submit" value="Register Event"/>
    </form>
</body>
</html>
```

#### Code: `EventServlet.java`
```java
package EJ;

import java.io.IOException;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/EventServlet")
public class EventServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        int age = Integer.parseInt(request.getParameter("age"));
        if (age >= 18) {
            RequestDispatcher rd = request.getRequestDispatcher("EventConfirmServlet");
            rd.forward(request, response);
        } else {
            response.getWriter().println("<h2>Registration Failed: Must be 18 or older to join event.</h2>");
        }
    }
}
```

#### Code: `EventConfirmServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/EventConfirmServlet")
public class EventConfirmServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("<h2>Event Registration Confirmed for " + request.getParameter("name") + "!</h2>");
    }
}
```

---

## B. JSP, JDBC and Web Forms

---

### Question 27: Develop a JSP application to display values obtained from different implicit objects.

#### Steps:
1. Create `implicit_demo.jsp` in `Web Pages`.
2. Output properties from `request`, `response`, `session`, `application`, `out`, `config`, `pageContext`, `page`.

#### Code: `implicit_demo.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>JSP Implicit Objects</title></head>
<body>
    <h2>JSP Implicit Objects Demo</h2>
    <p><b>Request Server Name:</b> <%= request.getServerName() %></p>
    <p><b>Request User Agent:</b> <%= request.getHeader("User-Agent") %></p>
    <p><b>Session ID:</b> <%= session.getId() %></p>
    <p><b>Application Context Path:</b> <%= application.getContextPath() %></p>
    <p><b>Servlet Config Name:</b> <%= config.getServletName() %></p>
    <p><b>Page Class Name:</b> <%= page.getClass().getName() %></p>
</body>
</html>
```

---

### Question 28: Create a JSP application demonstrating at least three methods of four implicit objects.

#### Steps:
1. Demonstrate methods for `request`, `response`, `session`, and `application`.

#### Code: `methods_demo.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Implicit Object Methods</title></head>
<body>
    <h2>Methods Demo</h2>

    <h3>1. Request Methods</h3>
    <p>Method: <%= request.getMethod() %></p>
    <p>Protocol: <%= request.getProtocol() %></p>
    <p>Remote Addr: <%= request.getRemoteAddr() %></p>

    <h3>2. Session Methods</h3>
    <% session.setAttribute("user", "Student"); %>
    <p>Session Attr: <%= session.getAttribute("user") %></p>
    <p>Creation Time: <%= session.getCreationTime() %></p>
    <p>Max Inactive Interval: <%= session.getMaxInactiveInterval() %></p>

    <h3>3. Application Methods</h3>
    <p>Server Info: <%= application.getServerInfo() %></p>
    <p>Major Version: <%= application.getMajorVersion() %></p>
    <p>Mime Type: <%= application.getMimeType("index.html") %></p>

    <h3>4. PageContext Methods</h3>
    <p>Out buffer size: <%= pageContext.getOut().getBufferSize() %></p>
    <p>Session active: <%= pageContext.getSession() != null %></p>
    <p>Request obj: <%= pageContext.getRequest().getClass().getSimpleName() %></p>
</body>
</html>
```

---

### Question 29: Create a JSP application demonstrating Expression Language (EL).

#### Steps:
1. Store values in request/session scopes.
2. Access using `${param.name}`, `${sessionScope.user}`, etc.

#### Code: `el_demo.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>EL Demo</title></head>
<body>
    <%
        request.setAttribute("course", "Enterprise Java");
        session.setAttribute("user", "Alice");
    %>
    <h2>Expression Language (EL) Examples</h2>
    <p>Course Name: ${requestScope.course}</p>
    <p>User Name: ${sessionScope.user}</p>
    <p>Header User-Agent: ${header["user-agent"]}</p>
    <p>Arithmetic (10 + 20): ${10 + 20}</p>
</body>
</html>
```

---

### Question 30: Create a JSP application demonstrating JSTL.

#### Steps:
1. Add JSTL 1.2 library to project.
2. Use `<c:set>`, `<c:out>`, `<c:if>`, and `<c:forEach>`.

#### Code: `jstl_demo.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head><title>JSTL Demo</title></head>
<body>
    <h2>JSTL Core Tags Demo</h2>
    <c:set var="score" value="85"/>
    <p>Score: <c:out value="${score}"/></p>

    <c:if test="${score >= 50}">
        <p style="color:green;">Status: PASSED</p>
    </c:if>

    <h3>Looping Numbers:</h3>
    <ul>
        <c:forEach var="i" begin="1" end="5">
            <li>Item <c:out value="${i}"/></li>
        </c:forEach>
    </ul>
</body>
</html>
```

---

### Question 31: Develop a JSP application demonstrating JSP Action Tags.

#### Steps:
1. Create `header.jsp`.
2. Use `<jsp:include>` and `<jsp:forward>` tags.

#### Code: `header.jsp`
```jsp
<h3>-- Standard Header Included --</h3>
```

#### Code: `action_demo.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Action Tags</title></head>
<body>
    <jsp:include page="header.jsp" />
    <h2>JSP Action Tags Demo</h2>
    <jsp:useBean id="date" class="java.util.Date" />
    <p>Current Time via Bean: <%= date %></p>
</body>
</html>
```

---

### Question 32: Develop a JSP application that passes values from one page to another and validates name, age, hobbies, email and gender.

#### Steps:
1. `form.jsp` accepts form inputs.
2. `process.jsp` validates fields.

#### Code: `form.jsp`
```html
<!DOCTYPE html>
<html>
<head><title>User Details</title></head>
<body>
    <form action="process.jsp" method="post">
        Name: <input type="text" name="name"/><br><br>
        Age: <input type="text" name="age"/><br><br>
        Hobbies: <input type="text" name="hobbies"/><br><br>
        Email: <input type="text" name="email"/><br><br>
        Gender: 
        <input type="radio" name="gender" value="Male"> Male
        <input type="radio" name="gender" value="Female"> Female<br><br>
        <input type="submit" value="Submit & Validate"/>
    </form>
</body>
</html>
```

#### Code: `process.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Validation Result</title></head>
<body>
    <h2>Validation Results</h2>
    <%
        String name = request.getParameter("name");
        String ageStr = request.getParameter("age");
        String hobbies = request.getParameter("hobbies");
        String email = request.getParameter("email");
        String gender = request.getParameter("gender");

        if(name != null && !name.isEmpty() && email != null && email.contains("@")) {
            out.println("<p style='color:green;'>All fields valid!</p>");
            out.println("<p>Name: " + name + "</p>");
            out.println("<p>Age: " + ageStr + "</p>");
            out.println("<p>Hobbies: " + hobbies + "</p>");
            out.println("<p>Email: " + email + "</p>");
            out.println("<p>Gender: " + gender + "</p>");
        } else {
            out.println("<p style='color:red;'>Validation Error: Check Email and Name!</p>");
        }
    %>
</body>
</html>
```

---

### Question 33: Create a JSP page that accepts employee number, employee name and salary, then inserts the record into an emp database table.

#### Steps:
1. Create table `emp(empno INT PRIMARY KEY, ename VARCHAR(50), salary DOUBLE)`.
2. JSP processes submission via JDBC.

#### Code: `emp_insert.jsp`
```jsp
<%@page import="java.sql.*"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Insert Employee</title></head>
<body>
    <h2>Insert Employee Record</h2>
    <form method="post">
        Emp No: <input type="text" name="empno"/><br><br>
        Ename: <input type="text" name="ename"/><br><br>
        Salary: <input type="text" name="salary"/><br><br>
        <input type="submit" value="Insert Employee"/>
    </form>

    <%
        String empno = request.getParameter("empno");
        if (empno != null) {
            String ename = request.getParameter("ename");
            double sal = Double.parseDouble(request.getParameter("salary"));
            try {
                Class.forName("org.apache.derby.jdbc.ClientDriver");
                Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
                PreparedStatement ps = conn.prepareStatement("INSERT INTO emp VALUES (?, ?, ?)");
                ps.setInt(1, Integer.parseInt(empno));
                ps.setString(2, ename);
                ps.setDouble(3, sal);
                ps.executeUpdate();
                out.println("<h3 style='color:green;'>Employee Inserted Successfully!</h3>");
            } catch(Exception e) {
                out.println("<h3 style='color:red;'>Error: " + e.getMessage() + "</h3>");
            }
        }
    %>
</body>
</html>
```

---

### Question 34: Create an HTML form with employee number, name, age, designation and salary; submit the data to a JSP page that updates the matching employee record.

#### Steps:
1. `index.html` submits update payload.
2. `update_emp.jsp` performs JDBC `UPDATE`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Update Employee</title></head>
<body>
    <h2>Update Employee Form</h2>
    <form action="update_emp.jsp" method="post">
        Emp No (Matching ID): <input type="text" name="empno"/><br><br>
        New Name: <input type="text" name="ename"/><br><br>
        New Age: <input type="text" name="age"/><br><br>
        New Designation: <input type="text" name="desig"/><br><br>
        New Salary: <input type="text" name="salary"/><br><br>
        <input type="submit" value="Update Record"/>
    </form>
</body>
</html>
```

#### Code: `update_emp.jsp`
```jsp
<%@page import="java.sql.*"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Update Result</title></head>
<body>
    <%
        int empno = Integer.parseInt(request.getParameter("empno"));
        String name = request.getParameter("ename");
        int age = Integer.parseInt(request.getParameter("age"));
        String desig = request.getParameter("desig");
        double sal = Double.parseDouble(request.getParameter("salary"));

        try {
            Class.forName("org.apache.derby.jdbc.ClientDriver");
            Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
            PreparedStatement ps = conn.prepareStatement("UPDATE emp SET ename=?, age=?, designation=?, salary=? WHERE empno=?");
            ps.setString(1, name);
            ps.setInt(2, age);
            ps.setString(3, desig);
            ps.setDouble(4, sal);
            ps.setInt(5, empno);
            int rows = ps.executeUpdate();
            if (rows > 0) out.println("<h2>Record Updated Successfully!</h2>");
            else out.println("<h2>Employee Record Not Found!</h2>");
        } catch(Exception e) {
            out.println("<h2>Error: " + e.getMessage() + "</h2>");
        }
    %>
</body>
</html>
```

---

### Question 35: Develop a JSP application to authenticate a user's login against a database.

#### Steps:
1. Create DB table `users_db(username VARCHAR(50), password VARCHAR(50))`.
2. JSP verifies input via `SELECT * FROM users_db WHERE username=? AND password=?`.

#### Code: `login.jsp`
```jsp
<%@page import="java.sql.*"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>DB Login</title></head>
<body>
    <h2>User Login</h2>
    <form method="post">
        Username: <input type="text" name="u"/><br><br>
        Password: <input type="password" name="p"/><br><br>
        <input type="submit" value="Login"/>
    </form>
    <%
        String u = request.getParameter("u");
        String p = request.getParameter("p");
        if (u != null && p != null) {
            try {
                Class.forName("org.apache.derby.jdbc.ClientDriver");
                Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
                PreparedStatement ps = conn.prepareStatement("SELECT * FROM users_db WHERE username=? AND password=?");
                ps.setString(1, u);
                ps.setString(2, p);
                ResultSet rs = ps.executeQuery();
                if (rs.next()) {
                    out.println("<h3 style='color:green;'>Authentication Successful! Welcome " + u + "</h3>");
                } else {
                    out.println("<h3 style='color:red;'>Invalid Username/Password!</h3>");
                }
            } catch(Exception e) {
                out.println("Error: " + e.getMessage());
            }
        }
    %>
</body>
</html>
```

---

### Question 36: Create a JSP and JDBC gym membership portal that allows users to register and log in using database records.

#### Steps:
1. `gym_register.jsp` inserts new member.
2. `gym_login.jsp` authenticates member details.

#### Code: `gym_register.jsp`
```jsp
<%@page import="java.sql.*"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Gym Register</title></head>
<body>
    <h2>Gym Registration</h2>
    <form method="post">
        Member Name: <input type="text" name="uname"/><br><br>
        Password: <input type="password" name="pass"/><br><br>
        Plan: <input type="text" name="plan"/><br><br>
        <input type="submit" value="Register Member"/>
    </form>
    <%
        String u = request.getParameter("uname");
        if (u != null) {
            try {
                Class.forName("org.apache.derby.jdbc.ClientDriver");
                Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
                PreparedStatement ps = conn.prepareStatement("INSERT INTO gym_members VALUES (?, ?, ?)");
                ps.setString(1, u);
                ps.setString(2, request.getParameter("pass"));
                ps.setString(3, request.getParameter("plan"));
                ps.executeUpdate();
                out.println("Registered successfully! <a href='gym_login.jsp'>Login here</a>");
            } catch(Exception e) { out.println("Error: " + e.getMessage()); }
        }
    %>
</body>
</html>
```

#### Code: `gym_login.jsp`
```jsp
<%@page import="java.sql.*"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Gym Login</title></head>
<body>
    <h2>Gym Member Login</h2>
    <form method="post">
        Name: <input type="text" name="uname"/><br><br>
        Password: <input type="password" name="pass"/><br><br>
        <input type="submit" value="Login"/>
    </form>
    <%
        String u = request.getParameter("uname");
        if (u != null) {
            try {
                Class.forName("org.apache.derby.jdbc.ClientDriver");
                Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
                PreparedStatement ps = conn.prepareStatement("SELECT * FROM gym_members WHERE uname=? AND pass=?");
                ps.setString(1, u);
                ps.setString(2, request.getParameter("pass"));
                ResultSet rs = ps.executeQuery();
                if (rs.next()) out.println("<h3>Welcome Gym Member " + u + "!</h3>");
                else out.println("<h3>Invalid credentials!</h3>");
            } catch(Exception e) { out.println("Error: " + e.getMessage()); }
        }
    %>
</body>
</html>
```

---

### Question 37: Create a JSP-based event registration form that accepts name, email, age and contact number, validates the input and displays the submitted details.

#### Steps:
1. `event_reg.jsp` contains HTML form and validation logic.

#### Code: `event_reg.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Event Reg</title></head>
<body>
    <h2>Event Registration</h2>
    <form method="post">
        Name: <input type="text" name="name"/><br><br>
        Email: <input type="text" name="email"/><br><br>
        Age: <input type="text" name="age"/><br><br>
        Contact: <input type="text" name="contact"/><br><br>
        <input type="submit" value="Register Event"/>
    </form>
    <%
        String name = request.getParameter("name");
        if(name != null) {
            String email = request.getParameter("email");
            int age = Integer.parseInt(request.getParameter("age"));
            String contact = request.getParameter("contact");
            if (age >= 18 && email.contains("@")) {
                out.println("<h3>Registration Successful!</h3>");
                out.println("<p>Name: " + name + "<br>Email: " + email + "<br>Age: " + age + "<br>Contact: " + contact + "</p>");
            } else {
                out.println("<h3 style='color:red;'>Invalid Input! Must be 18+ and valid email.</h3>");
            }
        }
    %>
</body>
</html>
```

---

### Question 38: Create a registration and login JSP application to register users and authenticate their credentials.

#### Steps:
1. Combine user registration and login in JSP pages using JDBC connectivity.

#### Code: `app_reg_login.jsp`
```jsp
<%@page import="java.sql.*"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Register & Login</title></head>
<body>
    <h2>Register</h2>
    <form method="post" action="app_reg_login.jsp?action=register">
        Username: <input type="text" name="u"/><br><br>
        Password: <input type="password" name="p"/><br><br>
        <input type="submit" value="Register"/>
    </form>
    <hr>
    <h2>Login</h2>
    <form method="post" action="app_reg_login.jsp?action=login">
        Username: <input type="text" name="u"/><br><br>
        Password: <input type="password" name="p"/><br><br>
        <input type="submit" value="Login"/>
    </form>

    <%
        String action = request.getParameter("action");
        if ("register".equals(action)) {
            try {
                Class.forName("org.apache.derby.jdbc.ClientDriver");
                Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
                PreparedStatement ps = conn.prepareStatement("INSERT INTO users_db VALUES (?, ?)");
                ps.setString(1, request.getParameter("u"));
                ps.setString(2, request.getParameter("p"));
                ps.executeUpdate();
                out.println("<p style='color:green;'>User Registered!</p>");
            } catch(Exception e){ out.println("Error: " + e.getMessage()); }
        } else if ("login".equals(action)) {
            try {
                Class.forName("org.apache.derby.jdbc.ClientDriver");
                Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
                PreparedStatement ps = conn.prepareStatement("SELECT * FROM users_db WHERE username=? AND password=?");
                ps.setString(1, request.getParameter("u"));
                ps.setString(2, request.getParameter("p"));
                ResultSet rs = ps.executeQuery();
                if(rs.next()) out.println("<p style='color:green;'>Logged In!</p>");
                else out.println("<p style='color:red;'>Invalid credentials.</p>");
            } catch(Exception e){ out.println("Error: " + e.getMessage()); }
        }
    %>
</body>
</html>
```

---

### Question 39: Develop a five-page web application using any two or three Java EE technologies.

#### Steps:
1. Page 1: `page1.html` (Landing)
2. Page 2: `page2.jsp` (Session store)
3. Page 3: `Servlet3.java` (Business Logic)
4. Page 4: `page4.jsp` (View EL display)
5. Page 5: `page5.jsp` (Summary)

#### Code: `page1.html`
```html
<!DOCTYPE html>
<html>
<body>
    <h2>Page 1: Multi-page App Portal</h2>
    <a href="page2.jsp">Go to Page 2 (Session Input)</a>
</body>
</html>
```

#### Code: `page2.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<body>
    <h2>Page 2: Enter Details</h2>
    <form action="Servlet3" method="post">
        User: <input type="text" name="u"/>
        <input type="submit" value="Next -> Page 3 Servlet"/>
    </form>
</body>
</html>
```

#### Code: `Servlet3.java`
```java
package EJ;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/Servlet3")
public class Servlet3 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String u = request.getParameter("u");
        request.getSession().setAttribute("appUser", u);
        response.sendRedirect("page4.jsp");
    }
}
```

#### Code: `page4.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<body>
    <h2>Page 4: EL Session Display</h2>
    <p>User from Session: ${sessionScope.appUser}</p>
    <a href="page5.jsp">Go to Page 5 Summary</a>
</body>
</html>
```

#### Code: `page5.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<body>
    <h2>Page 5: Application Summary</h2>
    <p>Thank you for navigating through the 5-page Java EE workflow, ${sessionScope.appUser}!</p>
</body>
</html>
```

---

### Question 40: Develop a Servlet application for a bakery that accepts cake quantity and price and calculates the total bill.

#### Steps:
1. Accept cake quantity and unit price in `index.html`.
2. Compute total in `BakeryServlet.java`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Bakery App</title></head>
<body>
    <h2>Bakery Bill Calculator</h2>
    <form action="BakeryServlet" method="post">
        Cake Quantity: <input type="number" name="qty"/><br><br>
        Price per Cake: <input type="text" name="price"/><br><br>
        <input type="submit" value="Calculate Total"/>
    </form>
</body>
</html>
```

#### Code: `BakeryServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/BakeryServlet")
public class BakeryServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        int qty = Integer.parseInt(request.getParameter("qty"));
        double price = Double.parseDouble(request.getParameter("price"));
        double total = qty * price;

        out.println("<h2>Bakery Invoice</h2>");
        out.println("<p>Total Bill Amount: $" + total + "</p>");
    }
}
```

---

### Question 41: Create a Servlet application for a mobile shop that accepts mobile price and discount percentage and calculates the final price.

#### Steps:
1. Pass mobile price and discount rate to `MobileShopServlet.java`.
2. Compute `finalPrice = price - (price * discount / 100)`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Mobile Shop</title></head>
<body>
    <h2>Mobile Shop Discount Calculator</h2>
    <form action="MobileShopServlet" method="post">
        Mobile Price: <input type="text" name="price"/><br><br>
        Discount (%): <input type="text" name="discount"/><br><br>
        <input type="submit" value="Calculate Final Price"/>
    </form>
</body>
</html>
```

#### Code: `MobileShopServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/MobileShopServlet")
public class MobileShopServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        double price = Double.parseDouble(request.getParameter("price"));
        double discount = Double.parseDouble(request.getParameter("discount"));
        double finalPrice = price - (price * discount / 100.0);

        out.println("<h2>Mobile Shop Bill</h2>");
        out.println("<p>Original Price: $" + price + "</p>");
        out.println("<p>Discount: " + discount + "%</p>");
        out.println("<h3>Final Price: $" + finalPrice + "</h3>");
    }
}
```

---

### Question 42: Develop a Servlet application for a hostel registration portal that stores student details in a session and displays them on a confirmation page.

#### Steps:
1. Submit hostel details via `index.html`.
2. Save to `HttpSession` in `HostelServlet.java` and redirect to `hostel_confirm.jsp`.

#### Code: `index.html`
```html
<!DOCTYPE html>
<html>
<head><title>Hostel Portal</title></head>
<body>
    <h2>Hostel Room Registration</h2>
    <form action="HostelServlet" method="post">
        Student Name: <input type="text" name="sname"/><br><br>
        Room Type: <input type="text" name="roomType"/><br><br>
        <input type="submit" value="Register Hostel"/>
    </form>
</body>
</html>
```

#### Code: `HostelServlet.java`
```java
package EJ;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/HostelServlet")
public class HostelServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        HttpSession session = request.getSession();
        session.setAttribute("sname", request.getParameter("sname"));
        session.setAttribute("roomType", request.getParameter("roomType"));
        response.sendRedirect("hostel_confirm.jsp");
    }
}
```

#### Code: `hostel_confirm.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Confirmation</title></head>
<body>
    <h2>Hostel Registration Confirmation</h2>
    <p>Student Name: ${sessionScope.sname}</p>
    <p>Assigned Room Type: ${sessionScope.roomType}</p>
</body>
</html>
```

---

### Question 43: Create a JSP application using EL to display flight booking details, including passenger name, flight number, destination and ticket price.

#### Steps:
1. `flight_input.jsp` sends parameters.
2. `flight_display.jsp` reads via `${param.xxx}` EL expressions.

#### Code: `flight_input.jsp`
```html
<!DOCTYPE html>
<html>
<body>
    <form action="flight_display.jsp" method="post">
        Passenger: <input type="text" name="pname"/><br><br>
        Flight No: <input type="text" name="fnum"/><br><br>
        Destination: <input type="text" name="dest"/><br><br>
        Price: <input type="text" name="price"/><br><br>
        <input type="submit" value="Book Flight"/>
    </form>
</body>
</html>
```

#### Code: `flight_display.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head><title>Flight Details</title></head>
<body>
    <h2>Flight Ticket Booking Details</h2>
    <p><b>Passenger Name:</b> ${param.pname}</p>
    <p><b>Flight Number:</b> ${param.fnum}</p>
    <p><b>Destination:</b> ${param.dest}</p>
    <p><b>Ticket Price:</b> $${param.price}</p>
</body>
</html>
```

---

### Question 44: Create a JSP application using EL to display student examination details, including roll number, name, subject and marks.

#### Steps:
1. `exam_input.jsp` sends exam data.
2. `exam_display.jsp` displays data via Expression Language.

#### Code: `exam_input.jsp`
```html
<!DOCTYPE html>
<html>
<body>
    <form action="exam_display.jsp" method="post">
        Roll No: <input type="text" name="roll"/><br><br>
        Name: <input type="text" name="name"/><br><br>
        Subject: <input type="text" name="subject"/><br><br>
        Marks: <input type="text" name="marks"/><br><br>
        <input type="submit" value="Submit Marks"/>
    </form>
</body>
</html>
```

#### Code: `exam_display.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<body>
    <h2>Student Examination Details</h2>
    <p>Roll No: ${param.roll}</p>
    <p>Name: ${param.name}</p>
    <p>Subject: ${param.subject}</p>
    <p>Marks: ${param.marks}</p>
</body>
</html>
```

---

### Question 45: Create a JSP application using EL to display employee details received from another page, including employee ID, name, department and designation.

#### Steps:
1. `emp_input.html` sends form data.
2. `emp_display.jsp` accesses attributes via `${param.id}`, etc.

#### Code: `emp_input.html`
```html
<!DOCTYPE html>
<html>
<body>
    <form action="emp_display.jsp" method="post">
        Emp ID: <input type="text" name="id"/><br><br>
        Name: <input type="text" name="name"/><br><br>
        Department: <input type="text" name="dept"/><br><br>
        Designation: <input type="text" name="desig"/><br><br>
        <input type="submit" value="Display Employee"/>
    </form>
</body>
</html>
```

#### Code: `emp_display.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<body>
    <h2>Employee Profile Details</h2>
    <p>Employee ID: ${param.id}</p>
    <p>Name: ${param.name}</p>
    <p>Department: ${param.dept}</p>
    <p>Designation: ${param.desig}</p>
</body>
</html>
```

---

### Question 46: Create a JSP application using EL to display product details received from another page, including product ID, name, quantity and price.

#### Steps:
1. Create input page `prod_input.html`.
2. Access values in `prod_display.jsp` using EL.

#### Code: `prod_input.html`
```html
<!DOCTYPE html>
<html>
<body>
    <form action="prod_display.jsp" method="post">
        Product ID: <input type="text" name="id"/><br><br>
        Name: <input type="text" name="name"/><br><br>
        Quantity: <input type="text" name="qty"/><br><br>
        Price: <input type="text" name="price"/><br><br>
        <input type="submit" value="View Product"/>
    </form>
</body>
</html>
```

#### Code: `prod_display.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<body>
    <h2>Product Summary</h2>
    <p>ID: ${param.id}</p>
    <p>Name: ${param.name}</p>
    <p>Quantity: ${param.qty}</p>
    <p>Price: $${param.price}</p>
</body>
</html>
```

---

### Question 47: Create a JSP application using JSTL to display a list of available books with titles, authors and prices.

#### Steps:
1. Import `jstl-1.2.jar` library into NetBeans project.
2. Use `<c:forEach>` loop over book list mock array/collection.

#### Code: `books_jstl.jsp`
```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head><title>JSTL Books</title></head>
<body>
    <h2>Available Books List</h2>
    <%
        java.util.List<String[]> books = new java.util.ArrayList<>();
        books.add(new String[]{"Java EE 7 Complete", "Oracle Press", "$45.00"});
        books.add(new String[]{"Servlet & JSP Programming", "Murach", "$39.50"});
        books.add(new String[]{"EJB 3 in Action", "Manning", "$52.10"});
        request.setAttribute("booksList", books);
    %>
    <table border="1" cellpadding="8">
        <tr><th>Title</th><th>Author</th><th>Price</th></tr>
        <c:forEach var="book" items="${booksList}">
            <tr>
                <td><c:out value="${book[0]}"/></td>
                <td><c:out value="${book[1]}"/></td>
                <td><c:out value="${book[2]}"/></td>
            </tr>
        </c:forEach>
    </table>
</body>
</html>
```

---

# SECTION 2: EJB & JPA Applications (30 Questions)

## Standard Execution Steps for EJB & JPA in NetBeans 8.0.2

1. **EJB / JPA Project Creation:**
   - Go to `File` > `New Project` > `Java Web` > `Web Application`.
   - Select **GlassFish Server 4.1**, Java EE Version: **Java EE 7 Web**.
2. **Persistence Unit (JPA):**
   - Right-click project > `New` > `Persistence Unit...`.
   - Name: `EJ_PU`, Persistence Provider: `EclipseLink (JPA 2.1)`.
   - Data Source: Select `jdbc/sample` (Derby Default). Click `Finish`.
3. **Session Beans / Entities:**
   - Right-click project > `New` > `Session Bean...` / `Entity Class...`.

---

## Q.2 — Unique Questions (A. EJB Applications)

---

### Question 1: Create a currency converter using EJB to convert Indian Rupees into US Dollars, Euros and British Pounds.

#### Code: `CurrencyConverterBean.java`
```java
package EJ;

import javax.ejb.Stateless;

@Stateless
public class CurrencyConverterBean {
    public double toUSD(double inr) { return inr / 83.0; }
    public double toEUR(double inr) { return inr / 90.0; }
    public double toGBP(double inr) { return inr / 105.0; }
}
```

#### Code: `ConverterServlet.java`
```java
package EJ;

import java.io.IOException;
import java.io.PrintWriter;
import javax.ejb.EJB;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/ConverterServlet")
public class ConverterServlet extends HttpServlet {
    @EJB
    private CurrencyConverterBean converter;

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        double inr = 1000.0;
        out.println("<h2>Currency Conversion for ₹" + inr + "</h2>");
        out.println("<p>USD: $" + String.format("%.2f", converter.toUSD(inr)) + "</p>");
        out.println("<p>EUR: €" + String.format("%.2f", converter.toEUR(inr)) + "</p>");
        out.println("<p>GBP: £" + String.format("%.2f", converter.toGBP(inr)) + "</p>");
    }
}
```

---

### Question 2: Develop a visitor hit-count application using a Singleton Session Bean.

#### Code: `HitCounterBean.java`
```java
package EJ;

import javax.ejb.Singleton;

@Singleton
public class HitCounterBean {
    private int hitCount = 0;

    public synchronized int incrementAndGet() {
        return ++hitCount;
    }
}
```

#### Code: `HitCounterServlet.java`
```java
package EJ;

import java.io.IOException;
import javax.ejb.EJB;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/HitCounterServlet")
public class HitCounterServlet extends HttpServlet {
    @EJB
    private HitCounterBean counter;

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        int hits = counter.incrementAndGet();
        response.getWriter().println("<h2>Singleton Visitor Count: " + hits + "</h2>");
    }
}
```

---

### Question 3: Develop a visitor statistics application using a Message-Driven Bean and Stateless Session Bean.

#### Code: `StatsStatelessBean.java`
```java
package EJ;

import javax.ejb.Stateless;

@Stateless
public class StatsStatelessBean {
    public String getStats(int visitorId) {
        return "Visitor #" + visitorId + " logged into statistics engine.";
    }
}
```

#### Code: `VisitorMDB.java`
```java
package EJ;

import javax.ejb.ActivationConfigProperty;
import javax.ejb.MessageDriven;
import javax.jms.*;

@MessageDriven(activationConfig = {
    @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Queue")
})
public class VisitorMDB implements MessageListener {
    @Override
    public void onMessage(Message message) {
        try {
            if (message instanceof TextMessage) {
                System.out.println("MDB Processed Message: " + ((TextMessage) message).getText());
            }
        } catch (JMSException e) { e.printStackTrace(); }
    }
}
```

---

### Question 4: Develop a Marks Entry application using EJB to accept student details and marks, store them in a database, and retrieve and display the records.

#### Code: `StudentMarksBean.java`
```java
package EJ;

import java.sql.*;
import javax.ejb.Stateless;

@Stateless
public class StudentMarksBean {
    public boolean insertMarks(int roll, String name, int marks) {
        try {
            Class.forName("org.apache.derby.jdbc.ClientDriver");
            Connection conn = DriverManager.getConnection("jdbc:derby://localhost:1527/sample", "app", "app");
            PreparedStatement ps = conn.prepareStatement("INSERT INTO student_marks VALUES (?, ?, ?)");
            ps.setInt(1, roll);
            ps.setString(2, name);
            ps.setInt(3, marks);
            return ps.executeUpdate() > 0;
        } catch (Exception e) { return false; }
    }
}
```

---

### Question 5: Develop a room reservation system using EJB.

#### Code: `RoomReservationBean.java`
```java
package EJ;

import javax.ejb.Stateful;

@Stateful
public class RoomReservationBean {
    private String roomType;
    private boolean reserved = false;

    public void selectRoom(String type) { this.roomType = type; }
    public String confirm() {
        this.reserved = true;
        return "Room " + roomType + " Reserved Successfully!";
    }
}
```

---

### Question 6: Develop a shopping cart application using a Stateful Session Bean.

#### Code: `ShoppingCartBean.java`
```java
package EJ;

import java.util.ArrayList;
import java.util.List;
import javax.ejb.Stateful;

@Stateful
public class ShoppingCartBean {
    private List<String> cart = new ArrayList<>();

    public void addItem(String item) { cart.add(item); }
    public List<String> getItems() { return cart; }
}
```

---

### Question 7: Develop an electricity bill calculator using EJB that calculates the bill from units consumed.

#### Code: `ElectricityBean.java`
```java
package EJ;

import javax.ejb.Stateless;

@Stateless
public class ElectricityBean {
    public double calculateBill(int units) {
        if (units <= 100) return units * 2.5;
        return (100 * 2.5) + ((units - 100) * 4.5);
    }
}
```

---

### Question 8: Develop a temperature converter using EJB to convert Celsius to Fahrenheit.

#### Code: `TempConverterBean.java`
```java
package EJ;

import javax.ejb.Stateless;

@Stateless
public class TempConverterBean {
    public double celsiusToFahrenheit(double c) {
        return (c * 9 / 5) + 32;
    }
}
```

---

### Question 9: Develop an EJB-based hotel room reservation application that accepts guest details, room type, checkin date and number of nights.

#### Code: `HotelBookingBean.java`
```java
package EJ;

import javax.ejb.Stateless;

@Stateless
public class HotelBookingBean {
    public double calculateTotal(String roomType, int nights) {
        double rate = "Deluxe".equalsIgnoreCase(roomType) ? 150.0 : 80.0;
        return rate * nights;
    }
}
```

---

### Question 10: Develop an EJB-based shopping bill calculator that accepts product price and quantity and calculates the total amount.

#### Code: `BillCalculatorBean.java`
```java
package EJ;

import javax.ejb.Stateless;

@Stateless
public class BillCalculatorBean {
    public double calculateTotal(double price, int qty) {
        return price * qty;
    }
}
```

---

### Question 11: Develop an EJB-based salary calculator that accepts basic salary and allowances and calculates gross salary.

#### Code: `SalaryBean.java`
```java
package EJ;

import javax.ejb.Stateless;

@Stateless
public class SalaryBean {
    public double computeGross(double basic, double allowances) {
        return basic + allowances;
    }
}
```

---

### Question 12: Create a Stateful Session Bean for a restaurant ordering system that allows customers to add and remove food items and calculate the total bill.

#### Code: `RestaurantOrderBean.java`
```java
package EJ;

import java.util.*;
import javax.ejb.Stateful;

@Stateful
public class RestaurantOrderBean {
    private Map<String, Double> orderMap = new HashMap<>();

    public void addItem(String name, double price) { orderMap.put(name, price); }
    public void removeItem(String name) { orderMap.remove(name); }
    public double getTotal() {
        double total = 0;
        for (double p : orderMap.values()) total += p;
        return total;
    }
}
```

---

## B. JPA Applications

---

### Question 13: Create a JPA application to store and retrieve book details.

#### Code: `Book.java`
```java
package EJ;

import java.io.Serializable;
import javax.persistence.*;

@Entity
public class Book implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String title;
    private double price;

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }
}
```

---

### Question 14: Create a JPA inventory application.

#### Code: `InventoryItem.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class InventoryItem {
    @Id
    private int itemId;
    private String itemName;
    private int quantity;

    public int getItemId() { return itemId; }
    public void setItemId(int itemId) { this.itemId = itemId; }
    public String getItemName() { return itemName; }
    public void setItemName(String itemName) { this.itemName = itemName; }
    public int getQuantity() { return quantity; }
    public void setQuantity(int quantity) { this.quantity = quantity; }
}
```

---

### Question 15: Create a JPA guestbook application to store and retrieve visitor details and comments.

#### Code: `Guestbook.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class Guestbook {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String visitorName;
    private String comment;

    public Long getId() { return id; }
    public String getVisitorName() { return visitorName; }
    public void setVisitorName(String visitorName) { this.visitorName = visitorName; }
    public String getComment() { return comment; }
    public void setComment(String comment) { this.comment = comment; }
}
```

---

### Question 16: Create a JPA application demonstrating ORM associations.

#### Code: `Department.java`
```java
package EJ;

import java.util.List;
import javax.persistence.*;

@Entity
public class Department {
    @Id
    private int id;
    private String name;

    @OneToMany(mappedBy = "department")
    private List<EmployeeRecord> employees;

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

---

### Question 17: Create a JPA application to store employee names and salaries.

#### Code: `EmployeeRecord.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class EmployeeRecord {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String empName;
    private double salary;

    @ManyToOne
    private Department department;

    public int getId() { return id; }
    public String getEmpName() { return empName; }
    public void setEmpName(String empName) { this.empName = empName; }
    public double getSalary() { return salary; }
    public void setSalary(double salary) { this.salary = salary; }
}
```

---

### Question 18: Create a JPA application to store course details and demonstrate ORM mapping.

#### Code: `Course.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class Course {
    @Id
    private String courseCode;
    private String courseName;

    public String getCourseCode() { return courseCode; }
    public void setCourseCode(String courseCode) { this.courseCode = courseCode; }
    public String getCourseName() { return courseName; }
    public void setCourseName(String courseName) { this.courseName = courseName; }
}
```

---

### Question 19: Create a JPA application to store product names and quantities.

#### Code: `Product.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String productName;
    private int quantity;

    public Long getId() { return id; }
    public String getProductName() { return productName; }
    public void setProductName(String name) { this.productName = name; }
    public int getQuantity() { return quantity; }
    public void setQuantity(int quantity) { this.quantity = quantity; }
}
```

---

### Question 20: Create a JPA application demonstrating One-to-One mapping.

#### Code: `Passport.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class Passport {
    @Id
    private String passportNumber;
    
    @OneToOne(mappedBy = "passport")
    private Person person;

    public String getPassportNumber() { return passportNumber; }
    public void setPassportNumber(String passportNumber) { this.passportNumber = passportNumber; }
}
```

#### Code: `Person.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class Person {
    @Id
    private int id;
    private String name;

    @OneToOne
    @JoinColumn(name = "passport_id")
    private Passport passport;

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

---

### Question 21: Create a JPA application to store student names and roll numbers.

#### Code: `StudentEntity.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class StudentEntity {
    @Id
    private int rollNumber;
    private String studentName;

    public int getRollNumber() { return rollNumber; }
    public void setRollNumber(int roll) { this.rollNumber = roll; }
    public String getStudentName() { return studentName; }
    public void setStudentName(String name) { this.studentName = name; }
}
```

---

### Question 22: Create a JPA application to store student names and marks.

#### Code: `StudentMarksEntity.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class StudentMarksEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String studentName;
    private double marks;

    public Long getId() { return id; }
    public String getStudentName() { return studentName; }
    public void setStudentName(String name) { this.studentName = name; }
    public double getMarks() { return marks; }
    public void setMarks(double marks) { this.marks = marks; }
}
```

---

### Question 23: Create a JPA application to store book names and prices.

#### Code: `BookStoreEntity.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class BookStoreEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int bookId;
    private String bookName;
    private double price;

    public int getBookId() { return bookId; }
    public String getBookName() { return bookName; }
    public void setBookName(String bookName) { this.bookName = bookName; }
    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }
}
```

---

### Question 24: Create a JPA-based student management system to insert, display and update student details.

#### Code: `StudentDAO.java`
```java
package EJ;

import javax.persistence.*;

public class StudentDAO {
    private EntityManagerFactory emf = Persistence.createEntityManagerFactory("EJ_PU");

    public void insertStudent(StudentEntity s) {
        EntityManager em = emf.createEntityManager();
        em.getTransaction().begin();
        em.persist(s);
        em.getTransaction().commit();
        em.close();
    }

    public StudentEntity findStudent(int roll) {
        EntityManager em = emf.createEntityManager();
        StudentEntity s = em.find(StudentEntity.class, roll);
        em.close();
        return s;
    }

    public void updateStudentName(int roll, String newName) {
        EntityManager em = emf.createEntityManager();
        em.getTransaction().begin();
        StudentEntity s = em.find(StudentEntity.class, roll);
        if (s != null) s.setStudentName(newName);
        em.getTransaction().commit();
        em.close();
    }
}
```

---

### Question 25: Create a JPA-based employee management system to insert, display and update employee details.

#### Code: `EmployeeDAO.java`
```java
package EJ;

import javax.persistence.*;

public class EmployeeDAO {
    private EntityManagerFactory emf = Persistence.createEntityManagerFactory("EJ_PU");

    public void create(EmployeeRecord e) {
        EntityManager em = emf.createEntityManager();
        em.getTransaction().begin();
        em.persist(e);
        em.getTransaction().commit();
        em.close();
    }

    public void updateSalary(int empId, double newSal) {
        EntityManager em = emf.createEntityManager();
        em.getTransaction().begin();
        EmployeeRecord e = em.find(EmployeeRecord.class, empId);
        if (e != null) e.setSalary(newSal);
        em.getTransaction().commit();
        em.close();
    }
}
```

---

### Question 26: Create a JPA-based grocery store application to insert and display product ID, name, quantity and price.

#### Code: `GroceryProduct.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class GroceryProduct {
    @Id
    private int productId;
    private String name;
    private int quantity;
    private double price;

    public int getProductId() { return productId; }
    public void setProductId(int id) { this.productId = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getQuantity() { return quantity; }
    public void setQuantity(int qty) { this.quantity = qty; }
    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }
}
```

---

### Question 27: Create a JPA-based student result system to store student roll number, name, subject and marks.

#### Code: `StudentResult.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class StudentResult {
    @Id
    private int rollNumber;
    private String name;
    private String subject;
    private double marks;

    public int getRollNumber() { return rollNumber; }
    public void setRollNumber(int roll) { this.rollNumber = roll; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getSubject() { return subject; }
    public void setSubject(String subject) { this.subject = subject; }
    public double getMarks() { return marks; }
    public void setMarks(double marks) { this.marks = marks; }
}
```

---

### Question 28: Create a JPA-based mobile shop application to insert, display and update mobile phone details.

#### Code: `MobilePhone.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class MobilePhone {
    @Id
    private int modelId;
    private String modelName;
    private double price;

    public int getModelId() { return modelId; }
    public void setModelId(int id) { this.modelId = id; }
    public String getModelName() { return modelName; }
    public void setModelName(String name) { this.modelName = name; }
    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }
}
```

---

### Question 29: Create a JPA-based vehicle showroom application to insert and display vehicle ID, model name and price.

#### Code: `Vehicle.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class Vehicle {
    @Id
    private int vehicleId;
    private String modelName;
    private double price;

    public int getVehicleId() { return vehicleId; }
    public void setVehicleId(int vehicleId) { this.vehicleId = vehicleId; }
    public String getModelName() { return modelName; }
    public void setModelName(String modelName) { this.modelName = modelName; }
    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }
}
```

---

### Question 30: Create a JPA-based bookshop application to insert and display book details.

#### Code: `BookshopEntity.java`
```java
package EJ;

import javax.persistence.*;

@Entity
public class BookshopEntity {
    @Id
    private int bookId;
    private String title;
    private String author;
    private double price;

    public int getBookId() { return bookId; }
    public void setBookId(int bookId) { this.bookId = bookId; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public String getAuthor() { return author; }
    public void setAuthor(String author) { this.author = author; }
    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }
}
```