# 排版后的内容

173 `ArrayList<Transaction> transactions = new ArrayList<>();`  
174 `try {`  
175 `Connection connection = DriverManager.getConnection(`  
176 `"jdbc:sqlite:C:\\Users\\spiro\\IdeaProjects\\e-coin\\db\\blockchain.db");`  
177 `PreparedStatement stmt = connection.prepareStatement(`  
178 `"SELECT * FROM TRANSACTIONS WHERE LEDGER_ID = ?");`  
179 `stmt.setInt(1, ledgerID);`  
180 `ResultSet resultSet = stmt.executeQuery();`  
181 `while (resultSet.next()) {`  
182 `transactions.add(new Transaction(`  
183 `resultSet.getBytes("FROM"),`  
184 `resultSet.getBytes("TO"),`  
185 `resultSet.getInt("VALUE"),`  
186 `resultSet.getBytes("SIGNATURE"),`  
187 `resultSet.getInt("LEDGER_ID"),`  
188 `resultSet.getString("CREATED_ON")`  
189 `));`  
190 `}`  
191 `resultSet.close();`  
192 `stmt.close();`  
193 `connection.close();`  
194 `} catch (SQLException e) {`  
195 `e.printStackTrace();`  
196 `}`  
197 `return transactions;`  
198 `}`  

该方法从数据库中检索并返回一个`Transaction`（交易）对象列表。它接收一个整数作为参数，该参数表示账本 ID，我们使用它仅检索所需账本的交易记录。

第 175 至 180 行是标准的 JDBC 代码，我们在此建立数据库连接、创建语句、将账本 ID 作为查询语句的一部分插入，并执行查询。第 181 至 190 行遍历结果集，为每个结果创建一个新交易，并将其添加到`transactions`（交易）ArrayList 中。方法的其余部分只是关闭连接和返回语句，返回我们刚刚从数据库中检索到的交易列表。

