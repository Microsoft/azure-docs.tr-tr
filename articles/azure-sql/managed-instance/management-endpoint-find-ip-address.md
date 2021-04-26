---
title: Yönetim uç noktası IP adresini bul
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örnek yönetimi uç noktası genel IP adresini almayı ve yerleşik güvenlik duvarı korumasını doğrulamayı öğrenin
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91617375"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Yönetim uç noktası IP adresini belirleme-Azure SQL yönetilen örneği 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneği sanal kümesi, Azure 'un yönetim işlemleri için kullandığı bir yönetim uç noktası içerir. Yönetim uç noktası, ağ düzeyinde yerleşik bir güvenlik duvarı ve uygulama düzeyinde karşılıklı sertifika doğrulaması ile korunur. Yönetim uç noktasının IP adresini belirleyebilirsiniz, ancak bu uç noktaya erişemezsiniz.

Yönetim IP adresini öğrenmek için, SQL yönetilen örnek FQDN 'niz üzerinde bir [DNS araması](/windows-server/administration/windows-commands/nslookup) yapın: `mi-name.zone_id.database.windows.net` . Bu, gibi bir DNS girişi döndürür `trx.region-a.worker.vnet.database.windows.net` . Daha sonra bu FQDN üzerinde ". VNET" kaldırılmış bir DNS araması yapabilirsiniz. Bu, yönetim IP adresini döndürür. 

Bu PowerShell kodu, \<MI FQDN\> SQL yönetilen ÖRNEĞININ DNS girdisiyle değiştirin: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

SQL yönetilen örneği ve bağlantısı hakkında daha fazla bilgi için bkz. [Azure SQL yönetilen örnek bağlantısı mimarisi](connectivity-architecture-overview.md).
