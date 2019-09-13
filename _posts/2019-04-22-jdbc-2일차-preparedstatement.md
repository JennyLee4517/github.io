---
title: "JDBC 2일차 : Prepared Statement"
date: 2019-04-22 18:00:00 -0400
categories: JDBC
comments: true


---

### Prepared Statement

- Statement 는 sql문을 완벽하게 만든 다음 execute할 때 실행한다.(execute에 파라미터로 sql문이 들어감)
- PreparedStatement는 Statement 생성 시 sql문을 미리 검사, 이 때 값이 들어갈 곳을 물음표(?)로 만들어도 된다. => 이후 ? 자리에 값을 바인딩 해준 후 최종적으로 execute한다.
- 두 statement의 차이 : 일반 statement는 execute할 때 sql 문 검사 후 실행하나 preparedStatement는 statement 생성 시 sql문을 검사하고 execute에서 실행한다. 이 때 execute의 파라미터로 sql문을 넣어선 안된다.

```java
package test.day0422;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Scanner;

import com.sun.corba.se.spi.orbutil.fsm.Guard.Result;

public class PrepareJdbcEx1 {

  Scanner sc = null;
  String url="jdbc:oracle:thin:@192.168.0.49:1521:xe";
  String driver ="oracle.jdbc.driver.OracleDriver";
  String user = "acorn03";
  String password="a1234";

  public PrepareJdbcEx1() {
    sc = new Scanner(System.in);

    try {
      Class.forName(driver);
    } catch (ClassNotFoundException e) {
      System.out.println("드라이버 연결 실패" + e.getMessage());
    }
  }

  //커넥션 메서드
  public Connection getConnection() {
    Connection conn = null;
    try {
      conn = DriverManager.getConnection(url, user, password);
    } catch (SQLException e) {
      System.out.println("커넥션 실패" + e.getMessage());
    }
    return conn;
  }

  //클로즈 메서드
  public void dbClose(Connection conn, PreparedStatement pstmt) {
    try {
      if(pstmt != null) pstmt.close();
      if(conn!= null) conn.close();
    }catch(SQLException e){

    }
  }

  //오버로딩 해준다 
  public void dbClose(Connection conn, PreparedStatement pstmt, ResultSet rs) {
    try {
      if(rs != null) rs.close();
      if(pstmt != null) pstmt.close();
      if(conn!= null) conn.close();
    }catch(SQLException e){

    }
  }

  public void dbClose(Connection conn, Statement stmt, ResultSet rs) {
    try {
      if(rs != null) rs.close();
      if(stmt != null) stmt.close();
      if(conn!= null) conn.close();
    }catch(SQLException e){

    }
  }


  public int getMenu() {

    System.out.println();
    System.out.println("********* 메뉴 *********");
    System.out.println("1. 추가 2. 삭제 3. 수정 4. 전체출력 \n5. 성별출력 6. 부서별출력 7. 이름검색 8. 종료");
    String ans = sc.nextLine();
    int n = Integer.parseInt(ans);

    return n;
  }

  //추가 메서드
  public void insert() {
    System.out.println("이름을 입력하세요.");
    String name = sc.nextLine();
    System.out.println("성별을 입력하세요.(m or f)");
    String gender = sc.nextLine();
    System.out.println("부서를 입력하세요. (dev / edu / hr)");
    String buseo = sc.nextLine();
    System.out.println("급여를 입력하세요.");
    int pay = Integer.parseInt(sc.nextLine());
    System.out.println("입사일을 입력하세요. yyyy-mm-dd형태로 입력");
    String ipsaday = sc.nextLine();

    Connection conn = null;
    PreparedStatement pstmt = null;
    String sql = "insert into sawonjh values (seq2jh.nextval, ?,?,?,?,?)";

    //1.connection
    conn = getConnection();

    try {

      //2. sql 검사 - prepared Statement생성
      pstmt = conn.prepareStatement(sql);

      //3. ?자리에 바인딩 하기 
      pstmt.setString(1, name);
      pstmt.setString(2, gender);
      pstmt.setString(3, buseo);
      pstmt.setInt(4, pay);
      pstmt.setString(5, ipsaday);

      //4. execute - 파라미터에 sql문을 주지 않는다는것이 특징임.
      pstmt.execute();

    } catch (SQLException e) {
      System.out.println("insert method 오류" +e.getMessage());
    }finally {
      dbClose(conn, pstmt);
    }


  }

  //삭제 메서드
  public void delete() {

    System.out.println("삭제할 데이타의 이름을 입력하세요.");
    String name = sc.nextLine();
    System.out.println("어떤 부서의 " +name+ "을 삭제하시겠습니까? (dev/edu/hr)");
    String buseo = sc.nextLine();

    Connection conn = null;
    PreparedStatement pstmt = null;
    String sql = "delete from sawonjh where name=? and buseo =?";
    conn = getConnection();

    try {
      pstmt = conn.prepareStatement(sql);

      pstmt.setString(1, name);
      pstmt.setString(2, buseo);

      int n = pstmt.executeUpdate();
      if(n == 0) {
        System.out.println("해당 이름&부서와 일치하는 데이타가 없습니다.");
      }else {
        System.out.println(name + "이(가) " +n + "개 삭제되었습니다.");
      }

    } catch (SQLException e) {
      System.out.println("데이타 삭제 오류" + e.getMessage());
    }finally {
      dbClose(conn, pstmt);
    }

  }

  //수정 메서드
  public void update() {

    System.out.println("수정할 사원의 시퀀스 번호를 입력하세요.");
    int	n = Integer.parseInt(sc.nextLine());
    System.out.println("수정할 이름을 입력하세요.");
    String name = sc.nextLine();
    System.out.println("수정할 성별을 입력하세요.");
    String gender = sc.nextLine();
    System.out.println("수정할 부서를 입력하세요.");
    String buseo = sc.nextLine();
    System.out.println("수정할 급여를 입력하세요.");
    int pay = Integer.parseInt(sc.nextLine());

    String sql = "update sawonjh set name=?, gender=?, buseo=?, pay=? where num=?";
    Connection conn = null;
    PreparedStatement pstmt = null;
    conn = getConnection();

    try {
      pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, name);
      pstmt.setString(2, gender);
      pstmt.setString(3, buseo);
      pstmt.setInt(4, pay);
      pstmt.setInt(5, n);

      int a = pstmt.executeUpdate();
      if(a==0)
        System.out.println("해당하는 시퀀스 번호가 없습니다.");
      else 
        System.out.println(a + "개의 데이타가 업데이트 되었습니다.");
    } catch (SQLException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }finally {
      dbClose(conn, pstmt);
    }


  }

  public void showTitle() {
    System.out.println("==================================================");
    System.out.println("번호\t이름\t성별\t부서\t급여\t입사일");
    System.out.println("--------------------------------------------------");

  }

  //전체출력 메서드
  public void writeAll() {

    Connection conn = null;
    Statement stmt = null;
    ResultSet rs = null;
    String sql = "select * from sawonjh order by num";

    conn = getConnection();

    try {
      stmt = conn.createStatement();
      rs = stmt.executeQuery(sql);

      showTitle();

      while(rs.next()) {
        System.out.println(rs.getInt("num") +"\t"+ rs.getString("name") +"\t"+ rs.getString("gender")
                           +"\t"+ rs.getString("buseo") +"\t"+ rs.getInt("pay") +"\t"+ rs.getDate("ipsaday"));
      }

    } catch (SQLException e) {
      System.out.println("writeAll error " + e.getMessage());

    }finally {
      dbClose(conn, stmt, rs);
    }



  }

  //성별출력 메서드
  public void writeGender() {

    System.out.println("출력할 성별을 입력하세요. (m or f)");
    String gender = sc.nextLine();

    Connection conn =null;
    PreparedStatement pstmt = null;
    ResultSet rs = null;
    conn = getConnection();

    String sql = "select * from sawonjh where gender=?";

    try {
      pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, gender);

      rs = pstmt.executeQuery();

      showTitle();

      while(rs.next()) {
        System.out.println(rs.getInt("num") +"\t"+ rs.getString("name") +"\t"+ rs.getString("gender")
                           +"\t"+ rs.getString("buseo") +"\t"+ rs.getInt("pay") +"\t"+ rs.getDate("ipsaday"));
      }

    } catch (SQLException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }finally {
      dbClose(conn, pstmt, rs);
    }



  }

  //부서별출력 메서드
  public void writeBuseo() {

    System.out.println("출력할 부서를 입력하세요. (dev / edu / hr)");
    String buseo = sc.nextLine();

    Connection conn = null;
    PreparedStatement pstmt = null;
    ResultSet rs = null;
    conn = getConnection();
    String sql = "select * from sawonjh where buseo=?";

    try {
      pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, buseo);

      rs = pstmt.executeQuery();

      showTitle();

      while(rs.next()) {
        System.out.println(rs.getInt("num") +"\t"+ rs.getString("name") +"\t"+ rs.getString("gender")
                           +"\t"+ rs.getString("buseo") +"\t"+ rs.getInt("pay") +"\t"+ rs.getDate("ipsaday"));
      }

    } catch (SQLException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }finally {
      dbClose(conn, pstmt, rs);
    }


  }

  //이름검색 메서드
  public void searchName() {

    System.out.println("검색할 이름을 입력하세요.");
    String name = sc.nextLine();

    Connection conn = null;
    PreparedStatement pstmt = null;
    ResultSet rs = null;
    String sql = "select * from sawonjh where name like ?";
    conn = getConnection();

    try {
      pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, "%" + name + "%");

      rs = pstmt.executeQuery();

      if(!rs.next()) {
        System.out.println("해당하는 이름과 일치하는 데이타가 없습니다.");

      }else {

        showTitle();

        do{
          System.out.println(rs.getInt("num") +"\t"+ rs.getString("name") +"\t"+ rs.getString("gender")
                             +"\t"+ rs.getString("buseo") +"\t"+ rs.getInt("pay") +"\t"+ rs.getDate("ipsaday"));
        }while(rs.next()); 
      }

    } catch (SQLException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }finally {
      dbClose(conn, pstmt, rs);
    }



  }


  public void process() {

    while(true) {
      switch(getMenu()) {
        case 1:
          insert();
          break;
        case 2:
          delete();
          break;
        case 3:
          update();
          break;
        case 4:
          writeAll();
          break;
        case 5:
          writeGender();
          break;
        case 6:
          writeBuseo();
          break;
        case 7:
          searchName();
          break;
        case 8:
          System.out.println("종료합니다.");
          System.exit(0);
        default:
          break;
      }
    }
  }

  public static void main(String[] args) {
    // TODO Auto-generated method stub
    PrepareJdbcEx1 pp = new PrepareJdbcEx1();
    pp.process();

  }
}
```

