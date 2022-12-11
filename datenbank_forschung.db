-------- AUFGABE ------------

-- TABELLE LÖSCHEN --
DROP TABLE "orte";


-- 1. Database erstellen --
create database forschung;

exec sp_helpdb forschung;

-- 2. filegroups erstellen --

alter database forschung add filegroup passiv;

alter database forschung add filegroup aktiv;

alter database forschung add filegroup indexe;

alter database forschung add filegroup protokoll;

-- 3. files erstellen --

alter database forschung
add file (name = forschung_passiv,
filename = 'e:\daten\forschung_passiv.ndf',
size = 15 GB,
maxsize = 20 GB,
filegrowth = 4%)
to filegroup passiv;

alter database forschung
add file (name = forschung_aktiv,
filename = 'f:\daten\forschung_aktiv.ndf',
size = 15 GB,
maxsize = 20 GB,
filegrowth = 4%)
to filegroup aktiv;

alter database forschung
add file (name = forschung_indexe,
filename = 'g:\daten\forschung_indexe.ndf',
size = 1 GB,
maxsize = 20 GB,
filegrowth = 4%)
to filegroup indexe;

alter database forschung
add file (name = forschung_protokoll,
filename = 'j:\protokoll\forschung_protokoll.ndf',
size = 3 GB,
maxsize = 20 GB,
filegrowth = 10%)
to filegroup protokoll;

-- 4. tabelle erstellen --
-- a) orte --
create table orte 
(ortid nchar(3) not null constraint ortid_ps primary key
constraint ortid_chk check(ortid like '0%' and cast(substring (ortid,2,2) as int)between 1 and 999),
plz int null constraint plz_chk check(plz between 01000 and 99999),
ortsname nvarchar(50) null constraint ortsname_chk check(ortsname in ('Weimar', 'Erfurt','Jena','Gotha','Suhl','Nordhausen','S�mmerda')));

select * from orte;
insert into orte values('1','99423','Weimar'); --> es geht nicht - ortid '0%' --
insert into orte values('2','00999','Erfurt'); --> es geht nicht - plz <01000 --
insert into orte values('001','99423','Berlin'); --> es geht nicht - Berlin ist falsch.
insert into orte values('ABC', '99084', 'Erfurt'); --> geht es nicht.
insert into orte values('002','ABCDE','Erfurt'); --> geht es nicht.
insert into orte values('001','99423','Weimar'); --> geht !
insert into orte values('002','99084','Erfurt');
insert into orte values('003','50001','Jena');
insert into orte values('004','66050','Gotha');
insert into orte values('005','33033','Suhl');
insert into orte values('006','77000','Nordhausen');
insert into orte values('007','15099','S�mmerda');

-- primary key / schon gemacht ! /--
alter table orte
add constraint ortid_pk
primary key(ortid);

-- b) abteilung --
create table abteilung
(abt_nr nchar(3) not null constraint abt_nr_ps primary key
constraint abt_nr_check check(abt_nr like 'a%' and cast(substring(abt_nr,2,2)as int) between 1 and 50),
abt_name nvarchar(50) not null constraint abt_name_chk check(abt_name like '[A-Z]%'),
ortid nchar(3) not null constraint ortid_fs references orte(ortid) on update cascade);				-- Fremdschl�ssel --

select * from abteilung;

-- checkpoint --
insert into abteilung values('a1', 'design', '001');
insert into abteilung values('a1', 'sound', '1'); ---> gehts nicht - ortid = '0%'
insert into abteilung values('a53', 'sound', '002'); ---> FEHLER ! = die Tabelle funktioniert (abt_nr max 50) --
insert into abteilung values('a2', 'sound', '02');  -->  geht nicht - ortid 02 existiert nicht.
insert into abteilung values('a3', 'graphic', '3'); --> geht nicht ('3' statt '003').
insert into abteilung values('a2', 'sound', '002'); ---> geht.
insert into abteilung values('a3', 'graphic', '003'); ---> geht.
select * from abteilung;

-- primary key - schon gemacht - --
alter table abteilung
add constraint abt_nr_pk
primary key(abt_nr);

-- c) mitarbeiter --
create table mitarbeiter
(m_nr int not null constraint anr_ps primary key
constraint m_nr_chk check (m_nr between 1000 and 9999),
m_name nvarchar(50) not null constraint m_name_chk check(m_name like '[A-Z]%'),
m_vorname nvarchar(50) not null constraint m_vorname_chk check(m_vorname like '[A-Z]%'),
ortid nchar(3) not null constraint ortid_mit_fs references orte(ortid) on update cascade,
strasse nvarchar(50) not null,
geb_dat date not null,
abt_nr nchar(3) not null constraint abt_nr_fs references abteilung(abt_nr)
constraint abt_nr_chk check(abt_nr like 'a%' and cast(substring(abt_nr,2,2)as int) between 1 and 50));


-- CHECK- Einschr�nkung l�schen -
ALTER TABLE dbo.mitarbeiter
DROP CONSTRAINT m_nr_chk;

-- checkpoint --
select * from mitarbeiter;
insert into mitarbeiter values('1000', 'M�ller', 'Michael', '001', 'Robert-Koch-Stra�e 2', '01.02.1990', 'a1'); --> geht
insert into mitarbeiter values('900', 'Schmidt', 'Julia', '002', 'Goethe Stra�e 3', '03.04.1991', 'a2'); --> geht es nicht (900)
insert into mitarbeiter values('ABCD', 'Schmidt', 'Julia', '002', 'Goethe Stra�e 3', '03.04.1991', 'a2'); --> geht es nicht (ABCD);

-- d) projekt
create table projekt
(pr_nr nchar(4) not null constraint pr_nr_ps primary key
constraint pr_nr_chk check(pr_nr like 'p%' and cast(substring(pr_nr,3,3)as int) between 1 and 150),
pr_name varchar(50) not null constraint pr_name_chk check(pr_name like '[A-Z]%'),
mittel int null constraint mittel_chk check(mittel between 1 and 2000000));

select * from projekt;
insert into projekt values ('z001','Projekt f�r Microsoft','100000'); --> Fehler z001
insert into projekt values ('p001','Projekt f�r Microsoft','100000�'); --> Fehler �
insert into projekt values ('p001','Projekt f�r Microsoft','100000'); --> geht

-- e) arbeiten --
create table arbeiten
(m_nr int not null constraint arb_m_nr_fs references dbo.mitarbeiter(m_nr) on update cascade
constraint arb_m_nr_chk check (m_nr between 1000 and 9999),
pr_nr nchar(4) not null constraint arb_pr_nr_fs references dbo.projekt (pr_nr) on update cascade
constraint arb_pr_nr_chk check(pr_nr like 'p%' and cast(substring(pr_nr,3,3)as int) between 1 and 150),
aufgabe nchar(100) not null constraint aufgabe_chk check(aufgabe like '[A-Z]%'),
einst_dat datetime not null);

-- 2 Primary keys in der Tabelle 'arbeiten' erstellen --

alter table arbeiten add constraint arbeiten_pk primary key (m_nr, pr_nr); 

select * from arbeiten;
insert into arbeiten values('1005','p001','Ein Database f�r Microsoft erstellen','18.06.2022');

drop table "arbeiten";
SET LANGUAGE polish;









