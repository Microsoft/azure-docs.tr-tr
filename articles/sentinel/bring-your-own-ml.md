---
title: Kendi ML 'nizi Azure Sentinel 'e taşıyın | Microsoft Docs
description: Bu makalede, Azure Sentinel 'de veri analizi için kendi makine öğrenimi algoritmalarınızı oluşturma ve kullanma açıklanmaktadır.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 2164b8ac6e62b8826d5879da07384769c503bfb5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598610"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Kendi Machine Learning (ML) Azure Sentinel 'e taşıyın

Machine Learning (ML), Azure Sentinel 'in ana underpinnings ve bunu ayrı olarak ayarlanmış ana özniteliklerden biridir. Azure Sentinel, çeşitli deneyimlerde ML 'yi sunmaktadır: [Fusion](fusion.md) bağıntı motoru ve Jupyter Not defterleri ve yeni kullanılabilir derleme-kendi ml (BYO ml) platformunda yerleşik olarak bulunur. 

ML algılama modelleri, yanlış pozitifleri azaltmak ve geleneksel bir yaklaşımla bulunmayan tehditleri belirlemek için bireysel ortamlara ve kullanıcı davranışlarındaki değişikliklere uyum sağlayabilir. Birçok güvenlik kuruluşu, güvenlik için ML 'nin değerini anlasa da, çoğu güvenlik ve ML 'de uzmanlığa sahip olan profesyonellere merkezlerini. Burada, güvenlik kuruluşları ve uzmanlarının ML yolculuğunda bizimle büyümek üzere sunulan çerçeveyi tasarlıyoruz. ML 'ye veya gerekli uzmanlığa sahip olmayan kuruluşlar, Azure Sentinel 'in yerleşik ML özelliklerine göre önemli bir koruma değeri alabilir.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="Machine Learning çerçevesi":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Kendi Machine Learning (BYO-ML) platformunu getir nedir?

