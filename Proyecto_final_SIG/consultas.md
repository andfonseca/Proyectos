# Consultas del proyecto finnal de SIG - CentroGeo

## Códigos utilizados para depurar base de datos
`-- Crear extensiones
create extension postgis;`

```sql
-- Eliminar columnas que no tienen datos
ALTER TABLE desinventar
DROP COLUMN magnitude;

ALTER TABLE desinventar 
DROP COLUMN disasterid;
```
`-- Asignar el dato a las columnas y quitar datos extraños`
```sql
UPDATE desinventar 
SET other  = 1
WHERE other = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN other TYPE INTEGER
USING other::INTEGER;

UPDATE desinventar 
SET health  = 1
WHERE health = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN health TYPE INTEGER
USING health::INTEGER;

UPDATE desinventar 
SET energy  = 1
WHERE energy  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN energy TYPE INTEGER
USING energy::INTEGER;

UPDATE desinventar 
SET education  = 1
WHERE education  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN education TYPE INTEGER
USING education::INTEGER;

UPDATE desinventar 
SET sewerage  = 1
WHERE sewerage  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN sewerage TYPE INTEGER
USING sewerage::INTEGER;

UPDATE desinventar 
SET aqueduct  = 1
WHERE aqueduct  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN aqueduct TYPE INTEGER
USING aqueduct::INTEGER;

UPDATE desinventar 
SET transport  = 1
WHERE transport  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN transport TYPE INTEGER
USING transport::INTEGER;

UPDATE desinventar 
SET "other loss"  = 1
WHERE "other loss"  = 'YES';
```
```sql
ALTER TABLE desinventar 
RENAME COLUMN "loss val_1" TO loss_val_1;

ALTER TABLE desinventar 
RENAME COLUMN "loss value" TO loss_value;

ALTER TABLE desinventar 
RENAME COLUMN "health cen" TO health_cen;

ALTER TABLE desinventar 
RENAME COLUMN "routes aff" TO routes_aff;

ALTER TABLE desinventar 
RENAME COLUMN "crops and" TO crops_and;
```sql
ALTER TABLE desinventar 
RENAME COLUMN "homes affe" TO homes_affe;

UPDATE desinventar 
SET homes_affe  = 1
WHERE homes_affe  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN homes_affe TYPE INTEGER
USING homes_affe::INTEGER;
```
```sql
ALTER TABLE desinventar 
RENAME COLUMN "homes dest" TO homes_dest;

UPDATE desinventar 
SET homes_dest  = 1
WHERE homes_dest  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN homes_dest TYPE INTEGER
USING homes_dest::INTEGER;

UPDATE desinventar 
SET relocated = 1
WHERE relocated = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN relocated TYPE INTEGER
USING relocated::INTEGER;

UPDATE desinventar 
SET evacuees = 1
WHERE evacuees = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN evacuees TYPE INTEGER
USING evacuees::INTEGER;

UPDATE desinventar 
SET affected = 1
WHERE affected = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN affected TYPE INTEGER
USING affected::INTEGER;

UPDATE desinventar 
SET victims = 1
WHERE victims = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN victims TYPE INTEGER
USING victims::INTEGER;

ALTER TABLE desinventar 
RENAME COLUMN "wounded; s" TO wounded_s;

UPDATE desinventar 
SET wounded_s  = 1
WHERE wounded_s  = 'YES';
```
```sql
ALTER TABLE desinventar
ALTER COLUMN wounded_s TYPE INTEGER
USING wounded_s::INTEGER;
```
`-- Crear columna de id para comuna en los puntos de desinventar`
```sql
alter table desinventar
add column id_comuna integer;
```
`-- Crear columna con el nombre de la columna en los puntos de desinventar`
```sql
alter table desinventar
add column nom_comuna varchar;
```
`-- Crear columna amaneza en los puntos de desinventar`
```sql
alter table desinventar
add column grado_amenaza varchar;
```
`-- Crear columna de id para barrio en los puntos de desinventar`
```sql
alter table desinventar
add column id_barrio varchar;
```
`-- Crear columna de nombre para barrio en los puntos de desinventar`
```sql
alter table desinventar
add column nom_barrio varchar;
```
`-- Crear columna de estrato para barrio en los puntos de desinventar`
```sql
alter table desinventar
add column estrato integer;
```
`-- Crear columna de uso del suelo en los puntos de desinventar`
```sql
alter table desinventar
add column suelo varchar;
```
`-- Crear columna de altura en los puntos de desinventar`
```sql
alter table desinventar
add column altura integer;
```
`-- Crear columna amaneza en los puntos de desinventar`
```sql
alter table barrio_vereda_2
add column grado_amenaza varchar;
```
`-- Crear columna de uso del suelo los barrios vereda`
```sql
alter table barrio_vereda_2
add column suelo varchar;
```
`-- Crear columna de altura en los barrios veredas`
```sql
alter table barrio_vereda_2
add column altura integer;
```
`-- Actualizar código del barrio en los puntos de desinventar`
```sql
update desinventar des
set id_barrio = foo.codigo
from (
	SELECT  de.id,
			la.codigo,
			la.nom_barrio,
			la.nombre_com
	from desinventar as de
	join (select  codigo as codigo,
					nombre as nom_barrio,
					nombre_com,
					geom 
			from "Barrio_Vereda") as la
	on st_intersects(de.geom, la.geom)) as foo
where des.id = foo.id;
```
`-- Actualizar nombre del barrio en los puntos de desinventar`
```sql
update desinventar des
set nom_barrio = foo.nom_barrio
from (
	SELECT  de.id,
			la.codigo,
			la.nom_barrio
	from desinventar as de
	join (select  codigo as codigo,
					nombre as nom_barrio,
					geom 
			from barrio_vereda_2) as la
	on st_intersects(de.geom, la.geom)) as foo
