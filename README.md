# Practica de consolidacion

Para este proyecto utilize Docker compose el cual contiene instrucciones para hacer funcionar un contenedor de postgres y de pgadmin.

Tambien se encuentra el backup de la base de datos creada.

### Docker compose

postgres necesita de un usuario 'postgres', de una cotraseña 'postgres' y de una base de datos 'postgres_db'. Se pueden cambiar en el archivo `docker-compose.yml`.

Leventar el servicio:
```bash
docker compose up
```

para bajar el servicio utilizar:
```bash
docker compose down
```
para obtener el id del contenedor, luego utilizar:
```bash
docker ps

docker inspect <your_postgres_id_container | your postgres_container_name> | grep IPAddress
```

Ingresar a pgadmin a traves del purto `80`, e ingresar el correo `admin@admin.com` y la contraseña `admin`. Ambas se pueden cambiar en el archivo `docker-compose.yml`.

Crear un nuevo server, en la opcion `Add new server`, en la pestaña `General` dar un nombre, como `local`.

En la pestaña `Connection`, dar la direccion recibida con el comando `docker inspect ...`, cambiar el `usuario` y la `contraseña`, que hayas puesto en tu `docker-compose.yml` y guardar.

### SQL

```sql
-- listar tablas --
SELECT tablename
FROM pg_catalog.pg_tables
WHERE schemaname != 'pg_catalog' AND 
    schemaname != 'information_schema';

-- listar detalles de la tabla --
select * from information_schema.columns where table_name = 'staff';

-- Aquellas usadas para insertar, modificar y eliminar un Customer, Staff y Actor --
select * from customer;
select * from rental;
select * from address;
select * from staff;
-- solo 2 id: 1, 2 --
select * from store;

-- customer --
-- customer_id, store_id, first_name, last_name, email, adress_id, activebool, create_date, last_update, active --
-- insert --
insert into customer values 
	(default, 2, 'felipe', 'gutierrez', 'felipe@gmail.com', 44, true, default, localtimestamp, 1),
	(default, 1, 'Ivan', 'Verdugo', 'ivan.verdugo@sakilacustomer.org', 1, true, '2023-05-18', localtimestamp, 1),
	(default, 1, 'Alfredo', 'Marileo', 'alfredo.marileo@sakilacustomer.org', 251, true, '2023-05-18', localtimestamp, 1)

-- update --
select * from customer order by customer_id desc
update customer set email = 'felipe@hotmail.com' where customer_id = 603;

-- delete --
delete from customer where customer_id = 603;

-- staff --
-- staff_id, first_name, last_name, address_id, email, store_id, username, password, last_update, picture --
select * from staff;
insert into staff values (default, 'Felipe', 'Gutierrez', 3, 'Felip.Gutierrez@sakilastaff.com', 2, true, 'Felipe', 'felipe123', localtimestamp, '')
update staff set email = 'Felipe.Gutierrez@sakilastaff.com' where staff_id = 4;
delete from staff where staff_id = 4;

-- actor --
-- actor_id, first_name, last_name, last_update
select * from actor where first_name = 'Michael';
select * from actor order by actor_id desc;

insert into actor values (default, 'Michael', 'Zera', localtimestamp);
update actor set last_name= 'Cera' where actor_id = 201;
delete from actor where actor_id = 201;

-- Listar todas las “rental” con los datos del “customer” dado un año y mes --
select r.rental_id, r.rental_date, c.customer_id, c.first_name, c.last_name from rental r
join customer c
on r.customer_id= c.customer_id
where
	extract(year from rental_date) = 2005 and
	extract(month from rental_date) = 5
	
-- Listar Número, Fecha (payment_date) y Total (amount) de todas las “payment” --
select * from payment
select payment_id, payment_date, amount from payment order by payment_date desc;
select count(*) as cantidad, payment_date, sum(amount) 
	from payment 
	group by payment_date 
	order by count(*) desc;

-- Listar todas las “film” del año 2006 que contengan un (rental_rate) mayor a 4.0. --
select * from film where release_year = 2006 and rental_rate >= 4;

-- Realiza un Diccionario de datos que contenga el nombre de las tablas y columnas, --
-- si éstas pueden ser nulas, y su tipo de dato correspondiente. --
SELECT
    t1.TABLE_NAME AS tabla_nombre,
    t1.COLUMN_NAME AS columna_nombre,
    t1.COLUMN_DEFAULT AS columna_defecto,
    t1.IS_NULLABLE AS columna_nulo,
    t1.DATA_TYPE AS columna_tipo_dato
FROM 
    INFORMATION_SCHEMA.COLUMNS t1
    INNER JOIN PG_CLASS t2 ON (t2.RELNAME = t1.TABLE_NAME)
WHERE 
    t1.TABLE_SCHEMA = 'public'
ORDER BY
    t1.TABLE_NAME;
```
