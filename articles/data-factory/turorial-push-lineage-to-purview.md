---
title: Data Factory köken verilerini Azure Purview’a iletme
description: Azure purview 'a Data Factory kökenini verilerini gönderme hakkında bilgi edinin
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361460"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Azure purview 'a Data Factory kökenini verileri gönderme (Önizleme)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu öğreticide, etkinlikleri çalıştıran bir işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini (UI) ve Azure purview hesabına kökenini verileri rapor etmek için kullanacaksınız. Ardından, tüm kökenini bilgilerini Azure purview hesabınızda görüntüleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure Data Factory**. Azure Data Factory yoksa, bkz. [Azure Data Factory oluşturma](./quickstart-create-data-factory-portal.md).
* **Azure purview hesabı**. Purview hesabı, Data Factory tarafından oluşturulan tüm kökenini verilerini yakalar. Azure purview hesabınız yoksa bkz. [Azure purview oluşturma](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Data Factory etkinlikleri çalıştırın ve Azure purview 'a kökenini verileri gönderin
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>1. Adım: Data Factory purview hesabınıza bağlama
Purview portalında, **Yönetim Merkezi**' ne gidin. **Dış bağlantılarda** **Data Factory** seçin ve yeni bir Data Factory bağlantı oluşturmak için **Yeni** düğme ' ye tıklayın. 
[![Data Factory ile purview hesabı ](./media/data-factory-purview/connect-adf-to-purview.png) arasında bağlantı oluşturma için ekran görüntüsü ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Açılan sayfada, bu purview hesabına bağlamak istediğiniz Data Factory seçebilirsiniz. 
![Yeni bağlantı için ekran görüntüsü](./media/data-factory-purview/new-adf-purview-connection.png)

Bağlantıyı oluşturduktan sonra durumu kontrol edebilirsiniz. **Bağlı** , Data Factory Ile bu takip görünümü arasındaki bağlantının başarıyla bağlandığı anlamına gelir. 
> [!NOTE]
> Data Factory ile Azure purview arasında bağlantı oluşturmak için, purview hesabı ve **katkıda bulunan** rolü Data Factory aşağıdaki rollerden birine atanmalıdır.
> - Sahip
> - Kullanıcı Erişimi Yöneticisi

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>2. Adım: Data Factory içinde kopyalama ve veri akışı etkinliklerini çalıştırma
Data Factory için işlem hatları, kopyalama etkinlikleri ve veri akışı etkinlikleri oluşturabilirsiniz. Kökenini veri yakalama için ek yapılandırma gerekmez. Kökenini verileri, Etkinlik yürütme sırasında otomatik olarak yakalanır.
![Kopyalama ve dataflow etkinliğinin ekran görüntüsü ](./media/data-factory-purview/adf-activities-for-lineage.png) kopyalama ve veri akışı etkinliklerinin nasıl oluşturulacağını bilmiyorsanız, bkz. [Azure Data Factory kullanarak Azure Blob depolama 'DAN Azure SQL veritabanı 'Na veri kopyalama](./tutorial-copy-data-portal.md) ve veri [akışlarını eşleme kullanarak verileri dönüştürme](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>3. Adım: Data Factory 'da SSIS paketi etkinliklerini yürütme
Data Factory için işlem hatları oluşturabilir, SSIS paketi etkinliklerini yürütebilirsiniz. Kökenini veri yakalama için ek yapılandırma gerekmez. Kökenini verileri, Etkinlik yürütme sırasında otomatik olarak yakalanır.
![SSIS paketi yürütme etkinliğinin ekran görüntüsü](./media/data-factory-purview/ssis-activities-for-lineage.png)

Çalışma SSIS paketi etkinliklerini nasıl oluşturacağınız hakkında bilgi sahibi değilseniz bkz. [Azure 'DA SSIS paketlerini çalıştırma](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>4. Adım: kökenini bilgilerini purview hesabınızda görüntüleme
Purview hesabınıza geri dönün. Giriş sayfasında **varlıkları görüntüle**' yi seçin. İstediğiniz varlığı seçin ve kökenini sekmesine tıklayın. Tüm kökenini bilgilerini görürsünüz.
[![Purview hesabının ](./media/data-factory-purview/view-dataset.png) ekran görüntüsü ](./media/data-factory-purview/view-dataset.png#lightbox)

Kopyalama etkinliği için kökenini verilerini görebilirsiniz.
[![Copy kökenini ](./media/data-factory-purview/copy-lineage.png) ekran görüntüsü ](./media/data-factory-purview/copy-lineage.png#lightbox)

Ayrıca, veri akışı etkinliği için kökenini verilerini de görebilirsiniz.
[![Veri akışı kökenini ](./media/data-factory-purview/dataflow-lineage.png) ekran görüntüsü ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> Kökenini of dataflow etkinliği için yalnızca kaynak ve havuz destekliyoruz. Kökenini for dataflow dönüştürmesi henüz desteklenmiyor.

Ayrıca, SSIS paketi yürütme etkinliğinin kökenini verilerini de görebilirsiniz.
[![SSIS kökenini ](./media/data-factory-purview/ssis-lineage.png) ekran görüntüsü ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> SSIS paketi yürütme etkinliğinin kökenini için yalnızca kaynağı ve hedefi destekliyoruz. Kökenini for dönüşümü henüz desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar
[Catalog kökenini Kullanıcı Kılavuzu](../purview/catalog-lineage-user-guide.md)

[Azure purview 'e Data Factory bağlama](connect-data-factory-to-azure-purview.md)