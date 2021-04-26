---
title: IoT için Azure Sentinel for Defender 'ı yapılandırma
description: Azure Sentinel 'in, bir IoT çözümü için Defender 'dan veri almak üzere nasıl yapılandırılacağını açıklar.
ms.topic: how-to
ms.date: 12/28/2020
ms.openlocfilehash: b481dd31b73e741d265a569076f1ddc076ad4a45
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778959"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel"></a>IoT için Defender 'daki verilerinizi Azure Sentinel 'e bağlama 

Tüm IoT olayları için Defender 'ı Azure Sentinel 'e aktarmak üzere IoT için Defender bağlayıcısını kullanın. 

Bu tümleştirme, kuruluşların genellikle onu ve sınırları aşan çok aşamalı saldırıları hızla algılamasını sağlar. Ayrıca, IoT 'nin Azure Sentinel 'in güvenlik düzenlemesi, otomasyonu ve yanıtı (SOAR) özellikleri ile tümleştirilmesi için Defender, yerleşik olarak iyileştirilmiş PlayBook 'lar kullanılarak otomatik yanıt ve önlemeye imkan tanıyor. 

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel 'in dağıtıldığı çalışma alanında **okuma** ve **yazma** izinleri
- **IoT Için Defender 'ın** ilgili IoT Hub **etkinleştirilmiş** olması gerekir
- Bağlanmak istediğiniz **abonelik** üzerinde **katkıda bulunan** izinlerinizin olması gerekir

## <a name="connect-to-defender-for-iot"></a>IoT için Defender 'a bağlanma

1. Azure Sentinel 'de, **veri bağlayıcıları** ' nı seçin ve sonra, galerinin (IoT Için Azure Güvenlik Merkezi 'nin hala bu şekilde adlandırılır) **için Defender** ' ı seçin.

1. Sağ bölmenin alt kısmından **bağlayıcı sayfasını aç**' a tıklayın.

1. Uyarıları ve cihaz uyarılarını Azure Sentinel 'e aktarmak istediğiniz her bir IoT Hub aboneliğinin yanındaki **Bağlan**' a tıklayın.
    - IoT için Defender, bir abonelik içindeki en az bir IoT Hub etkinleştirilmemişse bir hata iletisi alırsınız. Hatayı kaldırmak için IoT Hub içindeki IoT için Defender 'ı etkinleştirin.

1. IoT için Defender 'daki uyarıların Azure Sentinel 'de otomatik olarak olay oluşturmasını isteyip istemediğinize karar verebilirsiniz. **Olayları oluştur** altında, varsayılan analiz kuralının oluşturulan uyarılardan otomatik olarak olay oluşturmasını sağlamak için **Etkinleştir** ' i seçin. Bu kural, **analiz**  >  **etkin kuralları** altında değiştirilebilir veya düzenlenebilir.

> [!NOTE]
> **Abonelik** listesinin bağlantı değişiklikleri yapıldıktan sonra yenilenmesi için 10 saniye veya daha fazla zaman alabilir. 

## <a name="log-analytics-alert-view"></a>Log Analytics uyarı görünümü

Log Analytics bölümünde ilgili şemayı, IoT uyarıları için Defender 'ı göstermek üzere kullanmak için:

1. **Günlükleri**  >  **securityınsights**  >  **securityalert** olarak açın veya **securityalert** araması yapın.

1. Aşağıdaki KQL filtresini kullanarak yalnızca IoT tarafından oluşturulan uyarılar için Defender 'ı görmek üzere filtreleyin:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Hizmet notları

Bir **aboneliği** bağladıktan sonra merkez verileri yaklaşık 15 dakika sonra Azure 'da kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, IoT için Defender 'ı Azure Sentinel 'e bağlamayı öğrendiniz. Tehdit algılama ve güvenlik verileri erişimi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- Azure Sentinel kullanarak [verilerinize ilişkin görünürlük ve potansiyel tehditler alma](../sentinel/quickstart-get-visibility.md)hakkında bilgi edinin.
- [IoT güvenlik verilerinize erişme](how-to-security-data-access.md) hakkında bilgi edinin