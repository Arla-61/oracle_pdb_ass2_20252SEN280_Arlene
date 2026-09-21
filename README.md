# oracle_pdb_ass2_20252SEN280_Arlene
## Environment
 Oracle Database 21c (Multitenant Architecture)
 CDB: ORCL
 Tool: SQL*Plus (sqlplus / as sysdba)
 Monitoring: Oracle Enterprise Manager Database Express (https://localhost:5500/em)
 PDB created: AR_PDB_20252SEN280
 User created: ARLENE_PLSQLAUCA_20252SEN280
 
## Creating a Pluggable Database

A new PDB is cloned from the seed database (PDB$SEED) using CREATE PLUGGABLE DATABASE, with FILE_NAME_CONVERT mapping the seed's datafile paths to the new PDB's own directory.

sql
-- Check seed database datafiles
SELECT con_id, name FROM v$datafile WHERE con_id = 2;

-- Create the new pluggable database from the seed
CREATE PLUGGABLE DATABASE ar_pdb_20252SEN280
  ADMIN USER pdbadmin IDENTIFIED BY PdbAdmin123
  FILE_NAME_CONVERT = (
    'C:\ORACLE_21C\ORADATA\ORCL\PDBSEED',
    'C:\ORACLE_21C\ORADATA\ORCL\ar_pdb_20252SEN280'
  );

-- Confirm the PDB now exists (initially in MOUNTED state)
SHOW PDBS; 

The Output is :

<img width="682" height="507" alt="CREATING PDB" src="https://github.com/user-attachments/assets/917b89d7-65e6-4e28-9cad-ed122940d9fa" />

## Opening the PDB, Creating a User, and Managing Privileges

Once created, a PDB must be opened (READ WRITE) before it can be used. The session is then switched into the PDB's container to create a local user and grant the privileges needed to connect and build objects.

sql
-- Open the PDB for read/write access
ALTER PLUGGABLE DATABASE ar_pdb_20252SEN280 OPEN;
SHOW PDBS;

-- Switch session context into the new PDB
ALTER SESSION SET CONTAINER = ar_pdb_20252SEN280;
SHOW CON_NAME;

-- Create a local user inside the PDB
CREATE USER arlene_plsqlauca_20252SEN280 IDENTIFIED BY Arlene123;

-- Grant privileges
GRANT CREATE SESSION   TO arlene_plsqlauca_20252SEN280;
GRANT CREATE TABLE     TO arlene_plsqlauca_20252SEN280;
GRANT CREATE PROCEDURE TO arlene_plsqlauca_20252SEN280;

-- Verify the user exists
SELECT username FROM dba_users
WHERE username = 'ARLENE_PLSQLAUCA_20252SEN280';

The output is :

<img width="680" height="508" alt="CREATION OF USER   Its&#39; MNGT" src="https://github.com/user-attachments/assets/c779a246-43ae-4187-a88a-59838d9dbcd8" />

## Deleting the Pluggable Database

To remove a PDB entirely (including its underlying datafiles), the session must return to CDB$ROOT, close the PDB, then drop it.

sql
-- Return to the root container
ALTER SESSION SET CONTAINER = CDB$ROOT;
SHOW PDBS;

-- Close the PDB before dropping it
ALTER PLUGGABLE DATABASE ar_pdb_20252SEN280 CLOSE IMMEDIATE;

-- Drop the PDB and remove its datafiles from disk
DROP PLUGGABLE DATABASE ar_pdb_20252SEN280 INCLUDING DATAFILES;

-- Confirm removal
SHOW PDBS;
SELECT pdb_name FROM cdb_pdbs WHERE pdb_name = 'AR_PDB_20252SEN280';

The output is :

<img width="685" height="506" alt="DELETION OF PDB" src="https://github.com/user-attachments/assets/a3ce23e5-01b0-4269-ad03-0d58ab2e5e77" />

## Monitoring in Oracle Enterprise Manager (OEM)

The pluggable Database created is showed in the dashboard and the user created is showed bellow 
