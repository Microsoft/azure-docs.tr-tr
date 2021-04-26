---
title: Bölgeler arası geçiş Azure Data Lake Storage 1. | Microsoft Docs
description: Yeni bölgelerde kullanıma sunulduğunda Azure Data Lake Storage 1. bir geçişi planlarken ve tamamladıktan sonra dikkate almanız gerekenleri öğrenin.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723815"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Bölgeler arasında Azure Data Lake Storage 1. geçirme

Azure Data Lake Storage 1. yeni bölgelerde kullanılabilir hale geldiğinde, yeni bölgeden yararlanmak için bir kerelik geçiş yapmak isteyebilirsiniz. Taşımayı planlarken ve tamamladıktan sonra göz önüne almanız gerekenler hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Daha fazla bilgi için bkz. [ücretsiz Azure hesabınızı hemen oluşturun](https://azure.microsoft.com/pricing/free-trial/).
* **İki farklı bölgede Data Lake Storage 1. hesabı**. Daha fazla bilgi için bkz. [Azure Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Daha fazla bilgi için bkz. [Azure Data Factory'ye giriş](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Geçiş fikirleri

İlk olarak, uygulamanız için en uygun olan geçiş stratejisini, Data Lake Storage 1. verileri yazan, okuyan veya işleyen bir şekilde belirlemek. Bir strateji seçtiğinizde, uygulamanızın kullanılabilirlik gereksinimlerini ve geçiş sırasında gerçekleşen kapalı kalma süresini göz önünde bulundurun. Örneğin, en basit yaklaşım "yükseltme ve-kaydırma" bulutu geçiş modelini kullanmak olabilir. Bu yaklaşımda, tüm verileriniz yeni bölgeye kopyalanırken mevcut bölgenizde uygulamayı duraklatırsınız. Kopyalama işlemi tamamlandığında, yeni bölgede uygulamanızı sürdürür ve sonra eski Data Lake Storage 1. hesabını silebilirsiniz. Geçiş sırasında kapalı kalma süresi gereklidir.

Kapalı kalma süresini azaltmak için yeni bölgede yeni verileri geri almaya hemen başlayabilirsiniz. Gereken en az veri varsa, uygulamanızı yeni bölgede çalıştırın. Arka planda, mevcut Data Lake Storage 1. hesabından eski verileri yeni bölgedeki yeni Data Lake Storage 1. hesabına kopyalamaya devam edin. Bu yaklaşımı kullanarak, az kapalı kalma süresiyle yeni bölgeye geçiş yapabilirsiniz. Eski veriler kopyalandığında, eski Data Lake Storage 1. hesabını silin.

Geçişinizi planlarken göz önünde bulundurmanız gereken diğer önemli ayrıntılar şunlardır:

* **Veri hacmi**. Veri hacmi (gigabayt cinsinden, dosya ve klasör sayısı vb.), geçiş için gereken süreyi ve kaynakları etkiler.

* **Data Lake Storage 1. hesap adı**. Yeni bölgedeki yeni hesap adı genel olarak benzersiz olmalıdır. Örneğin, Doğu ABD 2 eski Data Lake Storage 1. Hesabınızın adı contosoeastus2.azuredatalakestore.net olabilir. Yeni Data Lake Storage 1. hesabınızı Kuzey AB contosonortheu.azuredatalakestore.net olarak adlandırın.

* **Araçlar**. Data Lake Storage 1. dosyalarını kopyalamak için [Azure Data Factory kopyalama etkinliğini](../data-factory/connector-azure-data-lake-store.md) kullanmanızı öneririz. Data Factory yüksek performans ve güvenilirliğe sahip veri hareketini destekler. Data Factory yalnızca klasör hiyerarşisini ve dosyaların içeriğini kopyaladığını aklınızda bulundurun. Eski hesapta kullandığınız erişim denetim listelerini (ACL 'Ler) yeni hesaba el ile uygulamanız gerekir. En iyi örnek senaryolar için performans hedefleri dahil daha fazla bilgi için bkz. [kopyalama etkinliği performansı ve ayarlama Kılavuzu](../data-factory/copy-activity-performance.md). Verilerin daha hızlı bir şekilde kopyalanmasını isterseniz, ek bulut veri taşıma birimleri kullanmanız gerekebilir. AdlCopy gibi bazı diğer araçlar, verilerin bölgeler arasında kopyalanmasını desteklemez.  

* **Bant genişliği ücretleri**. [Bant genişliği ücretleri](https://azure.microsoft.com/pricing/details/bandwidth/) geçerlidir çünkü veriler bir Azure bölgesinden dışarı aktarılmıştır.

* **Verilerinize Ilişkin ACL 'ler**. ACL 'Leri dosyalara ve klasörlere uygulayarak yeni bölgedeki verilerinizi güvenli hale getirin. Daha fazla bilgi için bkz. [Azure Data Lake Storage 1. depolanan verileri güvenli hale getirme](data-lake-store-secure-data.md). ACL 'nizi güncelleştirmek ve ayarlamak için geçişi kullanmanızı öneririz. Geçerli ayarlarınıza benzer ayarları kullanmak isteyebilirsiniz. Azure portal, [PowerShell cmdlet 'leri](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)veya SDK 'ları kullanarak herhangi bir dosyaya uygulanan ACL 'leri görüntüleyebilirsiniz.  

* **Analiz hizmetlerinin konumu**. En iyi performansı elde etmek için, Azure Data Lake Analytics veya Azure HDInsight gibi analiz hizmetlerinizin, verileriniz ile aynı bölgede olması gerekir.  

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Data Lake Storage 1. genel bakış](data-lake-store-overview.md)
