---
title: Azure Data Lake Storage 1. için olağanüstü durum kurtarma Kılavuzu | Microsoft Docs
description: Azure Data Lake Storage 1. yerel olarak yedekli depolama alanının ötesinde bölge genelinde kesintilerden veya yanlışlıkla silinmelerden daha fazla koruma yapmayı öğrenin.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: 48136f8d9172c3674e849e24efca4ae5070f83ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109128"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Data Lake Storage 1. için yüksek kullanılabilirlik ve olağanüstü durum kurtarma Kılavuzu

Data Lake Storage 1. yerel olarak yedekli depolama (LRS) sağlar. Bu nedenle, Data Lake Storage 1. hesabınızdaki veriler otomatik çoğaltmalar aracılığıyla bir veri merkezindeki geçici donanım arızalarına dayanıklı olur. Bu, dayanıklılık ve yüksek kullanılabilirlik sağlar ve Data Lake Storage 1. SLA 'yı ister. Bu makalede, verilerinizi nadir bölge genelinde kesintiler veya yanlışlıkla silinmelerden daha fazla korumaya yönelik yönergeler sunulmaktadır.

## <a name="disaster-recovery-guidance"></a>Olağanüstü durum kurtarma kılavuzu

Bir olağanüstü durum kurtarma planı hazırlamanız önemlidir. Kendi planınızı oluşturmanıza yardımcı olması için bu makaledeki bilgileri ve bu ek kaynakları gözden geçirin.

* [Azure uygulamaları için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Azure dayanıklılık teknik kılavuzu](/azure/architecture/framework/resiliency/overview)

### <a name="best-practice-recommendations"></a>En iyi yöntem önerileri

Kritik verilerinizi başka bir bölgedeki başka bir Data Lake Storage 1. hesabına, olağanüstü durum kurtarma planınızın ihtiyaçlarına göre hizalı bir sıklık ile kopyalamanızı öneririz. [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md)veya [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)dahil olmak üzere verileri kopyalamak için çeşitli yöntemler vardır. Azure Data Factory, yinelenen bir düzende veri taşıma işlem hatları oluşturmak ve dağıtmak için kullanışlı bir hizmettir.

Bölgesel bir kesinti oluşursa, verilerin kopyalandığı bölgedeki verilerinize erişebilirsiniz. Azure hizmet durumu [panosunu](https://azure.microsoft.com/status/) , dünya genelinde Azure hizmet durumunu tespit etmek için izleyebilirsiniz.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Verilerin bozulması veya yanlışlıkla silinmesi durumunda kurtarma kılavuzu

Data Lake Storage 1. otomatikleştirilmiş çoğaltmalar aracılığıyla veri dayanıklılığı sağlarken, bu, uygulamanızın (veya geliştiricilerin/kullanıcıların) verileri bozmasını veya yanlışlıkla silmesini engellemez.

Yanlışlıkla silinmeye engel olmak için, Data Lake Storage 1. hesabınız için ilk olarak doğru erişim ilkelerini ayarlamanızı öneririz. Bu, önemli kaynakları kilitlemek ve kullanılabilir [Data Lake Storage 1. güvenlik özelliklerini](data-lake-store-security-overview.md)kullanarak hesap ve dosya düzeyinde erişim denetimi uygulamak için [Azure kaynak kilitlerini](../azure-resource-manager/management/lock-resources.md) uygulamayı içerir. Ayrıca, başka bir Data Lake Storage 1. hesabı, klasörü veya Azure aboneliğine [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) veya [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) kullanarak kritik verilerinizin kopyalarını düzenli olarak oluşturmanızı öneririz. Bu yöntem, verilerin bozulması veya silinmesi durumunda kurtarılması için de kullanılabilir. Azure Data Factory, yinelenen bir düzende veri taşıma işlem hatları oluşturmak ve dağıtmak için kullanışlı bir hizmettir.

Ayrıca, veri erişimi denetim izlerini toplamak üzere bir Data Lake Storage 1. hesabı için [Tanılama günlük kaydını](data-lake-store-diagnostic-logs.md) etkinleştirebilirsiniz. Denetim izleri bir dosyayı kimin silmiş veya güncelleştirmiş olabileceği hakkında bilgi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Data Lake Storage 1. kullanmaya başlayın](data-lake-store-get-started-portal.md)
* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)