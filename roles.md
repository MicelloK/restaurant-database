# Uprawnienia

### Admin
```sql
CREATE ROLE admin
GRANT ALL PRIVILEGES ON u_justyna.dbo to admin
```

### Moderator
```sql
CREATE ROLE moderator

GRANT EXECUTE ON AddProductToMenu to moderator
GRANT EXECUTE ON DeleteCustomer to moderator
GRANT EXECUTE ON EmployeeInfo to moderator
GRANT EXECUTE ON GetDiscounts to moderator

-- worker privileges
GRANT EXECUTE ON AddCustomer to moderator
GRANT EXECUTE ON AddDeliveryDate to moderator
GRANT EXECUTE ON AddOrder to moderator
GRANT EXECUTE ON AddOrderDetail to moderator
GRANT EXECUTE ON AddOrderWithReservation to moderator
GRANT EXECUTE ON AddPayment to moderator
GRANT EXECUTE ON AddReservation to moderator
GRANT EXECUTE ON AddTableToReservation to moderator
GRANT SELECT ON CheckFreeTables to moderator
GRANT SELECT ON MenuForADay to moderator
GRANT EXECUTE ON CheckMenu to moderator
GRANT EXECUTE ON OrderPrice to moderator
GRANT EXECUTE ON PaymentsSum to moderator
GRANT EXECUTE ON CustomerInfo to moderator
GRANT EXECUTE ON ExtendReservation to moderator
GRANT EXECUTE ON GenerateBill to moderator
GRANT EXECUTE ON GetBill to moderator
GRANT EXECUTE ON OrderCancellation to moderator
GRANT EXECUTE ON ProductInfo to moderator
GRANT EXECUTE ON UpdateDeliveryDate to moderator
```

### Pracownik
```sql
CREATE ROLE worker

GRANT EXECUTE ON AddCustomer to worker
GRANT EXECUTE ON AddDeliveryDate to worker
GRANT EXECUTE ON AddOrder to worker
GRANT EXECUTE ON AddOrderDetail to worker
GRANT EXECUTE ON AddOrderWithReservation to worker
GRANT EXECUTE ON AddPayment to worker
GRANT EXECUTE ON AddReservation to worker
GRANT EXECUTE ON AddTableToReservation to worker
GRANT SELECT ON CheckFreeTables to worker
GRANT SELECT ON MenuForADay to worker
GRANT EXECUTE ON CheckMenu to worker
GRANT EXECUTE ON OrderPrice to worker
GRANT EXECUTE ON PaymentsSum to worker
GRANT EXECUTE ON CustomerInfo to worker
GRANT EXECUTE ON ExtendReservation to worker
GRANT EXECUTE ON GenerateBill to worker
GRANT EXECUTE ON GetBill to worker
GRANT EXECUTE ON OrderCancellation to worker
GRANT EXECUTE ON ProductInfo to worker
GRANT EXECUTE ON UpdateDeliveryDate to worker
```
