# SQL-Projekt

DROP TYPE IF EXISTS Status_Customer_Order cascade;

CREATE TYPE Status_Customer_Order AS ENUM ('offen', 'inBearbeitung', 'erledigt');
 
DROP TYPE IF EXISTS Status_Component cascade;
 
CREATE TYPE Status_Component AS ENUM ('offen', 'inAuftrag', 'inAuftrag-überzogen', 'geliefert');
 
DROP TYPE IF EXISTS Status_Production_Station cascade;
 
CREATE TYPE Status_Production_Station AS ENUM ('frei', 'belegt');


DROP TABLE IF EXISTS Customer cascade;

CREATE TABLE Customer
(
	Id int primary key,
	Customer_Name varchar(255) not null
);

DROP INDEX IF EXISTS idx_Customer_Id;

CREATE INDEX idx_Customer_Id on Customer(Id);

DROP TABLE IF EXISTS Customer_Order cascade;

CREATE TABLE Customer_Order
(
	Id int primary key,
	Customer_Id int not null,
	Order_Date timestamp default NOW(),
	Delivery_Date timestamp default null,
	Status Status_Customer_Order default 'offen',
	FOREIGN KEY (Customer_Id) 
	REFERENCES Customer (Id) 
);

DROP INDEX IF EXISTS idx_Customer_Order_Id;

CREATE INDEX idx_Customer_Order_Id on Customer_Order(Id);


DROP TABLE IF EXISTS Machine cascade;

CREATE TABLE Machine
(
	Id int primary key,
	Machine_Name varchar(128) not null
);

DROP INDEX IF EXISTS idx_Machine_Id;

CREATE INDEX idx_Machine_Id on Machine(Id);


DROP TABLE IF EXISTS Order_Machine cascade;

CREATE TABLE Order_Machine
(
	Customer_Order_Id int not null,
	Machine_Id int not null,
	Quantity int,
	FOREIGN KEY (Customer_Order_Id)
	REFERENCES Customer_Order (Id),
	FOREIGN KEY (Machine_Id)
	REFERENCES Machine (Id)
);

DROP TABLE IF EXISTS Component  cascade;

CREATE TABLE Component
(
	Id int primary key,
	Component_Name varchar(128),
	Extern boolean,
	Supplier_Name varchar(128)
);

DROP INDEX IF EXISTS idx_Component_Id;

CREATE INDEX idx_Component_Id on Component(Id);


DROP TABLE IF EXISTS Machine_Component cascade;

CREATE TABLE Machine_Component
(
	Machine_Id int not null,
	Component_Id int not null,
	FOREIGN KEY (Machine_Id)
	REFERENCES Machine (Id),
	FOREIGN KEY (Component_Id)
	REFERENCES Component (Id)
);

DROP TABLE IF EXISTS Production_Station cascade;

CREATE TABLE Production_Station
(
	Id int primary key,
	Status Status_Production_Station default 'frei'
);

DROP INDEX IF EXISTS idx_Production_Station_Id;

CREATE INDEX idx_Production_Station_Id on Production_Station(Id);


DROP TABLE IF EXISTS Production_Order cascade;

CREATE TABLE Production_Order
(
	Id int primary key,
	Machine_Id int,
	Customer_Order_Id int,
	Production_Station_Id int,
	Start_Date timestamp,
	Completion_Date timestamp,
	FOREIGN KEY (Machine_Id)
	REFERENCES Machine (Id),
	FOREIGN KEY (Customer_Order_Id)
	REFERENCES Customer_Order (Id),
	FOREIGN KEY (Production_Station_Id)
	REFERENCES Production_Station (Id)
);

DROP INDEX IF EXISTS idx_Production_Order_Id;

CREATE INDEX idx_Production_Order_Id on Production_Order(Id);


DROP TABLE IF EXISTS Component_Delivery cascade;

CREATE TABLE Component_Delivery 
(
	Component_Id int,
	Production_Order_Id int,
	Order_Date timestamp,
	Delivery_Date timestamp,
	Status Status_Component default 'offen',
	FOREIGN KEY (Component_Id)
	REFERENCES Component (Id),
	FOREIGN KEY (Production_Order_Id)
	REFERENCES Production_Order (Id)
);

INSERT INTO Production_Station (Id, Status)
  values  (1, 'belegt'),
	  (2, 'belegt'),
	  (3, 'belegt'),  
	  (4, 'belegt'),
	  (5, 'belegt'),
	  (6, 'belegt'),  
	  (7, 'belegt'),
	  (8, 'frei'),
	  (9, 'frei'), 
	  (10, 'frei'); 