where des.id = foo.id;
```
`-- Actualizar estrato del barrio en los puntos de desinventar`
```sql
update desinventar des
set estrato = foo.estrato
from (
	SELECT  de.id,
			la.codigo,
			la.estrato
	from desinventar as de
	join (select  codigo,
					estrato,
					geom 
			from barrio_vereda_2) as la
	on st_intersects(de.geom, la.geom)) as foo
where des.id = foo.id;
```
`-- Actualizar id de la comuna en los puntos de desinventar`
```sql
update desinventar des
set id_comuna  = foo.comuna
from (
	SELECT  de.id,
			la.comuna::integer,
			la.nombre
	from desinventar as de
	join (select  comuna,
					nombre,
					geom 
			from limite_administrativo) as la
	on st_intersects(de.geom, la.geom)) as foo
where des.id = foo.id;
```
`-- Actualizar el grado de amenaza en los puntos de desinventar`
```sql
update desinventar des
set grado_amenaza = foo.gram
from (
	SELECT  de.id,
			gram
	from desinventar as de
	join (select  grado_amen as gram,
					geom 
			from amenaza_por_movimientos_en_masa) as am
	on st_intersects(de.geom, am.geom)) as foo
where des.id = foo.id;
```
`-- Actualizar la elevación en los puntos de desinventar`
```sql
update desinventar des
set altura = foo.dn
from (
	SELECT  de.id,
			dn
	from desinventar as de
	join (select  dn,
					geom 
			from "Altura") as a
	on st_intersects(de.geom, a.geom)) as foo
where des.id = foo.id;
```
`-- Actualizar el tipo de suelo en los puntos de desinventar`
```sql
update desinventar des
set suelo = foo.clase_suel
from (
	SELECT  de.id,
			clase_suel
	from desinventar as de
	join (select  clase_suel,
					geom 
			from "Clasificacion_del_Suelo") as cs
	on st_intersects(de.geom, cs.geom)) as foo
where des.id = foo.id;
```
`-- Nueva tabla de datos organizados para desinventar`
```sql
create table desinventar_2 AS
select id,
		id_barrio, nom_barrio,
		id_comuna, nom_comuna,
		grado_amenaza, deaths, wounded_s, affected, "type of ca" as causa, observatio as obs,
		estrato, suelo, altura,
		"start date" as date, place,
		coordinate as lat_y, coordina_1 as lon_x, geom,
		serial, sources	 		
from desinventar d;
```
`-- Nueva tabla de datos organizados para barrios veredas`
```sql
create table num_eventos_barrio as
select bv.nombre as nombre_barrio, bv.geom_bv, bv.nombre_com,
bv.estrato, COUNT(di.id) as cantidad_eventos
from barrio_vereda as bv
join desinventar as di
on ST_Contains(bv.geom_bv, di.geom)
group by bv.nombre, bv.nombre_com, bv.geom_bv, bv.estrato
order by cantidad_eventos desc;
```
`-- Actualizar el grado de amenaza en los barrios vereda`
```sql
update barrio_vereda_2 as bve
set grado_amenaza = foo.gram
from (
	SELECT  bv.id,
			bv.nombre,
			bv.codigo,
			am.gram,
			COUNT(*) as cnt,
	        ROW_NUMBER() OVER (PARTITION BY bv.id ORDER BY COUNT(*) DESC) as clas -- Ordena los datos y les asigna un número de clasificacion
	from barrio_vereda_2 as bv
	join (select  grado_amen as gram,
					geom
	from amenaza_por_movimientos_en_masa) as am
	on ST_intersects(bv.geom, am.geom)
	-- where bv.codigo = '1103'
	group by bv.id, am.gram) as foo
where bve.id = foo.id and foo.clas = 1;
 --where foo.clas = 1:  Seleccionar los datos con mayor número de datos, y que tiene el puesto o clasificacion 1
```
`-- Actualizar la alatura en los barrios vereda`
```sql
update barrio_vereda_2 as bve
set altura = foo.dn
from (
	SELECT  bv.id,
			bv.nombre,
			bv.codigo,
			a.dn,
			COUNT(*) as cnt,
	        ROW_NUMBER() OVER (PARTITION BY bv.id ORDER BY COUNT(*) DESC) as clas -- Ordena los datos y les asigna un número de clasificacion
	from barrio_vereda_2 as bv
	join (select  dn,
					geom
	from "Altura") as a
	on ST_intersects(bv.geom, a.geom)
	-- where bv.codigo = '1103'
	group by bv.id, a.dn) as foo
where bve.id = foo.id and foo.clas = 1;
```
`-- Actualizar suelo en los barrios vereda`
```sql
update barrio_vereda_2 as bve
set suelo = foo.clase
from (
	SELECT  bv.id,
			bv.nombre,
			bv.codigo,
			cs.clase_suel as clase,
			COUNT(*) as cnt,
	        ROW_NUMBER() OVER (PARTITION BY bv.id ORDER BY COUNT(*) DESC) as clas -- Ordena los datos y les asigna un número de clasificacion
	from barrio_vereda_2 as bv
	join (select  clase_suel,
					geom
	from "Clasificacion_del_Suelo") as cs
	on ST_intersects(bv.geom, cs.geom)
	-- where bv.codigo = '1103'
	group by bv.id, cs.clase_suel) as foo
where bve.id = foo.id and foo.clas = 1;
```
`-- Borrar los datos de una columna`
```sql
UPDATE barrio_vereda_2 
SET grado_amenaza = NULL;
```
## Códigos utilizados para crear mapas
