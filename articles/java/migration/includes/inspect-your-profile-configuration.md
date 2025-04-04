---
author: KarlErickson
ms.author: karler
ms.reviewer: haiche
ms.date: 04/03/2023
---

### Inspect your profile configuration

The main configuration unit in WAS is the profile. As such, the **resources.xml** file contains a wealth of configuration that you must carefully consider for migration. The file includes references to more XML files that are stored in subdirectories. IBM advises that you should normally use the **IBM Console** to configure WAS's manageable objects and services, and allow WAS to maintain the **profiles/profile-name** folder. For more information, see [Managing profiles on distributed and IBM i operating systems](https://www.ibm.com/docs/en/was-nd/9.0.5?topic=environment-managing-profiles-distributed-i-operating-systems).

#### Inside your application

Inspect the **deployment.xml** file and/or the **WEB-INF/web.xml** file.
