---
title: Azure Sentinel 'e Logstash aracılığıyla veri kaynaklarını bağlama | Microsoft Docs
description: Günlükleri dış veri kaynaklarından Azure Sentinel 'e iletmek için Logstash 'i nasıl kullanacağınızı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 10812cf97f4f0dfc6f7957608eddf7acf929c3fc
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579758"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Veri kaynaklarını Azure Sentinel 'e bağlamak için Logstash 'i kullanma

> [!IMPORTANT]
> Logstash çıkış eklentisi kullanılarak veri alımı Şu anda genel önizlemededir. Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Logstash veri toplama altyapısı** Için Azure Sentinel 'in çıkış eklentisini kullanarak, Azure sentinel 'deki Log Analytics çalışma alanınıza doğrudan logstash aracılığıyla istediğiniz her türlü günlüğü gönderebilirsiniz. Günlüklerinizi, çıkış eklentisini kullanarak tanımlayacağınızı belirten özel bir tabloya gönderilir.

Logstash veri toplama altyapısıyla çalışma hakkında daha fazla bilgi edinmek için bkz. [Logstash ile çalışmaya başlama](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Genel Bakış

### <a name="architecture-and-background"></a>Mimari ve arka plan

![Günlük hazırlama mimarisinin diyagramı.](./media/connect-logstash/logstash-architecture.png)

Logstash altyapısı üç bileşenden oluşur:

- Giriş eklentileri: çeşitli kaynaklardan özelleştirilmiş veri koleksiyonu.
- Eklentileri filtrele: belirtilen ölçütlere göre verilerin Işlenmesi ve normalleştirilmesi.
- Çıkış eklentileri: toplanan ve işlenen verilerin çeşitli hedeflere gönderilmesi özelleştirilmiş.

> [!NOTE]
> - Azure Sentinel yalnızca kendi sağlanmış çıkış eklentisini destekler. Bu eklentinin geçerli sürümü v 1.0.0, 2020-08-25 yayınlandı. Azure Sentinel için üçüncü taraf çıkış eklentilerini veya herhangi bir türdeki başka bir Logstash eklentisini desteklemez.
>
> - Azure Sentinel 'in Logstash çıkış eklentisi **7,0 ile 7,9 arasında yalnızca logstash sürümlerini** destekler.

Logstash için Azure Sentinel çıktı eklentisi, Log Analytics çalışma alanınıza JSON biçimli verileri gönderir Log Analytics HTTP veri toplayıcı REST API ' nı kullanarak. Veriler özel günlüklere alınır.

- [Log Analytics REST API](/rest/api/loganalytics/create-request)hakkında daha fazla bilgi edinin.
- [Özel Günlükler](../azure-monitor/agents/data-sources-custom-logs.md)hakkında daha fazla bilgi edinin.

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Logstash 'te Azure Sentinel çıkış eklentisini dağıtma

### <a name="step-1-installation"></a>1. Adım: yükleme

Azure Sentinel çıktı eklentisi, Logstash koleksiyonunda kullanılabilir.

- ***[Microsoft-logstash-output-Azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)*** eklentisini yüklemek için [Eklentiler belgesi Ile çalışan](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) logstash içindeki yönergeleri izleyin.
   
- Logstash sisteminizin Internet erişimi yoksa, bir çevrimdışı eklenti paketi hazırlamak ve kullanmak için Logstash [çevrimdışı eklenti yönetimi](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) belgesindeki yönergeleri izleyin. (Bu, Internet erişimi ile başka bir Logstash sistemi oluşturmanızı gerektirir.)

### <a name="step-2-configuration"></a>2. Adım: yapılandırma

[Bir yapılandırma dosyası](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) belgesinin Logstash yapısındaki bilgileri kullanın ve aşağıdaki anahtarlar ve değerlerle Azure Sentinel çıktı eklentisini yapılandırmaya ekleyin. (Doğru yapılandırma dosyası söz dizimi tablodan sonra gösterilir.)

| Alan adı | Veri türü | Açıklama |
|----------------|---------------|-----------------|
| `workspace_id` | dize | Çalışma alanı KIMLIĞI GUID 'nizi girin (bkz. Ipucu). |
| `workspace_key` | string | Çalışma alanınızın birincil anahtar GUID 'nizi girin (bkz. Ipucu). |
| `custom_log_table_name` | string | Günlüklerin alınacağı tablonun adını ayarlayın. Her çıkış eklentisi için yalnızca bir tablo adı yapılandırılabilir. Günlük tablosu, **özel Günlükler** kategorisindeki **tablolarda** bulunan **Günlükler** altında Azure Sentinel 'de, bir sonek ile görünür `_CL` . |
| `endpoint` | string | İsteğe bağlı alan. Bu, varsayılan olarak Log Analytics uç noktasıdır. Alternatif bir uç nokta ayarlamak için bu alanı kullanın. |
| `time_generated_field` | string | İsteğe bağlı alan. Bu özellik Log Analytics varsayılan **TimeGenerated** alanını geçersiz kılar. Veri kaynağındaki zaman damgası alanının adını girin. Alandaki verilerin ISO 8601 biçimine () uyması gerekir `YYYY-MM-DDThh:mm:ssZ` |
| `key_names` | array | Log Analytics çıktı şeması alanlarının bir listesini girin. Her liste öğesi tek tırnak içine, virgülle ayrılmış öğeleri ve köşeli parantez içine alınmış tüm listeyi kapsamalıdır. Aşağıdaki örneğe bakın. |
| `plugin_flush_interval` | sayı | İsteğe bağlı alan. İleti aktarımları arasındaki en büyük aralığı (saniye olarak) Log Analytics olarak tanımlamak için ayarlayın. Varsayılan değer 5’tir. |
| `amount_resizing` | boolean | True veya false. İleti arabellek boyutunu alınan günlük verisi hacmine göre ayarlayan otomatik ölçeklendirme mekanizmasını etkinleştirin veya devre dışı bırakın. |
| `max_items` | sayı | İsteğe bağlı alan. Yalnızca `amount_resizing` "false" olarak ayarlandıysa geçerlidir. İleti arabellek boyutunda bir üst sınır ayarlamak için kullanın (kayıtlar). Varsayılan değer 2000’dir.  |
| `azure_resource_id` | string | İsteğe bağlı alan. Verilerin bulunduğu Azure kaynağının KIMLIĞINI tanımlar. <br>Kaynak KIMLIĞI değeri, özellikle yalnızca belirli verilere erişim sağlamak için [Resource-Context RBAC](resource-context-rbac.md) kullanıyorsanız faydalıdır. |
| | | |

> [!TIP]
> - Çalışma alanı KIMLIĞI ve birincil anahtarı, **aracılar yönetimi** altında çalışma alanı kaynağında bulabilirsiniz.
> - **Ancak**, yapılandırma dosyalarında düz metin olarak saklanan kimlik bilgileri ve diğer hassas bilgiler en iyi güvenlik uygulamalarıyla birlikte olmadığından, **çalışma alanı kimliğinizi** ve **çalışma alanı birincil anahtarınızı** yapılandırmaya güvenli bir şekilde eklemek için **logstash anahtar deposunu** kullanmanız önemle önerilir. Yönergeler için bkz. [elastik belgeler](https://www.elastic.co/guide/en/elasticsearch/reference/current/get-started-logstash-user.html) .

#### <a name="sample-configurations"></a>Örnek yapılandırma

Birkaç farklı seçenek kullanan bazı örnek konfigürasyonlar aşağıda verilmiştir.

- Bir dosya girişi kanalı kullanan temel bir yapılandırma:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- TCP giriş kanalı kullanan temel bir yapılandırma:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Gelişmiş bir yapılandırma:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > İç işleyişi, yapılandırması ve performans ayarları hakkında daha fazla bilgi edinmek için çıkış eklentisi [GitHub deposunu](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) ziyaret edin.

### <a name="step-3-restart-logstash"></a>3. Adım: Logstash 'i yeniden başlatma

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>4. Adım: Azure Sentinel 'de gelen günlükleri görüntüleme

1. İletilerin çıkış eklentisine gönderildiğini doğrulayın.

1. Azure Sentinel gezinti menüsünde **Günlükler**' e tıklayın. **Tablolar** başlığı altında **özel Günlükler** kategorisini genişletin. Yapılandırmada belirttiğiniz tablonun adını bulun ve tıklayın (bir `_CL` sonek ile).

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Günlük hazırlama özel günlüklerinin ekran görüntüsü.":::

1. Tablodaki kayıtları görmek için tablo adını şema olarak kullanarak sorgulayın.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Günlük hazırlama özel günlükleri sorgusunun ekran görüntüsü.":::

## <a name="monitor-output-plugin-audit-logs"></a>Çıktı eklentisi denetim günlüklerini izleme

Azure Sentinel çıkış eklentisinin bağlantısını ve etkinliğini izlemek için, uygun Logstash günlük dosyasını etkinleştirin. Günlük dosyası konumu için [Logstash Dizin düzeni](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) belgesine bakın.

Bu günlük dosyasında herhangi bir veri görmüyorsanız, çıkış eklentisinin veri aldığından emin olmak için bazı olayları yerel olarak oluşturup gönderin (giriş ve filtre eklentileri aracılığıyla). Azure Sentinel yalnızca çıkış eklentisine ilişkin sorunları destekleyecektir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel 'e dış veri kaynakları bağlamak için Logstash 'in nasıl kullanılacağını öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Yerleşik](tutorial-detect-threats-built-in.md) veya [özel](tutorial-detect-threats-custom.md) kurallar kullanarak Azure Sentinel ile tehditleri algılamaya başlayın.
