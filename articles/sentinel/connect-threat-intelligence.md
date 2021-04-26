---
title: Tehdit zekası verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Tehdit zekası verilerinin Azure Sentinel 'e nasıl bağlanacağını öğrenin.
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
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96121739"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Tehdit analizi sağlayıcılarından verilere bağlanma

> [!IMPORTANT]
> Azure Sentinel 'deki tehdit bilgileri veri bağlayıcıları Şu anda genel önizleme aşamasındadır.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel, kuruluşunuzun kullandığı tehdit göstergelerini içeri aktarmanıza olanak tanır. Bu, güvenlik analistlerinizin bilinen tehditleri algılama ve önceliklerini belirleme yeteneğini geliştirebilecek. Azure Sentinel 'in birkaç özelliği daha sonra kullanılabilir hale gelir veya geliştirilmiştir:

- **Analiz** , tehdit göstergelerinizi günlük olaylarının eşleşmelerini temel alarak uyarılar ve olaylar oluşturmak için etkinleştirebileceğiniz bir dizi zamanlanmış kural şablonu içerir.

- **Çalışma kitapları** , Azure Sentinel 'e aktarılan tehdit göstergeleri ve tehdit göstergelerinizi karşılayan analiz kurallarından oluşturulan tüm uyarılar hakkında özetlenen bilgiler sağlar.

- Arama **sorguları,** güvenlik araştırmacıya ortak arama senaryoları bağlamında tehdit göstergelerini kullanmasına izin verir.

- Kötü amaçlı davranışlar için anomali ve huniler araştırdığınızda, **Not defterleri** tehdit göstergelerini kullanabilir.

Sonraki bölümde listelenen tümleşik tehdit bilgileri platformu (tıp) ürünlerinden birini kullanarak, TAXıı sunucularına bağlanarak veya [Microsoft Graph Security Tiındicators API](/graph/api/resources/tiindicator)'siyle doğrudan tümleştirmeyi kullanarak tehdit göstergelerini Azure Sentinel 'e aktarabilirsiniz.

## <a name="integrated-threat-intelligence-platform-products"></a>Tümleşik tehdit bilgileri platformu ürünleri

- [Hatalı p açık kaynak tehdit bilgileri platformu](https://www.misp-project.org/)
    
    Tehdit göstergelerini Microsoft Graph güvenlik API 'sine geçirmek için hatalı p örnekleri sunan bir örnek betik için, bkz. [MIP to Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    ThreatStream tümleştirmelerini ve uzantılarını ve Microsoft Graph güvenlik API 'sine ThreatStream Intelligence bağlama yönergelerini indirmek için, [threatstream İndirmeleri](https://ui.threatstream.com/downloads) sayfasına bakın.

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Kılavuzlu yönergeler için bkz. [MineMeld kullanarak Microsoft Graph SECURITY API 'Sine ıocs gönderme](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect platformu](https://threatconnect.com/solution/)

    Bilgi için bkz. [Threatconnect tümleştirmeleri](https://threatconnect.com/integrations/) ve sayfada Microsoft Graph güvenlik API 'sine bakın.

- [EclecticIQ platformu](https://www.eclecticiq.com/solutions)

- [ThreatQ Threat Intelligence platformu](https://www.threatq.com/)

    Bilgi ve kılavuzlu yönergeler için bkz. [ThreatQ tümleştirmesi Için Microsoft Sentinel Bağlayıcısı](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview).

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Azure Sentinel 'i tehdit bilgileri platformunuza bağlama

### <a name="prerequisites"></a>Önkoşullar  

- Tıp ürününüzün veya güvenlik yöneticisi 'nin Microsoft Graph güvenlik Tiındicators API 'SI ile doğrudan tümleştirme kullanan özel uygulamanıza izin vermek için genel yönetici veya güvenlik yöneticisinin Azure AD rolü.

- Tehdit göstergelerini depolamak için Azure Sentinel çalışma alanına yönelik okuma ve yazma izinleri.

### <a name="instructions"></a>Yönergeler

1. Uygulama KIMLIĞI, uygulama gizli anahtarı ve Azure Active Directory kiracı KIMLIĞI almak için Azure Active Directory [bir uygulamayı kaydedin](/graph/auth-v2-service#1-register-your-app) . Microsoft Graph Security Tiındicators API 'siyle doğrudan tümleştirme kullanan tümleşik tıp ürününüzü veya uygulamanızı yapılandırırken bu değerlere ihtiyacınız vardır.

2. Kayıtlı uygulama için [API Izinlerini yapılandırma](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) : kayıtlı uygulamanıza, **Threatındictındictındictındicmators. ReadWrite. ownedby** uygulama izni Microsoft Graph ekleyin.

3. Azure Active Directory kiracı yöneticinizden kuruluşunuzun kayıtlı uygulamasına yönetici onayı vermesini isteyin. Azure Portal: **Azure Active Directory**  >  **uygulama kayıtları**  >  **\<_app name_>**  >  **API izinleri**  >  **için \<_tenant name_> yönetici izni ver**' i görüntüleyin.

4. Şunları belirterek, Azure Sentinel 'e göstergeler göndermek için Microsoft Graph Security Tiındicators API 'SI ile doğrudan tümleştirme kullanan tıp ürününüzü veya uygulamanızı yapılandırın:
    
    a. Kayıtlı uygulama KIMLIĞI, gizli anahtar ve kiracı KIMLIĞI için değerler.
    
    b. Hedef ürün için Azure Sentinel ' i belirtin.
    
    c. Eylem için uyarı ' ı belirtin.

5. Azure Portal **Azure Sentinel**  >  **veri bağlayıcıları** ' na gidin ve ardından **tehdit bilgileri platformları (Önizleme)** bağlayıcısını seçin.

6. **Bağlayıcı sayfasını aç**' ı ve sonra **Bağlan**' ı seçin.

7. Azure Sentinel 'e içeri aktarılan tehdit göstergelerini görüntülemek için **Azure Sentinel**'e gidin  >  ve **securityınsights**'ı günlüğe kaydeder ve sonra **threatıntelligenceındicator**' ı genişletin.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Azure Sentinel 'i TAXıı sunucularına bağlama

### <a name="prerequisites"></a>Önkoşullar

- Tehdit göstergelerini depolamak için Azure Sentinel çalışma alanına yönelik okuma ve yazma izinleri.

- TAXıı 2,0 sunucu URI 'SI ve koleksiyon KIMLIĞI.

### <a name="instructions"></a>Yönergeler

1. Azure Portal **Azure Sentinel**  >  **veri bağlayıcıları** ' na gidin ve **tehdit zekası-taxıı (Önizleme)** bağlayıcısını seçin.

2. **Bağlayıcı sayfasını aç**' ı seçin.

3. Metin kutularında gerekli ve isteğe bağlı bilgileri belirtin.

4. TAXıı 2,0 sunucusuyla bağlantıyı etkinleştirmek için **Ekle** ' yi seçin.

5. Ek TAXıı 2,0 sunucularınız varsa: 3 ve 4. adımları yineleyin.

6. Azure Sentinel 'e içeri aktarılan tehdit göstergelerini görüntülemek için **Azure Sentinel**'e gidin  >  ve **securityınsights**'ı günlüğe kaydeder ve sonra **threatıntelligenceındicator**' ı genişletin.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, tehdit bilgileri sağlayıcınızı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın.

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.