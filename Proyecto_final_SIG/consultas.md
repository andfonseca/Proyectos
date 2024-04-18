# Consultas del proyecto final de SIG - CentroGeo
Como le hago para trabajar
mejor


Otros
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
```
-- Validación dentro de los límites administrativos de Medellín
	-- Validación de los datos del SIMMA
```
```sql
create table simma_v as
select s.geom, id, inv_movimi, tipo, subtipo, a. altura, dn
from simma s
join altura a
on st_intersects(s.geom, a.geom)
where a.altura >= 1.500;

create index idx_geom_on_simma_v
on simma_v
using GIST (geom);

	-- Validación de los datos de DI
create table di_v as
select d. *, a.dn
from deslizamientos d
join altura a
on st_intersects(d.geom, a.geom)
where a.dn >= 1.500;

create index idx_geom_on_di_v
on di_v
using GIST (geom);
```
`-- Creación de buffers para identificar coincidencias
	-- Inicialmente, verificamos los elementos de di_v que están en cercanías a los 
	-- elementos de simma_v
		-- buffer inicial`
```sql
select id, ST_Buffer(geom, 1000) AS geom_buffer
from simma_v;
		-- Verificación con st_within
select di.id
from di_v di
join (
  select id, st_buffer(geom, 1000) as geom_buffer
  from simma_v
) as buffers
on ST_Within(di.geom, buffers.geom_buffer);

		-- Verificación de elementos por fuera del buffer
create table deslizamientos_v as
select di.*
from di_v di
left join (
    select st_buffer(geom, 1000) as geom_buffer
    from simma_v
) as buffers
on st_within(di.geom, buffers.geom_buffer)
where buffers.geom_buffer is null;

create index idx_geom_on_deslizamientos_v
on deslizamientos_v
using GIST (geom);
```
`-- A continuación, hacemos la clusterización de los datos en CrimeStat
	-- En el software CrimeStat, cargamos la capa de deslizamientos validadeos, o 
	-- deslizamientos_v. Utilizaremos el método de Nearest Neighbor Hierarchical
	-- Spatial Clustering (NNH) para generar una capa Convex Hull de agrupación para
	-- nuestros puntos. Los criterios son: Agrupar grupos de 3 puntos que 
	-- se encuentren en una proximidad de 500 metros. La capa resultante es CNNH1,
	-- la cual cargamos en QGIS y desde allí la vinculamos a nuestra base de datos.

-- Para finalizar la validación, identificaremos los centroides de los convex hull
	-- generados.

	-- Creamos la columna de centroide en CNNH1`
```sql
alter table "CNNH1" 
add column centroide GEOMETRY(Point);

	-- Generamos los centroides con la función st_centroid
update "CNNH1"
set centroide = ST_Centroid(geom);
```
`-- A continuación, generaremos buffers de 500 metros alrededor de los centroides 
	-- generados`