- 위의 내용에 DTO, DAO 더하기

```java
package test.day0422;

import java.util.List;
import java.util.Scanner;
import java.util.Vector;

public class PreparedJdbcEx2 {
  SawonDAO dao;
  Scanner sc = null;
  List<SawonDTO> list = null;
  public PreparedJdbcEx2(){
    list = new Vector<SawonDTO>();
    dao = SawonDAO.getInctance();
  }
  public void showTitle() {
    System.out.println("==================================================");
    System.out.println("번호\t이름\t성별\t부서\t급여\t입사일");
    System.out.println("--------------------------------------------------");
  }
  public int getMenu() {
    sc = new Scanner(System.in);
    System.out.println();
    System.out.println("***메뉴***");
    System.out.println("1.추가 2.삭제 3.번호순출력 4.이름순출력 5.급여순출력 6.종료");
    int n = Integer.parseInt(sc.nextLine());
    return n;
  }
  public void process() {
    while(true) {
      switch(getMenu()) {
        case 1:
          insert();
          break;
        case 2:
          delete();
          break;
        case 3:
          write(1);
          break;
        case 4:
          write(2);
          break;
        case 5:
          write(3);
          break;
        case 6:
          System.out.println("종료합니다.");
          System.exit(0);
        default:
          System.out.println("다시 입력해주세요."); 
      }
    }
  }
  public void insert() {
    System.out.println("이름을 입력하세요.");
    String name = sc.nextLine();
    System.out.println("성별을 입력하세요.");
    String gender = sc.nextLine();
    System.out.println("부서를 입력하세요.");
    String buseo = sc.nextLine();
    System.out.println("급여를 입력하세요.");
    int pay = Integer.parseInt(sc.nextLine());
    SawonDTO s = new SawonDTO();
    s.setName(name);
    s.setGender(gender);
    s.setBuseo(buseo);
    s.setPay(pay);
    dao.insert(s);
  }
  public void delete() {
    System.out.println("삭제할 데이타의 이름을 입력해 주십시오.");
    String name = sc.nextLine();
    System.out.println("어느 부서의 " + name + "을 삭제하시겠습니까?");
    String buseo = sc.nextLine();
    dao.delete(name,buseo);
  }
  public void write(int n) {
    String condition ="";
    switch(n){
      case 1:
        condition = "num";
        break;
      case 2:
        condition = "name";
        break;
      case 3:
        condition = "pay";
        break;
      default:
        condition = "num"; 
    }
    showTitle();
    for(SawonDTO a:dao.writeAll(condition)) {
      System.out.println(a.getNum() +"\t"+ a.getName() +"\t"+ a.getGender() +"\t"+ a.getBuseo() +"\t"+ a.getPay() +"\t"+ a.getIpsaday());
    }
  }

  public static void main(String[] args) {
    // TODO Auto-generated method stub
    PreparedJdbcEx2 pp = new PreparedJdbcEx2();
    pp.process();
  }
}
```

