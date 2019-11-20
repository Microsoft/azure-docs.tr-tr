---
title: Azure Application Gateway yenilikleri
description: En son sürüm notları, bilinen sorunlar, hata düzeltmeleri, kullanım dışı işlevler ve yaklaşan değişiklikler gibi Azure Application Gateway yenilikleri hakkında bilgi edinin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: b2e7b9f28c84b769fe64be82f91418cc2a7d634f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044383"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure Application Gateway 'daki yenilikler nelerdir?

Azure Application Gateway, sürekli olarak güncelleştirilir. En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

- En son yayınlar
- Bilinen sorunlar
- Hata düzeltmeleri
- Kullanım dışı işlev

## <a name="new-features"></a>Yeni özellikler

|Özellik  |Açıklama  |Eklenme tarihi  |
|---------|---------|---------|
|Araştırma geliştirmeleri |Application Gateway v2 SKU 'sunda özel araştırma geliştirmeleri sayesinde, [isteğe bağlı arka uç durumu testlerini](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) kolaylaştırdık ve arka uç sistem durumunu gidermenize yardımcı olmak için [daha fazla tanılama bilgisi](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) eklemiş olduğumuz [araştırma yapılandırmasını](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)basitleştirdik. çıkışları.  |2019 Ekim |
|Daha fazla ölçüm |Uygulama ağ geçidi v2 SKU 'nuzu izlemenize yardımcı olması için aşağıdaki yeni ölçümleri ekledik: [zamanlama ile ilgili ölçümler](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), arka uç yanıt durumu, alınan bayt, gönderilen bayt, istemci TLS protokolü ve geçerli işlem birimleri. Bkz. [Application Gateway v2 SKU 'su tarafından desteklenen ölçümler](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Ağustos 2019 |
|WAF özel kuralları |Application Gateway WAF_v2 artık özel kuralların oluşturulmasını desteklemektedir. Bkz. [Application Gateway özel kurallar](custom-waf-rules-overview.md). |Haziran 2019 |
|Otomatik ölçeklendirme, bölge artıklığı, statik VIP desteği GA |Otomatik ölçeklendirmeyi, bölge yedekliliği, performansı, statik VIP 'leri, Key Vault, üst bilgi yeniden yazmayı destekleyen v2 SKU 'SU için genel kullanılabilirlik. Bkz. [Application Gateway otomatik ölçeklendirme belgeleri](application-gateway-autoscaling-zone-redundant.md). |Nisan 2019 |
|Key Vault tümleştirme |Application Gateway artık HTTPS etkin dinleyicilerine eklenen sunucu sertifikaları için Key Vault (genel önizlemede) tümleştirmeyi desteklemektedir. [Key Vault sertifikalarla SSL sonlandırmasına](key-vault-certs.md)bakın. |Nisan 2019 |
|Üst bilgi CRUD/yeniden yazar     |Artık HTTP üstbilgilerini yeniden yazabilirsiniz. Daha fazla bilgi için bkz. [öğretici: uygulama ağ geçidi oluşturma ve HTTP üstbilgilerini yeniden yazma](tutorial-http-header-rewrite-powershell.md) .|Aralık 2018|
|WAF yapılandırma ve dışlama listesi     |WAF 'nizi yapılandırmanıza ve hatalı pozitif sonuçları azaltmanıza yardımcı olacak daha fazla seçenek ekledik. Daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı istek boyutu sınırları ve dışlama listeleri](application-gateway-waf-configuration.md) .|Aralık 2018|
|Otomatik ölçeklendirme, bölge artıklığı, statik VIP desteği      |V2 SKU 'SU sayesinde, otomatik ölçeklendirme, gelişmiş performans ve daha fazlası gibi birçok geliştirme vardır. Daha fazla bilgi için bkz. [Azure Application Gateway nedir?](overview.md) .|Eylül 2018|
|Bağlantı boşaltma     |Bağlantı boşaltma, arka uç havuzlarınızdaki üyeleri sorunsuz bir şekilde kaldırmanıza olanak sağlar. Daha fazla bilgi için bkz. [Bağlantı boşaltma](overview.md#connection-draining).|Eylül 2018|
|Özel hata sayfaları     |Özel hata sayfaları ile, Web sitelerinizin geri kalanının biçimi içinde bir hata sayfası oluşturabilirsiniz. Bunu etkinleştirmek için, bkz. [Application Gateway özel hata sayfaları oluşturma](custom-error.md).|Eylül 2018|
|Ölçüm geliştirmeleri     |Gelişmiş ölçümlerle Application Gateway durumunu daha iyi bir şekilde görüntüleyebilirsiniz. Application Gateway ölçümleri etkinleştirmek için, [Application Gateway Için arka uç sistem durumu, tanılama günlükleri ve ölçümler](application-gateway-diagnostics.md)bölümüne bakın.|Haziran 2018|

## <a name="next-steps"></a>Sonraki adımlar

Azure Application Gateway hakkında daha fazla bilgi için bkz. [azure Application Gateway nedir?](overview.md)
