# Procedury

### 1. Dodawanie nowego zamówienia
```sql
CREATE PROCEDURE AddOrder(
    @customerID INT,
    @employeeID INT,
    @paymentMethod VARCHAR(20),
    @deliveryDate DATETIME = NULL)
AS
BEGIN
    BEGIN TRY
        IF @deliveryDate IS NULL
            BEGIN
                SET @deliveryDate = DATEADD(HOUR, 1, GETDATE())
            END
        IF NOT EXISTS(SELECT * FROM Customers WHERE CustomerID = @customerID)
            BEGIN
                THROW 52000, N'Unknown customer!', 1;
            END
        IF NOT EXISTS(SELECT * FROM Employees WHERE EmployeeID = @employeeID)
            BEGIN
                THROW 52000, N'Unknown employee!', 1;
            END

        INSERT INTO Orders(CustomerID, EmployeeID, OrderDate, DeliveryDate, PaymentMethod)
        VALUES (@customerID, @employeeID, GETDATE(), @deliveryDate, @paymentMethod)
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 2. Dodawanie menu
```sql
CREATE PROCEDURE AddMenu(
    @StartDate DATE,
    @EndDate DATE
)
AS
BEGIN
    BEGIN TRY
        IF ((SELECT COUNT(*)
             FROM Menu
             WHERE (@StartDate BETWEEN StartDate AND EndDate)
                OR (@EndDate BETWEEN StartDate AND EndDate)) > 0)
            BEGIN
                THROW 50002, N'There is applicable menu, can not add new one!', 1
            END

        INSERT INTO Menu (StartDate, EndDate)
        VALUES (@StartDate, @EndDate)
    END TRY
    BEGIN CATCH
        DECLARE @Message NVARCHAR(1000) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 50002, @Message, 1;
    END CATCH
END
```

### 3. Dodawanie szczegółów zamówienia
```sql
CREATE PROCEDURE AddOrderDetail(
    @orderID INT,
    @productID INT,
    @quantity INT)
AS
BEGIN
    DECLARE @DeliveryDate DATE
    SET @DeliveryDate = (SELECT DeliveryDate
                         FROM Orders
                         WHERE OrderID = @orderID)

    BEGIN TRY
        IF NOT EXISTS(SELECT P.productID
                      FROM Menu
                               LEFT JOIN MenuDetails MD ON Menu.MenuID = MD.MenuID
                               JOIN Products P ON MD.ProductID = P.ProductID
                      WHERE @DeliveryDate BETWEEN StartDate AND EndDate
                        AND @productID = P.ProductID)
            BEGIN
                THROW 50051, N'There is no menu for the given week!', 1;
            END

        IF (SELECT dbo.SeaFood(@productID)) = 1 AND (SELECT dbo.EarlyOrderSeaFood(@OrderID)) = 0
            BEGIN
                THROW 50051, N'Can not place an order for sea food for that day!', 1;
            END

        IF (@quantity < 1)
            BEGIN
                THROW 50051, N'products amount must be positive!', 1;
            END
        IF EXISTS(
                SELECT * FROM OrderDetails OD WHERE OD.OrderID = @orderID AND OD.ProductID = @productID
            )
            BEGIN
                UPDATE OrderDetails
                SET Quantity = Quantity + @quantity
                WHERE ProductID = @productID
            END

        INSERT INTO OrderDetails (OrderID, ProductID, Quantity)
        VALUES (@orderID, @productID, @quantity)
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 50051, @msg, 1;
    END CATCH
END
```

### 4. Dodawanie nowego klienta
```sql
CREATE PROCEDURE AddCustomer(
    @FirstName VARCHAR(15),
    @LastName VARCHAR(15),
    @CompanyName VARCHAR(20) = NULL,
    @Country VARCHAR(20),
    @City VARCHAR(20),
    @Street VARCHAR(20),
    @Phone VARCHAR(18) = NULL,
    @RegularCustomer INT = 1
)
AS
BEGIN
    INSERT INTO Customers (FirstName, LastName, CompanyName, Country, City, Street, Phone, RegularCustomer)
    VALUES (@FirstName, @LastName, @CompanyName, @Country, @City, @Street, @Phone, @RegularCustomer)
END
```

### 5. Aktualizowanie daty dostarczenia zamówienia
```sql
CREATE PROCEDURE AddDeliveryDate(@orderID INT)
AS
BEGIN
    BEGIN TRY
        IF NOT EXISTS(
                SELECT * FROM Orders WHERE OrderID = @orderID
            )
            BEGIN
                THROW 50053, N'Order do not exits', 1;
            END
        ELSE
            BEGIN
                UPDATE Orders
                SET DeliveryDate = GETDATE()
                WHERE OrderID = @orderID
            END
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 50053, @msg, 1;
    END CATCH
