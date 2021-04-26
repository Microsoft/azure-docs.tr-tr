---
title: Sunucu Güven Grubu
titleSuffix: Azure SQL Managed Instance
description: Sunucu güven grubu ve Azure SQL yönetilen örnekleri arasındaki güveni yönetme hakkında bilgi edinin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790738"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>SQL yönetilen örnekleri arasında güven ayarlamak ve yönetmek için sunucu güven gruplarını kullanın
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Sunucu güven grubu, Azure SQL yönetilen örnekleri arasındaki güveni yönetmek için kullanılan bir kavramdır. Bir grup oluşturarak, üyeleri arasında sertifika tabanlı bir güven oluşturulur. Bu güven, farklı çapraz örnek senaryolar için kullanılabilir. Sunucuları gruptan kaldırma veya grubu silme, sunucular arasındaki güveni kaldırır. Sunucu güven grubu oluşturmak veya silmek için kullanıcının yönetilen örnek üzerinde yazma izinlerine sahip olması gerekir.
[Sunucu güven grubu](/azure/templates/microsoft.sql/allversions) , Azure Portal **SQL güven grubu** olarak etiketlenmiş bir Azure Resource Manager nesnesidir.

> [!NOTE]
> Sunucu güven grubu, Azure SQL yönetilen örnekleri arasındaki dağıtılmış işlemlerin genel önizlemede kullanıma sunulmuştur ve şu anda bu makalede daha sonra açıklanacak bazı sınırlamalar vardır.

## <a name="server-trust-group-setup"></a>Sunucu güven grubu kurulumu

Aşağıdaki bölümde sunucu güven grubunun kurulumu açıklanmaktadır.

1. [Azure Portal](https://portal.azure.com/) gidin.

2. Yeni oluşturulan bir sunucu güven grubuna eklemeyi planladığınız Azure SQL yönetilen örneği ' ne gidin.

3. **Güvenlik** ayarları ' na **SQL güven grupları** sekmesini seçin.

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Sunucu güveni grupları":::

4. Sunucu güven grubu yapılandırması sayfasında, **Yeni Grup** simgesini seçin.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Yeni Grup":::

5. **SQL güven grubu** oluştur dikey penceresinde **Grup adı**' nı ayarlayın. Grup üyelerinin bulunduğu tüm bölgelerde benzersiz olması gerekir. **Güven kapsamı** , sunucu güven grubuyla etkinleştirilen çapraz örnek senaryonun türünü tanımlar. Önizleme aşamasında yalnızca geçerli güven kapsamı **Dağıtılmış işlemdedir**, bu nedenle önceden seçilir ve değiştirilemez. Tüm **Grup üyeleri** aynı **aboneliğe** ait olmalıdır, ancak farklı kaynak grupları altında olabilir. Grubun üyesi olacak Azure SQL yönetilen örneğini seçmek için **kaynak grubunu** ve **SQL Server/örneğini** seçin.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Sunucu güven grubu oluştur dikey penceresi":::

6. Tüm gerekli alanlar doldurulduktan sonra **Kaydet**' e tıklayın.

## <a name="server-trust-group-maintenance-and-deletion"></a>Sunucu güven grubu bakımı ve silme

Sunucu güven grubu düzenlenemiyor. Yönetilen bir örneği bir gruptan kaldırmak için, grubu silip yeni bir tane oluşturmanız gerekir.

Aşağıdaki bölümde sunucu güven grubu silme işlemi açıklanmaktadır. 
1. Azure portala gidin.
2. Güven grubuna ait yönetilen bir örneğe gidin.
3. **Güvenlik** ayarları ' na **SQL güven grupları** sekmesini seçin.
4. Silmek istediğiniz güven grubunu seçin.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Sunucu güven grubu seç":::
5. **Grubu Sil**' e tıklayın.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Sunucu güven grubunu sil":::
6. Silmeyi onaylamak için sunucu güven grubu adını yazın ve **Sil**' e tıklayın.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Sunucu güven grubu Silmeyi Onayla":::

> [!NOTE]
> Sunucu güven grubunun silinmesi, iki yönetilen örnek arasındaki güveni hemen kaldıramayabilir. Güven kaldırma, yönetilen örneklerin [yük devretmesi](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) çağrılarak zorlanabilir. Bu sorunun en son güncelleştirmelerine yönelik [bilinen sorunları](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues) denetleyin.

## <a name="limitations"></a>Sınırlamalar

Genel Önizleme sırasında, sunucu güven grupları için aşağıdaki sınırlamalar geçerlidir.
 * Sunucu güven grubunun adı, üyelerinin bulunduğu tüm bölgelerde benzersiz olmalıdır.
 * Grup yalnızca Azure SQL yönetilen örnekleri içerebilir ve aynı Azure aboneliği altında olmalıdır.
 * Önizlemede, grupta tam olarak iki yönetilen örnek olabilir. İki yönetilen örnek üzerinde dağıtılmış işlemleri yürütmek için, yönetilen örneklerin her çifti için sunucu güven grubu oluşturmanız gerekir.
 * Dağıtılmış işlemler, sunucu güven grupları için geçerli olan kapsamdır.
 * Sunucu güven grubu yalnızca Azure portal yönetilebilir. PowerShell ve CLı desteği daha sonra sunulacaktır.
 * Sunucu güven grubu Azure portal düzenlenemiyor. Yalnızca oluşturulabilir veya bırakılabilir.
 * Dağıtılmış işlemlerin ek sınırlamaları senaryonuzla ilgili olabilir. Çoğu önemli biri, sanal ağ veya VNET eşlemesi aracılığıyla özel uç noktaları üzerinden yönetilen örnekler arasında bağlantı olması gerekir. [Yönetilen örnek için geçerli dağıtılmış işlem sınırlamalarından](../database/elastic-transactions-overview.md#limitations)haberdar olduğunuzdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

* Azure SQL yönetilen örneği 'nde dağıtılmış işlemler hakkında daha fazla bilgi için bkz. [Dağıtılmış işlemler](../database/elastic-transactions-overview.md).
* Yayın güncelleştirmeleri ve bilinen sorunlar durumu için bkz. [yönetilen örnek sürüm notları](../database/doc-changes-updates-release-notes.md).
* Özellik istekleriniz varsa, bunları [yönetilen örnek forumuna](https://feedback.azure.com/forums/915676-sql-managed-instance)ekleyin.