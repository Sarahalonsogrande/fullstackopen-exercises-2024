# fullstackopen-exercises-2024
Fullstackopen exercises (Part0-Part13) - 2024

CREATE OR ALTER FUNCTION GetInfo.udfGetCustomerTotalImpuesto
(@CustomerId int = null, @fechaDesde DATE = null, @fechaHasta DATE = null)
RETURNS INT
AS
BEGIN
DECLARE @ImporteTotalImpuesto INT = 0
 
IF ( @CustomerID IS NULL ) 
		SELECT @CustomerID = 11078            
			FROM Sales.SalesOrderHeader
 
	IF ( @fechaDesde IS NULL )
	SELECT @fechaDesde = MIN(OrderDate)
	FROM Sales.SalesOrderHeader
	IF ( @fechaHasta IS NULL )
	SELECT @fechaHasta = MAX(OrderDate)
		FROM Sales.SalesOrderHeader
	IF @fechaDesde > @fechaHasta
	BEGIN
	RETURN NULL;  
	END
	SELECT @ImporteTotalImpuesto = ROUND (SUM(TaxAmt),2)  
		FROM sales.SalesOrderHeader
	WHERE CustomerID= @CustomerId AND OrderDate BETWEEN @fechaDesde AND @fechaHasta;
RETURN @ImporteTotalImpuesto;

CREATE OR ALTER FUNCTION GetInfo.udfImporteTotalProducto
 (@ProductId int = null, @TerritoryId int = null, @fechaDesde DATETIME = null, @fechaHasta DATETIME = null)
 RETURNS MONEY
 AS
 BEGIN
 DECLARE @ImporteTotalProducto MONEY = 0
 
IF ( @ProductID IS NULL ) 
     SELECT @ProductID = 714            
       FROM Sales.SalesOrderHeader
 
  IF ( @fechaDesde IS NULL )
   SELECT @fechaDesde = MIN(OrderDate)
   FROM Sales.SalesOrderHeader

   IF ( @fechaHasta IS NULL )
   SELECT @fechaHasta = MAX(OrderDate)
     FROM Sales.SalesOrderHeader

   IF @fechaDesde > @fechaHasta
   BEGIN
   RETURN NULL;  
   END
   SELECT @ImporteTotalProducto = SUM(UnitPrice*OrderQty)  
     FROM sales.SalesOrderHeader SOH
     INNER JOIN Sales.SalesOrderDetail SOD
   ON SOH.SalesOrderID = SOD.SalesOrderID
   WHERE ProductID= @ProductId AND OrderDate BETWEEN @fechaDesde AND @fechaHasta;
 RETURN @ImporteTotalProducto;
 END;


 SELECT GetInfo.udfImporteTotalProducto (745,2, '2011-05-31', '2011-08-01') AS ImporteTotalProducto

 CREATE OR ALTER FUNCTION GetInfo.udfGetCate... by Carlos (Unverified)Carlos (Unverified)21:26
CREATE OR ALTER FUNCTION GetInfo.udfGetCategoryTotalQuantity
 (@CategoryId int = null, @SubCategoryId int = null, @fechaDesde DATETIME = null, @fechaHasta DATETIME = null)
 RETURNS INT
 AS
 BEGIN
 DECLARE @CantidadTotalProductosOrdenados INT = 0
 
IF ( @CategoryId IS NULL ) 
     SELECT @CategoryId = 3            
       FROM Production.ProductSubcategory
 
  IF ( @fechaDesde IS NULL )
   SELECT @fechaDesde = MIN(OrderDate)
   FROM Sales.SalesOrderHeader

   IF ( @fechaHasta IS NULL )
   SELECT @fechaHasta = MAX(OrderDate)
     FROM Sales.SalesOrderHeader

   IF @fechaDesde > @fechaHasta
   BEGIN
   RETURN NULL;  
   END
   -- AQUI EMPIEZA LA SELECT 
   SELECT @CantidadTotalProductosOrdenados
   PSC.ProductSubcategoryID,
 ProductCategoryID,
 OrderDate,
 SUM (OrderQty) 'CantidadTotal'
 FROM Production.ProductSubcategory PSC
 INNER JOIN Production.Product PP
 ON PSC.ProductSubcategoryID = PP.ProductSubcategoryID
 INNER JOIN Sales.SalesOrderDetail SOD
 ON PP.ProductID = SOD.ProductID
 INNER JOIN Sales.SalesOrderHeader SOH
 ON SOD.SalesOrderID =SOH.SalesOrderID
 GROUP BY PP.ProductID,
 PSC.ProductSubcategoryID,
 ProductCategoryID,
 OrderQty, OrderDate
   --WHERE ProductID= @ProductId AND OrderDate BETWEEN @fechaDesde AND @fechaHasta;
   --- AQUI TERMINA
 RETURN @CantidadTotalProductosOrdenados;
 END;


 SELECT GetInfo.udfGetCategoryTotalQuantity (745,2, '2011-05-31', '2011-08-01') AS CantidadTotalProductosOrdenados
