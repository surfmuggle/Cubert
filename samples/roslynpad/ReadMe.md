# Use Roslynpad  to connect to SQL Server

## Using Ado.Net to get two result sets from a stored procedure 

This samples uses a [SqlDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqldatareader?view=dotnet-plat-ext-7.0) to iterate over the results retunred from the database

```cs
#r "nuget: Microsoft.Data.SqlClient, 5.1.1"

using Microsoft.Data.SqlClient;

// remove line breaks in your code 
string conStr = @"Data Source=<your-db-server>;
   Initial Catalog=<your-db>;
   Integrated Security=False;
   TrustServerCertificate=true;
   User id=<your-user>;
   Password=<your-pass>";

var products = new List<Product>();     
    
string sqlQuery =  "SELECT Product_Id, ProductName FROM dbo.ORDER WHERE Product_Id in (1080, 1081, 1082);";
using (SqlConnection connection = new SqlConnection(conStr))
{
    SqlCommand command = new SqlCommand(sqlQuery, connection);
    connection.Open();
    
    using (SqlDataReader reader = command.ExecuteReader())
    {
       while (reader.Read())
       {
           // fill the list
           products.Add(new Product{Id=reader.GetInt(0), ProductName=reader.GetString(1)});
       }
    }    
}
products.Dump();

public class Product
{
    public int Id{ get; set; }
    public string ProductName { get; set; }        
};
```


## Using Dapper to get two result sets from a stored procedure 
This samples shows how to call a stored procedures that returns two result sets.

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
