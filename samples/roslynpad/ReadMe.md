# Use Roslynpad and Dapper to connect to SQL Server

## Stored Procedure with two result sets
This samples shows how to call a stored procedures that returns two resultsets.

The stored procedures looks like this

```sql
CREATE PROCEDURE [dbo].[usp_Two_Result_Sets](
   @ProductId int = NULL,	
   @TraderId int = NULL	
)
AS 
BEGIN 
SELECT Id = @ProductId, 'Product 1' as ProductName
UNION ALL 
SELECT Id = @ProductId+1, 'Product 2' as ProductName

SELECT Id = @TraderId, 'Trader 1' as TraderName
UNION ALL
SELECT Id = @TraderId+1, 'Trader 2' as TraderName
END
```

To call it with dapper inside roslynpad you can do this

```cs
#r "nuget: Microsoft.Data.SqlClient, 5.1.1"
#r "nuget: System.Data.Common, 4.3.0"
#r "nuget: Dapper, 2.0.151"

using System.Data;
using Microsoft.Data.SqlClient;
using Dapper;

// remove line breaks in your code 
string conStr = @"Data Source=<your-db-server>;
   Initial Catalog=<your-db>;
   Integrated Security=False;
   TrustServerCertificate=true;
   User id=<your-user>;
   Password=<your-pass>";
  

List<Product> products;
List<Trader> traders;

using (IDbConnection  con = new SqlConnection(conStr))
{
   var reader = con.QueryMultiple("usp_Two_Result_Sets",
                                   param: new { ProductId = 123,  TraderId = 456 },
                                   commandType: CommandType.StoredProcedure);
   products = reader.Read<Product>().ToList();
   traders = reader.Read<Trader>().ToList();
}
products.Dump();
traders.Dump();

public class Product
{
    public int Id { get; set; }    
    public string ProductName { get; set; }
}
public class Trader
{
    public int Id { get; set; }    
    public string TraderName { get; set; }
}
```

The output in Roslynpad looks like this

![Roslynpad Dapper stored procedure with two resultsets](https://github.com/surfmuggle/Cubert/assets/1271555/b8cc0c1e-c66f-41ef-80d7-9aa2be19612b)