INSERT INTO Component (Id, Component_Name, Extern, Supplier_Name)
  values (1, 'Komponente #1', true, 'Lieferant #1'),
  (2, 'Komponente #2', false, null),
  (3, 'Komponente #3', false, null),
  (4, 'Komponente #4', true, 'Lieferant #2'),
  (5, 'Komponente #5', false, null),
  (6, 'Komponente #6', false, null),
  (7, 'Komponente #7', false, null),
  (8, 'Komponente #8', true, 'Lieferant #3'),
  (9, 'Komponente #9', false, null),
  (10, 'Komponente #10', false, null),
  (11, 'Komponente #11', true, 'Lieferant #4'),
  (12, 'Komponente #12', true, 'Lieferant #5'),
  (13, 'Komponente #13', false, null),
  (14, 'Komponente #14', true, 'Lieferant #6'),
  (15, 'Komponente #15', false, null),
  (16, 'Komponente #16', false, null),
  (17, 'Komponente #17', false, null),
  (18, 'Komponente #18', true, 'Lieferant #7'),
  (19, 'Komponente #19', false, null),
  (20, 'Komponente #20', false, null),
  (21, 'Komponente #21', true, 'Lieferant #8'),
  (22, 'Komponente #22', true, 'Lieferant #9'),
  (23, 'Komponente #23', false, null),
  (24, 'Komponente #24', true, 'Lieferant #10'),
  (25, 'Komponente #25', false, null);
	   
INSERT INTO Machine (Id, Machine_Name)
  values (1,'Maschine #1'),
  (2,'Maschine #2'),
  (3,'Maschine #3'),
  (4,'Maschine #4'),
  (5,'Maschine #5'),
  (6,'Maschine #6'),
  (7,'Maschine #7'),
  (8,'Maschine #8'),
  (9,'Maschine #9'),
  (10,'Maschine #10'); 

INSERT INTO Customer (Id, Customer_Name)
  values (1, 'Kunde #1'),
  (2, 'Kunde #2'),
  (3, 'Kunde #3'),
  (4, 'Kunde #4'),
  (5, 'Kunde #5'),
  (6, 'Kunde #6'),
  (7, 'Kunde #7'),
  (8, 'Kunde #8'),
  (9, 'Kunde #9'),
  (10, 'Kunde #10');
  
 INSERT INTO Customer_Order (Id, Customer_Id, Order_Date, Delivery_Date, Status)
  values (1, 1, now() - interval '110' day, now() - interval '20' day, 'erledigt'),
  (2, 2, now() - interval '100' day, now() - interval '10' day, 'erledigt'),
  (3, 3, now() - interval '95' day, now() - interval '5' day, 'erledigt'),
  (4, 4, now() - interval '85' day, null, 'inBearbeitung'),
  (5, 5, now() - interval '80' day, null, 'inBearbeitung'),
  (6, 6, now() - interval '79' day, null, 'inBearbeitung'),
  (7, 7, now() - interval '43' day, null, 'offen'),
  (8, 8, now() - interval '20' day, null, 'offen'),
  (9, 9, now() - interval '15' day, null, 'offen'),
  (10, 10, now(), null, 'offen');
  
INSERT INTO Production_Order (Id, Machine_Id, Customer_Order_Id, Production_Station_Id, Start_Date, Completion_Date)
  values
  (1, 1, 1, 1, now() - interval '34' day, now() - interval '20' day), 
  (2, 1, 1, 2, now() - interval '34' day, now() - interval '20' day),
  (3, 2, 1, 3, now() - interval '34' day, now() - interval '20' day),
  (4, 3, 2, 4, now() - interval '24' day, now() - interval '10' day),
  (5, 4, 2, 5, now() - interval '24' day, now() - interval '10' day),
  (6, 5, 2, 6, now() - interval '24' day, now() - interval '10' day),
  (7, 6, 3, 7, now() - interval '19' day, now() - interval '5' day),
  (8, 7, 3, 8, now() - interval '19' day, now() - interval '5' day),
  (9, 8, 3, 9, now() - interval '19' day, now() - interval '5' day),
  (10, 9, 4, 10, now() - interval '9' day, null),
  (11, 10, 4, 1, now() - interval '9' day, null),
  (12, 1, 4, 2, now() - interval '9' day, null),
  (13, 2, 5, 3, now() - interval '4' day, null),
  (14, 3, 5, 4, now() - interval '4' day, null),
  (15, 4, 5, 5, now() - interval '4' day, null),
  (16, 5, 6, 6, now() - interval '3' day, null),
  (17, 6, 6, null, null, null),
  (18, 7, 6, null, null, null),
  (19, 8, 7, null, null, null),
  (20, 9, 7, null, null, null),
  (21, 9, 7, null, null, null),
  (22, 2, 8, null, null, null),
  (23, 1, 8, null, null, null),
  (24, 9, 8, null, null, null),
  (25, 3, 9, null, null, null),
  (26, 4, 9, null, null, null),
  (27, 5, 9, null, null, null),
  (28, 6, 10, null, null, null),
  (29, 7, 10, null, null, null),
  (30, 8, 10, null, null, null);
  

