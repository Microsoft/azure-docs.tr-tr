---
title: Yedekleme Merkezi'ni kullanarak tüm yedeklemelerinizi idare etme
description: Tüm kaynaklarınızın yedekleme merkezi ile yedekleme açısından uyumlu olduğundan emin olmak için Azure ortamınızı nasıl yöneteceğinizi öğrenin.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 283c99c4b17683850f71b25fb2006784e43f3b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506218"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Yedekleme Merkezi'ni kullanarak tüm yedeklemelerinizi idare etme

Yedekleme merkezi, tüm kaynaklarınızın yedekleme açısından uyumlu olduğundan emin olmak için Azure ortamınızı yönetmenize yardımcı olur. Aşağıda, yedekleme merkezi 'nin bazı idare özellikleri verilmiştir:

* Yedekleme için Azure Ilkelerini görüntüleyin ve atayın

* Yedekleme için tüm yerleşik Azure Ilkelerinde kaynaklarınızın uyumluluğunu görüntüleyin.

* Yedekleme için yapılandırılmamış tüm veri kaynaklarını görüntüleyin.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

* Desteklenen ve desteklenmeyen senaryoların ayrıntılı bir listesi için [destek matrisine](backup-center-support-matrix.md) bakın.

## <a name="azure-policies-for-backup"></a>Yedekleme için Azure Ilkeleri

Yedekleme için kullanılabilen tüm [Azure ilkelerini](../governance/policy/overview.md) görüntülemek için yedekleme menü öğesi **için Azure ilkeleri** ' ni seçin. Bu, abonelikleriniz ve kaynak gruplarınız için kullanılabilir olan [yedekleme için yerleşik ve özel Azure ilke tanımlarının](policy-reference.md) tümünü görüntüler.

Tanımlardan herhangi birini seçmek, ilkeyi bir kapsama [atamanıza](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) izin verir.

![Azure Ilke tanımlarını seçin](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Yedekleme uyumluluğu

Yedekleme uyumluluğu menü öğesine tıkladığınızda kaynaklarınızın [uyumluluğunu](../governance/policy/how-to/get-compliance-data.md) , Azure ortamınıza atadığınız çeşitli yerleşik ilkelere göre görüntülemenize yardımcı olur. Tüm ilkelerle uyumlu olan kaynakların yüzdesini ve bir veya daha fazla uyumlu olmayan kaynağa sahip ilkeleri görüntüleyebilirsiniz.

![Yedekleme uyumluluğunu görüntüleme](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Korunabilir veri kaynakları

**Korunabilir veri kaynakları** menü öğesinin seçilmesi, yedekleme için yapılandırılmamış tüm veri kaynaklarınızı görüntülemenize izin verir. Listeye veri kaynağı aboneliği, kaynak grubu, konum, tür ve etiketlere göre filtre uygulayabilirsiniz. Yedeklenmesi gereken bir veri kaynağını tanımladıktan sonra, ilgili kılavuz öğesine sağ tıklayıp kaynak için yedeklemeyi yapılandırmak üzere **Yedekle** ' yi seçebilirsiniz.

![Korunabilir veri kaynakları menüsü](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Veri kaynağı türü olarak **Azure VM 'de SQL** ' i seçerseniz **korunabilir veri kaynakları** görünümü, yedekleme IÇIN yapılandırılmış herhangi bir SQL veritabanına sahip olmayan tüm Galeri VM 'lerinin listesini görüntüler.
> Veri kaynağı türü olarak **Azure depolama (Azure dosyaları)** seçeneğini belirlerseniz **korunabilir veri kaynakları** görünümü, yedekleme için yapılandırılmış herhangi bir dosya paylaşımına sahip olmayan tüm depolama hesaplarının (dosya paylaşımlarını destekleyen) listesini görüntüler.


## <a name="next-steps"></a>Sonraki adımlar

* [Yedeklemeleri izleme ve çalıştırma](backup-center-monitor-operate.md)
* [Yedekleme merkezi 'ni kullanarak eylem gerçekleştirme](backup-center-actions.md)
* [Yedeklemeleriniz hakkında Öngörüler edinin](backup-center-obtain-insights.md)