```sql
create table eventos_posibles as
select id, st_buffer(centroide, 500) as buffer
from "CNNH1";

select UpdateGeometrySRID('eventos_posibles', 'buffer', 3857);

create index idx_eventos_posibles on eventos_posibles using GIST (buffer);
```
`-- Para terminar la validación, crearemos una tabla para los buffers de los datos
	-- oficiales sobre deslizamientos, para identificar cómo se relacionan 
	-- en el mapa`
 ```sql
create table eventos_confirmados as
select id, st_buffer(geom, 1000) as buffer
from simma_v;

select UpdateGeometrySRID('eventos_confirmados', 'buffer', 3857);

create index idx_eventos_confirmados on eventos_confirmados using GIST (buffer);
```
`-- Para efectos de pulido y agrupación, construimos dos tablas que contiene
	-- los puntos de desinventar que coinciden con los buffers tanto de SIMMA 
	-- como de DI.`
 ```sql
create table eventos_confirmados_puntos as
select dv. *
from di_v as dv
join eventos_confirmados as ec
on st_within(dv.geom, ec.buffer);

create index idx_eventos_confirmados_puntos on eventos_confirmados_puntos using GIST (geom);

create table eventos_posibles_puntos as
select des. *
from deslizamientos_v as des
join eventos_posibles as ep
on st_within(des.geom, ep.buffer);

create index idx_eventos_posibles_puntos on eventos_posibles_puntos using GIST (geom);
```
`-- Adicionalmente, para pulir mejor la información, haremos un último join entre
	-- los buffers y los suelos con condición de amenaza alta por movimientos en masa

	-- Primero creamos una tabla nueva de eventos totales`
 ```sql
select * into eventos_total from eventos_confirmados
union
select * from eventos_posibles;

	-- Y luego usamos la función st_intersects para identificar la interacción entre
		-- los polígonos de buffers y de amenazas.

alter table "Amenaza_por_Movimientos_en_Masa" 
   alter column geom
   type Geometry(MultiPolygon, 3857)
   using ST_Transform(geom, 3857);

create table suelo_amenaza_eventos as
select apmem. *
from "Amenaza_por_Movimientos_en_Masa" apmem
join eventos_total et
on st_intersects(et.buffer, apmem.geom)
where apmem.grado_amen = 'Alta';

create index idx_suelo_amenaza_eventos on suelo_amenaza_eventos using GIST (geom);
```
`-- También crearemos una tabla para generar los polígonos de las zonas con condición
	-- de amenaza alta por movimientos en masa que NO se intersectan con nuestros datos.`
 ```sql
create table suelo_amenaza_no_eventos as
select apmem.*
from "Amenaza_por_Movimientos_en_Masa" apmem
where apmem.grado_amen = 'Alta'
and not exists (
    select 1
    from eventos_total et
    where st_intersects(et.buffer, apmem.geom)
);

create index idx_suelo_amenaza_no_eventos on suelo_amenaza_no_eventos using GIST (geom);

-- También podemos contabilizar los eventos en tablas ya existentes
alter table simma_v
add column num_eventos INT;

update simma_v
set num_eventos = (
    select COUNT(*)
    from eventos_confirmados_puntos ecp
    where st_within(ecp.geom, ec.buffer)
    and simma_v.id = ec.id 
)
from eventos_confirmados ec
where simma_v.id = ec.id;

alter table "CNNH1" 
add column num_eventos int;

update "CNNH1" 
set num_eventos = (
    select count(*)
    from eventos_posibles_puntos epp
    where st_within(epp.geom, ep.buffer)
    and "CNNH1".id = ep.id 
)
from eventos_posibles ep
where "CNNH1".id = ep.id;
```
`-- Y en las comunas y corregimientos de la ciudad, para identificar según la
	-- estratificación socioeconómica los sectores más vulnerables según el nivel
	-- de registros de afectaciones.`
```sql
alter table limite_administrativo 
add column num_eventos_posibles int;

select la.nombre, count(epp.id) as puntos_dentro
from limite_administrativo la
left join eventos_posibles_puntos epp on st_contains(la.geom, epp.geom)
group by la.nombre;

update limite_administrativo la
set num_eventos_posibles = (
    select COUNT(epp.id)
    from eventos_posibles_puntos epp
    where st_contains(la.geom, epp.geom)
)
where la.geom is not null;
```
`-- Por último, contabilizamos los eventos por comuna. Para hacer esto, contaremos
	-- tanto los que cayeron dentro del buffer de eventos oficiales como en los
	-- de eventos no oficiales y los sumaremos posteriormente`
 ```sql
alter table limite_administrativo 
add column num_eventos_confirmados int;

select la.nombre, COUNT(ecp.id) as puntos_dentro
from limite_administrativo la
left join eventos_confirmados_puntos ecp on st_contains(la.geom, ecp.geom)
group by la.nombre;

update limite_administrativo la
set num_eventos_confirmados = (
    select count(ecp.id)
    from eventos_confirmados_puntos ecp
    where st_contains(la.geom, ecp.geom)
)
where la.geom is not null;

alter table limite_administrativo 
add column num_eventos_total int;

update limite_administrativo
set num_eventos_total = num_eventos_confirmados + num_eventos_posibles;
```
