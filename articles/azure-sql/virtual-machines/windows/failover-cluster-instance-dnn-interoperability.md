---
title: SQL Server FCı & DNN ile özellik birlikte çalışabilirliği
description: "Belirli SQL Server özellikleriyle ve Azure VM 'lerinde SQL Server yük devretme kümesi örneği olan dağıtılmış ağ adı (DNN) kaynağıyla çalışırken dikkat edilecek ek konular hakkında bilgi edinin. "
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358223"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>SQL Server FCı & DNN ile özellik birlikte çalışabilirliği
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Sabit kodlanmış bir sanal ağ adına (VNN) dayanan bazı SQL Server özellikler vardır. Bu nedenle, dağıtılmış ağ adı (DNN) kaynağını yük devretme kümesi örneğiniz ve Azure VM 'lerinde SQL Server kullanırken bazı ek hususlar vardır. 

Bu makalede, DNN kaynağını kullanırken ağ diğer adını nasıl yapılandıracağınızı ve hangi SQL Server özelliklerinin ek olarak dikkat gerektirdiğini öğrenin.

## <a name="create-network-alias-fci"></a>Ağ diğer adı (FCı) oluştur

Bazı sunucu tarafı bileşenleri sabit kodlanmış bir VNN değeri kullanır ve sanal nn değerini DNN DNS adıyla eşleyen bir ağ diğer adı gerektirir. VNN ' i DNN DNS adına eşleyen bir diğer ad oluşturmak için [sunucu diğer adı oluşturma](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) ' daki adımları izleyin. 

Varsayılan bir örnek için, VNN 'yi doğrudan DNN DNS adına eşleyebilir, bu da VNN = DNN DNS adıdır.
Örneğin, VNN adı ise, `FCI1` örnek adı ise `MSSQLSERVER` ve DNN ise `FCI1DNN` (daha önce bağlanan istemciler `FCI` ve şimdi bağlantısı varsa `FCI1DNN` ), vnn öğesini `FCI1` DNN 'ye eşleyin `FCI1DNN` . 

Adlandırılmış bir örnek için, ağ diğer adı eşlemesinin tam örnek için yapılması gerekir, örneğin `VNN\Instance`  =  `DNN\Instance` . Örneğin, VNN adı ise, `FCI1` örnek adı ise `instA` ve DNN ise `FCI1DNN` (daha önce bağlanan istemciler `FCI1\instA` ve şimdi bağlantısı varsa `FCI1DNN\instaA` ), vnn öğesini `FCI1\instaA` DNN 'ye eşleyin `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>İstemci sürücüleri

ODBC, OLEDB, ADO.NET, JDBC, PHP ve Node.js sürücüleri için, kullanıcıların, bağlantı dizesindeki sunucu adı olarak DNN DNS adını açıkça belirtmesi gerekir. Yük devretme sonrasında hızlı bağlantı sağlamak için, `MultiSubnetFailover=True` SQL istemcisi destekliyorsa bağlantı dizesine ekleyin. 

## <a name="tools"></a>Araçlar

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)ve [SQL Server veri araçları](/sql/ssdt/sql-server-data-tools) kullanıcılarının, bağlantı dizesindeki sunucu adı olarak DNN DNS adını açıkça belirtmesi gerekir. 

## <a name="availability-groups-and-fci"></a>Kullanılabilirlik grupları ve FCı

Çoğaltmalardan biri olarak bir yük devretme kümesi örneği kullanarak her zaman açık kullanılabilirlik grubu yapılandırabilirsiniz. Bu yapılandırmada, FCı çoğaltmasının yansıtma uç noktası URL 'sinin FCı DNN 'yi kullanması gerekir. Benzer şekilde, FCı salt okunurdur çoğaltma olarak kullanılıyorsa, FCı çoğaltmasına yönelik salt okuma yönlendirmenin FCı DNN 'yi kullanması gerekir. 

Yansıtma uç noktasının biçimi: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Örneğin, DNN DNS adınız `dnnlsnr` ve `5022` FCI 'nin yansıtma uç noktasının bağlantı noktası olması halinde, uç nokta URL 'sini oluşturmak için Transact-SQL (T-SQL) kod parçacığı şöyle görünür: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Benzer şekilde, salt okuma yönlendirme URL 'SI için biçim: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Örneğin, DNN DNS adınız `dnnlsnr` ve `1444` salt okunurdur SQL Server FCI tarafından kullanılan bağlantı noktası ise, salt okuma yönlendirme URL 'sini oluşturmak için T-SQL kod parçacığı şöyle görünür: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

URL 'deki bağlantı noktasını, varsayılan 1433 bağlantı noktası ise atlayabilirsiniz. Adlandırılmış bir örnek için, adlandırılmış örnek için bir statik bağlantı noktası yapılandırın ve bunu salt okuma yönlendirme URL 'SI içinde belirtin.  

## <a name="replication"></a>Çoğaltma

Çoğaltmanın üç bileşeni vardır: Yayımcı, dağıtıcı, abone. Bu bileşenlerden herhangi biri bir yük devretme kümesi örneği olabilir. FCı VNN, çoğaltma yapılandırmasında çok büyük bir şekilde kullanıldığından, hem açıkça hem de örtük olarak, çoğaltmanın çalışması için VNN 'yi DNN ile eşleyen bir ağ diğer adı gerekli olabilir. 

Çoğaltma içinde FCı adı olarak VNN adını kullanmaya devam edin, ancak *çoğaltmayı yapılandırmadan önce* aşağıdaki uzak durumlarda bir ağ diğer adı oluşturun:

