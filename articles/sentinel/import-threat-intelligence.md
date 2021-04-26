---
title: Tehdit zekasını Azure Sentinel 'e aktarma | Microsoft Docs
description: Verileri analiz etmek, tehditleri algılamak ve uyarılar ve olaylar oluşturmak için Azure Sentinel 'de tehdit bilgileri akışlarını kullanın. Tehdit bilgilerini çalışma kitapları ile görselleştirin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574992"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Tehdit analizini Azure Sentinel’e aktarma

## <a name="introduction-to-threat-intelligence"></a>Tehdit zekasına giriş

Cyber Threat Intelligence (CTı), sistemler ve kullanıcılara yönelik bilinen mevcut veya olası tehditleri açıklayan bir bilgi. Bu tür bilgiler, belirli bir tehdit aktörlerinin, altyapısının ve tekniklerinin, siber tehditleri ile ilişkili belirli IP adresi, etki alanı ve dosya karmalarının gözlemlendiği, yazılı raporlardan çok sayıda formu alır. CTı, kuruluşlar tarafından olağan dışı etkinliklere önemli bağlam sağlamak için kullanılır, böylece güvenlik personeli kişilerin ve varlıklarının korunması için hızlı bir işlem gerçekleştirebilir. CTı, açık kaynaklı veri akışları, tehdit zekapaylaşma toplulukları, ticari zeka akışları ve bir kuruluştaki güvenlik araştırmalarında toplanan yerel zeka gibi birçok yerden kaynaklıdır.

Azure Sentinel gibi bir güvenlik bilgileri ve olay yönetimi (SıEM) çözümü içinde, en çok kullanılan CTı biçimi, genellikle uzlaşma veya ıocs göstergeleri olarak adlandırılan tehdit göstergelerdir. Tehdit göstergeleri, kimlik avı, botlar veya kötü amaçlı yazılım gibi bilinen tehdit etkinlikleri ile URL 'Ler, dosya karmaları veya IP adresleri gibi gözlemleri ilişkilendiren veri. Bu tehdit zekasından genellikle, bir kuruluştaki olası tehditleri korumak ve algılamak için büyük ölçekte güvenlik ürünlerine uygulanabileceğinden ve daha geniş ölçekte Otomasyon sağlamak üzere politik tehdit bilgileri denir. Azure Sentinel 'de, ortamınızda gözlemlenen kötü amaçlı etkinlikleri algılamaya yardımcı olması için tehdit göstergelerini kullanabilir ve yanıt kararlarını bilgilendirmek için güvenlik araştırmacıya bağlamı sağlayabilirsiniz.

Tehdit zekasını (TI) aşağıdaki etkinliklerle Azure Sentinel ile tümleştirebilirsiniz:

- [Tehdit zekasını](./connect-threat-intelligence.md) Azure Sentinel 'e aktarmak IÇIN çeşitli TI platformlarına yönelik **veri bağlayıcıları** kullanın.
- **Günlüklerde** ve Azure Sentinel 'In yeni **tehdit bilgileri** alanında içeri aktarılan tehdit zekasını görüntüleyin ve yönetin.
- İçeri aktarılan tehdit zekasını kullanarak güvenlik uyarıları ve olaylar oluşturmak için yerleşik **analitik** kural şablonlarını kullanın.
- **Tehdit bilgileri çalışma kitabıyla** Azure Sentinel 'teki tehdit zekası hakkındaki önemli bilgileri görselleştirin.

Tehdit bilgileri ayrıca, arama ve **Not defterleri** **gibi diğer** Azure Sentinel deneyimleri içinde yararlı bir bağlam sağlar ve bu makalede kapsanmayan, bu deneyimlerin Azure Sentinel 'de bulunan ve Not defterlerindeki CTI kullanımını kapsayan, bu [harika blog postasında bu harika bir Web günlüğü gönderisine](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)değinmektedir.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Tehdit bilgileri için Azure Sentinel veri bağlayıcıları

Azure Sentinel 'deki tüm diğer olay verileri gibi tehdit göstergeleri, veri bağlayıcıları kullanılarak içeri aktarılır. Azure Sentinel 'de, özellikle tehdit göstergeleri, **Threat Intelligence-TAXıı** ve **Threat Intelligence platformları** için sunulan iki veri Bağlayıcısı vardır. Kuruluşunuzun tehdit göstergelerini nerede depolayadığına bağlı olarak, tek başına ya da her iki bağlayıcıyı birlikte kullanabilirsiniz. Her veri bağlayıcısından bahsedelim.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Tehdit bilgileri platformu veri Bağlayıcısı ile Azure Sentinel 'e tehdit göstergeleri ekleme

Birçok kuruluş tehdit bilgileri platformu (tıp) çözümlerini çeşitli kaynaklardan toplamak, platform içindeki verileri seçmek ve ardından ağ cihazları, Gelişmiş tehdit koruması çözümleri veya Azure Sentinel gibi çeşitli güvenlik çözümlerine hangi tehdit göstergelerinin uygulanacağını seçmek için kullanır. Kuruluşunuz, MISP, anomali ThreatStream, ThreatConnect, EclecticIQ platform, ThreatQ Threat Intelligence platformu veya Palo Alto Networks gibi tümleşik bir tıp çözümünü kullanıyorsa, **tehdit bilgileri platformları veri Bağlayıcısı** , tehdit göstergelerini Azure Sentinel 'e aktarmak için ipucunu kullanmanıza olanak sağlar. Bağlayıcı bunu gerçekleştirmek için [Microsoft Graph Security Tiındicators API](/graph/api/resources/tiindicator) 'siyle çalıştığından, bağlayıcı Azure Sentinel 'e göstergeler (ve Defender ATP gibi diğer Microsoft güvenlik çözümlerine) göndermek Için TIıNDICATORS API 'sinden yararlanmak üzere tüm özel tehdit bilgileri platformu tarafından da kullanılabilir.

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Tehdit bilgileri içeri aktarma yolu":::

Tümleştirme göstergelerini tümleşik tıp veya özel tehdit bilgileri çözümünüzden Azure Sentinel 'e aktarmak için şu adımları izleyin:

1. Azure Active Directory bir uygulama KIMLIĞI ve Istemci gizli anahtarı edinin

1. Bu bilgileri tıp çözümünüz veya özel uygulamanıza girin

1. Azure Sentinel 'de tehdit bilgileri platformu veri bağlayıcısını etkinleştirme

Bu adımların her birine ayrıntılı bir bakış aşağıda verilmiştir.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Azure Active Directory bir uygulama KIMLIĞI ve Istemci gizli anahtarı edinin

Bir tıp ile veya özel çözümle çalışırken, Tiındicators API 'SI, tehdit göstergelerini bağlamak ve göndermek için bazı temel bilgiler gerektirir. Edinmeniz gereken üç bilgi parçası şunlardır:
- Uygulama (istemci) kimliği
- Dizin (kiracı) kimliği
- Gizli anahtar

Bu bilgiler, aşağıdaki üç adımı içeren **uygulama kaydı** adlı bir işlem aracılığıyla Azure Active Directory her zaman gelir:
- Azure Active Directory ile uygulama kaydetme
- Microsoft Graph Tiındicators API 'sine bağlanmak ve tehdit göstergeleri göndermek için uygulamanın gerektirdiği izinleri belirtin
- Bu izinleri bu uygulamaya vermek için kuruluşunuzdan onay alın.  

**Azure Active Directory olan bir uygulamayı kaydetme**

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Active Directory** hizmetine gidin.

1. Menüden **uygulama kayıtları** ' nı seçin ve **Yeni kayıt**' yi seçin.

1. Uygulama kaydınız için bir ad seçin, **tek kiracılı** radyo düğmesini seçin ve **Kaydet**' i seçin.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Uygulamayı kaydetme":::

1. Ortaya çıkan ekrandan, **uygulama (istemci) kimliği** ve **Dizin (kiracı) kimliği** değerlerini kopyalayın. Bunlar, daha sonra tıp veya özel çözümünüzü Azure Sentinel 'e tehdit göstergeleri gönderecek şekilde yapılandırmak için gereken ilk iki bilgi parçalarından oluşur.

**Uygulamanın gerektirdiği izinleri belirtin**

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Active Directory** hizmetine gidin.

1. Menüden **uygulama kayıtları** ' nı seçin ve yeni kaydedilen uygulamanızı seçin.

1. Menüden **API izinleri** ' ni seçin ve **izin Ekle** düğmesine tıklayın.

1. **BIR API seçin** sayfasında, Microsoft Graph izinleri listesinden seçmek için **Microsoft Graph** ' yi seçin.

1. **Uygulamanızın ne tür izinlere ihtiyacı** olduğu sorulduğunda? **Uygulama izinleri**' ni seçin. Bu, uygulama KIMLIĞI ve uygulama gizli dizileri (API anahtarları) ile kimlik doğrulaması yapan uygulamalar tarafından kullanılan izin türüdür.

1. **Threatındicators. ReadWrite. OwnedBy** ' ı seçin ve bu izni uygulamanızın izin listesine eklemek Için **izin Ekle** ' yi seçin.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="İzinleri belirtme":::

**Bu izinleri vermek için kuruluşunuzdan onay alın**

1. İzin vermek için uygulamanızın API izinleri sayfasında **kiracınız için yönetici onayı verme** ' yi seçmek üzere bir Azure Active Directory genel yöneticisinin olması gerekir. Hesabınızda genel yönetici rolüne sahip değilseniz, bu düğme kullanılamaz ve kuruluşunuzdaki genel bir yöneticiden bu adımı gerçekleştirmesini isteyebilirsiniz ve bu adımı gerçekleştirin.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Onay ver":::

1. Uygulamanıza izin verildiğinde, **durum**' un altında yeşil bir onay işareti görürsünüz.
 
Uygulamanız kaydedildikten ve izinler verildiğinden, listenizden son şeyi uygulamanız için bir istemci gizli anahtarı alabilirsiniz.

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Active Directory** hizmetine gidin.

1. Menüden **uygulama kayıtları** ' nı seçin ve yeni kaydedilen uygulamanızı seçin.

1. Menüdeki gizli dizileri **&** seçin ve uygulamanız için gizli dızı (API anahtarı) almak için **yeni istemci gizli** düğmesine tıklayın.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="İstemci gizliliğini al":::

1. Bu sayfadan uzaklaşmanız durumunda, bu parolayı tekrar alamamanız halinde **Ekle** düğmesine tıklayın ve **istemci gizliliğini kopyalamaya dikkat** edin. IPUCUNUZU veya özel çözümünüzü yapılandırırken bu değere ihtiyaç duyarsınız.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Bu bilgileri tıp çözümünüz veya özel uygulamanıza girin

Artık, tıp veya özel çözümünüzü Azure Sentinel 'e tehdit göstergeleri gönderecek şekilde yapılandırmak için gereken üç bilgi parçasına sahipsiniz. 
- Uygulama (istemci) kimliği
- Dizin (kiracı) kimliği
- Gizli anahtar

Bu değerleri, gerekli olduğunda tümleşik Ipucu veya özel çözümünüzün yapılandırmasına girin ve tehdit göstergeleri Azure Sentinel 'e hedeflenmiş Microsoft Graph Tiındicators API 'SI aracılığıyla gönderilir.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Azure Sentinel 'de tehdit bilgileri platformu veri bağlayıcısını etkinleştirme

Tümleştirme sürecinin son adımı, Azure Sentinel 'de **tehdit bilgileri platformu** veri bağlayıcısının etkinleştirilmesidir. Bu, tıp veya özel çözümünüzdeki Azure Sentinel 'e Microsoft Graph Tiındicators API aracılığıyla gönderilen tehdit göstergelerini içeri aktaran adımdır. Bu göstergeler, kuruluşunuzun tüm Azure Sentinel çalışma alanlarında kullanılabilir. Her çalışma alanı için tehdit bilgileri platformu veri bağlayıcısını etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Sentinel** hizmetine gidin.

1. IPUCUNDAN veya özel çözümünüzden gönderilen tehdit göstergelerini içeri aktarmak istediğiniz **çalışma alanını** seçin.

1. Menüden **veri bağlayıcıları** ' nı seçin, bağlayıcılar galerisinden **tehdit zekası platformları** ' nı seçin ve **bağlayıcı sayfası aç** düğmesine tıklayın.

1. Uygulama kaydını zaten tamamlayıp tıp veya özel çözümünüzü tehdit göstergeleri gönderecek şekilde yapılandırdıysanız, yalnızca sol adım **Bağlan** düğmesine tıklamakta.

Birkaç dakika içinde tehdit göstergelerinin bu Azure Sentinel çalışma alanına yönlendirip başlamamalıdır.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Tehdit bilgileri-TAXıı veri Bağlayıcısı ile Azure Sentinel 'e tehdit göstergeleri ekleme

Tehdit zekasını iletilmek üzere en yaygın olarak benimsenen endüstri standardı, [STIX veri biçiminin ve TAXıı protokolünün bir birleşimidir](https://oasis-open.github.io/cti-documentation/). Kuruluşunuz geçerli STIX/TAXıı sürümünü (2,0 veya 2,1) destekleyen çözümlerden tehdit göstergelerini alırsa tehdit bilgilerini Azure Sentinel 'e taşımak için **tehdit zekataxıı** veri bağlayıcısını kullanabilirsiniz. Threat Intelligence-TAXıı veri Bağlayıcısı, Azure Sentinel 'deki yerleşik bir TAXıı istemcisinin, TAXıı 2. x sunucularından tehdit zekasını içeri aktarmasını sağlar.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXıı içeri aktarma yolu":::
 
Bir TAXıı sunucusundan STIX biçimli tehdit göstergelerini Azure Sentinel 'e aktarmak için şu adımları izleyin:

1. TAXıı Server API kökünü ve koleksiyon KIMLIĞINI edinin

1. Azure Sentinel 'de Threat Intelligence-TAXıı veri bağlayıcısını etkinleştirme

Şimdi bu adımların her birine ayrıntılı bir göz atalım.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>TAXıı Server API kökünü ve koleksiyon KIMLIĞINI edinin

TAXıı 2. x sunucuları, tehdit zekasını barındıran URL 'Ler olan API kökleri duyurur. Çoğu zaman API kökünün, TAXıı sunucusunu barındıran tehdit bilgileri sağlayıcısının belge sayfası aracılığıyla elde edilebilir. Ancak bazen tanıtılan tek bilgi, bulma uç noktası olarak bilinen bir URL 'dir. Bu durumda, bulma uç noktasını kullanarak API kökünü kolayca bulabilirsiniz. Birlikte çalışmak istediğiniz TAXıı sunucu API kökünü ve koleksiyon kimliklerini zaten biliyorsanız, sonraki bölüme atlayın ve **Azure Sentinel 'de tehdit zekası-TAXıı veri bağlayıcısını etkinleştirin**.

, API kökünü bulmak ve yalnızca bulma uç noktasından başlayarak bir TAXıı sunucusunun koleksiyonlarına göz atmak için Windows ve çoğu Linux dağıtımlarında bulunan [kıvrımlı](https://en.wikipedia.org/wiki/CURL)adlı basit bir komut satırı yardımcı programının nasıl kullanılacağına ilişkin gerçek bir örneğe bakalım. Bu örnekte, [anomali Limo](https://www.anomali.com/community/limo) threatstream taxıı 2,0 sunucusunun bulma uç noktasını kullanacağız.

1.  Bir tarayıcıdan, API kökünü almak için [Threatstream TAXıı 2,0 sunucu bulma uç noktasına](https://limo.anomali.com/taxii) gidin. Kullanıcı ve parolayla kimlik doğrulaması yapın `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  , https://limo.anomali.com/api/v1/taxii2/feeds/) API kökünde barındırılan koleksiyon kimliklerinin listesine gitmek için, bir önceki adımdan, kıvrımlı yardımcı programını ve API kökünü kullanın

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Artık Azure Sentinel 'e anomali Limo tarafından sunulan bir veya daha fazla TAXıı sunucu koleksiyonuna bağlanmak için gereken tüm bilgilere sahipsiniz.

| **API kökü** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Koleksiyon kimliği |
| ------------------------------------------------------------ | ------------: |
| **Phsi tank**                                               | 107           |
| **Abuse.ch fidye ve IP 'Leri**                                  | 135           |
| **Abuse.ch fidye etki alanları**                              | 136           |
| **DShield tarama IP 'Leri**                                     | 150           |
| **Kötü amaçlı yazılımdan koruma etki alanı listesi-Hotlist**                            | 200           |
| **Blmalis TOR düğümleri**                                      | 209           |
| **Gelişmekte olan tehditler c&C Server**                              |  31           |
| **Lihigh Malwaredomains**                                    |  33           |
| **Siber suç**                                               |  41           |
| **Gelişmekte olan tehditler-tehlikede**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Azure Sentinel 'de Threat Intelligence-TAXıı veri bağlayıcısını etkinleştirme

Tehdit göstergelerini bir TAXıı sunucusundan Azure Sentinel 'e aktarmak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Sentinel** hizmetine gidin.

1. TAXıı sunucusundan tehdit göstergelerini içeri aktarmak istediğiniz **çalışma alanını** seçin.

1. Menüden **veri bağlayıcıları** ' nı seçin, bağlayıcılar galerisinden **Threat Intelligence-taxıı** ' i seçin ve **bağlayıcı sayfası aç** düğmesine tıklayın.

1. Bu TAXıı sunucu koleksiyonu, **API kök URL 'si**, **koleksiyon kimliği**, **Kullanıcı adı** (gerekliyse) ve **parola** (gerekliyse) için bir **ad** yazın ve **Ekle** düğmesine tıklayın.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="TAXıı sunucularını yapılandırma":::
 
Bir TAXıı sunucusuyla bağlantı kurulamanın başarıyla oluşturulduğunu ve aynı veya farklı bir TAXıı sunucusundan birden çok koleksiyona bağlanmak için istediğiniz kadar bir adım (4) tekrardan bir onay almalısınız.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Azure Sentinel 'de tehdit göstergelerini görüntüleme

Tehdit göstergelerini, tehdit **bilgileri platformunu** ve/veya **Threat Intelligence-taxıı** veri bağlayıcısını kullanarak Azure Sentinel 'e başarıyla aktardığınıza göre, bunları Azure Sentinel olay verilerinizin depolandığı **günlüklerde bulunan günlük** Içindeki **threatıntelligenceındicator** tablosunda görüntüleyebilirsiniz. Bu tablo, analiz ve çalışma kitapları gibi diğer Azure Sentinel özellikleri tarafından gerçekleştirilen sorguların temelini oluşturur. **Threatıntelligenceındicator** tablosunda tehdit göstergelerini bulma ve görüntüleme.

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Sentinel** hizmetine gidin.

1. Tehdit göstergeleri ile veri bağlayıcısını kullanarak tehdit göstergelerini içeri aktardığınız **çalışma alanını** seçin.

1. Azure Sentinel menüsünün **genel** bölümünde bulunan **Günlükler** ' i seçin.

1. **Threatıntelligenceındicator** tablosu, **Azure Sentinel** grubunun altında bulunur.

1. Tablo adının yanındaki **Önizleme verileri** simgesini (göz) seçin ve bu tablodan kayıtları gösterecek bir sorgu yürütmek için **sorgu Düzenleyicisi 'nde** göster düğmesini seçin.

Sonuçlarınız aşağıda gösterildiği gibi örnek tehdit göstergesine benzer olmalıdır:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Örnek sorgu verileri":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Azure Sentinel 'in yeni tehdit bilgileri alanında tehdit göstergelerini yönetin

Azure Sentinel menüsünden erişilebilen yeni **tehdit bilgileri** alanıyla, bir **Günlükler** sorgusu yazmadan içeri aktarılan tehdit göstergelerini de görüntüleyebilir, sıralayabilir, filtreleyebilir ve arayabilirsiniz. Bu yeni alan ayrıca doğrudan Azure Sentinel arabirimi içinde tehdit göstergeleri oluşturmanıza ve gösterge etiketleme gibi genel tehdit zekası yönetim görevlerini gerçekleştirmenize ve güvenlik araştırmalamasıyla ilgili yeni göstergeler oluşturmanıza da olanak sağlar.
En yaygın görevlerden birine göz atalım, kolayca gruplandırma ve başvuru için yeni tehdit göstergeleri ve etiketleme göstergeleri oluşturun.

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Sentinel** hizmetine gidin.

1. Tehdit göstergeleri ile veri bağlayıcısını kullanarak tehdit göstergelerini içeri aktardığınız **çalışma alanını** seçin.

1. Azure Sentinel menüsündeki tehdit yönetimi bölümünden **tehdit bilgileri** ' ni seçin.

1. Sayfanın üst menüsünden **Yeni Ekle** düğmesini seçin.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Yeni bir tehdit göstergesi ekleyin" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Gösterge türünü seçin, ardından **Yeni gösterge** panelinde kırmızı bir yıldız işareti (*) ile işaretlenen gerekli alanları doldurun.

1. **Uygula**’yı seçin. Gösterge, göstergeler kılavuzuna eklenir ve **Günlükler** Içindeki Threatıntelligenceındicator tablosuna de gönderilir.

Tehdit göstergelerini etiketleme, bulmayı kolaylaştırmak için bunları gruplamak için kolay bir yoldur. Genellikle, belirli bir olayla ilgili göstergelere veya belirli bir aktörden veya iyi bilinen bir saldırı kampanyasından tehditleri temsil eden göstergelere bir etiket uygulayabilirsiniz. Tehdit göstergelerini tek tek veya çoklu seçim göstergelerini etiketleyebilir ve hepsini bir kez etiketlendirebilirsiniz. Aşağıda gösterilen birden çok göstergeyi bir olay KIMLIĞIYLE etiketlemeyle ilgili bir örnek aşağıda verilmiştir. Etiketleme serbest biçimli olduğundan, tehdit göstergesi etiketleri için standart adlandırma kuralları oluşturmak önerilen bir uygulamadır. Her göstergeye birden çok etiket uygulayabilirsiniz.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Tehdit göstergelerine Etiketler uygulama" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Analiz, tehdit göstergelerinizi olası tehditleri tespit etmek için koyar

Tehdit göstergelerinizi Azure Sentinel 'e alındı; Bunların nasıl görüntüleneceğini ve yönetileceğini gördünüz; Artık sizin için neler yapabileceğini görün. Azure Sentinel gibi SıEM çözümlerinde tehdit göstergeleri için en önemli kullanım örneği, Power Analytics kurallarına yöneliktir.  Bu gösterge tabanlı kurallar, kuruluşunuzdaki güvenlik tehditlerini algılamak için veri kaynaklarınızdan gelen ham olayları tehdit göstergelerinizi karşılaştırarak karşılaştırır. Azure Sentinel **Analytics**'te, zamanlanmış olarak çalışan ve güvenlik uyarıları üreten analiz kuralları oluşturun. Kurallar, kuralın ne sıklıkla çalışacağını, hangi tür sorgu sonuçlarının güvenlik uyarıları ürettiğini ve uyarılar oluşturulduğunda tetiklenecek otomatik yanıtları belirleme yapılandırmalarının yanı sıra sorgular tarafından da çalıştırılır.

Sıfırdan her zaman yeni analiz kuralları oluşturabilirsiniz, ancak Azure Sentinel, Microsoft Güvenlik mühendisleri tarafından oluşturulan ve gereksinimlerinizi karşılayacak şekilde kullanabileceğiniz yerleşik bir kural şablonları kümesi sağlar. Tehdit göstergelerini kullanan kural şablonlarını, "**TI Map**..." ile başlayan tüm bunlar gibi kolay bir şekilde belirleyebilirsiniz. Tüm bu kural şablonları benzer şekilde çalışır; tek fark, tehdit göstergeleri türünün kullanıldığı tek farklılık (etki alanı, e-posta, dosya karması, IP adresi veya URL) ve hangi olay türünün eşleşeceğini gösterir. Her şablon, kuralın çalışması için gereken gerekli veri kaynaklarını listeler. böylece, Azure Sentinel 'de gerekli olan olayları zaten içeri aktardıysanız bir bakışta görebilirsiniz.

Bu kural şablonlarından birine göz atalım ve Azure Sentinel 'e aktardığınız tehdit göstergelerini kullanarak güvenlik uyarıları oluşturmak için kuralı etkinleştirme ve yapılandırma konusunda yol göstereceğiz. Bu örnekte, **AzureActivity Için TI eşleme IP varlığı** adlı kural şablonunu kullanacağız. Bu kural, tüm Azure etkinlik olaylarınızı içeren herhangi bir IP adresi türü tehdit göstergesi ile eşleşir. Bir eşleşme bulunduğunda, bir **uyarının** ve güvenlik işlemleri ekibiniz tarafından araştırılması için karşılık gelen bir **olayın** oluşturulması gerekir. Bu analiz kuralı, **tehdit bilgileri** veri bağlayıcılarından birini veya her ikisini de etkinleştirdiyseniz (tehdit göstergelerini içeri aktarmak için) ve **Azure etkinlik** verileri bağlayıcısını (Azure abonelik düzeyi olaylarınızı içeri aktarmak için) etkinleştirdiyseniz başarıyla çalışır.

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Sentinel** hizmetine gidin.

1. **Azure etkinlik** verileri bağlayıcısını kullanarak **tehdit bilgileri** veri bağlayıcıları ve Azure etkinlik verilerini kullanarak tehdit göstergelerini içeri aktardığınız **çalışma alanını** seçin.

1. Azure Sentinel menüsündeki **yapılandırma** bölümünden **analiz** ' ı seçin.

1. Kullanılabilir analiz kuralı şablonlarının listesini görmek için **kural şablonları** sekmesini seçin.

1. **TI Map IP varlığı AzureActivity adresine** gidin ve gerekli tüm veri kaynaklarını aşağıda gösterildiği gibi bağladığınıza emin olun.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Gerekli veri kaynakları":::

1. Bu kuralı seçin ve **kural oluştur** düğmesini seçin. Bu, kuralı yapılandırmak için bir sihirbaz açar. Ayarları burada doldurun ve **Sonraki: kural mantığını ayarla >** düğmesini seçin.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Analiz kuralı oluştur":::

1. Sihirbazın kural mantığı kısmı şunları içerir:
    - Kuralda kullanılacak sorgu.

    - Azure Sentinel 'in hesaplar, IP adresleri ve URL 'Ler gibi varlıkları nasıl tanılacağını belirten varlık eşlemeleri, böylece **Olaylar** ve **araştırmalar** , bu kural tarafından oluşturulan herhangi bir güvenlik uyarısındaki verilerle çalışmayı anlayacaktır.

    - Bu kuralı çalıştırma zamanlaması.

    - Bir güvenlik uyarısı oluşturulmadan önce gereken sorgu sonuçlarının sayısı.

    Şablondaki varsayılan ayarlar şunlardır:
    - Bir saat sonra çalıştırın.

    - **Threatıntelligenceındicator** TABLOSUNDAKI tüm IP adresi tehdit göstergelerini, **AzureActivity** tablosundan olayların son bir saati IÇINDE bulunan herhangi bir IP adresiyle eşleştirin.

    - Sorgu sonuçları sıfırdan büyükse bir güvenlik uyarısı oluşturun, yani herhangi bir eşleşme bulunursa.

Varsayılan ayarları bırakabilir veya gereksinimlerinizi karşılayacak şekilde değiştirebilirsiniz. İşiniz bittiğinde, **Sonraki: otomatikleştirilmiş yanıt >** düğmesini seçin

1. Sihirbazın bu adımı, bu analiz kuralından bir güvenlik uyarısı oluşturulduğunda tetiklemek istediğiniz herhangi bir Otomasyonu yapılandırmanıza olanak tanır. Azure Sentinel 'de Otomasyon, Azure Logic Apps tarafından desteklenen **playbooks** kullanılarak yapılır. Daha fazla bilgi edinmek için bu [öğreticiye bakın: Azure Sentinel 'de otomatik tehdit yanıtlarını ayarlama](./tutorial-respond-threats-playbook.md). Bu örnekte, devam etmek için **İleri: gözden geçir >** düğmesini seçeceğiz.

1. Bu son adım, kuraldaki ayarları doğrular. Kuralı etkinleştirmeye hazırsanız **Oluştur** düğmesini seçin ve işiniz bitmiştir.

Analiz kuralınızı etkinleştirmiş olduğunuza göre, etkin kuralınızı Azure Sentinel 'in **analiz** bölümünün **etkin kurallar** sekmesinde bulabilirsiniz. Etkin kuralı buradan düzenleyebilir, etkinleştirebilir, devre dışı bırakabilir, çoğaltabilir veya silebilirsiniz. Yeni kural etkinleştirme sonrasında hemen çalışır ve bundan sonra tanımlanan zamanlamaya göre çalıştırılır.

Varsayılan ayarlara göre, kural zamanlamasında her çalıştığında bulunan sonuçlar güvenlik uyarısı oluşturur. Azure Sentinel 'deki güvenlik uyarıları **, Azure Sentinel grubu altında** bulunan **securityalert** tablosunda Azure Sentinel 'in **Günlükler** bölümünde görüntülenebilir.

Azure Sentinel 'de, analiz kurallarından oluşturulan uyarılar, Azure Sentinel menüsünde **tehdit yönetimi** altındaki **olaylarda** bulunan güvenlik olayları da oluşturur. Olaylar, uygun yanıt eylemlerini belirlemek için güvenlik işlemleri ekiplerinizin önceliklendirme ve araştırılacağı şeydir. Bu öğreticide ayrıntılı bilgiler bulabilirsiniz [: Azure Sentinel ile olayları araştırın](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Çalışma kitapları, tehdit zekası hakkında öngörüler sağlar

Son olarak, Azure Sentinel 'de tehdit zekalarınızla ilgili önemli bilgileri görselleştirmek için bir Azure Sentinel çalışma kitabı kullanabilirsiniz ve çalışma kitaplarını iş gereksinimlerinize göre kolayca özelleştirebilirsiniz.
Azure Sentinel 'de sunulan tehdit bilgileri çalışma kitabını bulmayı ve ayrıca çalışma kitabında nasıl düzenleme yapılacağını adım adım göstereceğiz.

1. [Azure Portal](https://portal.azure.com/) açın ve **Azure Sentinel** hizmetine gidin.

1. Tehdit göstergeleri ile veri bağlayıcısını kullanarak tehdit göstergelerini içeri aktardığınız **çalışma alanını** seçin.

1. Azure Sentinel menüsünün **tehdit yönetimi** bölümünde **çalışma kitapları** ' nı seçin.

1. **Tehdit zekası** başlıklı çalışma kitabına gidin ve aşağıda gösterildiği gibi **Threatıntelligenceındicator** tablosunda verileriniz olduğunu doğrulayın.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Verileri doğrulama":::
 
1. **Kaydet** düğmesini seçin ve çalışma kitabını depolamak Için bir Azure konumu seçin. Çalışma kitabını dilediğiniz şekilde değiştirip değişikliklerinizi kaydettiğinizde bu adım gereklidir.

1. Şimdi görüntüleme ve düzenlemeyle çalışma kitabını açmak için **kaydedilmiş çalışma kitabını görüntüle** düğmesini seçin.

1. Artık şablon tarafından sunulan varsayılan grafikleri görmeniz gerekir. Şimdi grafiklerden birine bazı değişiklikler yapalım. Çalışma kitabı için düzenleme moduna girmek üzere sayfanın en üstündeki **Düzenle** düğmesini seçin.

1. Tehdit türüne göre yeni bir tehdit göstergeleri grafiği ekleyelim. Sayfanın en altına kaydırın ve sorgu Ekle ' yi seçin.

1. **Log Analytics çalışma alanı günlük sorgusu** metin kutusuna aşağıdaki metni ekleyin:
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. **Görselleştirme** açılır penceresinde **çubuk grafik**' i seçin.

1. **Düzenle** düğmesini seçin. Çalışma kitabınız için yeni bir grafik oluşturdunuz.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Çubuk grafik":::

Çalışma kitapları, Azure Sentinel 'in tüm yönlerine yönelik Öngörüler sunan güçlü etkileşimli panolar sağlar. Çalışma kitaplarıyla yapabileceğiniz bir tam nokta vardır ve sağlanan şablonlar harika bir başlangıç noktası olmakla birlikte, büyük olasılıkla bu şablonları incelemek ve özelleştirmek veya birçok farklı veri kaynağını birleştiren yeni panolar oluşturmak isteyeceksiniz, böylece verilerinizi benzersiz yollarla görselleştirebilirsiniz. Azure Sentinel çalışma kitapları Azure Izleyici çalışma kitaplarını temel aldığı için, daha fazla sayıda belge mevcuttur ve birçok şablon daha vardır. Başlamak için harika bir yer, [Azure izleyici çalışma kitapları ile etkileşimli raporlar oluşturmaya](../azure-monitor/visualize/workbooks-overview.md)yönelik bu makaledir. 

Ayrıca, GitHub üzerinde ek şablonlar indirebileceğiniz ve kendi şablonlarınıza katkıda bulunan zengin bir [Azure izleyici çalışma kitapları](https://github.com/microsoft/Application-Insights-Workbooks) vardır.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Sentinel 'in tehdit bilgileri özellikleri ve yeni tehdit bilgileri dikey penceresi hakkında bilgi edindiniz. Azure Sentinel 'in tehdit bilgileri yeteneklerini kullanma konusunda pratik yönergeler için aşağıdaki makalelere bakın:

- [Tehdit zekası verilerini](./connect-threat-intelligence.md) Azure Sentinel 'e bağlayın.
- Azure Sentinel 'de [yerleşik](./tutorial-detect-threats-built-in.md) veya [özel](./tutorial-detect-threats-custom.md) uyarılar oluşturun ve olayları [araştırın](./tutorial-investigate-cases.md) .