END
```

### 6. Wyświetlanie informacji o pracowniku
```sql
CREATE PROCEDURE EmployeeInfo(@employeeID INT)
AS
BEGIN
    BEGIN TRY
        IF NOT EXISTS(
                SELECT *
                FROM Employees
                WHERE @employeeID = EmployeeID
            )
            BEGIN
                THROW 52000, N'There is no employee with given ID', 1
            END
        SELECT FirstName,
               LastName,
               Position,
               ReportsTo,
               Country,
               City,
               Street,
               Phone,
               DateEmployment,
               DateRelease
        FROM Employees
        WHERE EmployeeID = @employeeID
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 7. Wyświetlanie informacji o kliencie
```sql
CREATE PROCEDURE CustomerInfo(@customerID INT)
AS
BEGIN
    BEGIN TRY
        IF NOT EXISTS(
                SELECT *
                FROM Customers
                WHERE @customerID = CustomerID
            )
            BEGIN
                THROW 52000, N'There is no customer with given ID', 1
            END
        SELECT FirstName,
               LastName,
               CompanyName,
               Country,
               City,
               Street,
               Phone,
               RegularCustomer
        FROM Customers
        WHERE CustomerID = @customerID
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 8. Wyświetlanie informacji o danym produkcie
```sql
CREATE PROCEDURE ProductInfo(@productID INT)
AS
BEGIN
    BEGIN TRY
        IF NOT EXISTS(
                SELECT *
                FROM Products
                WHERE ProductID = @productID
            )
            BEGIN
                THROW 52000, N'There is no product with given ID', 1
            END
        SELECT P.ProductName,
               P.Description                     "Product Description",
               Cat.Description                   "Category Description",
               STRING_AGG(C.ComponentName, ', ') Components
        FROM Products P
                 JOIN Categories Cat ON P.CategoryID = Cat.CategoryID
                 JOIN ProductDetails PD ON P.ProductID = PD.ProductID
                 JOIN Components C ON PD.ComponentID = C.ComponentID
        WHERE P.ProductID = @productID
        GROUP BY P.ProductName, P.Description, Cat.Description
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 9. Wyświetlanie rachunku za zamówienie
```sql
CREATE PROCEDURE GetBill(@orderID INT)
AS
BEGIN
    BEGIN TRY
        IF NOT EXISTS(
                SELECT *
                FROM Orders
                WHERE OrderID = @orderID
            )
            BEGIN
                THROW 52000, N'There is no order with given ID', 1;
            END
        IF NOT EXISTS(
                SELECT *
                FROM Bills
                         JOIN Orders O ON Bills.BillID = O.BillID
                WHERE O.OrderID = @orderID
            )
            BEGIN
                THROW 52000, N'Order does not have generated bill', 1;
            END
        SELECT C.CustomerID, C.FirstName, C.LastName, B.Date "Billing date", dbo.OrderPrice(@orderID) "Order Price"
        FROM Bills B
                 JOIN Customers C ON B.CustomerID = C.CustomerID
                 JOIN Orders O ON B.BillID = O.BillID
        WHERE O.OrderID = @orderID
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 10. Usuwanie klienta z bazy danych
```sql
CREATE PROCEDURE DeleteCustomer(@customerID INT)
AS
BEGIN
    BEGIN TRY
        IF NOT EXISTS(
                SELECT *
                FROM Customers
                WHERE CustomerID = @customerID
            )
            BEGIN
                THROW 52000, N'There is no customer with given ID', 1;
            END
        DECLARE @an NVARCHAR(10) = 'xxxxxxxx'
        UPDATE Customers
        SET FirstName   = @an,
            LastName    = @an,
            CompanyName = @an,
            Country     = @an,
            City        = @an,
            Street      = @an,
            Phone       = @an
        WHERE CustomerID = @customerID
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 11. Dodawanie stolika do rezerwacji
```sql
CREATE PROCEDURE AddTableToReservation(@reservationID INT, @tableID INT)
AS
BEGIN
    BEGIN TRY
        IF NOT EXISTS(
                SELECT *
                FROM Reservations
                WHERE ReservationID = @reservationID
            )
            BEGIN
                THROW 52000, N'There is no table with given ID', 1;
            END

        DECLARE @startDate DATETIME
        DECLARE @endDate DATETIME
        SELECT @startDate = StartDate, @endDate = EndDate FROM Reservations WHERE ReservationID = @reservationiD
        IF EXISTS(
                SELECT *
                FROM ReservationDetails
                         JOIN Reservations R ON ReservationDetails.ReservationID = R.ReservationID
                WHERE StartDate <= @endDate
                  AND EndDate >= @startDate
                  AND TableID = @tableID
            )
            BEGIN
                THROW 52000, N'Unavailable table', 1;
            END

        INSERT INTO ReservationDetails
        VALUES (@reservationID, @tableID)
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 12. Dodawanie nowej rezerwacji
```sql
CREATE PROCEDURE AddReservation(
    @startDate DATETIME,
    @endDate DATETIME,
    @orderID INT,
    @tableID INT
)
AS
BEGIN
    BEGIN TRY
        IF EXISTS(
                SELECT *
                FROM Reservations
                WHERE OrderID = @orderID
            )
            BEGIN
                THROW 52000, N'Reservation already exists', 1;
            END
        DECLARE @customerID INT = (SELECT Customers.CustomerID
                                   FROM Customers
                                            JOIN Orders O ON Customers.CustomerID = O.CustomerID
                                   WHERE OrderID = @orderID)
        DECLARE @regular BIT = (SELECT RegularCustomer
                                FROM Customers
                                WHERE CustomerID = @customerID)
        IF (@regular = 0)
            BEGIN
                THROW 52000, N'To make reservation you have to be regular customer', 1;
            END
        IF ((SELECT dbo.OrderPrice(@orderID)) < 50 OR (SELECT dbo.OrderPrice(@orderID)) IS NULL)
            BEGIN
                THROW 52000, N'Order price has to be at least 50', 1;
            END

        INSERT INTO Reservations (StartDate, EndDate, OrderID)
        VALUES (@startDate, @endDate, @orderID)

        DECLARE @newID INT = (SELECT ReservationID
                              FROM Reservations
                              WHERE OrderID = @orderID AND StartDate = @startDate AND EndDate = @endDate)

        EXEC AddTableToReservation @newID, @tableID
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 13. Generowanie rachunku
```sql
CREATE PROCEDURE GenerateBill(@orderID INT)
AS
BEGIN
    BEGIN TRY
        IF ((SELECT BillID FROM Orders WHERE OrderID = @orderID) IS NOT NULL)
            BEGIN
                THROW 52000, N'Bill already exits', 1;
            END

        DECLARE @orderPrice SMALLMONEY = dbo.OrderPrice(@orderID)
        DECLARE @paymentSum SMALLMONEY = dbo.PaymentsSum(@orderID)
        IF (@orderPrice > @paymentSum OR @orderPrice IS NULL OR @paymentSum IS NULL)
            BEGIN
                THROW 52000, N'Payments have not been settled', 1;
            END

        DECLARE @currDate DATETIME = GETDATE()
        DECLARE @customerID INT = (SELECT CustomerID FROM Orders WHERE OrderID = @orderID)
        INSERT INTO Bills(Date, CustomerID)
        VALUES (@currDate, @customerID)

        DECLARE @billID INT = (SELECT BillID FROM Bills WHERE Date = @currDate AND CustomerID = @customerID)
        UPDATE Orders
        SET BillID = @billID
        WHERE OrderID = @orderID
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 14. Dodawanie płatności
```sql
CREATE PROCEDURE AddPayment(@orderID INT, @price SMALLMONEY)
AS
BEGIN
    INSERT INTO Payments(Price, PaymentDate, OrderID)
    VALUES (@price, GETDATE(), @orderID)
