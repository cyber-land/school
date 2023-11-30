```SQL
CREATE TABLE Doctor (
doctor_id int,
firstname varchar(50) not null,
lastname varchar(50) not null,
address varchar(255),
city varchar(50),
state varchar(50),
birthday date,
CONSTRAINT PK_Doc PRIMARY KEY (doctor_id)
);

CREATE TABLE Hospital (
hospital_id int,
name varchar(50) not null,
address varchar(255),
city varchar(50),
state varchar(50),
bed_capacity int,
CONSTRAINT PK_H PRIMARY KEY (hospital_id)
);

CREATE TABLE Patient (
patient_id int,
firstname varchar(50) not null,
lastname varchar(50) not null,
birthday date,
gender char(1),
address varchar(255),
city varchar(50),
state varchar(50),
annual_revenue int,
family_doctor int,
CONSTRAINT PK_Pat PRIMARY KEY (patient_id)
);

CREATE TABLE FamilyDoctor (
doctor_id int,
firstname varchar(50) not null,
lastname varchar(50) not null,
address varchar(255),
city varchar(50),
state varchar(50),
CONSTRAINT PK_FamDoc PRIMARY KEY (doctor_id)
);

CREATE TABLE Visit (
visit_id int,
visit_date date,
patient_ID int,
doctor_ID int,
visit_type_id int,
hospital_id int,
amount_paid int,
CONSTRAINT PK_Vis PRIMARY KEY (visit_id)
);

CREATE TABLE Visit_type (
visit_type_id int,
description varchar(255),
CONSTRAINT PK_Vis PRIMARY KEY (visit_type_id)
);

alter table Visit
add constraint FK1_V1 foreign key (doctor_id)
references Doctor (doctor_id);

alter table Visit
add constraint FK1_V2 foreign key (patient_id)
references Patient (patient_id);

alter table Visit
add constraint FK1_V3 foreign key (hospital_id)
references Hospital (hospital_id);

alter table Visit
add constraint FK1_V4 foreign key (visit_type_id)
references Visit_type (visit_type_id);


insert into Doctor (doctor_id, firstname, lastname, address, city, state, birthday) values
(1, 'a', 'b', 'add1', 'trento', 'italy', '1989-08-22'),
(2, 'aa', 'bb', 'add2', 'trieste', 'italy', '1966-06-12'),
(3, 'aaa', 'bbb', 'add3', 'torino', 'italy', '1978-02-28'),
(4, 'aaaa', 'bbbb', 'add4', 'milano', 'italy', '1956-07-28'),
(5, 'aaaaa', 'bbbb', 'add5', 'trento', 'italy', '1961-04-23');

insert into Patient (patient_id, firstname, lastname, birthday, gender, city, state, annual_revenue, family_doctor) values
(11, 'a', 'b', '1999-01-22', 'M', 'trento', 'italy', 34000, 1),
(12, 'aa', 'bb', '1966-06-12', 'F', 'roma', 'italy', 45000, 4),
(13, 'aaa', 'bbb', '1968-12-31', 'F', 'palermo', 'italy', 32000, 3),
(14, 'aaaa', 'bbbb', '1976-03-01', 'M', 'trieste', 'italy', 28000, 1),
(15, 'aaaaa', 'bbbb', '1951-04-23', 'F', 'trento', 'italy', 61000, 4);

insert into Hospital (hospital_id, name, address, city, state, bed_capacity) values
(21, 'h', 'add21', 'trento', 'italy', 340),
(22, 'hh', 'add22', 'roma', 'italy', 450),
(23, 'hhh', 'add23', 'palermo', 'italy', 320),
(24, 'hhhh', 'add24', 'trieste', 'italy', 280),
(25, 'hhhhh', 'add25', 'trento', 'italy', 610),
(26, 'hhhhh', 'add25', 'london', 'uk', 170),
(27, 'hhhhh', 'add25', 'munich', 'germany', 220),
(28, 'hhhhh', 'add25', 'berlin', 'germany', 70);

INSERT into Visit_type (visit_type_id, description) values
(90, 'Ecografia'),
(91, 'Ragiografia torace'),
(92, 'Otorino'),
(93, 'Prelievo sangue');

insert into Visit (visit_id, visit_date, patient_id, doctor_id, visit_type_id, hospital_id, amount_paid) values
(41, '2022-01-22', 12, 1, 91, 21, 150),
(42, '2022-01-22', 11, 1, 92, 22, 250),
(43, '2022-01-22', 11, 5, 91, 28, 150),
(44, '2022-01-22', 12, 4, 92, 23, 250),
(45, '2022-01-22', 15, 1, 93, 24, 50),
(46, '2022-01-22', 15, 5, 91, 22, 150),
(47, '2022-01-22', 13, 1, 92, 23, 250),
(48, '2022-01-22', 14, 2, 93, 26, 50),
(49, '2022-01-22', 13, 1, 91, 27, 150);
```

# queries

```SQL
select distinct Doctor.doctor_id
from Doctor, Patient, Visit
where Doctor.doctor_id = Visit.doctor_ID
and Patient.patient_id = Visit.patient_ID
and Doctor.city = Patient.city;

SELECT DISTINCT d.doctor_id
FROM Doctor d
JOIN Visit v ON d.doctor_id = v.doctor_ID
JOIN Patient p ON v.patient_ID = p.patient_id
WHERE d.city = p.city;

-- Per ogni paziente, mostrare nome, cognome, numero di visite fatte e totale delle parcelle pagate

select firstname, lastname,
count(*), sum(amount_paid)
from Patient, Visit
where Patient.patient_id = Visit.patient_ID
group by patient_id;

select p.firstname,p.lastname, count(*),sum(amount_paid)
from Patient p
NATURAL JOIN Visit v
-- JOIN Visit v ON p.patient_id = v.patient_ID
group by p.patient_id;

--  Per ogni Paese di residenza dei pazienti, mostrare il numero di visite di tipo "Otorino"

select p.state, count(*) as visite,
GROUP_CONCAT(p.patient_id) as patient_ids
from Patient p, Visit v, Visit_type t
where p.patient_id = v.patient_ID
and t.visit_type_id = v.visit_type_id
and t.description = 'Otorino'
group by p.state;

-- Mostrare patient_id, reddito annuo e spese mediche di pazienti che nel 2022 hanno speso più dell'1% del loro reddito in visite mediche

select p.patient_id, p.annual_revenue,
sum(amount_paid) as spese_mediche
from Patient p, Visit v
where p.patient_ID = v.patient_ID
and YEAR(v.visit_date) = 2022
group by p.patient_id
having spese_mediche > (annual_revenue/100);

-- Selezionare l'ospedale con i maggiori ricavi per visite

select h.hospital_id
from Hospital h, Visit v
where h.hospital_id = v.hospital_id
group by h.hospital_id
order by sum(amount_paid) desc
limit 1;
```