ML kaynaklarına sahip kuruluşlar ve benzersiz iş ihtiyaçları için özelleştirilmiş ML modelleri derlemek istiyorsanız **BYO-ml platformunu** sunuyoruz. Platform, [](/azure/databricks/scenarios/what-is-azure-databricks) / ml ortamını oluşturmak için Azure Databricks[Apache Spark](http://spark.apache.org/) ortamı ve jupi not defterlerini kullanır. Aşağıdaki bileşenleri sağlar:

- verilere erişmenize ve sonuçları Log Analytics (LA) ' a İtmenize yardımcı olacak kitaplıkları içeren bir BYO-ML paketi, sonuçları algılama, araştırma ve arama ile tümleştirebilmenizi sağlar. 

- Kuruluşunuzdaki belirli güvenlik sorunlarına uyacak şekilde özelleştirebileceğiniz ML algoritması şablonları. 

- modeli eğitme ve model Puanlama zamanlaması için örnek Not defterleri. 

Tüm bunların yanı sıra, kendi ML modellerinizi ve/veya kendi Spark ortamınızı Azure Sentinel ile tümleştirilebilen şekilde getirebilirsiniz.

BYO-ML platformunda, kendi ML modellerinizi oluşturmaya yönelik bir hızlı başlangıç yapabilirsiniz: 

- Örnek veriler içeren Not defteri, üretim verilerini işleme konusunda endişelenmenize gerek kalmadan uçtan uca deneyim almanıza yardımcı olur.

- Spark ortamıyla tümleştirilmiş paket, altyapıyı yönetme konusundaki güçlükleri ve kesintileri azaltır.

- Kitaplıklar veri taşımalarını destekler. Eğitim ve Puanlama Not defterleri, uçtan uca deneyimi gösterir ve ortamınıza uyarlamanız için bir şablon olarak görev yapar.

### <a name="use-cases"></a>Uygulama alanları
 
BYO-ML platformu ve paketi, kendi ML algılamalarını oluşturmanız için gereken zaman ve çabayı önemli ölçüde azaltır ve Azure Sentinel 'de belirli güvenlik sorunlarını çözme özelliğini açığa çıkarın. Platform aşağıdaki kullanım örneklerini destekler:

**Özelleştirilmiş bir model almak IÇIN ml algoritmasını eğitme:** Mevcut bir ML algoritmasını (Microsoft tarafından veya kullanıcı topluluğu tarafından paylaşılır) alabilir ve verilerinize ve ortamınıza daha uygun bir özelleştirilmiş ML modeli almak için kendi verilerinize kolayca eğitebilirsiniz.

**Özelleştirilmiş modeli almak için BIR ml algoritması şablonunu değiştirin:** Bir ML algoritma şablonunu (Microsoft veya kullanıcı topluluğu tarafından paylaşılır) değiştirebilir ve özel bir modeli belirli bir sorunla karşılayacak şekilde türetmek için kendi verilerinizde değiştirilen algoritmayı eğitebilirsiniz.

**Kendi modelinizi oluşturun:** Azure Sentinel 'in BYO-ML platformu ve yardımcı programlarını kullanarak kendi modelinizi sıfırdan oluşturun.

**Databricks/Spark ortamınızı tümleştirin:** Mevcut Databricks/Spark ortamınızı Azure Sentinel 'e tümleştirin ve kendi benzersiz durumları için ML modelleri derlemek için BYO-ML kitaplıklarını ve şablonlarını kullanın.

**Kendı ml modelinizi Içeri aktarın:** Kendi ML modellerinizi içeri aktarabilir ve BYO-ML platformunu ve yardımcı programlarını kullanarak bunları Azure Sentinel ile tümleştirin.

**Ml algoritmasını paylaşma:** Benimsemek ve uyum sağlamak için topluluğun ML algoritmasını paylaşabilirsiniz.

**Ml 'Yi kullanarak SecOps 'yi güçlendirin:** kendı özel ml modelinizi ve arama, algılama, araştırma ve yanıt sonuçlarınızı kullanın.

Bu makalede, BYO-ML platformunun bileşenleri ve Azure Sentinel ile özelleştirilmiş bir ML algılaması sunmak için platformun ve anormal kaynak erişim algoritmasından nasıl yararlanılacağı gösterilmektedir.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark ortamı

[Apache Spark™](http://spark.apache.org/) , veri işlem hatları oluşturmaya yönelik Birleşik bir çerçeve sağlayarak büyük verileri basitleştirecek şekilde daha ileri bir iletme yaptı. Azure Databricks Spark etrafında yerleşik bir sıfır yönetimi bulut platformu sağlayarak bunu daha fazla alır. Veri işlem hatları ve platform sorunlarını gidermek yerine, işletmeniz üzerinde bir etkisi olan yanıtları bulmaya odaklanabilmeniz için BYO-ML platformunuz için Databricks kullanmanızı öneririz.
Zaten Databricks veya başka bir Spark ortamınız varsa ve var olan kurulumu kullanmayı tercih ediyorsanız BYO-ML paketi de üzerinde ince çalışacaktır. 

## <a name="byo-ml-package"></a>BYO-ML paketi

BYO ML paketi, güvenlik için ML 'nin ön ucundaki Microsoft 'un en iyi yöntemlerini ve araştırmasını içerir. Bu pakette, güvenlik sorunları için aşağıdaki yardımcı programlar, Not defterleri ve algoritma şablonları listesini sağlıyoruz.

| Dosya adı | Description |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | Azure 'dan blob 'ları okumak ve Log Analytics yazmak için yardımcı programları içerir. |
| Anomalousstrasampledata | Not defteri, Azure Sentinel 'de oluşturulan eğitim ve örnek verileri test eden anormal kaynak erişim modelinin kullanımını gösterir. |
| Anomalousoytraining. ipynb | Algoritmayı eğiten, modelleri derlemek ve kaydetmek için Not defteri. |
| Anomalousraypuanlama. ipynb | Bir modelin çalışmasını zamanlamak için Not defteri, sonucu görselleştirin ve puanı Azure Sentinel 'e geri yazın. |
|

Sunduğumuz ilk ML algoritması şablonu, [anormal kaynak erişimi algılaması](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)içindir. İşbirlikçi bir filtreleme algoritmasına dayalıdır ve Windows dosya paylaşma erişim günlükleri (olay KIMLIĞI 5140 olan güvenlik olayları) ile eğitilmiş olur. Günlükteki bu model için gereken önemli bilgiler, Kullanıcı ve kaynaklara erişilme eşleşmesidir. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Örnek Izlenecek yol: anormal dosya paylaşma erişimi algılama 

BYO-ML platformunun anahtar bileşenleriyle tanışdığınıza göre, özelleştirilmiş bir ML algılaması sunmak için platformun ve bileşenlerin nasıl kullanılacağını gösteren bir örnek aşağıda verilmiştir.

### <a name="setup-the-databricksspark-environment"></a>Databricks/Spark ortamını ayarlayın

Henüz bir tane yoksa, kendi Databricks ortamınızı oluşturmanız gerekecektir. Yönergeler için [Databricks hızlı başlangıç](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) belgesine bakın.

### <a name="auto-export-instruction"></a>Otomatik dışa aktarma yönergesi

Azure Sentinel 'de kendi verilerinizi temel alan özel ML modelleri oluşturmak için, ML modelinin Databricks 'ten erişebilmesi için Log Analytics verilerinizi bir BLOB depolama alanına veya Olay Hub kaynağına aktarmanız gerekir. [Azure Sentinel 'e veri](connect-data-sources.md)alma hakkında bilgi edinin.

Bu örnekte, Azure Blob depolamada dosya paylaşma erişim günlüğü için eğitim verilerinize sahip olmanız gerekir. Verilerin biçimi not defterinde ve kitaplıklarda belgelenmiştir.

[Azure komut satırı arabirimi 'ni (CLI)](/cli/azure/monitor/log-analytics)kullanarak Log Analytics verilerinizi otomatik olarak dışa aktarabilirsiniz. 

Komutları çalıştırmak için, Log Analytics çalışma alanınızda, depolama hesabınızda ve EventHub kaynağınız için **katkıda** bulunan rolünü atamış olmanız gerekir. 

Otomatik dışa aktarmayı ayarlamak için örnek bir komut kümesi aşağıda verilmiştir:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Özel verileri dışarı aktar

Log Analytics otomatik dışarı aktarma tarafından desteklenmeyen özel veriler için, mantıksal uygulama veya diğer çözümleri kullanarak verilerinizi taşıyabilirsiniz. [Dışarı aktarma Log Analytics verilerine blob Store](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) bloguna ve betiğe başvurabilirsiniz.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Azure sentinel dışındaki verilerle bağıntı

Ayrıca, Azure Sentinel dışından BLOB depolama veya Olay Hub 'ına veri getirebilir ve ML modellerinizi oluşturmak için bunları Azure Sentinel verileriyle ilişkilendirileyebilirsiniz. 
 
### <a name="copy-and-install-the-related-packages"></a>İlgili paketleri kopyalayın ve yükler

Yukarıda belirtilen Azure Sentinel GitHub deposundan BYO-ML paketini Databricks ortamınıza kopyalayın. Ardından, not defterlerini açın ve gerekli kitaplıkları kümelerinize yüklemek için Not Defteri içindeki yönergeleri izleyin.

### <a name="model-training-and-scoring"></a>Model eğitimi ve Puanlama

İki not defteri 'ndeki yönergeleri izleyerek, kendi ortamınıza ve kaynaklarınıza göre yapılandırmayı değiştirin, modelinizi eğitme ve oluşturma adımlarını izleyin ve ardından modeli gelen dosya paylaşma erişim günlüklerine puan verecek şekilde zamanlayın.

### <a name="write-results-to-log-analytics"></a>Log Analytics sonuçları yaz

Puanlama elde etmeniz için, Puanlama not defterindeki modülünü Azure Sentinel örneğinizle ilişkili Log Analytics çalışma alanına puan sonuçları yazmak için kullanabilirsiniz.

### <a name="check-results-in-azure-sentinel"></a>Azure Sentinel 'de sonuçları denetleme

Puanlanmış sonuçlarınızı ilgili günlük ayrıntılarıyla birlikte görmek için, Azure Sentinel portalınıza geri dönün. Özel günlüklerde **günlükler** >, sonuçları **AnomalousResourceAccessResult_CL** tablosunda (veya kendi özel tablo adınızla) görürsünüz. Bu sonuçları, araştırma ve avcılık deneyimlerinizi geliştirmek için kullanabilirsiniz.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="anormal kaynak erişim günlükleri":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>ML sonuçlarıyla özel analiz kuralı oluşturma

ML sonuçlarının özel Günlükler tablosunda olduğunu onayladıktan sonra puanları uygunlukta memnun kaldığınızda, sonuçlara göre bir algılama oluşturabilirsiniz. Azure Sentinel portalından analizler **' e gidin** ve [Yeni bir algılama kuralı oluşturun](tutorial-detect-threats-custom.md). Aşağıda, algılamayı oluşturmak için kullanılan sorguyu gösteren bir örnek verilmiştir.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="B Y O M L algılama için özel analiz kuralı oluştur":::

### <a name="view-and-respond-to-incidents"></a>Olayları görüntüleme ve yanıtlama
ML sonuçlarını temel alarak analiz kuralını ayarladıktan sonra, sorguda ayarladığınız eşiğin üstünde sonuçlar varsa, Azure Sentinel 'teki **Olaylar** sayfasında bir olay oluşturulur ve ortaya çıkacak. 

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel 'in BYO-ML platformunu kullanarak verileri analiz etmek ve tehditleri algılamak için kendi makine öğrenimi algoritmalarınızı oluşturma veya içeri aktarma konusunda bilgi edindiniz.

- Bkz. makine öğrenimi ve [Azure Sentinel bloguna](https://aka.ms/azuresentinelblog)ilişkin diğer ilgili konular.