END
```

### 15. Dodawanie nowego produktu
```sql
CREATE PROCEDURE AddNewProduct(@productName VARCHAR(30), @productDescription VARCHAR(30), @categoryID INT, @outDate DATE = NULL)
AS
BEGIN
    BEGIN TRY
        IF EXISTS(
                SELECT *
                FROM Products
                WHERE ProductName = @productName
                  AND Description = @productDescription
                  AND CategoryID = @categoryID
            )
            BEGIN
                THROW 52000, N'Product already exits on products list', 1;
            END

        INSERT INTO Products(ProductName, Description, CategoryID, OutDate)
        VALUES (@productName, @productDescription, @categoryID, @outDate)
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 16. Dodawanie składników produktu
```sql
CREATE PROCEDURE AddProductComponent(@productID INT, @componentID INT)
AS
BEGIN
    BEGIN TRY
        IF EXISTS(
                SELECT *
                FROM ProductDetails
                WHERE ProductID = @productID
                  AND ComponentID = @componentID
            )
            BEGIN
                THROW 52000, N'Component already exits on product components list', 1;
            END
        INSERT INTO ProductDetails(ComponentID, ProductID)
        VALUES (@componentID, @productID)
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 17. Dodawanie nowego składnika
``` sql
CREATE PROCEDURE AddNewComponent(@componentName VARCHAR(30), @supplier VARCHAR(30))
AS
BEGIN
    BEGIN TRY
        IF EXISTS(
                SELECT *
                FROM Components
                WHERE ComponentName = @componentName
                  AND Supplier = @supplier
            )
            BEGIN
                THROW 52000, N'Component already exits on components list', 1;
            END

        INSERT INTO Components(ComponentName, Supplier)
        VALUES (@componentName, @supplier)
    END TRY
    BEGIN CATCH
        DECLARE @msg NVARCHAR(2048) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 52000, @msg, 1;
    END CATCH
