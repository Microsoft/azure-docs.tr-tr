---
title: Azure purview kullanarak SYNAPSE içindeki verileri bulma, bağlama ve keşfetme
description: Verileri bulma, bunları bağlama ve SYNAPSE içinde keşfetme hakkında rehberlik
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567902"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Azure purview kullanarak SYNAPSE içindeki verileri bulma, bağlama ve keşfetme 

Bu belgede, bir Azure purview hesabını SYNAPSE 'e kaydederken gerçekleştirebileceğiniz etkileşimlerin türünü öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar 

- [Azure purview hesabı](../../purview/create-catalog-portal.md) 
- [SYNAPSE çalışma alanı](../quickstart-create-workspace.md) 
- [Azure purview hesabını SYNAPSE 'e bağlama](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>SYNAPSE 'de Azure purview kullanma 

SYNAPSE içinde Azure purview kullanımı, bu purview hesabına erişiminizin olmasını gerektirir. SYNAPSE geçişleri-purview izniniz aracılığıyla. Örnek olarak, bir Curator izin rolünüzün olması, Azure purview tarafından taranan meta verileri düzenleyebileceksiniz. 

### <a name="data-discovery-search-datasets"></a>Veri bulma: veri kümelerini ara 

Azure takip görünümü tarafından kaydedilen ve taranan verileri bulmak için, SYNAPSE çalışma alanının üst merkezinde bulunan arama çubuğunu kullanabilirsiniz. Tüm kuruluş verilerinizi aramak için Azure takip görünümü ' nü seçtiğinizden emin olun. 

[![Azure purview varlıklarını arayın](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure purview eylemleri 

SYNAPSE ' de kullanılabilen Azure purview özelliklerinin bir listesi aşağıda verilmiştir: 
- Meta verilere **genel bakış** 
- Sınıflandırmalar, Sözlük terimleri, veri türleri ve açıklamalarla meta verilerin **şemasını** görüntüleyin ve düzenleyin 
- Bağımlılıkları anlamak ve etki analizini yapmak için **kökenini** görüntüleyin. Hakkında daha fazla bilgi için bkz. [kökenini](../../purview/catalog-lineage-user-guide.md)
- Bir veri kümesi üzerinde kimin sahip veya uzman olduğunu bildiğiniz **kişileri** görüntüleyin ve düzenleyin 
- Belirli bir veri kümesinin hiyerarşik bağımlılıklarını anlamak için **ilgili** . Bu deneyim, veri hiyerarşisine gözatmak için yararlıdır.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>SYNAPSE kaynaklarıyla veri kümeleri üzerinden gerçekleştirebileceğiniz eylemler 

### <a name="connect-data-to-synapse"></a>Verileri SYNAPSE 'e bağlama 

- SYNAPSE için yeni bir **bağlı hizmet** oluşturabilirsiniz. Bu eylem, verileri SYNAPSE 'e kopyalamak veya veri hub 'ınıza (ADLSg2 gibi desteklenen veri kaynakları için) eklemek için gerekir 
- Dosyalar, klasörler veya tablolar gibi nesneler için, doğrudan **Yeni bir tümleştirme veri kümesi** oluşturabilir ve zaten oluşturulmuşsa mevcut bir bağlı hizmetten yararlanabilirsiniz 

Mevcut bir bağlı hizmet veya tümleştirme veri kümesi varsa, henüz çıkarsanamıyor. 

###  <a name="develop-in-synapse"></a>SYNAPSE 'de geliştirme 

Gerçekleştirebileceğiniz üç eylem vardır: **yenı SQL betiği**, **Yeni Not defteri** ve **Yeni veri akışı**. 

Destek türüne bağlı olarak **yenı SQL betiği** ile şunları yapabilirsiniz: 
- Verilerin şeklini anlamak için ilk 100 satırı görüntüleyin. 
- SYNAPSE SQL veritabanından dış tablo oluşturma 
- Verileri bir Synapse SQL veritabanına yükleme 
 
**Yeni Not defteri** ile şunları yapabilirsiniz: 
- Verileri Spark veri çerçevesine yükleme 
- Spark tablosu oluşturma (Bu şekilde Parquet biçiminde yaparsanız, aynı zamanda sunucusuz bir SQL havuzu tablosu da oluşturulur). 
 
**Yeni veri akışı** ile veri akışı ardışık düzeninde bir kaynak kullanılabilen bir tümleştirme veri kümesi oluşturabilirsiniz. Veri akışı, veri dönüştürme işlemi gerçekleştirmek için kod içermeyen bir geliştirici özelliğidir. [SYNAPSE içinde veri akışı kullanma](../quickstart-data-flow.md)hakkında daha fazla bilgi için.

##  <a name="nextsteps"></a>Sonraki adımlar 

- [Azure SYNAPSE varlıklarını Azure purview 'da kaydetme ve tarama](../../purview/register-scan-azure-synapse-analytics.md)
- [Azure purview Veri Kataloğu 'nda veri arama](../../purview/how-to-search-catalog.md)
