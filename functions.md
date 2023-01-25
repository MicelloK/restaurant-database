# Funkcje

### 1. Sprawdzanie czy menu jest wymieniane co dwa tygodnie
```sql
CREATE FUNCTION CheckMenu(@CheckDate DATE)
    RETURNS INT
AS
BEGIN
    DECLARE @PreviousMenuID INT
    DECLARE @SameProducts INT
    DECLARE @NextMenuID INT
    DECLARE @MenuLength INT

    SET @PreviousMenuID = (SELECT DISTINCT MenuID FROM MenuForADay(DATEADD(DAY, -14, @CheckDate)))
    SET @NextMenuID = (SELECT DISTINCT MenuID FROM MenuForADay(@CheckDate))
    SET @SameProducts = (SELECT COUNT(*)
                         FROM (SELECT ProductID
                               FROM MenuDetails
                               WHERE MenuID = @NextMenuID
                               INTERSECT
                               SELECT ProductID
                               FROM MenuDetails
                               WHERE MenuID = @PreviousMenuID) OUT)
    SET @MenuLength = (SELECT COUNT(*) FROM MenuDetails WHERE MenuID = (@PreviousMenuID)) / 2
    IF @SameProducts <= @MenuLength
        BEGIN
            RETURN 1
        END
    RETURN 0
END
```

###  2. Sprawdzanie wolnych stolików
```sql
CREATE FUNCTION CheckFreeTables(@StartDate DATETIME, @EndDate DATETIME)
    RETURNS TABLE
        AS
        RETURN
        SELECT TableID, Quantity, Category
        FROM Tables
        EXCEPT
        SELECT T.TableID, T.Quantity, T.Category
        FROM Reservations
                 JOIN ReservationDetails RD ON Reservations.ReservationID = RD.ReservationID
                 JOIN Reservations R2 ON RD.ReservationID = R2.ReservationID
                 JOIN Tables T ON RD.TableID = T.TableID
        WHERE (@StartDate BETWEEN R2.StartDate AND R2.EndDate)
           OR (@EndDate BETWEEN R2.StartDate AND R2.EndDate)
```

### 3. Sprawdzanie zniżki dla klienta
```sql
CREATE FUNCTION GetDiscounts(@customerID INT, @regularClientDiscount DECIMAL(3, 2), @timeDiscount DECIMAL(3, 2))
    RETURNS DECIMAL(3, 2)
AS
BEGIN
    DECLARE @discount DECIMAL(3, 2)
    SET @discount = 0

    IF ((SELECT RegularCustomer FROM Customers WHERE CustomerID = @customerID) = 1)
        BEGIN
            SET @discount += @regularClientDiscount
        END

    IF ((SELECT DiscountID FROM Discounts WHERE GETDATE() BETWEEN StartDate AND EndDate) IS NOT NULL)
        BEGIN
            SET @discount += @timeDiscount
        END
    RETURN @discount
END
```

### Sprawdzanie menu na konkretny dzień
```sql
CREATE FUNCTION MenuForADay(@day DATE)
    RETURNS TABLE
        AS
        RETURN
        SELECT M.MenuID, p.productID, p.ProductName, p.Description ProductsDesc, c.Description CategoryDesc, Price
        FROM Menu AS m
                 JOIN MenuDetails AS d ON m.MenuID = d.MenuID
                 JOIN Products AS p ON d.ProductID = p.ProductID
                 JOIN Categories AS c ON c.CategoryID = p.CategoryID
        WHERE @day <= m.EndDate
          AND @day >= m.StartDate
```

### 5. Cena za zamówienie
```sql
CREATE FUNCTION OrderPrice(@orderID INT)
    RETURNS SMALLMONEY
AS
BEGIN
    DECLARE @result SMALLMONEY
    SET @result = (SELECT SUM(OD.Quantity * MD.Price) AS Price
                   FROM Orders AS O
                            JOIN OrderDetails OD ON O.OrderID = OD.OrderID
                            JOIN Products PD ON OD.ProductID = PD.ProductID
                            JOIN MenuDetails MD ON OD.ProductID = MD.ProductID
                            JOIN Menu M ON MD.MenuID = M.MenuID
                   WHERE O.DeliveryDate >= M.StartDate
                     AND O.DeliveryDate <= M.EndDate
                     AND O.OrderID = @OrderID)
    IF (SELECT COUNT(CustomerDiscount) FROM Discounts WHERE OrderID = @orderID) > 0
        BEGIN
            SET @result = (@result * (100 - (SELECT TOP 1 CustomerDiscount FROM Discounts WHERE OrderID = @orderID))) /
                          100
        END
    ELSE
        IF (SELECT RegularCustomer
            FROM Customers
                     JOIN Orders o ON o.CustomerID = Customers.CustomerID
            WHERE OrderID = @orderID) = 1
            BEGIN
                SET @result = 0.95 * @result
            END
    RETURN @result
END
```

### Suma płatności za zamówienie
```sql
CREATE FUNCTION PaymentsSum(@orderID INT)
    RETURNS SMALLMONEY
AS
BEGIN
    DECLARE @result SMALLMONEY
    SET @result = (SELECT SUM(Price)
                   FROM Payments
                   WHERE OrderID = @orderID)
    RETURN @result
END
```

