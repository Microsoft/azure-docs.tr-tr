---
title: Azure Güvenlik Merkezi Veri Güvenliği | Microsoft Belgeleri
description: Bu belgede Azure Güvenlik Merkezi'nde verilerin yönetilmesi ve korunması açıklanmaktadır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596286"
---
# <a name="azure-security-center-data-security"></a>Azure Güvenlik Merkezi veri güvenliği

Azure Güvenlik Merkezi, müşterilerin tehditleri engellemesine, algılamasına ve yanıt vermesine yardımcı olmak için yapılandırma bilgileri, meta veriler, olay günlükleri ve daha fazlası dahil güvenlikle ilgili verileri toplar ve işler. Microsoft kodlamadan hizmet çalıştırma konularına kadar her alanda uyumluluk ve güvenlik yönergelerine kesin olarak bağlı kalmaktadır.

Bu makalede, güvenlik merkezi 'nde verilerin yönetilmesi ve korunması açıklanmaktadır.

## <a name="data-sources"></a>Veri kaynakları
Güvenlik Merkezi, güvenlik durumlarınızın görünürlüğünü sağlamak, güvenlik açıklarını belirlemek ve azaltıcı etkenleri önermek ve etkin tehditleri algılamak için aşağıdaki kaynaklardaki verileri analiz eder:

- **Azure hizmetleri**: Bu hizmetin kaynak sağlayıcısıyla iletişim kurarak dağıttığınız Azure hizmetlerinin yapılandırmasıyla ilgili bilgileri kullanır.
- **Ağ trafiği**: Microsoft 'un altyapısından kaynak/hedef IP/bağlantı noktası, paket boyutu ve ağ protokolü gibi örneklenmiş ağ trafiği meta verilerini kullanır.
- **Iş ortağı çözümleri**: güvenlik duvarları ve kötü amaçlı yazılımdan koruma çözümleri gibi tümleşik iş ortağı çözümlerinden güvenlik uyarılarını kullanır.
- **Makineleriniz**: yapılandırma ayrıntılarını ve Windows olay ve denetim günlükleri gibi güvenlik olaylarıyla ilgili bilgileri ve makinelerinizden syslog iletilerini kullanır.


## <a name="data-protection"></a>Veri koruma

### <a name="data-segregation"></a>Veri ayırma
Veriler, hizmetin tamamında her bir bileşen üzerinde mantıksal olarak ayrı tutulur. Tüm veriler kuruluşa göre etiketlenir. Bu etiketleme, veri yaşam döngüsü boyunca devam eder ve her bir hizmet katmanında uygulanır.

### <a name="data-access"></a>Veri erişimi
Microsoft personeli, güvenlik önerileri sağlamak ve olası güvenlik tehditlerini araştırmak için, işlem oluşturma olayları ve diğer yapıtlar dahil olmak üzere Azure hizmetleri tarafından toplanan veya çözümlenen bilgilere erişebilir. bu durum, yanlışlıkla müşteri verilerini veya kişisel verileri içerebilir. 

Microsoft 'un müşteri verilerini kullanmayacak veya reklam ya da benzer ticari amaçlar için bilgi türetmesini bildiren [Microsoft Online Services veri koruma ekine](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)uyduk. Müşteri Verileri yalnızca size Azure hizmetlerini sağlamak için, bu hizmetleri sağlamayla uyumlu amaçlar da dahil olmak üzere gerektiğinde kullanılır. Müşteri Verileri üzerindeki tüm haklarınız saklıdır.

