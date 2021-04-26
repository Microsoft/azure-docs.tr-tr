---
title: Azure Sentinel özel bağlayıcıları oluşturma kaynakları | Microsoft Docs
description: Azure Sentinel için özel bağlayıcılar oluşturmaya yönelik kullanılabilir kaynaklar hakkında bilgi edinin. Yöntemler Log Analytics Agent ve API, Logstash, Logic Apps, PowerShell ve Azure Işlevlerini içerir.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: a1aaf89624f8d0ab48692629d859f3c1bdb4ba67
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738909"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Azure Sentinel özel bağlayıcıları oluşturma kaynakları

Azure Sentinel, [Azure hizmetleri ve dış çözümler için çok sayıda yerleşik bağlayıcı](connect-data-sources.md)sağlar ve ayrıca, özel bir bağlayıcı olmadan bazı kaynaklardaki verileri geri almak da destekler.

Mevcut çözümlerden herhangi birini kullanarak veri kaynağınızı Azure Sentinel 'e bağlanamıyorsanız, kendi veri kaynağı bağlayıcınızı oluşturmayı düşünün.

Desteklenen bağlayıcıların tam listesi için bkz. [Azure Sentinel: bağlayıcılar genel (CEF, syslog, Direct, Agent, Custom ve more)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) blog gönderisi.

## <a name="compare-custom-connector-methods"></a>Özel bağlayıcı yöntemlerini karşılaştırın

Aşağıdaki tabloda, bu makalede açıklanan özel bağlayıcılar oluşturmak için her bir yöntemle ilgili önemli ayrıntılar karşılaştırılmaktadır. Her yöntem hakkında daha fazla ayrıntı için tablodaki bağlantıları seçin.