```java
package test.day0422;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;
import java.util.Vector;

public class SawonDAO {

  String url="jdbc:oracle:thin:@192.168.0.49:1521:xe";
  String driver ="oracle.jdbc.driver.OracleDriver";
  String user = "acorn03";
  String password="a1234";
  private SawonDAO() {
    try {
      Class.forName(driver);
    } catch (ClassNotFoundException e) {
      System.out.println("드라이버 연결 실패" + e.getMessage());
    }
  }
  //인스턴스를 얻으려면 이 메서드를 실행해야 한다.(new 어쩌구로 생성하는거 안되게 만들어보기)
  public static SawonDAO getInctance() {
    return new SawonDAO();
  }
  public Connection getConnection() {
    Connection conn = null;
    try {
      conn = DriverManager.getConnection(url, user, password);
    } catch (SQLException e) {
      System.out.println("연결 실패" +e.getMessage() );
    }
    return conn;
  }
  public void dbClose(PreparedStatement pstmt, Connection conn) {
    try {
      if(pstmt!= null) pstmt.close();
      if(conn!= null) conn.close();
    }catch(SQLException e) {
    }
  }
  public void dbClose(PreparedStatement pstmt, Connection conn, ResultSet rs) {
    try {
      if(rs!= null) rs.close();
      if(pstmt!= null) pstmt.close();
      if(conn!= null) conn.close();
    }catch(SQLException e) {
    }
  }
  public void insert(SawonDTO dto) {
    String sql = "insert into sawonjh values (seq2jh.nextval, ?,?,?,?,sysdate)";
    Connection conn = null;
    PreparedStatement pstmt = null;
    conn = getConnection();
    try{
      pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, dto.getName());
      pstmt.setString(2, dto.getGender());
      pstmt.setString(3, dto.getBuseo());
      pstmt.setInt(4, dto.getPay());
      pstmt.execute();
    }catch(SQLException e) {
    }finally {
      dbClose(pstmt, conn);
    }
  }
  public void delete(String name, String buseo) {
    String sql = "delete from sawonjh where name=? and buseo=?";
    Connection conn = null;
    PreparedStatement pstmt = null;
    conn = getConnection();
    try {
      pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, name);
      pstmt.setString(2, buseo);
      int n = pstmt.executeUpdate();
      if(n==0)
        System.out.println("삭제할 데이타가 존재하지 않습니다.");
      else {
        System.out.println("총 " + n + "개의 데이타가 삭제되었습니다.");
      }

    } catch (SQLException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }finally {
      dbClose(pstmt, conn);
    }
  }
  public List<SawonDTO> writeAll(String condition) {
    List<SawonDTO> list = new Vector<SawonDTO>();
    String sql = "select * from sawonjh order by " + condition;
    Connection conn = null;
    PreparedStatement pstmt = null;
    ResultSet rs = null;
    conn = getConnection();
    try {
      pstmt = conn.prepareStatement(sql);
      rs = pstmt.executeQuery();
      while(rs.next()) {
        SawonDTO s = new SawonDTO();
        s.setNum(rs.getInt("num"));
        s.setName(rs.getString("name"));
        s.setGender(rs.getString("gender"));
        s.setBuseo(rs.getString("buseo"));
        s.setPay(rs.getInt("pay"));
        s.setIpsaday(rs.getTimestamp("ipsaday"));
        list.add(s);
      }
    } catch (SQLException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }finally {
      dbClose(pstmt, conn, rs);
    }
    return list;
  }
}
```

```java
package test.day0422;

import java.sql.Timestamp;

public class SawonDTO {
  private int num;
  private String name;
  private String gender;
  private String buseo;
  private int pay;
  private Timestamp ipsaday;
  SawonDTO(){
  }

  public int getNum() {
    return num;
  }

  public void setNum(int num) {
    this.num = num;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public String getGender() {
    return gender;
  }

  public void setGender(String gender) {
    this.gender = gender;
  }

  public String getBuseo() {
    return buseo;
  }

  public void setBuseo(String buseo) {
    this.buseo = buseo;
  }

  public int getPay() {
    return pay;
  }

  public void setPay(int pay) {
    this.pay = pay;
  }

  public Timestamp getIpsaday() {
    return ipsaday;
  }

  public void setIpsaday(Timestamp ipsaday) {
    this.ipsaday = ipsaday;
  }
}
```

