# Widoki

### 1. Aktualne menu
```sql
CREATE VIEW ViewMenu AS
    select ProductName, P.Description DescriptionProducts, C.Description DescriptionCategory, Price
    from Menu
    LEFT JOIN MenuDetails MD ON Menu.MenuID = MD.MenuID
    JOIN Products P ON MD.ProductID = P.ProductID
    JOIN Categories C ON P.CategoryID = C.CategoryID
    WHERE GETDATE() BETWEEN StartDate AND EndDate
```

### 2. Miesięczne wpływy
```sql
CREATE VIEW PaymentMonthlyStats AS
    SELECT YEAR(Date) AS Year, MONTH(Date) AS Month, COUNT(*) AS NumberOfBills, SUM(Price) AS TotalAmount
    FROM Bills
    JOIN Orders O ON Bills.BillID = O.BillID
    JOIN Payments P ON O.OrderID = P.OrderID
    GROUP BY YEAR(DATE), MONTH(Date)
```

### 3. Miesięczna liczba rezerwacji i miesięczna liczba zarezerwowanych stolików
```sql
CREATE VIEW MonthlyTableReservationReport AS
    SELECT YEAR(StartDate) AS Year, DATENAME(MONTH,StartDate) AS Month, COUNT(*) AS NumberOfReservations,
           SUM(Quantity) AS TotalTablesReserved
    FROM Reservations
    JOIN ReservationDetails ON Reservations.ReservationID = ReservationDetails.ReservationID
    JOIN Tables T ON ReservationDetails.TableID = T.TableID
    GROUP BY DATENAME(MONTH,StartDate ), YEAR(StartDate);
```

### 4. Tygodniowa liczba rezerwacji i tygodniowa liczba zarezerwowanych stolików
```sql
CREATE VIEW WeeklyTableReservationReport AS
    SELECT  DATENAME(weekday, StartDate) AS Day, COUNT(*) AS NumberOfReservations,
           SUM(Quantity) AS TotalTablesReserved
    FROM Reservations
    JOIN ReservationDetails ON Reservations.ReservationID = ReservationDetails.ReservationID
    JOIN Tables T ON ReservationDetails.TableID = T.TableID
    GROUP BY DATEPART(weekday, StartDate), DATENAME(weekday, StartDate)
```

### 5. Wartość rabatu dla klienta
```sql
CREATE VIEW CustomerDiscounts AS
    SELECT DISTINCT Customers.CustomerID, Customers.FirstName, Customers.LastName,
            Discounts.CustomerDiscount, Discounts.EndDate
    FROM Customers
    LEFT JOIN Discounts ON Customers.CustomerID = Discounts.CustomerID
```

### 6. Opis produktu i lista składników
```sql
CREATE VIEW ProductsComponents AS
    SELECT p.ProductName, p.Description ProductDescription, c.Description CategoryDescription, STRING_AGG(C2.ComponentName, ',') Components
    from Products p
    JOIN Categories C ON P.CategoryID = C.CategoryID
    JOIN ProductDetails PD ON p.ProductID = PD.ProductID
    JOIN Components C2 ON PD.ComponentID = C2.ComponentID
    GROUP BY  p.ProductName, p.Description, c.Description
```

### 7. Statystyki zamówień dla każdego klienta
```sql
CREATE VIEW CustomersStats AS
    SELECT
        Customers.CustomerId, Customers.FirstName, Customers.LastName,Customers.CompanyName, SUM(P.Price) AS TotalAmount,
        COUNT(*) AS NumberOfOrders, MIN(Orders.OrderDate) AS FirstOrderDate,
        MAX(Orders.OrderDate) AS LastOrderDate,
        SUM(MD.Price) OrderPrice
    FROM Customers
    JOIN Orders ON Customers.CustomerID = Orders.CustomerID
    JOIN Payments P ON Orders.OrderID = P.OrderID
    JOIN OrderDetails OD ON Orders.OrderID = OD.OrderID
    JOIN Products P2 ON OD.ProductID = P2.ProductID
    JOIN MenuDetails MD ON OD.ProductID = MD.ProductID
    GROUP BY Customers.CustomerID, Customers.FirstName, Customers.LastName, Customers.CompanyName
```
