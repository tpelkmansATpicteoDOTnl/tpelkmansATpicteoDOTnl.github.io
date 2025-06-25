---
title: "Is Your SQL Server Traffic Secure? Why TLS Encryption Is No Longer Optional"
date: 2025-06-22
categories: [Encryption]
author: Twan Pelkmans
image: https://upload.wikimedia.org/wikipedia/commons/4/41/Microsoft_SQL_Server_2025_icon.svg
summary: "It takes minimal effort to install a TLS certificate to encrypt the traffic between your client and the SQL Server. Every new operational database product provided by Microsoft now has encryption in flight by default. Why would you not do it for your current SQL installations? Do not expose your data especially when the amount of effort is minimal."
---
![SQL Server Logo](/assets/img/Picteo.jpg)

### Why TLS encryption matters
TLS encryption is often misconfigured or entirely omitted in SQL Server installations—a critical oversight that exposes sensitive data to risk. It is a necessary configuration step during installation of a SQL instance which tends to get forgotten all too often or brushed away as unnecessary. More and more awareness is generated for these forgotten scenarios, even up to the point where legislation catches up [^1] and determines omission as a punishable act.

[^1]: https://www.ncsc.nl/over-ncsc/wettelijke-taak/wat-gaat-de-nis2-richtlijn-betekenen-voor-uw-organisatie

TLS encryption with SQL Server protects the data traffic between a SQL Server instance and the client/user. This encryption ensures that any information exchanged is securely transmitted and cannot be easily intercepted or tampered with.

When TLS is enabled, SQL Server uses a digital certificate to establish a secure connection. This certificate must be installed on the server and should match the server’s name. Once the certificate is in place, SQL Server can be configured to enforce encryption for all incoming connections.

To set this up, administrators typically use SQL Server Configuration Manager to assign the certificate to the SQL Server instance and enable encryption settings. 

## NCSC recommendations
The NCSC (National Cyber Security Center) published renewed guidelines for TLS encryption back in 2021 [^2]. These guidelines stated TLS1.3 was the most future proof protocol for secure IP (Internet Protocol) traffic. The guidelines were meant as an aid during procurement, set-up and review, to assist IT organisations when stating their requirements. TLS1.2 was considered sufficient, not good, in terms of future-proofness. 


[^2]: https://english.ncsc.nl/latest/news/2021/january/19/it-security-guidelines-for-transport-layer-security-2.1

## The risk of doing nothing
It is getting progressively easier to decrypt data. The necessary virtual hardware is available through cloud providers [^3] and it is getting cheaper because of technical progress in the area of computational power [^4]. 

Imagine this scenario: A simple sniffer, which is a piece of software that collects encrypted traffic and sends it to an internet addres,  inadvertently imported through innocent looking actors like mail and browsers, could collect and store data for later decryption. In a couple of years, when decryption is just a low-cost, low-threshold action, your data collected today might turn up in the hands of wrong parties. Hopefully the public availability of that data can be considered harmless by then.  

[^3]: https://azure.microsoft.com/en-us/solutions/quantum-computing
[^4]: https://news.microsoft.com/source/features/innovation/microsofts-majorana-1-chip-carves-new-path-for-quantum-computing/


## How TLS works
A TLS server certificate contains the name of the SQL Server. Establishing the identity of the server in TLS 1.3 is a critical part of the handshake process. It ensures that the client is communicating with the legitimate server and not an imposter. This step can be skipped providing the 'Trust server certificate' option (usually a checkbox in the connection popup). Having to trust the server is a sure sign that the certificate is incorrectly configured and can therefore not be verified by the client, like with self-signed certificates. 

After the identity checks pass, the client can trust that the server is legitimate. When configured correctly, the identity of the client can also be checked as a legitimate party in the process. 

Client and server now establish a shared secret for this session. If client and server are configured for TLS1.3, it only uses temporary (aka ephemeral) shared secrets, as opposed to older versions like with TLS1.0 or SSL. That ensures that when a shared secret gets compromised in the future, at least the data sent today will remain secure. 

As soon as all handshakes are complete, the identity of server is checked, possibly client also, a shared secret is established for this session, then the actual data can be securely encrypted and sent over the network.
Since TLS1.3 is the latest standard, it also uses the most secure encryption mechanisms during initial setup and encryption in flight, which causes more difficulty to decrypt traffic in malicious attempts.  

## Check your configuration
This article discusses just an excerpt of possible secure configuration settings. There are more settings that can influence the secureness of your traffic in flight. One of the checks that can be performed is given in below script. When the script gives results in a SQL session, there is definitely room for improvement. Wether the script returns results depends on current open connections with the SQL server, also provided it is run with adequate privileges. The script doesn't report on the level of encryption, for that checks are needed on OS level. 

```sql
-- If this query returns any results, you may have configured TLS encryption incorrectly
-- If it does not return results, it just confirms there are currently no uncompliant connections. 
-- It does not guarantee your configuration is correctly configured.
    select * 
    from sys.dm_exec_connections
    where (auth_scheme !='KERBEROS'
            or encrypt_option!='TRUE')
        and net_transport='TCP'
```
**Note:** This query only checks current connections and requires appropriate privileges. It does not confirm full TLS configuration or protocol version.

## Conclusion
TLS encryption is no longer a "nice-to-have"—it’s a baseline requirement for secure SQL Server deployments. With increasing regulatory pressure and evolving cyber threats, proactive encryption is the only safe path forward.
When correctly configured server (and client) have their identity checked in the communication setup, no imposters or man in the middle scenarios are present. Secrets to encrypt traffic are established and only valid for this session with TLS1.3. This assures that any other compromised secrets can not decrypt traffic for the current session. TLS1.3 is the latest standard and leverages security by using more secure encryption, during data transit but also during the initial handshake. 

<!--# Summarize key takeaways and next steps.-->
1. Install a valid TLS certificate on your SQL Server
2. Configure SQL Server to use the certificate
3. Enforce encryption for all connections
4. Ensure clients support and prefer TLS 1.3
5. Disable outdated protocols (SSL, TLS 1.0/1.1, optionally TLS 1.2)

## Footnotes
