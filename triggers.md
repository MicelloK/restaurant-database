# Triggery

### 1. Trigger ustawiający końcową datę promocji 7 dni od złożonego zamówienia
```sql
CREATE TRIGGER tr_UpdateDiscountEndDate
ON OrderDetails
AFTER INSERT, UPDATE
AS
BEGIN
    SET NOCOUNT ON;
    DECLARE @OrderID INT;
    DECLARE @StartDate DATE;
    DECLARE @EndDate DATE;
    DECLARE @CustomerID INT;
    SELECT @OrderID = OrderID FROM inserted;
    DECLARE @TotalAmount SMALLMONEY;
    SET @CustomerID =(SELECT CustomerID FROM Orders WHERE OrderID=@OrderID)
    DECLARE @OldDiscount INT;
    DECLARE @NewDiscount INT;
    SELECT @TotalAmount=sum(dbo.OrderPrice(o.OrderID)), @CustomerID=CustomerID FROM Orders o
        WHERE CustomerID =@CustomerID
        GROUP BY CustomerID
    SELECT @OldDiscount= COUNT(*) FROM Discounts
        WHERE  @CustomerID=CustomerID
    SET @NewDiscount = FLOOR(@TotalAmount/1000) - @OldDiscount
    DECLARE @cnt INT = 0;
    WHILE @cnt < @NewDiscount
        BEGIN
            SET @StartDate = Getdate();
            SET @EndDate = DATEADD(DAY, 7, GETDATE());
            INSERT INTO Discounts(CUSTOMERID, CUSTOMERDISCOUNT, STARTDATE, ENDDATE, ORDERID )
            VALUES (@customerID, 5, @StartDate, @EndDate, @OrderID)
            SET @cnt = @cnt +1;
        END
END
```

### 2. Trigger przypisujący stałą zniżkę, jeśli klient ma 10 lub więcej zamówień w restauracji
```sql
CREATE TRIGGER tr_UpdateRegularCustomer
ON Orders
AFTER INSERT
AS
BEGIN
    SET NOCOUNT ON;
    DECLARE @CustomerID INT;
    SELECT @CustomerID = CustomerID FROM inserted;
    UPDATE Customers
    SET RegularCustomer = 1
    WHERE CustomerID = @CustomerID AND
        (SELECT COUNT(*) FROM Orders WHERE CustomerID = @CustomerID) >= 10;
END
```