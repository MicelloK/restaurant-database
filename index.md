# Indeksy

```sql
-- customer id
CREATE UNIQUE INDEX Customers_pk
ON Customers (CustomerID)

-- order id
CREATE UNIQUE INDEX Orders_pk
ON Orders (OrderID)

-- reservation id
CREATE UNIQUE INDEX Reservation_pk
ON Reservations (ReservationID)

-- table id
CREATE UNIQUE INDEX Tables_pk
ON Tables (TableID)

-- menu id
CREATE UNIQUE INDEX Menu_pk
ON Menu (MenuID)

-- discount id
CREATE UNIQUE INDEX Discount_pk
ON Discounts (DiscountID)

-- employee id
CREATE UNIQUE INDEX Employee_pk
ON Employees (EmployeeID)

-- bill id
CREATE UNIQUE INDEX Bill_pk
ON Bills (BillID)

-- payment id
CREATE UNIQUE INDEX Payment_pk
ON Payments (PaymentID)

-- product id
CREATE UNIQUE INDEX Product_pk
ON Products (ProductID)

-- component id
CREATE UNIQUE INDEX Component_pk
ON Components (ComponentID)

-- category id
CREATE UNIQUE INDEX Category_pk
ON Categories (CategoryID)

-- menu date
CREATE UNIQUE INDEX Menu_date
ON Menu (StartDate, EndDate)

-- order date
CREATE UNIQUE INDEX Orders_Date
ON Orders (OrderDate, DeliveryDate)
```