|Yöntem açıklaması  |Özellik | Sunucusuz    |Karmaşıklık  |
|---------|---------|---------|---------|
|**[Log Analytics Aracısı](#connect-with-the-log-analytics-agent)** <br>Şirket içi ve IaaS kaynaklarından dosya toplamak için idealdir   | Yalnızca dosya koleksiyonu  |   No      |Düşük         |
|**[Logstash](#connect-with-logstash)** <br>Şirket içi ve IaaS kaynakları için en iyisi, bir eklentinin kullanılabildiği herhangi bir kaynak ve Logstash ile zaten tanıdık olan kuruluşlar  | Kullanılabilir eklentiler, ayrıca özel eklenti, yetenekler önemli esneklik sağlar.   |   Eşleşen çalıştırmak için bir VM veya VM kümesi gerektirir           |   Zayıf eklentilerle birçok senaryoyu destekler      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Yüksek maliyetli; yüksek hacimli verilerden kaçının <br>Düşük hacimli bulut kaynakları için en iyisi  | Codeless programlama, algoritma uygulama desteği olmadan sınırlı esneklik sağlar.<br><br> Gereksinimlerinizi zaten destekleyen kullanılabilir bir eylem yoksa, özel bir eylem oluşturmak karmaşıklık ekleyebilir.    |    Yes         |   Zayıf basit, kodsuz kullanacaksınız geliştirme      |
|**[PowerShell](#connect-with-powershell)** <br>Prototip ve dönemsel dosya yüklemeleri için en iyisi | Dosya koleksiyonu için doğrudan destek. <br><br>PowerShell, daha fazla kaynak toplamak için kullanılabilir, ancak kodlama ve betiği hizmet olarak yapılandırmaya gerek duyar.      |No               |  Düşük       |
|**[Log Analytics API 'SI](#connect-with-the-log-analytics-api)** <br>Tümleştirme uygulayan ISV 'Ler ve benzersiz koleksiyon gereksinimleri için en iyisi   | Kod ile kullanılabilen tüm özellikleri destekler.  | Uygulamaya bağlıdır           |     Yüksek    |
|**[Azure işlevleri](#connect-with-azure-functions)** Yüksek hacimli bulut kaynakları ve benzersiz koleksiyon gereksinimleri için en iyisi  | Kod ile kullanılabilen tüm özellikleri destekler.  |  Yes             |     Geniş programlama bilgisi gerektirir    |
|     |         |                |

> [!TIP]
> Aynı bağlayıcı için Logic Apps ve Azure Işlevleri kullanma karşılaştırmaları için, bkz.:
> 
> - [Azure Sentinel 'de fastly Web uygulaması güvenlik duvarı günlüklerini alma](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel GitHub topluluğu): [Logic App Connector](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure işlev Bağlayıcısı](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Log Analytics aracısıyla bağlantı kurmak

Veri kaynağınız dosyalardaki olayları sunmızda, özel bağlayıcınızı oluşturmak için Azure Izleyici Log Analytics aracısını kullanmanızı öneririz.

- Daha fazla bilgi için bkz. [Azure izleyici 'de özel Günlükler toplama](../azure-monitor/agents/data-sources-custom-logs.md).

- Bu yöntemin bir örneği için bkz. [Azure izleyici 'de Linux için Log Analytics aracısıyla özel JSON veri kaynakları toplama](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Logstash ile bağlanma

[Logstash](https://www.elastic.co/logstash)hakkında bilginiz varsa, özel bağlayıcınızı oluşturmak Için [Azure Sentinel için logstash çıkış eklentisiyle](connect-logstash.md) logstash kullanmak isteyebilirsiniz.

Azure Sentinel Logstash output eklentisi ile herhangi bir Logstash girişi ve filtreleme eklentisini kullanabilir ve Azure Sentinel 'i bir Logstash işlem hattı için çıkış olarak yapılandırabilirsiniz. Logstash, Event Hubs, Apache Kafka, dosyalar, veritabanları ve bulut hizmetleri gibi çeşitli kaynaklardan girişi etkinleştiren büyük bir eklenti kitaplığına sahiptir. Olayları ayrıştırmak, gereksiz olayları filtrelemek, değerleri gizleme ve daha fazlasını yapmak için filtreleme eklentilerini kullanın.

Logstash 'i özel bağlayıcı olarak kullanma örnekleri için bkz.:

- [Azure Sentinel (blog) kullanarak AWS günlüklerinde büyük bir Ihlal TTPs için](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) arama
- [Radware Azure Sentinel uygulama kılavuzu](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Faydalı Logstash eklentileri örnekleri için bkz.:

- [CloudWatch giriş eklentisi](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs eklentisi](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google Cloud Storage giriş eklentisi](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub girişi eklentisi](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash Ayrıca bir küme kullanarak ölçekli veri toplamayı etkinleştirir. Daha fazla bilgi için bkz. [bir yük dengeli Logstash VM 'yi ölçekli bir şekilde kullanma](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Logic Apps bağlanma

Azure Sentinel için sunucusuz, özel bağlayıcı oluşturmak üzere bir [Azure mantıksal uygulaması](../logic-apps/index.yml) kullanın.

> [!NOTE]
> Logic Apps kullanarak sunucusuz Bağlayıcılar oluştururken, bağlayıcılarınız için Logic Apps kullanılması çok büyük miktarlarda veri için uygun maliyetli olabilir.
>
> Bu yöntemi yalnızca düşük hacimli veri kaynakları için kullanmanızı veya veri karşıya yüklemelerinizi zenginleştirmenizi öneririz.
>

1. **Logic Apps başlatmak için aşağıdaki tetikleyicilerden birini kullanın**:

    |Tetikleyici  |Description  |
    |---------|---------|
    |**Yinelenen bir görev**     |   Örneğin, mantıksal uygulamanızı belirli dosyalardan, veritabanlarından veya dış API 'lerden düzenli olarak verileri almak için zamanlayın. <br>Daha fazla bilgi için, bkz. [Azure Logic Apps yinelenen görevleri ve iş akışlarını oluşturma, zamanlama ve çalıştırma](../connectors/connectors-native-recurrence.md).      |
    |**İsteğe bağlı tetikleme**     | El ile veri toplama ve test için mantıksal uygulamanızı isteğe bağlı olarak çalıştırın. <br>Daha fazla bilgi için bkz.  [https uç noktaları kullanarak çağrı, tetikleyici veya iç içe mantıksal uygulamalar](../logic-apps/logic-apps-http-endpoint.md).        |
    |**HTTP/S uç noktası**     |  Akış için önerilir ve kaynak sistem veri aktarımını başlatabilirler. <br>Daha fazla bilgi için bkz. [http veya https üzerinden hizmet uç noktalarını çağırma](../connectors/connectors-native-http.md).       |
    |     |         |

1. **Olaylarınızı almak için bilgileri okuyan mantıksal uygulama bağlayıcılarından herhangi birini kullanın**. Örnek:

    - [REST API bağlanma](/connectors/custom-connectors/)
    - [SQL Server bağlanma](/connectors/sql/)
    - [Dosya sistemine bağlanma](/connectors/filesystem/)

    > [!TIP]
    > REST API 'lere, SQL Server 'lara ve dosya sistemlerine yönelik özel bağlayıcılar, şirket içi veri kaynaklarından veri almayı da destekler. Daha fazla bilgi için bkz. [Şirket içi veri ağ geçidi belgelerini yüklemeyi](/connectors/filesystem/) .
    >

1. **Almak istediğiniz bilgileri hazırlayın**.

    Örneğin, JSON içeriğindeki özelliklere erişmek için [JSON 'u Ayrıştır eylemini](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) kullanın ve mantıksal uygulamanız için giriş belirttiğinizde bu özellikleri dinamik içerik listesinden seçmenizi sağlar.

    Daha fazla bilgi için bkz. [Azure Logic Apps veri Işlemlerini gerçekleştirme](../logic-apps/logic-apps-perform-data-operations.md).

1. **Log Analytics verileri yazın**.

    Daha fazla bilgi için bkz. [Azure Log Analytics veri toplayıcısı](/connectors/azureloganalyticsdatacollector/) belgeleri.

Logic Apps kullanarak Azure Sentinel için nasıl özel bağlayıcı oluşturabileceğiniz hakkında örnekler için, bkz.:

- [Veri Toplayıcı API 'SI ile veri işlem hattı oluşturma](/connectors/azureloganalyticsdatacollector/)
- [Web kancası kullanarak Palo Alto Prma Logic App Connector](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel GitHub topluluğu)
- [Zamanlanan etkinleştirme (blog) Ile Microsoft ekiplerinizin çağrılarınızın güvenliğini sağlama](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600)
- [Alienkasadan OTX tehdit göstergelerini Azure Sentinel 'e yerleştirme](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)

## <a name="connect-with-powershell"></a>PowerShell ile bağlanma

[Karşıya yükleme-AzMonitorLog PowerShell betiği](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) , PowerShell kullanarak olayları veya bağlam bilgilerini Azure Sentinel 'e komut satırından akışı sağlar. Bu akış, veri kaynağınız ve Azure Sentinel arasında etkili bir şekilde özel bağlayıcı oluşturuyor.

Örneğin, aşağıdaki betik bir CSV dosyasını Azure Sentinel 'e yükler:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

[Karşıya yükleme-AzMonitorLog PowerShell betiği](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) betiği aşağıdaki parametreleri kullanır:

|Parametre  |Açıklama  |
|---------|---------|
|**Workspaceıd**     |   Verilerinizi depoladığınız Azure Sentinel çalışma alanı KIMLIĞINIZ.  [Çalışma alanı kimliğinizi ve anahtarınızı bulun](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Verilerinizi depoladığınız Azure Sentinel çalışma alanı için birincil veya ikincil anahtar. [Çalışma alanı kimliğinizi ve anahtarınızı bulun](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Verileri depolamak istediğiniz özel günlük tablosunun adı. **_CL** soneki tablo adınızın sonuna otomatik olarak eklenir.  |
|**AddComputerName**     |   Bu parametre varsa, komut dosyası, geçerli bilgisayar adını **bilgisayar** adlı bir alana her günlük kaydına ekler.      |
|**TaggedAzureResourceId**     | Bu parametre varsa, betik tüm karşıya yüklenen günlük kayıtlarını belirtilen Azure kaynağıyla ilişkilendirir. <br><br>Bu ilişki, kaynak bağlamı sorguları için karşıya yüklenen günlük kayıtlarını sağlar ve kaynak merkezli, rol tabanlı erişim denetimine uyar.       |
|**AdditionalDataTaggingName**     |      Bu parametre mevcut olduğunda, komut dosyası her günlük kaydına, yapılandırılan ada ve **AdditionalDataTaggingValue** parametresi için yapılandırılmış değere sahip bir alan ekler. <br><br>Bu durumda, **AdditionalDataTaggingValue** boş olmamalıdır. |
|**AdditionalDataTaggingValue**     |   Bu parametre varsa, komut dosyası her günlük kaydına, yapılandırılan değere ve **AdditionalDataTaggingName** parametresi için yapılandırılan alan adına sahip bir alan ekler. <br><br>**AdditionalDataTaggingName** parametresi boşsa, ancak bir değer yapılandırılırsa, varsayılan alan adı **veri etiketlemesinin** olur.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Çalışma alanı KIMLIĞINIZI ve anahtarınızı bulun

Azure Sentinel 'de çalışma alanı **kimliği** ve **WorkspaceKey** parametrelerinin ayrıntılarını bulun:

1. Azure Sentinel 'de, sol taraftaki **Ayarlar** ' ı seçin ve ardından **çalışma alanı ayarları** sekmesini seçin.

1. **Log Analytics kullanmaya başlama**' nın altında  >  ,**bir veri kaynağını bağlama** bölümünde **Windows ve Linux Agents yönetimi**' ni seçin.

1. **Windows Server** sekmelerinde çalışma alanı kimliği, birincil anahtar ve ikincil anahtarınızı bulun.
## <a name="connect-with-the-log-analytics-api"></a>Log Analytics API 'siyle bağlanma

Log Analytics veri toplayıcı API 'sini kullanarak doğrudan bir yeniden bekleyen uç nokta çağırmak için olayları Azure Sentinel 'e aktarabilirsiniz.

Daha fazla programlama gerektiren bir yeniden bir uç noktanın çağrılması de daha fazla esneklik sağlar.

Daha fazla bilgi için, [Log Analytics veri toplayıcı API 'sine](../azure-monitor/logs/data-collector-api.md), özellikle de aşağıdaki örneklere bakın:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Azure Işlevleri ile bağlanma

Azure Işlevleri 'ni, daha az bir özel bağlayıcı oluşturmak için, Resttedbir API ve [PowerShell](../azure-functions/functions-reference-powershell.md)gibi çeşitli kodlama dilleri ile birlikte kullanın.

Bu yöntemin örnekleri için bkz.:

- [Azure Işleviyle VMware karbon siyah bulut uç noktası standardını Azure Sentinel 'e bağlama](connect-vmware-carbon-black.md)
- [Azure Işleviyle okta tek Sign-On Azure Sentinel 'e bağlama](connect-okta-single-sign-on.md)
- [Azure Işleviyle Azure Sentinel 'e dokunarak, yazım noktanızı bağlayın](connect-proofpoint-tap.md)
- [Qualys VM 'nizi Azure Işlevi ile Azure Sentinel 'e bağlama](connect-qualys-vm.md)
- [XML, CSV veya diğer veri biçimlerini geri ödeme](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Azure Sentinel Ile yakınlaştırmayı izleme](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Office 365 YÖNETIM API verilerini Azure Sentinel 'e almak için bir işlev uygulaması dağıtma](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel GitHub topluluğu)

## <a name="parse-your-custom-connector-data"></a>Özel bağlayıcı verilerinizi ayrıştırın

İlgili bilgileri ayıklamak ve Azure Sentinel 'de ilgili alanları doldurmak için özel bağlayıcıınızın yerleşik ayrıştırma tekniğini kullanabilirsiniz.

Örnek:

- **Logstash kullandıysanız**, verilerinizi ayrıştırmak Için [grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) filtresi eklentisini kullanın.
- **Bir Azure işlevi kullandıysanız**, verilerinizi kodla ayrıştırın. Daha fazla bilgi için bkz. [çözümleyiciler](normalization.md#parsers).

Azure Sentinel sorgu sırasında ayrıştırmayı destekler. Sorgu zamanında ayrıştırma, verileri özgün biçimde göndermenizi ve sonra gerektiğinde isteğe bağlı olarak ayrıştırmayı sağlar.

Sorgu zamanında ayrıştırma, verilerinizin tam yapısını zaman içinde bilmeniz gerekmez, özel bağlayıcınızı oluşturduğunuzda, hatta ayıklamanız gereken bilgileri de belirtmezsiniz. Bunun yerine, araştırma sırasında bile verilerinizi dilediğiniz zaman ayrıştırın.

> [!NOTE]
> Ayrıştırıcınızı güncelleştirme, Azure Sentinel 'e zaten sahip olduğunuz veriler için de geçerlidir.
> 
## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki işlemlerden biriyle ortamınızı güvenli hale getirmek için Azure Sentinel 'de alınan verileri kullanın:

- [Uyarılara yönelik görünürlük elde etme](quickstart-get-visibility.md)
- [Verilerinizi görselleştirin ve izleyin](tutorial-monitor-your-data.md)
- [Olayları araştırma](tutorial-investigate-cases.md)
- [Tehditleri algılama](tutorial-detect-threats-built-in.md)
- [Tehdit önlemeyi otomatikleştirme](tutorial-respond-threats-playbook.md)
- [Tehditleri arama](hunting.md)