| **Çoğaltma bileşeni (DNN ile FCı)** | **Uzak bileşen** | **Ağ diğer adı eşlemesi** | **Ağ eşlemesi olan sunucu**| 
|---------|---------|---------|-------- | 
|Publisher | Dağıtım | Yayımcı DNN 'ye Yayımcı| Dağıtım| 
|Dağıtım|Abone |Dağıtım DNN 'ye dağıtımcı VNET| Abone | 
|Dağıtım|Publisher | Dağıtım DNN 'ye dağıtımcı VNET | Publisher| 
|Abone| Dağıtım| Abone 'e abone olan abonelik DNN | Dağıtım| 

Örneğin, bir çoğaltma topolojisinde DNN kullanarak bir FCı olarak yapılandırılmış bir yayımcıya sahip olduğunu ve dağıtıcı uzakta olduğunu varsayalım. Bu durumda, yayımcıyı VNET 'e eşlemek için dağıtıcı sunucusunda bir ağ diğer adı oluşturun: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="DNN DNS adını SQL Server Yapılandırma Yöneticisi kullanarak ağ diğer adı olarak yapılandırın." :::

Aşağıdaki görüntü örneği gibi adlandırılmış bir örnek için tam örnek adını kullanın: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Adlandırılmış bir örnek için bir ağ diğer adı yapılandırırken tam örnek adını kullanın." :::

## <a name="database-mirroring"></a>Veritabanı yansıtma

Veritabanı yansıtma ortağı olarak, bir FCı ile veritabanı yansıtmayı yapılandırabilirsiniz. SQL Server Management Studio GUI yerine [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) kullanarak yapılandırın. T-SQL kullanmak, veritabanı yansıtma uç noktasının VNN yerine DNN kullanılarak oluşturulmasını güvence altına alacak. 

Örneğin, DNN DNS adınız `dnnlsnr` ve veritabanı yansıtma uç noktası 7022 ise, aşağıdaki T-SQL kod parçacığı veritabanı yansıtma ortağını yapılandırır: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

İstemci erişimi için, **Yük devretme ortağı** özelliği veritabanı yansıtma yük devretmesini işleyebilir, ancak FCI yük devretmeyi işleyebilir. 

## <a name="msdtc"></a>MSDTC

FCı, Microsoft Dağıtılmış İşlem Düzenleyicisi (MSDTC) tarafından koordine edilen dağıtılmış işlemlere katılabilir. Hem kümelenmiş MSDTC hem de Yerel MSDTC, FCı DNN ile desteklenir, ancak Azure 'da, kümelenmiş MSDTC için bir yük dengeleyici hala gereklidir. FCı 'da tanımlanan DNN, Azure 'daki kümelenmiş MSDTC için Azure Load Balancer gereksinimini değiştirmez. 

## <a name="filestream"></a>Akışı

FCı içindeki bir veritabanı için FILESTREAM desteklense de, DNN ile dosya sistemi API 'Leri kullanarak FILESTREAM veya FileTable 'a erişilmesi desteklenmez. 

## <a name="linked-servers"></a>Bağlı sunucular

Bir FCı DNN ile bağlı sunucu kullanılması desteklenir. Bağlı bir sunucu yapılandırmak için DNN 'yi doğrudan kullanın veya VNN 'yi DNN ile eşlemek için bir ağ diğer adı kullanın. 


Örneğin, adlandırılmış örnek için DNN DNS adına sahip bağlı bir sunucu oluşturmak için `dnnlsnr` `insta1` aşağıdaki Transact-SQL (T-SQL) komutunu kullanın:

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Alternatif olarak, bunun yerine sanal ağ adını (VNN) kullanarak bağlı sunucuyu oluşturabilirsiniz, ancak daha sonra VNN 'i DNN 'ye eşlemek için bir ağ diğer adı tanımlamanız gerekir. 

Örneğin, örnek adı `insta1` , vnn adı `vnnname` ve DNN adı Için `dnnlsnr` AŞAĞıDAKI Transact-SQL (T-SQL) komutunu kullanarak vnn kullanarak bağlı bir sunucu oluşturun:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Ardından, eşlenecek bir ağ diğer adı oluşturun `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Sık sorulan sorular


- Hangi SQL Server sürümü DNN desteğini getiriyor? 

   SQL Server 2019 CU2 UYGULAMAZSANıZ ve üzeri.

- DNN kullanıldığında beklenen yük devretme süresi nedir?

   DNN için yük devretme süresi, herhangi bir zaman eklenmeksizin (Azure Load Balancer kullanırken araştırma süresi gibi) yalnızca FCı yük devretme süresi olacaktır.

- SQL istemcilerinin OLEDB ve ODBC ile DNN 'yi desteklemesi için herhangi bir sürüm gereksinimi var mı?

   `MultiSubnetFailover=True`DNN için bağlantı dizesi desteği öneririz. SQL Server 2012 (11. x) tarihinden itibaren kullanılabilir.

- DNN 'yi kullanmanı için gereken SQL Server yapılandırma değişiklikleri var mı? 

   SQL Server DNN 'yi kullanmak için herhangi bir yapılandırma değişikliği gerektirmez, ancak bazı SQL Server özellikleri daha fazla değerlendirme gerektirebilir. 

- DNN birden çok alt ağ kümesini destekliyor mu?

   Evet. Küme, DNS 'deki DNN 'yi, alt ağdan bağımsız olarak kümedeki tüm düğümlerin fiziksel IP adresleriyle bağlar. SQL istemcisi, alt ağdan bağımsız olarak DNS adının tüm IP adreslerini dener. 



## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. 

- [Windows küme teknolojileri](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server yük devretme kümesi örnekleri](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

