---
title: 'Öğretici: kullanmaya başlama yönetici ekleme'
description: Bu öğreticide, çalışma alanınıza başka bir yönetici kullanıcı eklemeyi öğreneceksiniz.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553507"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>SYNAPSE çalışma alanınıza yönetici ekleme

Bu öğreticide, SYNAPSE çalışma alanınıza yönetici eklemeyi öğreneceksiniz. Bu Kullanıcı çalışma alanı üzerinde tam denetime sahip olacak.

## <a name="overview"></a>Genel Bakış

Şimdiye kadar Başlarken kılavuzunda, çalışma alanında *yaptığınız etkinliklere* odaklandık. Çalışma alanını 1. ADıMDA oluşturduğunuz için SYNAPSE çalışma alanının yöneticisi olursunuz. Şimdi başka bir kullanıcıyı ( `ryan@contoso.com` ) bir yönetici olarak yapacağız. Bu işlem tamamlandığında, Ryan çalışma alanında yapabileceğiniz her şeyi gerçekleştirebilir.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: çalışma alanı için sahip rolü

`ryan@contoso.com`Çalışma alanında Azure RBAC **Owner** rolüne atayın.

1. Azure portal açın ve SYNAPSE çalışma alanınızı açın.
1. Sol tarafta **Access Control (IAM)** öğesini seçin.
1. `ryan@contoso.com` **Sahip** rolüne ekleyin. 
1. **Kaydet**’e tıklayın.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>SYNAPSE RBAC: SYNAPSE yönetici rolü çalışma alanı

`ryan@contoso.com`Çalışma alanında SYNAPSE RBAC **SYNAPSE yönetici** rolüne atayın.

1. SYNAPSE Studio 'da çalışma alanınızı açın.
1. Yönetim hub 'ını açmak için sol tarafta **Yönet** ' e tıklayın.
1. **Güvenlik** altında **erişim denetimi**' ne tıklayın.
1. **Ekle**'ye tıklayın.
1. **Kapsamı** **çalışma alanı** olarak ayarlayın.
1. `ryan@contoso.com` **SYNAPSE yönetici** rolüne ekleyin. 
1. Sonra **Uygula**' ya tıklayın.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: birincil depolama hesabındaki rol atamaları

`ryan@contoso.com`Çalışma alanının birincil depolama hesabındaki **sahip** rolüne atayın.
`ryan@contoso.com`Çalışma alanının birincil depolama hesabındaki **Azure Depolama Blobu veri katılımcısı** rolüne atayın.

1. Azure portal çalışma alanının birincil depolama hesabını açın.
1. Sol tarafta **Access Control (IAM)** öğesine tıklayın.
1. `ryan@contoso.com` **Sahip** rolüne ekleyin. 
1. `ryan@contoso.com` **Azure Depolama Blobu veri katılımcısı** rolüne Ekle

## <a name="dedicated-sql-pools-db_owner-role"></a>Adanmış SQL havuzları: db_owner Rol

`ryan@contoso.com`Çalışma alanındaki her ADANMıŞ SQL havuzundaki **db_owner** atayın.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Synapse Analytics ile çalışmaya başlama](get-started.md)
* [Çalışma alanı oluşturma](quickstart-create-workspace.md)
* [Sunucusuz SQL havuzu kullanma](quickstart-sql-on-demand.md)