END
```

### 18. Dodawanie produktu do menu
```sql
CREATE PROCEDURE AddProductToMenu(
    @ProductID INT,
    @MenuID INT,
    @Price SMALLMONEY
)
AS
BEGIN
    DECLARE @StartMenuDate DATE
    DECLARE @EndMenuDate DATE
    DECLARE @tmp DATE
    SELECT @StartMenuDate = StartDate, @EndMenuDate = EndDate FROM Menu WHERE MenuID = @MenuID
    SET @tmp = @StartMenuDate
    BEGIN TRY
        IF ((SELECT COUNT(*) FROM MenuDetails WHERE MenuID = @MenuID AND ProductID = @ProductID) = 0)
            BEGIN
                INSERT INTO MenuDetails(menuid, productid, price)
                VALUES (@MenuID, @ProductID, @Price)
                WHILE @tmp <= @EndMenuDate
                    BEGIN
                        IF (SELECT dbo.CheckMenu(@tmp)) = 0
                            BEGIN
                                DELETE
                                FROM MenuDetails
                                WHERE MenuID = @MenuID AND ProductID = @productID AND Price = @Price;
                                THROW 50003, N'Can not add this product, 50% dishes rule', 1
                            END
                        SET @tmp = DATEADD(DAY, 1, @tmp)
                    END
            END
        ELSE
            BEGIN
                THROW 50003, N'Product is in this Menu', 1
            END
    END TRY
    BEGIN CATCH
        DECLARE @Message NVARCHAR(1000) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 50003, @Message, 1;
    END CATCH
END
```

### 19. Przedłużenie rezerwacji
```sql
CREATE PROCEDURE ExtendReservation(
    @ReservationID INT,
    @NewEndTime DATETIME
)
AS
BEGIN
    DECLARE @OldEndTIME DATETIME
    SET @OldEndTime = (SELECT EndDate FROM Reservations WHERE @ReservationID = ReservationID)
    BEGIN TRY
        IF ((SELECT COUNT(*)
             FROM Tables
                      JOIN ReservationDetails RD ON Tables.TableID = RD.TableID
                      JOIN Reservations R3 ON RD.ReservationID = R3.ReservationID
             WHERE StartDate BETWEEN @OldEndTIME AND @NewEndTime
               AND Tables.tableId IN (SELECT tables.tableid
                                      FROM tables
                                               JOIN ReservationDetails RD ON Tables.TableID = RD.TableID
                                      WHERE ReservationID = @ReservationID)) = 0)
            BEGIN
                UPDATE Reservations SET EndDate = @NewEndTime WHERE ReservationID = @ReservationID
            END
        ELSE
            THROW 50004, N'Table is occupied', 1
    END TRY
    BEGIN CATCH
        DECLARE @Message NVARCHAR(1000) = N'ERROR: ' + ERROR_MESSAGE();
        THROW 50004, @Message, 1;
    END CATCH
END
```

### 20. Anulowanie zamówienia
```sql
CREATE PROCEDURE OrderCancellation(
    @OrderID INT
)
AS
BEGIN
    BEGIN TRY
        IF ((SELECT COUNT(*) FROM Orders WHERE OrderID = @OrderID) = 0)
            BEGIN
                THROW 50333, N'There is no such order', 1
            END
        ELSE
            BEGIN
                UPDATE Orders SET OrderDate = NULL WHERE OrderID = @OrderID
            END
    END TRY
    BEGIN CATCH
        DECLARE @Message NVARCHAR(1000) = N'error: ' + ERROR_MESSAGE();
        THROW 50333, @Message, 1;
    END CATCH
END
```

### 21. Dodawanie płatności za zamówienie
```sql
CREATE PROCEDURE AddPayment(@orderID INT, @price SMALLMONEY)
AS
BEGIN
    INSERT INTO Payments(Price, PaymentDate, OrderID)
    VALUES (@price, GETDATE(), @orderID)
END
```
