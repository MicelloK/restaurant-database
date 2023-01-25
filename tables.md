# Tabele

### Rachunki
```sql
CREATE TABLE Bills (
    BillID int NOT NULL IDENTITY(1,1),
    Date datetime NOT NULL,
    CustomerID int NOT NULL,
    CONSTRAINT Bills_pk PRIMARY KEY (BillID)
);
```

### Kategorie
```sql
CREATE TABLE Categories (
    CategoryID int NOT NULL IDENTITY(1,1),
    Description varchar(30) NOT NULL,
    CONSTRAINT Categories_pk PRIMARY KEY (CategoryID)
);
```

### Składniki
```sql
CREATE TABLE Components (
    ComponentID int NOT NULL IDENTITY(1,1),
    ComponentName varchar(20) NOT NULL,
    Supplier varchar(30) NOT NULL,
    CONSTRAINT Components_pk PRIMARY KEY (ComponentID)
);
```

### Klienci
```sql
CREATE TABLE Customers (
    CustomerID int NOT NULL IDENTITY(1,1),
    FirstName varchar(15) NOT NULL,
    LastName varchar(15) NOT NULL,
    CompanyName varchar(20) NULL DEFAULT NULL,
    Country varchar(20) NOT NULL,
    City varchar(20) NOT NULL,
    Street varchar(20) NOT NULL,
    Phone varchar(18) NULL DEFAULT NULL, --UNIQUE,
    RegularCustomer bit NULL DEFAULT 0,
    CONSTRAINT Customers_pk PRIMARY KEY (CustomerID)
);
```

### Zniżki
```sql
CREATE TABLE Discounts (
    DiscountID int NOT NULL IDENTITY(1,1),
    CustomerID int NOT NULL,
    CustomerDiscount decimal(5,2) NOT NULL,
    StartDate date NOT NULL,
    EndDate date NOT NULL,
    OrderID int NOT NULL,
    CHECK (EndDate > StartDate),
    CONSTRAINT Discounts_pk PRIMARY KEY (DiscountID)
);
```

### Pracownicy
```sql
CREATE TABLE Employees (
    EmployeeID int NOT NULL IDENTITY(1,1),
    FirstName varchar(15) NOT NULL,
    LastName varchar(15) NOT NULL,
    Position varchar(30) NOT NULL DEFAULT 'Employee',
    Country varchar(20) NOT NULL,
    City varchar(20) NOT NULL,
    Street varchar(20) NOT NULL,
    Phone varchar(18) NOT NULL UNIQUE,
    DateEmployment date NOT NULL,
    DateRelease date NULL,
    ReportsTo int NOT NULL,
    CONSTRAINT Employees_pk PRIMARY KEY (EmployeeID)
);
```

### Menu
```sql
CREATE TABLE Menu (
    MenuID int NOT NULL IDENTITY(1,1),
    StartDate date NOT NULL,
    EndDate date NOT NULL,
    CHECK (EndDate > StartDate),
    CONSTRAINT Menu_pk PRIMARY KEY (MenuID)
);
```

### Szczegóły menu
```sql
CREATE TABLE MenuDetails (
    MenuID int NOT NULL,
    ProductID int NOT NULL,
    Price smallmoney NOT NULL,
    CONSTRAINT MenuDetails_pk PRIMARY KEY (MenuID,ProductID)
);
```

### Zamówienia
```sql
CREATE TABLE Orders (
    OrderID int NOT NULL IDENTITY(1,1),
    CustomerID int NOT NULL,
    EmployeeID int NOT NULL,
    OrderDate datetime DEFAULT GETDATE(),
    DeliveryDate datetime default NULL,
    PaymentMethod varchar(20) NOT NULL,
    BillID int,
    CHECK (PaymentMethod in ('Credit Card', 'Debit Card', 'PayPal', 'Cash') AND DeliveryDate >= OrderDate),
    CONSTRAINT Orders_pk PRIMARY KEY (OrderID),
);
```

### Szczegóły zamówień
```sql
CREATE TABLE OrderDetails (
    OrderID int NOT NULL,
    ProductID int NOT NULL,
    Quantity int NOT NULL DEFAULT 1,
    CONSTRAINT OrderDetails_pk PRIMARY KEY (OrderID,ProductID)
);
```

### Dokonane płatności
```sql
CREATE TABLE Payments (
    PaymentID int NOT NULL IDENTITY(1,1),
    Price smallmoney NOT NULL,
    PaymentDate datetime NOT NULL DEFAULT GETDATE(),
    OrderID int NOT NULL,
    CONSTRAINT Payments_pk PRIMARY KEY (PaymentID)
);
```

### Produkty
```sql
CREATE TABLE Products (
    ProductID int NOT NULL IDENTITY(1,1),
    ProductName varchar(30) NOT NULL UNIQUE,
    Description varchar(30) NULL DEFAULT NULL,
    CategoryID int NOT NULL,
    OutDate date NOT NULL,
    CONSTRAINT Products_pk PRIMARY KEY (ProductID)
);
```

### Szczegóły produktu
```sql
CREATE TABLE ProductDetails (
    ComponentID int NOT NULL,
    ProductID int NOT NULL,
    CONSTRAINT ProductDetails_pk PRIMARY KEY (ProductID,ComponentID)
);
```

### Rezerwacje
```sql
CREATE TABLE Reservations (
    ReservationID int NOT NULL IDENTITY(1,1),
    StartDate datetime NOT NULL,
    EndDate datetime NOT NULL,
    OrderID int NOT NULL,
    CHECK (EndDate > StartDate),
    CONSTRAINT Reservations_pk PRIMARY KEY (ReservationID)
);
```

### Szczegóły rezerwacji
```sql
CREATE TABLE ReservationDetails (
    ReservationID int NOT NULL,
    TableID int NOT NULL,
    CONSTRAINT ReservationDetails_pk PRIMARY KEY (TableID,ReservationID)
);
```

### Stoliki
```sql
CREATE TABLE Tables (
    TableID int NOT NULL IDENTITY(1,1),
    Quantity int NOT NULL,
    Category varchar(30) NOT NULL,
    CONSTRAINT Tables_pk PRIMARY KEY (TableID)
);
```

