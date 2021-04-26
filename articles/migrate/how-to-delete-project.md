---
title: Azure Geçişi projesini silme
description: Bu makalede, Azure portal kullanarak bir Azure geçişi projesini nasıl silebileceğinizi öğrenin.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714749"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Geçişi projesini silme

Bu makalede bir [Azure geçişi](./migrate-services-overview.md) projesinin nasıl silineceği açıklanır.


## <a name="before-you-start"></a>Başlamadan önce

Bir projeyi silmeden önce:

- Bir projeyi sildiğinizde, proje ve bulunan makine meta verileri silinir.
- Bağımlılık analizi için sunucu değerlendirmesi aracına bir Log Analytics çalışma alanı eklediyseniz, çalışma alanını silmek istediğinize karar verin. 
    - Çalışma alanı otomatik olarak silinmez. El ile silin.
    - Silmeden önce bir çalışma alanının ne için kullanıldığını doğrulayın. Aynı Log Analytics çalışma alanı birden çok senaryo için kullanılabilir.
    - Projeyi silmeden önce, OMS çalışma alanı altında **Azure geçişi-sunucuları**  >  **Azure geçişi-sunucu değerlendirmesi**' ndeki çalışma alanına bir bağlantı bulabilirsiniz. 
    - Bir projeyi sildikten sonra bir çalışma alanını silmek için ilgili kaynak grubundaki çalışma alanını bulun ve [Bu yönergeleri](../azure-monitor/logs/delete-workspace.md)izleyin.


## <a name="delete-a-project"></a>Projeyi silme


1. Azure portal, projenin oluşturulduğu kaynak grubunu açın.
2. Kaynak grubu sayfasında **gizli türleri göster**' i seçin.
3. Projeyi ve silmek istediğiniz ilişkili kaynakları seçin.
    - Azure geçişi projelerinin kaynak türü **Microsoft. Migrate/migrateprojects**.
    - Bir sonraki bölümde, bir Azure geçişi projesinde bulma, değerlendirme ve geçiş için oluşturulan kaynakları gözden geçirin.
    - Kaynak grubu yalnızca Azure geçişi projesini içeriyorsa, tüm kaynak grubunu silebilirsiniz.
    - Azure geçişi 'nin önceki sürümünden bir projeyi silmek istiyorsanız, adımlar aynıdır. Bu projelerin kaynak türü **geçiş projem**.


## <a name="created-resources"></a>Oluşturulan kaynaklar

Bu tablolar, bir Azure geçişi projesinde bulma, değerlendirme ve geçiş için oluşturulan kaynakları özetler.

> [!NOTE]
> Anahtar kasasını, güvenlik anahtarları içerebileceğinden dikkatli bir şekilde silin.

### <a name="projects-with-public-endpoint-connectivity"></a>Ortak uç nokta bağlantısı olan projeler

#### <a name="vmwarephysical-server"></a>VMware/fiziksel sunucu

**Kaynak** | **Tür**
--- | ---
"Appliancename" kV | Key Vault
"Appliancename" sitesi | Microsoft. OffAzure/VMwareSites
ProjectName | Microsoft. Migrate/migrateprojects
"ProjectName" projesi | Microsoft. Migrate/assessmentProjects
"ProjectName" rskasa | Kurtarma Hizmetleri kasası
"ProjectName"-Migratekasa-* | Kurtarma Hizmetleri kasası
migrateappligwsa* | Depolama hesabı
migrateappzasa * | Depolama hesabı
migrateapplicsa* | Depolama hesabı
migrateapplikv * | Key Vault
migrateapplisbns* | Service Bus Ad Alanı

#### <a name="hyper-v-vm"></a>Hyper-V VM

**Kaynak** | **Tür**
--- | ---
ProjectName | Microsoft. Migrate/migrateprojects
"ProjectName" projesi | Microsoft. Migrate/assessmentProjects
HyperV * kV | Key Vault
HyperV * sitesi | Microsoft. OffAzure/HyperVSites
"ProjectName"-Migratekasa-* | Kurtarma Hizmetleri kasası

<br/>
Aşağıdaki tabloda, Azure [özel bağlantısı](./how-to-use-azure-migrate-with-private-endpoints.md)kullanarak özel bir ağ üzerinden sunucu bulma, değerlendirme ve geçirme Için Azure geçişi tarafından oluşturulan kaynaklar özetlenmektedir.

### <a name="projects-with-private-endpoint-connectivity"></a>Özel uç nokta bağlantısı olan projeler

#### <a name="vmware-vms---agentless-migrations"></a>VMware VM 'Leri-aracısız geçişler

**Tür** | **Kaynak** | **Özel uç nokta <br/>** |
--- | --- | ---
Microsoft. Migrate/migrateprojects | ProjectName | "ProjectName" \* PE 
Bulma sitesi (ana site) | "ProjectName" * mastersite | "ProjectName" \* mastersite \* PE 
Microsoft. Migrate/assessmentProjects | "ApplianceName" * projesi | "ApplianceName" \* Proje \* PE 
Key Vault | "ProjectName" * kV | "ProjectName" \* kV \* PE
Microsoft. OffAzure/VMwareSites | "ApplianceName" * sitesi | NA
Kurtarma Hizmetleri kasası | "ApplianceName" * kasa | NA
Depolama hesabı | "ApplianceName" * ABD | "ApplianceName" \* USA \* PE
Kurtarma Hizmetleri kasası | "ProjectName"-Migratekasa-* | NA
Depolama hesabı | migrateappligwsa* | NA
Depolama hesabı | migrateappzasa * | NA
Key Vault | migrateapplikv * | NA
Service Bus Ad Alanı | migrateapplisbns* | NA

#### <a name="hyper-v-vms"></a>Hyper-V Sanal Makineleri 

**Tür** | **Kaynak** | **Özel uç nokta <br/>** |
--- | --- | ---
Microsoft. Migrate/migrateprojects | ProjectName | "ProjectName" \* PE 
Bulma sitesi (ana site) | "ProjectName" * mastersite | "ProjectName" \* mastersite \* PE 
Microsoft. Migrate/assessmentProjects | "ApplianceName" * projesi | "ApplianceName" \* Proje \* PE 
Key Vault | "ProjectName" * kV | "ProjectName" \* kV \* PE
Microsoft. OffAzure/HyperVSites | "ApplianceName" * sitesi | NA
Kurtarma Hizmetleri kasası | "ProjectName"-Migratekasa-* | "ProjectName"-Migratekasa-* PE

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Fiziksel sunucular/AWS VM 'Leri/GCP VM 'Leri 

**Tür** | **Kaynak** | **Özel uç nokta <br/>** |
--- | --- | ---
Microsoft. Migrate/migrateprojects | ProjectName | "ProjectName" \* PE 
Bulma sitesi (ana site) | "ProjectName" * mastersite | "ProjectName" \* mastersite \* PE 
Microsoft. Migrate/assessmentProjects | "ApplianceName" * projesi | "ApplianceName" \* Proje \* PE 
Key Vault | "ProjectName" * kV | "ProjectName" \* kV \* PE
Microsoft. OffAzure/sunucusites | "ApplianceName" * sitesi | NA
Kurtarma Hizmetleri kasası | "ProjectName"-Migratekasa-* | "ProjectName"-Migratekasa-* PE


## <a name="next-steps"></a>Sonraki adımlar

Ek [değerlendirme](how-to-assess.md) ve [geçiş](how-to-migrate.md) araçları eklemeyi öğrenin. 
