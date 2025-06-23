---
title: "Your SQL Server without a TLS certificate? Why take the risk? Encrypt your traffic!"
date: 2025-06-22
categories: [Encryption]
author: Twan Pelkmans
image: https://upload.wikimedia.org/wikipedia/commons/4/41/Microsoft_SQL_Server_2025_icon.svg
summary: "It takes minimal effort to install a TLS certificate to encrypt the traffic between your client and the SQL Server. Every new operational database product provided by Microsoft now has encryption in flight by default. Why would you not do it for your current SQL installations? Do not expose your data especially when the amount of effort is minimal."
---
![SQL Server Logo](/assets/img/Picteo.jpg)

<!--### Introduction-->
On too many occasions I see TLS encryption is not set up correctly on Microsoft SQL server installations. This is a necessary configuration step during installation of a SQL instance which tends to get forgotten all too often or brushed away as unnecessary. 
More and more awareness is generated for these forgotten scenarios, even up to the point where legislation catches up [^1] and determines omission as a punishable act.


[^1]: https://www.ncsc.nl/over-ncsc/wettelijke-taak/wat-gaat-de-nis2-richtlijn-betekenen-voor-uw-organisatie

<!--### Briefly introduce the topic and its relevance.-->
The NCSC (National Cyber Security Center) published renewed guidelines for TLS encryption back in 2021 [^2]. These guidelines stated TLS1.3 was the most future proof protocol for secure IP (Internet Protocol) traffic. 
The guidelines were meant as an aid during procurement, set-up and review, to assist IT organisations when stating their requirements. TLS1.2 was considered sufficient, not good, in terms of future-proofness. 


[^2]: https://english.ncsc.nl/latest/news/2021/january/19/it-security-guidelines-for-transport-layer-security-2.1


<!--## Background-->
It is getting easier to decrypt data. The necessary virtual hardware is available through cloud providers [^3] and it is getting cheaper because of technical progress in the area of computational power [^4]  

[^3]: https://azure.microsoft.com/en-us/solutions/quantum-computing
[^4]: https://news.microsoft.com/source/features/innovation/microsofts-majorana-1-chip-carves-new-path-for-quantum-computing/


<!--# Provide context or background information.-->

<!--## Main Content-->

<!--### Subheading 1-->

<!--# Explain the first main point.-->

<!--### Subheading 2-->

<!--# Discuss the second main point.-->

<!--## Code Examples-->
```sql
-- If this query returns any results, you may have configured TLS encryption incorrectly
-- If it does not return results, it just confirms there are currently no uncompliant connections. It does not guarantee your configuration is correctly configured.
    select * 
    from sys.dm_exec_connections
    where (auth_scheme !='KERBEROS'
            or encrypt_option!='TRUE')
        and net_transport='TCP'
```
<!--## Conclusion-->

<!--# Summarize key takeaways and next steps.-->

