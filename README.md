# jade-all
Jade,是Java应用程序访问Mysql数据库的一个小框架.

**数据库访问流程图**
<pre><code>
     ,---------------.          ,---.                                           ,----.             ,----------.
     |JavaApplication|          |DAO|                                           |Jade|             |DataServer|
     `-------+-------'          `-+-'                                           `-+--'             `----+-----'
             |                    |                                               |                     |      
             |<------------------>|                                               |                     |      
             |                    |                                               |                     |      
             |                    |        1.Implement jdade specification        |                     |      
             |                    |----------------------------------------------->                     |      
             |                    |                                               |                     |      
             |                    |                                               | 2.Commit sql request|      
             |                    |                                               | -------------------->      
             |                    |                                               |                     |      
             |                    |                                               |   3.Return result   |      
             |                    |                                               | <- - - - - - - - - -       
             |                    |                                               |                     |      
             |                    |4.To convert a database object to a Java object|                     |      
             |                    | according to the DAO specification            |                     |      
             |                    |<- - - - - - - - - - - - - - - - - - - - - - - -                     |      
     ,-------+-------.          ,-+-.                                           ,-+--.             ,----+-----.
     |JavaApplication|          |DAO|                                           |Jade|             |DataServer|
     `---------------'          `---'                                           `----'             `----------'
</code></pre>
**工程依赖**
<ul>
<li>JDK1.7或更高版本</li>
<li>Maven3.x</li>
<li>MySql数据库服务器</li>
</ul>

**简单调用示例**
<ul>
	<li>添加如下依赖:
		<dependency>
			<groupId>com.github.leixuan6</groupId>
			<artifactId>jade-all</artifactId>
			<version>1.0.0-SNAPSHOT</version>
		</dependency>
	</li>
	<li>设置src/main/resource/db.properties文件
		<pre>
			<code>
			driverClassName=com.mysql.jdbc.Driver
			url=jdbc:mysql://127.0.0.1/jade?characterEncoding=UTF-8
			username=root
			password=123456
			maxTotal=1
			maxIdle=1
			maxWait=-1
			</code>
		</pre>
	</li>
	<li>调用JadeBootstrap.start方法，传入的路径参数。Jade扫描这个路径下的DAO，Jade会注册这些DAO服务</li>
	<li>应用层实现DAO规范</li>
	<li>参考org.jade.example目录下的例子</li>
</ul>

**实现Jade的DAO规范**
<pre><code>
@DAO
public interface AccountDAO {

	@SQL(val = "Select * from Account ", type = SQLType.SELECT)
	Account query();
	
	@SQL(val = "Select * from Account ", type = SQLType.SELECT)
	List<Account> queryList();

	@SQL(val="Select * from Account Where id = ID",type = SQLType.SELECT)
	Account queryById(@SQLParam("ID")int id);
	
	@SQL(val="Update Account set name = 'NAME' where id = ID",type = SQLType.UPDATE)
	int updateById(@SQLParam("ID")int id,@SQLParam("NAME")String name);
	
	@SQL(val="Insert into Account()Values(ID,'NAME')",type = SQLType.INSERT)
	int insert(@SQLParam("ID")int id,@SQLParam("NAME")String name);
}

</code></pre>

**Java对象，实现getset方法**
<pre><code>
public class Account {
	private int id;

	private String name;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "Account [id=" + id + ", name=" + name + "]";
	}
}
</code>
</pre>

**测试增删改查**
<pre><code>
public class JadeTest {
	public static void main(String[] args) {
		JadeBootstrap.start("org.jade");
		AccountDAO dao = JadeDAOService.getDao(AccountDAO.class);
		dao.deleteById(1);
		dao.insert(1, "test");
		dao.query();
		dao.queryById(1);
		dao.queryById2(1, "test");
		dao.updateById(1, "test2");
		
	}
}
</code>
</pre>
