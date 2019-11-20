---
title: Tehdit zekası verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Tehdit zekası verilerinin Azure Sentinel 'e nasıl bağlanacağını öğrenin.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 44b3830465bf2b5aa06612aa868b086b120f1ece
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372269"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Tehdit zekası sağlayıcılarından veri bağlama

> [!IMPORTANT]
> Azure Sentinel 'deki tehdit bilgileri platformu veri Bağlayıcısı Şu anda genel önizleme aşamasındadır.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel, kuruluşunuzun kullandığı tehdit göstergelerini içeri aktarmanıza olanak tanır. Bu, güvenlik analistlerinizin bilinen tehditleri algılama ve önceliklerini belirleme yeteneğini geliştirebilir. Azure Sentinel 'in birkaç özelliği daha sonra kullanılabilir hale gelir veya geliştirilmiştir:

- **Analiz** , tehdit göstergelerinizi günlük olaylarının eşleşmelerini temel alan uyarılar ve olaylar oluşturmak için etkinleştirebileceğiniz bir dizi zamanlanmış kural şablonu içerir.

- **Çalışma kitapları** , Azure Sentinel 'e aktarılan tehdit göstergeleri ve tehdit göstergelerinizi karşılayan analiz kurallarından oluşturulan uyarılar hakkında özetlenen bilgiler sağlar.

- Arama **sorguları,** güvenlik araştırmacıya ortak arama senaryoları bağlamında tehdit göstergelerini kullanmasına izin verir.

- Kötü amaçlı davranışlar için anomali ve huniler araştırdığınızda, **Not defterleri** tehdit göstergelerini kullanabilir.

Bir sonraki bölümde listelenen tümleşik tehdit bilgileri platformu (tıp) ürünlerinden birini kullanarak veya [Microsoft Graph güvenlik Tiındicators API](https://aka.ms/graphsecuritytiindicators)'siyle doğrudan tümleştirmeyi kullanarak tehdit göstergelerini Azure Sentinel 'e aktarabilirsiniz.

## <a name="integrated-threat-intelligence-platform-products"></a>Tümleşik tehdit bilgileri platformu ürünleri

- [Hatalı p açık kaynak tehdit bilgileri platformu](https://www.misp-project.org/)
    
    Tehdit göstergelerini Microsoft Graph güvenlik API 'sine geçirmek için hatalı p örnekleri sunan bir örnek betik için, bkz. [MIP to Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Kılavuzlu yönergeler için bkz. [MineMeld kullanarak Microsoft Graph SECURITY API 'Sine ıocs gönderme](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect platformu](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Önkoşullar  

- Tıp ürününüzün veya güvenlik yöneticisi 'nin Microsoft Graph güvenlik Tiındicators API 'SI ile doğrudan tümleştirme kullanan özel uygulamanıza izin vermek için genel yönetici veya güvenlik yöneticisinin Azure AD rolü.

- Tehdit göstergelerini depolamak için Azure Sentinel çalışma alanına yönelik okuma ve yazma izinleri.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Azure Sentinel 'i tehdit bilgileri sağlayıcınıza bağlama

1. Uygulama KIMLIĞI, uygulama gizli anahtarı ve Azure Active Directory kiracı KIMLIĞI almak için Azure Active Directory [bir uygulamayı kaydedin](/graph/auth-v2-service#1-register-your-app) . Microsoft Graph Security Tiındicators API 'siyle doğrudan tümleştirme kullanan tümleşik tıp ürününüzü veya uygulamanızı yapılandırırken bu değerlere ihtiyacınız vardır.

2. Kayıtlı uygulama için [API Izinlerini yapılandırma](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) : kayıtlı uygulamanıza, **Threatındictındictındictındicmators. ReadWrite. ownedby** uygulama izni Microsoft Graph ekleyin.

3. Azure Active Directory kiracı yöneticinizden kuruluşunuzun kayıtlı uygulamasına yönetici onayı vermesini isteyin. Azure portal: **Azure Active Directory** > **uygulama kayıtları** >  **\<_uygulama adı_>**  > **API izinleri**0**2 Kiracı adı için yönetici izni verme4**.

4. Şunları belirterek, Azure Sentinel 'e göstergeler göndermek için Microsoft Graph Security Tiındicators API 'SI ile doğrudan tümleştirme kullanan tıp ürününüzü veya uygulamanızı yapılandırın:
    
    a. Kayıtlı uygulama KIMLIĞI, gizli anahtar ve kiracı KIMLIĞI için değerler.
    
    b. Hedef ürün için Azure Sentinel ' i belirtin.
    
    c. Eylem için uyarı ' ı belirtin.

5. Azure portal **Azure Sentinel** > **veri bağlayıcıları** ' na gidin ve ardından **tehdit bilgileri platformları (Önizleme)** bağlayıcısını seçin.

6. **Bağlayıcı sayfasını aç**' ı ve sonra **Bağlan**' ı seçin.

7. Azure Sentinel 'e aktarılan tehdit göstergelerini görüntülemek için **Azure Sentinel-Logs** > **securityınsights**' a gidin ve sonra **Threatıntelligenceındicator**' ı genişletin.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, tehdit bilgileri sağlayıcınızı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın.

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats.md)başlayın.
