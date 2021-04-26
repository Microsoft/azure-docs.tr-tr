---
title: Akamai güvenlik olayları toplayıcılarınızı Azure Sentinel 'e bağlama | Microsoft Docs
description: Akamai çözümlerinin güvenlik günlüklerini Azure Sentinel 'e çekmek için Akamai güvenlik olayları bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında Akamai verileri görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711969"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Akamai güvenlik olayları toplayıcılarınızı Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Akamai güvenlik olayları Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede, Akamai güvenlik olayları toplayıcılarınızı Azure Sentinel 'e bağlama açıklanmaktadır. Akamai güvenlik olayları veri Bağlayıcısı, Akamai günlüklerinizi Azure Sentinel ile kolayca bağlamanıza olanak tanır. böylece, çalışma kitaplarında verileri görüntüleyebilir, özel uyarılar oluşturacak şekilde sorgulayabilir ve araştırmayı geliştirebilirsiniz. Akamai güvenlik olayları toplayıcısı ve Azure Sentinel arasında tümleştirme, CEF biçimli syslog, Linux tabanlı bir günlük ileticisi ve Log Analytics Aracısı kullanımını sağlar. Ayrıca, kusto işlevine dayalı özel olarak oluşturulmuş bir günlük ayrıştırıcısı kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanınızda okuma ve yazma izinlerine sahip olmanız gerekir.

- Çalışma alanı için paylaşılan anahtarlar için okuma izninizin olması gerekir. [Çalışma alanı anahtarları hakkında daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Akamai güvenlik olayları günlüklerini Azure Sentinel 'e gönder

Günlüklerini Azure Sentinel 'e almak için, Akamai güvenlik olayları toplayıcılarınızı, CEF biçiminde bir Linux tabanlı günlük iletme sunucusuna (rsyslog veya Syslog-ng çalıştıran) Syslog iletileri gönderecek şekilde yapılandırın. Bu sunucuda Log Analytics Aracısı yüklü olacaktır ve Aracı günlükleri Azure Sentinel çalışma alanınıza iletir.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden **Akamai güvenlik olayları (Önizleme)** öğesini seçin ve ardından **bağlayıcı sayfası**' nı açın.

1. **Yapılandırma** altında, **yönergeler** sekmesindeki yönergeleri izleyin:

    1. **1 altında. Linux Syslog Aracısı yapılandırması** -çalışır durumda bir günlük ileticisi yoksa veya başka bir tane gerekiyorsa bu adımı izleyin. Bilgi için bkz. [1. Adım: günlük ileticisini](connect-cef-agent.md) Azure Sentinel belgelerinde, boyutlandırma bilgileri, daha ayrıntılı yönergeler ve ayrıntılı açıklamalar için dağıtma.

    1. **2 altında. Ortak olay biçimi (CEF) günlüklerini Syslog aracısına ilet** - [SIEM tümleştirmesini yapılandırmak](https://developer.akamai.com/tools/integrations/siem) ve [bir CEF bağlayıcısını ayarlamak](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)için Akamai 'in yönergelerini izleyin. Bu bağlayıcı SıEM OPEN API 'sini kullanarak Akamai çözümlerinizin neredeyse gerçek zamanlı olarak güvenlik olaylarını alır ve bunları JSON 'dan CEF biçimine dönüştürür.
    
        Bu yapılandırma aşağıdaki öğeleri içermelidir:
    
        - Günlük hedefi – günlük iletme sunucunuzun ana bilgisayar adı ve/veya IP adresi
        - Protokol ve bağlantı noktası – TCP 514 (Aksi takdirde önerilir), günlük iletme sunucunuzdaki Syslog Daemon öğesinde paralel değişikliği yaptığınızdan emin olun.
        - Günlük biçimi – CEF
        - Günlük türleri: tümü kullanılabilir

    1. 3 ' ün altında **. Bağlantıyı doğrulama** -bağlayıcı sayfasına komutu kopyalayarak ve günlük ileticisinde çalıştırarak veri alımını doğrulayın. Daha ayrıntılı yönergeler ve açıklamalar için bkz. [3. Adım:](connect-cef-verify.md) Azure Sentinel belgelerindeki bağlantıyı doğrulama.

        Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler **günlüklerde**, **Azure Sentinel** bölümünde, *commonsecuritylog* tablosunda görünür.

Bu veri Bağlayıcısı, beklenen şekilde çalışması için bir kusto Işlevini temel alan bir ayrıştırıcıya bağımlıdır. Sorgularda ve çalışma kitaplarında kullanmak üzere **AkamaiSIEMEvent** kusto işlevini ayarlamak için aşağıdaki adımları kullanın.

1. Azure Sentinel gezinti menüsünde **Günlükler**' i seçin.

1. Aşağıdaki sorguyu kopyalayın ve sorgu penceresine yapıştırın.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. **Kaydet** açılan düğmesine tıklayın ve **Kaydet**' e tıklayın. **Kaydet** panelinde,

    1. **Ad** alanına **AkamaiSIEMEvent** girin.

    1. **Farklı kaydet** altında **işlev**' ı seçin.

    1. **Işlev diğer adı** altında **AkamaiSIEMEvent** girin.

    1. **Kategori** altında **işlevler** girin.

    1. **Kaydet**’e tıklayın.

    İşlev uygulamalarının etkinleştirilmesi genellikle 10 ila 15 dakika sürer.

Artık `AkamaiSIEMEvent` sorgu penceresinin en üst satırına girerek Akamai verilerini sorgulamaya hazırsınız demektir.

Daha fazla sorgu örneği için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Akamai güvenlik olaylarını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .