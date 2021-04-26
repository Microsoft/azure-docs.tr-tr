---
title: Azure Web uygulaması güvenlik duvarı (WAF) verilerini Azure Sentinel 'e bağlama
description: Azure WAF verilerini Azure Sentinel 'e bağlamayı öğrenin.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655910"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Azure Web uygulaması güvenlik duvarı 'ndan (WAF) veri bağlama

Web uygulamaları, yaygın olarak bilinen güvenlik açıklarından yararlanan kötü amaçlı saldırılara karşı giderek daha da yöneliktir. Azure Web uygulaması güvenlik duvarı (WAF), Web uygulamalarınızın kod ekleme ve siteler arası betik oluşturma gibi yaygın güvenlik açıklarından ve tehditlerden merkezi bir şekilde korunmasını sağlar. Azure WAF, Azure [Application Gateway](../web-application-firewall/ag/ag-overview.md) hizmeti, [Azure ön kapı](../web-application-firewall/afds/afds-overview.md) hizmeti ve [Azure Content Delivery Network (CDN)](../web-application-firewall/cdn/cdn-overview.md) WAF ilkesi aracılığıyla (Şu anda genel önizleme aşamasında) dağıtılabilir.
Azure WAF günlüklerini Azure Sentinel 'e bağlanarak, çalışma kitaplarında günlük verilerini görüntülemenize, özel uyarılar oluşturmak için bu uygulamayı kullanmanıza ve araştırmanızı iyileştirebilecek şekilde katabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında okuma ve yazma izinlerine sahip olmanız gerekir.

## <a name="connect-to-azure-waf"></a>Azure WAF 'ye bağlanma

### <a name="instructions-tab"></a>Yönergeler sekmesi

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. Veri bağlayıcıları galerisinden **Azure Web uygulaması güvenlik duvarını (WAF)** seçin ve ardından Önizleme bölmesinde **bağlayıcı sayfasını aç** ' ı seçin.

1. Günlüklerini bağlamak istediğiniz WAF kaynağı türü için bağlantıyı seçin – **Application Gateway kaynak >açın**, **ön kapı kaynağı >açın** veya **Content Delivery Network (CDN) WAF ilkesi >** açın ve kaynak listesinin ekranında bir kez, listeden bir WAF kaynağı seçin.

    1. WAF kaynağının gezinti menüsünde **Tanılama ayarları**' nı seçin.

    1. Listenin en altında **+ Tanılama ayarı Ekle** ' yi seçin.

    1. **Tanılama ayarları** ekranında, **Tanılama ayarları adı** alanına bir ad yazın.

    1. **Log Analytics gönder** onay kutusuna tıklayın. Burada iki yeni alan görüntülenir. İlgili **aboneliği** ve **Log Analytics çalışma alanını** (Azure Sentinel 'in bulunduğu yer) seçin.

    1. Günlüklerini almak istediğiniz olan kural türlerinin onay kutularına tıklayın. Her kaynak türü için Önerilerimiz:

        | Kaynak | Alma için seçilecek Günlükler |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF ilkesi      | WebApplicationFirewallLogs |
        |

    1. **Kaydet**’i seçin.

### <a name="next-steps-tab"></a>Sonraki adımlar sekmesi

- Azure WAF günlük verileriniz hakkında öngörü almak için **Azure Web uygulaması güvenlik duvarı** veri Bağlayıcısı ile birlikte paketlenmiş önerilen çalışma kitaplarına, sorgu örneklerine ve analiz kuralı şablonlarına bakın.

- **Günlüklerde** Azure WAF verilerini sorgulamak için sorgu penceresine **AzureDiagnostics** yazın.

> [!NOTE]
>
> Bu veri bağlayıcısıyla bağlantı durumu göstergeleri (veri bağlayıcıları galerisindeki bir renk şeridi ve veri türü adlarının yanında bulunan bağlantı simgeleri), yalnızca geçen iki haftada bir noktada verilerin alınmış olması halinde *bağlı* (yeşil) olarak görünür. Veri alımı olmadan iki hafta geçtikten sonra bağlayıcının bağlantısı kesilmekte olarak gösterilir. Daha fazla veri geldiğinde *bağlantılı* durum döndürülür.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Azure WAF günlüklerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.