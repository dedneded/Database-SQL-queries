# Database-SQL-queries

Здесь представлена схема реляционной базы данных для центра проката спецтехники и различные запросы на выборку данных.

## Схема базы данных


## База данных

[Здесь](https://github.com/dedneded/Database-SQL-queries/blob/main/SQL.db) расположен файл с упрощенной версией схемы базы данных, поскольку некоторые таблицы и строки не использовались в запросах. 

## Запросы

### Самая популярная услуга за сентябрь

```
SELECT ID, Name, Price, ID_parent, ID_categoryOfVehicle, COUNT(*) as count FROM Service s
JOIN ServiceInOrder sr On s.ID = sr.ID_service
JOIN OrderServiceInOrder osr On osr.ID_serviceInOrder = sr.ID_service
WHERE sr.End between '2021/09/01' and '2021/09/31'
GROUP BY s.ID
ORDER BY count(*) DESC
LIMIT 1
```
### Менеджер у которого больше всего заказов за август
```
SELECT ID, FIO, PhoneNumber, COUNT(*) as count FROM Employee e
JOIN 'Order' o On e.ID = o.ID_employee
WHERE End between '2021/08/01' and '2021/08/31'
GROUP BY e.ID
ORDER BY count(*) DESC
LIMIT 1
```
### 10 заказов, где было больше всего услуг
```
SELECT ID, Start, End, Price, ID_client, ID_branch, ID_employee, COUNT(*) as count FROM 'Order' o
JOIN OrderServiceInOrder os On os.ID_order = o.ID
GROUP BY ID
ORDER BY count(*) DESC
LIMIT 10
```
### Неиспользуемые в сентябре, но используемые в августе услуги
```
SELECT ID_serviceINOrder FROM OrderServiceInOrder os
JOIN 'Order' o ON os.ID_order = o.ID
WHERE (o.End between '2021/08/01' and '2021/08/31')
EXCEPT
SELECT ID_serviceINOrder FROM OrderServiceInOrder os
JOIN 'Order' o ON os.ID_order = o.ID
WHERE (o.End between '2021/09/01' and '2021/09/31')
```
### Мастера (менеджеры) и количество их заказов в сентябре
```
SELECT e.ID, e.FIO, count(*) as count
FROM 'Order' o
JOIN Employee e on o.ID_employee = e.ID
WHERE End between '2021/09/01' and '2021/09/31'
GROUP BY e.ID
```
### Заказы, которые НЕ выполнял такой-то мастер

#### Для менеджеров:
```
SELECT * FROM 'Order' o
WHERE o.ID_employee != @ID
```
#### Для водителей:
```
SELECT ID, Start, End, Price, ID_client, ID_branch, ID_employee FROM 'Order' o
JOIN OrderDriver od On o.ID = od.ID_order
WHERE od.ID_driver != @ID
```
### Заказ, где есть услуга с таким-то названием
```
SELECT ID_order FROM OrderServiceInOrder os
JOIN Service s On os.ID_serviceInOrder = s.ID
WHERE s.Name = @Name
```
### Заказы, где номера телефонов клиентов начинаются на +7999
```
SELECT o.* 
FROM Client cl
INNER JOIN 'Order' o on cl.ID = o.ID_client
AND cl.PhoneNumber LIKE '+7999%'
```
### Заказы, где НЕТ услуг с такими-то ID
```
SELECT * FROM 'Order' WHERE ID NOT IN(
SELECT ID_order FROM OrderServiceInOrder WHERE ID_serviceInOrder IN(
SELECT ID FROM Service
JOIN OrderServiceInOrder os ON Service.ID =os.ID_serviceInOrder
WHERE ID_serviceInOrder = @ID))
```

