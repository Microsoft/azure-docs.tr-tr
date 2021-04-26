---
title: Verileri tasarımcıya aktarma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning veri kümeleri ve veri Içeri aktarma modülünü kullanarak Azure Machine Learning tasarımcısına verileri nasıl içeri aktaracağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: a2cc0840b7ba4b26cf9f5b1219fc189230870774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97739867"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Azure Machine Learning tasarımcısına veri aktarma

Bu makalede, özel çözümler oluşturmak için kendi verilerinizi tasarımcıda nasıl içeri aktaracağınızı öğreneceksiniz. Verileri tasarımcıya içeri aktarabilmeniz için kullanabileceğiniz iki yol vardır: 

* **Azure Machine Learning veri kümeleri** -verilerinizi yönetmenize yardımcı olan gelişmiş özellikleri etkinleştirmek için Azure Machine Learning veri [kümelerini](concept-data.md#datasets) kaydettirin.
* **Veri modülünü Içeri aktarma** -çevrimiçi veri kaynaklarında verilere doğrudan erişmek Için [verileri içeri aktarma](algorithm-module-reference/import-data.md) modülünü kullanın.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning veri kümelerini kullanma

Verileri tasarımcıya aktarmak için veri [kümelerini](concept-data.md#datasets) kullanmanızı öneririz. Bir veri kümesini kaydettiğinizde, [sürüm oluşturma ve izleme](how-to-version-track-datasets.md) ve [veri izleme](how-to-monitor-datasets.md)gibi gelişmiş veri özelliklerinden tam olarak yararlanabilirsiniz.

### <a name="register-a-dataset"></a>Veri kümesini kaydetme

Var olan veri kümelerini [SDK ile program aracılığıyla](how-to-create-register-datasets.md#datasets-sdk) veya [Azure Machine Learning Studio 'da görsel olarak](how-to-connect-data-ui.md#create-datasets)kaydedebilirsiniz.

Ayrıca, herhangi bir tasarımcı modülünün çıkışını bir veri kümesi olarak kaydedebilirsiniz.

1. Kaydetmek istediğiniz verilerin çıkışı olan modülü seçin.

1. Özellikler bölmesinde, **çıktılar + Günlükler**  >  **kayıt veri kümesi**' ni seçin.

    ![Register DataSet seçeneğine nasıl gidebileceğiniz gösteren ekran görüntüsü](media/how-to-designer-import-data/register-dataset-designer.png)

Modül çıkış verileri tablosal biçimindeyse, çıktıyı bir **dosya veri kümesi** veya **tablo veri kümesi** olarak kaydetmeyi seçmeniz gerekir.

 - **Dosya veri kümesi** , modülün çıkış klasörünü dosya veri kümesi olarak kaydeder. Çıktı klasörü, tasarımcı 'nın dahili olarak kullandığı bir veri dosyası ve meta dosyaları içerir. Tasarımcıda kayıtlı veri kümesini kullanmaya devam etmek istiyorsanız bu seçeneği belirleyin. 

 - **Tablo veri kümesi** , yalnızca modülün çıkış veri dosyasını tablo veri kümesi olarak kaydeder. Bu biçim, örneğin otomatikleştirilmiş Machine Learning veya Python SDK 'Sı gibi diğer araçlarla kolayca tüketilebilir. Kayıtlı veri kümesini Tasarımcı dışında kullanmayı planlıyorsanız bu seçeneği belirleyin.  
 

### <a name="use-a-dataset"></a>Veri kümesi kullanma

Kayıtlı veri kümeleriniz, **veri kümeleri** altında modül paletinde bulunabilir. Bir veri kümesini kullanmak için sürükleyin ve ardışık düzen tuvaline bırakın. Ardından, veri kümesinin çıkış bağlantı noktasını tuvaldeki diğer modüllere bağlayın. 

Bir dosya veri kümesini kaydettiğinizde, veri kümesinin çıkış bağlantı noktası türü **Anydirectory** olur. Tablosal veri kümesini kaydettiğinizde **Dataframedirectory** ise veri kümesinin çıkış bağlantı noktası türüdür. Veri kümesinin çıkış bağlantı noktasını tasarımcıda diğer modüllere bağladığınızda, veri kümelerinin ve modüllerin bağlantı noktası türünün hizalı olması gerektiğini unutmayın.

![Tasarımcı paletindeki kaydedilen veri kümelerinin konumunu gösteren ekran görüntüsü](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Tasarımcı [veri kümesi sürümü oluşturmayı](how-to-version-track-datasets.md)destekler. Veri kümesi modülünün Özellik panelinde veri kümesi sürümünü belirtin.

### <a name="limitations"></a>Sınırlamalar 

- Şu anda tasarımcıda tablo veri kümesini görselleştirebilirsiniz. Tasarımcı dışında bir dosya veri kümesi kaydettiğinizde, tasarımcı tuvalinde görselleştirilemez.
- Veri kümeniz sanal ağ (VNet) içinde depolanır. Görselleştirmek istiyorsanız, veri deposundaki çalışma alanı yönetilen kimliğini etkinleştirmeniz gerekir.
    1. İlgili veri deposuna gidin ve **kimlik bilgilerini güncelleştir** 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="güncelleştirme kimlik bilgilerini"::: seçin
    1. Çalışma alanı yönetilen kimliğini etkinleştirmek için **Evet** ' i seçin.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Çalışma alanı yönetilen kimliğini etkinleştir":::

## <a name="import-data-using-the-import-data-module"></a>Verileri Içeri aktarma modülünü kullanarak içeri aktarma

Verileri içeri aktarmak için veri kümelerini kullanmanızı öneririz, ayrıca [verileri Içeri aktarma](algorithm-module-reference/import-data.md) modülünü de kullanabilirsiniz. Veri Içeri aktarma modülü, veri kümenizin Azure Machine Learning kaydedilmesini atlar ve verileri doğrudan bir [veri deposundan](concept-data.md#datastores) veya http url 'sinden içeri aktarır.

Veri alma modülünü kullanma hakkında ayrıntılı bilgi için bkz. [veri başvurusunu Içeri aktarma sayfası](algorithm-module-reference/import-data.md).

> [!NOTE]
> Veri kümenizin çok fazla sütunu varsa, aşağıdaki hatayla karşılaşabilirsiniz: "boyut sınırlaması nedeniyle doğrulama başarısız oldu". Bunu önlemek için [veri kümesini veri kümeleri arabirimine kaydedin](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Desteklenen kaynaklar

Bu bölüm, tasarımcı tarafından desteklenen veri kaynaklarını listeler. Veriler bir veri deposundan veya [tablo veri kümesinden](how-to-create-register-datasets.md#dataset-types)tasarımcıya gelir.

### <a name="datastore-sources"></a>Veri deposu kaynakları
Desteklenen veri deposu kaynaklarının listesi için bkz. [Azure Storage hizmetlerindeki verilere erişme](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Tablo veri kümesi kaynakları

Tasarımcı, aşağıdaki kaynaklardan oluşturulan tablo veri kümelerini destekler:
 * Sınırlandırılmış dosyalar
 * JSON dosyaları
 * Parquet dosyaları
 * SQL sorguları

## <a name="data-types"></a>Veri türleri

Tasarımcı aşağıdaki veri türlerini dahili olarak tanır:

* Dize
* Tamsayı
* Ondalık
* Boole
* Tarih

Tasarımcı, modüller arasında veri geçirmek için bir iç veri türü kullanır. Veri [kümesine Dönüştür](algorithm-module-reference/convert-to-dataset.md) modülünü kullanarak verilerinizi veri tablosu biçimine açıkça dönüştürebilirsiniz. İç biçim dışındaki biçimleri kabul eden herhangi bir modül, verileri bir sonraki modüle geçirmeden önce sessizce dönüştürür.

## <a name="data-constraints"></a>Veri kısıtlamaları

Tasarlayıcıdaki modüller, işlem hedefinin boyutuyla sınırlıdır. Daha büyük veri kümelerinde daha büyük bir Azure Machine Learning işlem kaynağı kullanmanız gerekir. Azure Machine Learning işlem hakkında daha fazla bilgi için bkz. [Azure Machine Learning işlem hedefleri nelerdir?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Bir sanal ağdaki verilere erişme

Çalışma alanınız bir sanal ağda ise, tasarımcıda verileri görselleştirmek için ek yapılandırma adımları gerçekleştirmeniz gerekir. Bir sanal ağda veri depoları ve veri kümelerinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure sanal ağında Azure Machine Learning Studio 'Yu kullanma](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticiyle tasarımcı temellerini öğrenin [: tasarımcı ile otomobil fiyatını tahmin](tutorial-designer-automobile-price-train-score.md)edin.
