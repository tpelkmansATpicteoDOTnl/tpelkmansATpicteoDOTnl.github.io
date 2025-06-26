---
title: "Encryption at rest affects your SQL backups, what whitepapers don't tell you"
date: 2025-06-26
categories: [Encryption]
author: Twan Pelkmans
image: https://upload.wikimedia.org/wikipedia/commons/4/41/Microsoft_SQL_Server_2025_icon.svg
summary: "It takes just one parameter to encrypt backups. The challenge lies with keeping your backup certificates in sync with your backups especially when you cycle your certificates regularly"
---
![SQL Server Logo](/assets/img/Picteo.jpg)

## Create a Database Master Key
Suggested Visual: Screenshot of SQL Server Management Studio (SSMS) with a query window executing the following script:

```sql
USE master;
GO
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'YourSecurePassword123!';
GO
```
This master key is created in the master database and is fundamental for encrypting certificates and keys used in backup encryption.

## Generate a Certificate for Backup Encryption
Suggested Visual: SSMS confirmation message indicating successful creation of the certificate.

```sql
USE master;
GO
CREATE CERTIFICATE BackupCertificate
   WITH SUBJECT = 'Certificate for SQL Server Backup Encryption';
GO
```
This certificate will be referenced during the backup process to enable encryption.

## Execute an Encrypted Backup Operation
Suggested Visual: SSMS displaying execution of a BACKUP DATABASE statement including the ENCRYPTION clause.

```sql
BACKUP DATABASE ExampleDB
TO DISK = 'E:\Backups\ExampleDB_Encrypted.bak'
WITH 
  COMPRESSION,
  ENCRYPTION (
    ALGORITHM = AES_256,
    SERVER CERTIFICATE = BackupCertificate
  );
GO
```
In this example, the AES_256 encryption algorithm is employed, which is widely regarded as robust and secure.

## Restore from an Encrypted Backup
Suggested Visual: Screenshot showing successful database restoration from the encrypted .bak file.

```sql
RESTORE DATABASE ExampleDB
FROM DISK = 'E:\Backups\ExampleDB_Encrypted.bak';
GO
To restore this backup on another server instance, the corresponding certificate and master key must be restored beforehand.
```
## Back Up the Certificate and Private Key
Suggested Visual: File explorer showing .cer and .pvk files saved to a secure directory.

```sql
BACKUP CERTIFICATE BackupCertificate
TO FILE = 'E:\CertStore\BackupCertificate.cer'
WITH PRIVATE KEY (
    FILE = 'E:\CertStore\BackupCertificatePrivateKey.pvk',
    ENCRYPTION BY PASSWORD = 'AnotherSecurePassword!'
);
GO
```
Retention of the certificate and private key is critical for restoring encrypted backups elsewhere.