INSERT INTO Component_Delivery (Component_Id, Production_Order_Id, Order_Date, Delivery_Date, Status)
  values
  (1, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (2, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (3, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (4, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (5, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (6, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (7, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (8, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (9, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (10, 1, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  
  (1, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (2, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (3, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (4, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (5, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (6, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (7, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (8, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (9, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (10, 2, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  
  (2, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (4, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (6, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (8, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (10, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (12, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (14, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (16, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (18, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  (20, 3, now() - interval '89' day, now() - interval '34' day, 'geliefert'),
  
  (1, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (3, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (5, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (7, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (9, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (11, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (13, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (15, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (17, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (19, 4, now() - interval '79' day, now() - interval '24' day, 'geliefert'),

  (3, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (4, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (8, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (11, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (12, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (13, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (14, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (15, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (20, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (21, 5, now() - interval '79' day, now() - interval '24' day, 'geliefert'),

  (2, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (5, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (8, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (11, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (14, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (17, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (20, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (22, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (24, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),
  (25, 6, now() - interval '79' day, now() - interval '24' day, 'geliefert'),

  (1, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (2, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (3, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (6, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (9, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (12, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (15, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (18, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (21, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (24, 7, now() - interval '74' day, now() - interval '19' day, 'geliefert'),

  (5, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (9, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (12, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (14, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (16, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (18, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (20, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (22, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (23, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (25, 8, now() - interval '74' day, now() - interval '19' day, 'geliefert'),

  (4, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (7, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (10, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (13, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (16, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (19, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (22, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (23, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (24, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  (25, 9, now() - interval '74' day, now() - interval '19' day, 'geliefert'),
  
  (1, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (8, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (9, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (11, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (15, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (16, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (17, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (19, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (20, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (22, 10, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  
  (2, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (5, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (6, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (8, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (14, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (15, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (17, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (18, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (21, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (25, 11, now() - interval '64' day, now() - interval '9' day, 'geliefert'),

  (1, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (2, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (3, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (4, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (5, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (6, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (7, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (8, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (9, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  (10, 12, now() - interval '64' day, now() - interval '9' day, 'geliefert'),
  
  (2, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (4, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (6, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (8, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (10, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (12, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (14, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (16, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (18, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (20, 13, now() - interval '59' day, now() - interval '4' day, 'geliefert'),

  (1, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (3, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (5, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (7, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (9, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (11, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (13, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (15, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (17, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (19, 14, now() - interval '59' day, now() - interval '4' day, 'geliefert'),

  (3, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (4, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (8, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (11, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (12, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (13, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (14, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (15, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (20, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  (21, 15, now() - interval '59' day, now() - interval '4' day, 'geliefert'),
  
  (2, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (5, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (8, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (11, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (14, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (17, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (20, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (22, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (24, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),
  (25, 16, now() - interval '58' day, now() - interval '3' day, 'geliefert'),

  (1, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (2, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (3, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (6, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (9, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (12, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (15, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (18, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (21, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (24, 17, now() - interval '58' day, null, 'inAuftrag-überzogen'),

  (5, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (9, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (12, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (14, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (16, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (18, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (20, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (22, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (23, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  (25, 18, now() - interval '58' day, null, 'inAuftrag-überzogen'),
  
  (4, 19, now() - interval '22' day, null, 'inAuftrag'),
  (7, 19, now() - interval '22' day, null, 'inAuftrag'),
  (10, 19, now() - interval '22' day, null, 'inAuftrag'),
  (13, 19, now() - interval '22' day, null, 'inAuftrag'),
  (16, 19, now() - interval '22' day, null, 'inAuftrag'),
  (19, 19, now() - interval '22' day, null, 'inAuftrag'),
  (22, 19, now() - interval '22' day, null, 'inAuftrag'),
  (23, 19, now() - interval '22' day, null, 'inAuftrag'),
  (24, 19, now() - interval '22' day, null, 'inAuftrag'),
  (25, 19, now() - interval '22' day, null, 'inAuftrag'),
  
  (1, 20, now() - interval '22' day, null, 'inAuftrag'),
  (8, 20, now() - interval '22' day, null, 'inAuftrag'),
  (9, 20, now() - interval '22' day, null, 'inAuftrag'),
  (11, 20, now() - interval '22' day, null, 'inAuftrag'),
  (15, 20, now() - interval '22' day, null, 'inAuftrag'),
  (16, 20, now() - interval '22' day, null, 'inAuftrag'),
  (17, 20, now() - interval '22' day, null, 'inAuftrag'),
  (19, 20, now() - interval '22' day, null, 'inAuftrag'),
  (20, 20, now() - interval '22' day, null, 'inAuftrag'),
  (22, 20, now() - interval '22' day, null, 'inAuftrag'),
  
  (2, 21, now() - interval '22' day, null, 'inAuftrag'),
  (5, 21, now() - interval '22' day, null, 'inAuftrag'),
  (6, 21, now() - interval '22' day, null, 'inAuftrag'),
  (8, 21, now() - interval '22' day, null, 'inAuftrag'),
  (14, 21, now() - interval '22' day, null, 'inAuftrag'),
  (15, 21, now() - interval '22' day, null, 'inAuftrag'),
  (17, 21, now() - interval '22' day, null, 'inAuftrag'),
  (18, 21, now() - interval '22' day, null, 'inAuftrag'),
  (21, 21, now() - interval '22' day, null, 'inAuftrag'),
  (25, 21, now() - interval '22' day, null, 'inAuftrag'),
  
  (1, 22, null, null, 'offen'),
  (2, 22, null, null, 'offen'),
  (3, 22, null, null, 'offen'),
  (4, 22, null, null, 'offen'),
  (5, 22, null, null, 'offen'),
  (6, 22, null, null, 'offen'),
  (7, 22, null, null, 'offen'),
  (8, 22, null, null, 'offen'),
  (9, 22, null, null, 'offen'),
  (10, 22, null, null, 'offen'),
  
  (2, 23, null, null, 'offen'),
  (4, 23, null, null, 'offen'),
  (6, 23, null, null, 'offen'),
  (8, 23, null, null, 'offen'),
  (10, 23, null, null, 'offen'),
  (12, 23, null, null, 'offen'),
  (14, 23, null, null, 'offen'),
  (16, 23, null, null, 'offen'),
  (18, 23, null, null, 'offen'),
  (20, 23, null, null, 'offen'),
  
  (1, 24, null, null, 'offen'),
  (3, 24, null, null, 'offen'),
  (5, 24, null, null, 'offen'),
  (7, 24, null, null, 'offen'),
  (9, 24, null, null, 'offen'),
  (11, 24, null, null, 'offen'),
  (13, 24, null, null, 'offen'),
  (15, 24, null, null, 'offen'),
  (17, 24, null, null, 'offen'),
  (19, 24, null, null, 'offen'),
  
  (1, 25, null, null, 'offen'),
  (3, 25, null, null, 'offen'),
  (5, 25, null, null, 'offen'),
  (7, 25, null, null, 'offen'),
  (9, 25, null, null, 'offen'),
  (11, 25, null, null, 'offen'),
  (13, 25, null, null, 'offen'),
  (15, 25, null, null, 'offen'),
  (17, 25, null, null, 'offen'),
  (19, 25, null, null, 'offen'),

  (3, 26, null, null, 'offen'),
  (4, 26, null, null, 'offen'),
  (8, 26, null, null, 'offen'),
  (11, 26, null, null, 'offen'),
  (12, 26, null, null, 'offen'),
  (13, 26, null, null, 'offen'),
  (14, 26, null, null, 'offen'),
  (15, 26, null, null, 'offen'),
  (20, 26, null, null, 'offen'),
  (21, 26, null, null, 'offen'),

  (2, 27, null, null, 'offen'),
  (5, 27, null, null, 'offen'),
  (8, 27, null, null, 'offen'),
  (11, 27, null, null, 'offen'),
  (14, 27, null, null, 'offen'),
  (17, 27, null, null, 'offen'),
  (20, 27, null, null, 'offen'),
  (22, 27, null, null, 'offen'),
  (24, 27, null, null, 'offen'),
  (25, 27, null, null, 'offen'),

  (1, 28, null, null, 'offen'),
  (2, 28, null, null, 'offen'),
  (3, 28, null, null, 'offen'),
  (6, 28, null, null, 'offen'),
  (9, 28, null, null, 'offen'),
  (12, 28, null, null, 'offen'),
  (15, 28, null, null, 'offen'),
  (18, 28, null, null, 'offen'),
  (21, 28, null, null, 'offen'),
  (24, 28, null, null, 'offen'),

  (5, 29, null, null, 'offen'),
  (9, 29, null, null, 'offen'),
  (12, 29, null, null, 'offen'),
  (14, 29, null, null, 'offen'),
  (16, 29, null, null, 'offen'),
  (18, 29, null, null, 'offen'),
  (20, 29, null, null, 'offen'),
  (22, 29, null, null, 'offen'),
  (23, 29, null, null, 'offen'),
  (25, 29, null, null, 'offen'),

  (4, 30, null, null, 'offen'),
  (7, 30, null, null, 'offen'),
  (10, 30, null, null, 'offen'),
  (13, 30, null, null, 'offen'),
  (16, 30, null, null, 'offen'),
  (19, 30, null, null, 'offen'),
  (22, 30, null, null, 'offen'),
  (23, 30, null, null, 'offen'),
  (24, 30, null, null, 'offen'),
  (25, 30, null, null, 'offen')
  ;
  
INSERT INTO Order_Machine (Customer_Order_Id, Machine_Id, Quantity) 
  values (1, 1, 2),
  (1, 2, 1),
  (2, 3, 1),
  (2, 4, 1),
  (2, 5, 1),
  (3, 6, 1),
  (3, 7, 1),
  (3, 8, 1),
  (4, 9, 1),
  (4, 10, 1),
  (4, 1, 1),
  (5, 2, 1),
  (5, 3, 1),
  (5, 4, 1),
  (6, 5, 1),
  (6, 6, 1),
  (6, 7, 1),
  (7, 8, 1),
  (7, 9, 2),
  (8, 2, 1),
  (8, 1, 1),
  (8, 9, 1),
  (9, 3, 1),
  (9, 4, 1),
  (9, 5, 1),
  (10, 6, 1),
  (10, 7, 1),
  (10, 8, 1);
  
INSERT INTO Machine_Component (Machine_Id, Component_Id)
  values 
  (1, 1), (1, 2), (1, 3), (1, 4), (1, 5), (1, 6), (1, 7), (1, 8), (1, 9), (1, 10),
  (2, 2), (2, 4), (2, 6), (2, 8), (2, 10), (2, 12), (2, 14), (2, 16), (2, 18), (2, 20),
  (3, 1), (3, 3), (3, 5), (3, 7), (3, 9), (3, 11), (3, 13), (3, 15), (3, 17), (3, 19),
  (4, 3), (4, 4), (4, 8), (4, 11), (4, 12), (4, 13), (4, 14), (4, 15), (4, 20), (4, 21),
  (5, 2), (5, 5), (5, 8), (5, 11), (5, 14), (5, 17), (5, 20), (5, 22), (5, 24), (5, 25),
  (6, 1), (6, 2), (6, 3), (6, 6), (6, 9), (6, 12), (6, 15), (6, 18), (6, 21), (6, 24),
  (7, 5), (7, 9), (7, 12), (7, 14), (7, 16), (7, 18), (7, 20), (7, 22), (7, 23), (7, 25),
  (8, 4), (8, 7), (8, 10), (8, 13), (8, 16), (8, 19), (8, 22), (8, 23), (8, 24), (8, 25),
  (9, 1), (9, 8), (9, 9), (9, 11), (9, 15), (9, 16), (9, 17), (9, 19), (9, 20), (9, 22),
  (10, 2), (10, 5), (10, 6), (10, 8), (10, 14), (10, 15), (10, 17), (10, 18), (10, 21), (10, 25);


  
# Abfragen
# 3 Abfragen mit jeweils einem Join

# Alle Kunden mit Aufträgen, die InBearbeitung sind
select k.Customer_Name, a.Order_Date, a.Delivery_Date, a.Status
from Customer k
  join Customer_Order a on a.Customer_Id = k.Id
  where a.Status = 'inBearbeitung';
  
# Alle Kunden mit Aufträgen und deren Status
select k.Id, k.Customer_Name, a.Status, count(*)
from Customer k
  join Customer_Order a on a.Customer_Id = k.Id
  group by k.Id, k.Customer_Name, a.Status
  order by 1;
  
# Maschinen die ein bestimmter Kunde bestellt hat
select co.Customer_Id, om.Machine_Id, om.Quantity
from Customer_Order co
	join Order_Machine om on om.Customer_Order_Id = co.Id;

# Belegung der Produktionsstationen durch Maschinen der jeweiligen Kunden
select co.Customer_id, po.Machine_Id, po.Production_Station_Id
from Customer_Order co
	join Production_Order po on po.Customer_Order_Id = co.Id;
	
# 2 Abfragen mit mehr als einem Join

# Bestellungen bei denen nach nicht alle Komponenten geliefert wurden
# hier die offenen Bestellungen für Kunde 6
select distinct cu.customer_name, co.id as OrderId, co.order_date, 
	m.machine_name, om.machine_id, cd.status, po.id as ProductionOrder,
	c.component_name
	from Customer cu 
	join Customer_Order co on co.customer_id = cu.id
	join Order_Machine om on om.customer_order_id = co.id
	join Machine m on m.id = om.machine_id
	join Production_Order po on po.customer_order_id = om.customer_order_id
	join Component_Delivery cd on cd.production_order_id = po.Id and cd.status != 'geliefert'
	join Component c on c.id = cd.component_id
	where cu.customer_name = 'Kunde #6' and m.machine_name = 'Maschine #7'
	order by 3 asc;


# Es  soll  ermittelt  werden, zu  wie  viel  Prozent  die  einzelnen Fertigungsstationenin einem bestimmten Zeitraum ausgelastet sind
Select count (distinct po.production_station_id) * 0.1 * 100 || ' %' as "Auslastung"
from production_order po
	where po.start_date > (now() - interval '20' day) 
	and po.start_date < (now() - interval '15' day)
	Order by 1;
	
# Funktion zur ermittlung der Auslastung aller Stationen in einem Zeitraum
create or REPLACE function get_usage(startdate TIMESTAMP, enddate TIMESTAMP)
returns FLOAT
language plpgsql
as
$$
declare
   station_usage float;
begin
              
    select 
       COALESCE(100 * (sum(CASE
                  -- start vor und ende nach dem zeitraum oder jeweils genau drauf 
                    WHEN (po.start_date <= startdate) and
                         (COALESCE(po.completion_date, now()) >= enddate) THEN
                     TRUNC(DATE_PART('day', enddate - startdate))
                  -- start liegt vor dem Zeitraum, ende ist drin     
                    WHEN (po.start_date < startdate) and
                         (COALESCE(po.completion_date, now()) <= enddate) THEN
                     TRUNC(DATE_PART('day', po.completion_date - startdate))
                  -- start ist innerhalb des Zeitraums - ende ist grösser oder offen    
                    WHEN (po.start_date >= startdate) and
                         (COALESCE(po.completion_date, now()) > enddate) THEN
                     TRUNC(DATE_PART('day', enddate - po.start_date))
                    ELSE
                     0
                  END)) /
       (10 * TRUNC(DATE_PART('day', enddate - startdate))), '0.0') into station_usage
  from production_station ps
  join production_order po
    on po.production_station_id = ps.id
where (startdate, enddate) overlaps(po.start_date, po.completion_date);
      
   return station_usage;
end;
$$;    
 
# Alle Produktionsdaten
select * from production_order;
 
# aufruf der Auslastungsfunktion für einen gewünschten Zeitraum
select 
              10 * ABS(TRUNC(DATE_PART('day', '2021-01-12'::TIMESTAMP - '2021-01-08'::TIMESTAMP))) || ' Days' as COMPLETE_DAYS,
              get_usage('2021-01-08'::TIMESTAMP, '2021-01-12'::TIMESTAMP) || ' %';
 
# aufruf der Auslastungsfunktion für einen gewünschten Zeitraum (relativ zu now()
select 
              10 * ABS(TRUNC(DATE_PART('day', (now() - interval '18' day) - (now() - interval '10' day)))) || ' Days' as COMPLETE_DAYS,
              get_usage((now() - interval '18' day)::TIMESTAMP, (now() - interval '10' day)::TIMESTAMP) || ' %';
