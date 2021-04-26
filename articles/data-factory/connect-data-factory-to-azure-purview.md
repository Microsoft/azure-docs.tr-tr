---
title: Azure Purview’a bir Data Factory bağlama
description: Azure purview 'a bir Data Factory bağlama hakkında bilgi edinin
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727949"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory Azure purview 'a bağlanma (Önizleme)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede Data Factory Azure purview 'a nasıl bağlanabileceğiniz ve Azure Data Factory etkinliklerin veri kopyalama, veri akışı ve SSIS paketi yürütme kökenini etkinliklerinin nasıl raporlanmasının açıklanmaktadır.


## <a name="connect-data-factory-to-azure-purview"></a>Azure purview 'e Data Factory bağlama
Azure purview, veri kullanıcılarının bulut ve şirket içi ortamları kapsayan verileri genelinde veri yönetimini merkezi olarak yönetmesine yönelik yeni bir bulut hizmetidir. Data Factory Azure Mağazası 'na bağlayabilirsiniz ve bağlantı, kopyalama, veri akışı ve SSIS paketi yürütme kökenini verilerini yakalamak için Azure purview 'ı kullanmanıza olanak sağlar. Data Factory 'yi Azure purview 'a bağlamak için iki yol vardır:
### <a name="register-azure-purview-account-to-data-factory"></a>Azure purview hesabını Data Factory kaydetme
1. ADF portalında,   ->  **Azure purview**'ı Yönet ' e gidin. **Bir purview hesabına Bağlan**' ı seçin. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Bir purview hesabı kaydetme ekran görüntüsü.":::
2. **Azure aboneliği** arasından seçim yapabilir veya **el ile girebilirsiniz**. **Azure aboneliğinden** erişiminiz olan hesabı seçebilirsiniz. 
3. Bağlandıktan sonra, **purview hesabı**' nda yer alarak purview hesabının adını görmeniz gerekir. 
4. Veri aramak için Azure Data Factory portalının en üst merkezinde bulunan arama çubuğunu kullanabilirsiniz. 

Azure purview hesabını Data Factory 'a kaydettikten sonra Azure Data Factory portalında uyarı görürseniz, sorunu onarmak için aşağıdaki adımları izleyin:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Bir purview hesabının kaydedilmesinin uyarısı için ekran görüntüsü.":::

1. Azure portal gidin ve veri fabrikanızı bulun. Bölüm "Etiketler" i seçin ve **catalogUri** adlı bir etiket olup olmadığını görün. Aksi takdirde, lütfen ADF portalında Azure purview hesabının bağlantısını kesip yeniden bağlayın.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Bir purview hesabı kaydetme etiketlerinin ekran görüntüsü.":::

2. Data Factory bir Azure purview hesabı kaydetmeye izin verilip verilmediğini denetleyin. Bkz. [bağlanma Azure Data Factory ve Azure purview](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Azure purview 'da Data Factory kaydetme
Data Factory Azure purview ' de nasıl kaydedeceğinizi öğrenmek için bkz. [bağlanma Azure Data Factory ve Azure purview](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Kökenini verilerini Azure purview 'a raporla
Müşteriler Azure Data Factory ' de kopyalama, veri akışı veya SSIS paketi yürütme etkinliğini çalıştırdığınızda, müşteriler bağımlılık ilişkisini alabilir ve veri kaynakları ve hedef arasındaki tüm iş akışı işlemine ilişkin üst düzey bir genel bakışa sahip olabilir.
Azure Data Factory kökenini nasıl toplayacağınız için bkz. [Data Factory kökenini](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Sonraki adımlar
[Catalog kökenini Kullanıcı Kılavuzu](../purview/catalog-lineage-user-guide.md)

[Öğretici: Azure purview 'a Data Factory kökenini verileri gönderme](turorial-push-lineage-to-purview.md)