### <a name="data-use"></a>Veri kullanımı
Microsoft, önleme ve algılama olanaklarımızı geliştirmek için birden çok kiracı genelinde görülen desenleri ve tehdit bilgilerini kullanır; Bu konuda [Gizlilik Bildirimizdeki](https://privacy.microsoft.com/privacystatement)gizlilik taahhütlerine göre yaptık.

## <a name="manage-data-collection-from-machines"></a>Makinelerden veri toplamayı yönetme
Azure'da Güvenlik Merkezi'ni etkinleştirdiğinizde her Azure aboneliğiniz için veri toplama etkinleştirilir. Ayrıca, güvenlik merkezi 'nde abonelikleriniz için veri toplamayı etkinleştirebilirsiniz. Veri toplama etkinleştirildiğinde, Güvenlik Merkezi, desteklenen tüm Azure sanal makinelerinde ve oluşturulan tüm yeni makinelerde Log Analytics aracısını sağlar.

Log Analytics Aracısı, güvenlikle ilgili çeşitli yapılandırma ve olayları [Windows Için olay izleme](/windows/win32/etw/event-tracing-portal) (ETW) izlemeleri halinde tarar. Ayrıca, işletim sistemi, makinenin çalışması sırasında olay günlüğü olaylarını ortaya koyar. Bu tür verilerin örnekleri şunlardır: işletim sistemi türü ve sürümü, işletim sistemi günlükleri (Windows olay günlükleri), çalışan işlemler, makine adı, IP adresleri, oturum açmış kullanıcı ve kiracı kimliği. Log Analytics Aracısı olay günlüğü girişlerini ve ETW izlemelerini okur ve bunları analiz için çalışma alanınıza kopyalar. Log Analytics Aracısı Ayrıca işlem oluşturma olaylarını ve komut satırı denetimini de sunar.

Azure Defender kullanmıyorsanız, ayrıca güvenlik Ilkesindeki sanal makinelerden veri toplamayı devre dışı bırakabilirsiniz. Azure Defender tarafından korunan abonelikler için veri toplama işlemi gereklidir. Veri toplama devre dışı bırakılsa bile, VM diski anlık görüntüleri ve yapıt toplama işlemi etkin olmaya devam eder.

Makinelerinizden toplanan verilerin depolandığı çalışma alanını ve bölgeyi belirtebilirsiniz. Varsayılan olarak, aşağıdaki tabloda gösterildiği gibi makinelerinizden toplanan verileri en yakın çalışma alanına depoladığınızda:

| VM'nin Bulunduğu Coğrafi Bölge                                      | Çalışma Alanının Bulunduğu Coğrafi Bölge  |
|---------------------------------------------|----------------|
| Birleşik Devletler, Brezilya, Güney Afrika         | Amerika Birleşik Devletleri  |
| Kanada                                      | Kanada         |
| Avrupa (Birleşik Krallık hariç)           | Avrupa         |
| Birleşik Krallık                              | Birleşik Krallık |
| Asya (Hindistan, Japonya, Kore, Çin hariç) | Asya Pasifik   |
| Güney Kore                                       | Asya Pasifik   |
| Hindistan                                       | Hindistan          |
| Japonya                                       | Japonya          |
| Çin                                       | Çin          |
| Avustralya                                   | Avustralya      |
|                                             |                |

> [!NOTE]
> **Depolama Için Azure Defender** , yapıtları ilgili Azure kaynağının konumuna göre bölgeli olarak depolar. [Depolama Için Azure Defender 'A giriş](defender-for-storage-introduction.md)hakkında daha fazla bilgi edinin.


## <a name="data-consumption"></a>Veri tüketimi

Müşteriler aşağıdaki veri akışlarından Güvenlik Merkezi ile ilgili verilere erişebilir:


| Akış                                                                                | Veri türleri                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure etkinlik günlüğü](../azure-monitor/essentials/activity-log.md)                       | Tüm güvenlik uyarıları, onaylanan Güvenlik Merkezi [tam zamanında](security-center-just-in-time.md) erişim istekleri ve [Uyarlamalı uygulama denetimleri](security-center-adaptive-application.md)tarafından oluşturulan tüm uyarılar.|
| [Azure İzleyici günlükleri](../azure-monitor/data-platform.md)                      | Tüm güvenlik uyarıları.                                                                                                                                                                                                |
| [Azure Kaynak Grafı](../governance/resource-graph/overview.md)                      | Güvenlik uyarıları, güvenlik önerileri, güvenlik açığı değerlendirmesi sonuçları, güvenli puan bilgileri, uyumluluk denetimlerinin durumu ve daha fazlası.                                                                       |
| [Azure Güvenlik Merkezi REST API](/rest/api/securitycenter/) | Güvenlik uyarıları, güvenlik önerileri ve daha fazlası.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Azure Güvenlik Merkezi'nde verilerin yönetilmesi ve korunması hakkında bilgi aldınız. 

Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik Merkezi nedir?](security-center-introduction.md)