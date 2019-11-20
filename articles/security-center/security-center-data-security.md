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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 9dfed5ec14460c3c6213d528f59b86eda15aa1a7
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520890"
---
# <a name="azure-security-center-data-security"></a>Azure Güvenlik Merkezi Veri Güvenliği
Müşterilerin tehditleri önlemesine, algılamasına ve yanıt vermesine yardımcı olmak amacıyla Azure Güvenlik Merkezi, güvenlikle ilgili veriler, meta veriler, olay günlükleri, kilitlenme dökümü dosyaları ve diğer verileri toplar ve işler. Microsoft kodlamadan hizmet çalıştırma konularına kadar her alanda uyumluluk ve güvenlik yönergelerine kesin olarak bağlı kalmaktadır.

Bu makalede Azure Güvenlik Merkezi'nde verilerin yönetilmesi ve korunması açıklanmaktadır.

## <a name="data-sources"></a>Veri kaynakları
Azure Güvenlik Merkezi, güvenlik durumunuzu görüntüleme, güvenlik açıklarını tanımlayıp çözümler önerme ve etkin tehditleri saptama amacıyla aşağıdaki kaynaklarda bulunan verileri analiz eder:

- Azure Hizmetleri: Dağıttığınız Azure hizmetlerinin yapılandırmasına ilişkin bilgileri, ilgili hizmetin kaynak sağlayıcısıyla iletişim kurarak kullanır.
- Ağ Trafiği: Microsoft’un altyapısından kaynak/hedef IP/bağlantı noktası, paket boyutu ve ağ protokolü gibi örneği alınmış ağ trafiği meta verilerini kullanır.
- İş Ortağı Çözümleri: Güvenlik duvarları ve kötü amaçlı yazılımdan koruma çözümleri gibi tümleşik iş ortağı çözümlerinden güvenlik uyarılarını kullanır.
- Sanal Makineleriniz ve Sunucularınız: Sanal makinelerinizdeki yapılandırma bilgilerinin yanı sıra Windows olay ve denetim günlükleri, IIS günlükleri, syslog iletileri ve kilitlenme bilgi dökümü dosyaları gibi güvenlik olaylarına ilişkin bilgileri kullanır. Ayrıca, bir uyarı oluşturulduğunda Azure Güvenlik Merkezi etkilenen VM diskinin anlık görüntüsünü oluşturur ve adli amaçlar için kayıt defteri dosyası gibi uyarıyla ilgili makine yapıtlarını VM diskinden ayıklar.


## <a name="data-protection"></a>Veri koruma
**Veri ayırma**: Veriler hizmet boyunca her bir bileşende mantıksal olarak ayrı tutulur. Tüm veriler kuruluşa göre etiketlenir. Bu etiketleme, veri yaşam döngüsü boyunca devam eder ve her bir hizmet katmanında uygulanır.

**Veri erişimi**: Microsoft personeli, güvenlik önerileri sunmak ve olası güvenlik tehditlerini araştırmak amacıyla Azure hizmetleri tarafından sunulan, Müşteri Verilerini ya da sanal makinelerinizdeki kişisel verileri de içerebilecek kilitlenme dökümü dosyaları, işlem oluşturma olayları, VM diski anlık görüntüleri ve yapıtları dahil toplanan veya analiz edilen bilgilere erişebilir. Microsoft’un Müşteri Verilerini kullanmayacağını veya reklam ya da benzeri ticari amaçlarla bundan bilgi türetmeyeceğini belirten [Microsoft Çevrimiçi Hizmet Koşulları ve Gizlilik Bildirimi](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) belgesine bağlı kalıyoruz. Müşteri Verileri yalnızca size Azure hizmetlerini sağlamak için, bu hizmetleri sağlamayla uyumlu amaçlar da dahil olmak üzere gerektiğinde kullanılır. Müşteri Verileri üzerindeki tüm haklarınız saklıdır.

**Veri kullanımı**: Microsoft önleme ve algılama özelliklerimizi geliştirmek amacıyla birden fazla kiracıda görülen modelleri ve tehdit bilgilerini kullanır; bunu [Gizlilik Bildirimi](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) belgemizde açıklanan gizlilik taahhütlerine uygun şekilde yaparız.

## <a name="data-location"></a>Veri konumu

**Çalışma Alanlarınız**: Aşağıdaki coğrafi bölgeler için bir çalışma alanı belirtilir ve kilitlenme bilgi dökümlerinin yanı sıra bazı uyarı verisi türleri de dahil olmak üzere Azure sanal makinelerinizden toplanan veriler en yakın çalışma alanında depolanır.

| VM'nin Bulunduğu Coğrafi Bölge                              | Çalışma Alanının Bulunduğu Coğrafi Bölge |
|-------------------------------------|---------------|
| Birleşik Devletler, Brezilya, Güney Afrika | Amerika Birleşik Devletleri |
| Kanada                              | Kanada        |
| Avrupa (Birleşik Krallık hariç)   | Avrupa        |
| Birleşik Krallık                      | Birleşik Krallık |
| Asya (Hindistan, Japonya, Kore, Çin hariç)   | Asya Pasifik  |
| Güney Kore                              | Asya Pasifik  |
| Hindistan                               | Hindistan         |
| Japonya                               | Japonya         |
| Çin                               | Çin         |
| Avustralya                           | Avustralya     |


VM diski anlık görüntüleri, VM diski ile aynı depolama hesabına kaydedilir.

Başka ortamlarda çalışan sanal makineler ve sunucular için (ör. şirket içi), toplanan verilerin depolanacağı çalışma alanını ve bölgeyi belirtebilirsiniz.

**Azure Güvenlik Merkezi Depolama Alanı**: İş ortağı uyarılarının da dahil olduğu güvenlik uyarıları hakkındaki bilgiler, ilgili Azure kaynağının konumuna göre bölgesel olarak depolanırken güvenlik sistem durumu ve öneriler hakkındaki bilgiler, müşterinin konumuna göre merkezi olarak Amerika Birleşik Devletleri'nde veya Avrupa'da depolanır.
Azure Güvenlik Merkezi, kilitlenme döküm dosyalarının kısa ömürlü kopyalarını toplar ve açıktan yararlanma girişimlerinin ve başarılı uzlaşmaların kanıtı olarak analiz eder. Azure Güvenlik Merkezi bu analizi çalışma alanıyla aynı coğrafi bölgede gerçekleştirir ve analiz tamamlandığında kısa ömürlü kopyaları siler.

Makine yapıları VM ile aynı bölgede merkezi olarak depolanır.


## <a name="managing-data-collection-from-virtual-machines"></a>Sanal makinelerden veri toplamayı yönetme

Azure'da Güvenlik Merkezi'ni etkinleştirdiğinizde her Azure aboneliğiniz için veri toplama etkinleştirilir. Abonelikleriniz için veri toplamayı Azure Güvenlik Merkezi'nin Güvenlik İlkesi bölümünden de etkinleştirebilirsiniz. Veri toplama etkin olduğunda Azure Güvenlik Merkezi, desteklenen tüm mevcut Azure sanal makinelerinde ve yeni oluşturulan sanal makinelerde Microsoft Monitoring Agent'ı hazırlar.
Microsoft Monitoring Agent, [Windows için Olay İzleme](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) izlerinde güvenlikle ilgili çeşitli yapılandırmaları ve olayları tarar. Ayrıca, işletim sistemi, makinenin çalışması sırasında olay günlüğü olaylarını ortaya koyar. Bu tür verilerin örnekleri şunlardır: işletim sistemi türü ve sürümü, işletim sistemi günlükleri (Windows olay günlükleri), çalışan işlemler, makine adı, IP adresleri, oturum açmış kullanıcı ve kiracı kimliği. Microsoft Monitoring Agent, olay günlüğü girişleri ile ETW izlerini okur ve analiz için çalışma alanlarınıza kopyalar. Microsoft Monitoring Agent ayrıca kilitlenme bilgi dökümü dosyalarını çalışma alanlarınıza kopyalar, işlem oluşturma olaylarını etkinleştirir ve komut satırı denetimine olanak tanır.

Azure Güvenlik Merkezi Ücretsiz sürümünü kullanıyorsanız Güvenlik İlkesi'nde sanal makinelerden veri toplamayı devre dışı bırakabilirsiniz. Veri Toplama, Standart katmandaki abonelikler için gereklidir. Veri toplama devre dışı bırakılsa bile, VM diski anlık görüntüleri ve yapıt toplama işlemi etkin olmaya devam eder.

## <a name="data-consumption"></a>Veri Tüketimi

Müşteriler, farklı veri akışlarındaki Güvenlik Merkezi ile ilişkili verileri aşağıda gösterildiği gibi kullanabilir:

* **Azure etkinliği**: tüm güvenlik uyarıları, onaylanan [Güvenlik Merkezi istekleri](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) ve [Uyarlamalı uygulama denetimleri](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)tarafından oluşturulan tüm uyarılar.
* **Azure izleyici günlükleri**: tüm güvenlik uyarıları.


> [!NOTE]
> Güvenlikle ilgili öneriler, REST API’si tarafından da kullanılabilir. Daha fazla bilgi için bkz. [Güvenlik Kaynağı Sağlayıcısı REST API Başvurusu](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Ayrıca bkz.
Bu belgede Azure Güvenlik Merkezi'nde verilerin yönetilmesi ve korunması hakkında bilgi aldınız. Azure Güvenlik Merkezi hakkında daha fazla bilgi edinmek bkz.:

* [Azure Güvenlik Merkezi Planlama ve İşlemler Kılavuzu](security-center-planning-and-operations-guide.md) - Azure Güvenlik Merkezi'ni benimsemek için tasarım ile ilgili dikkat edilmesi gerekenleri planlama ve anlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md) - Azure kaynaklarınızın sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) - Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md) - İş ortağı çözümlerinizin sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) - Hizmet kullanımı ile ilgili sık sorulan soruları burada bulabilirsiniz
* [Azure Güvenlik Blogu](https://blogs.msdn.com/b/azuresecurity/) - Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulabilirsiniz
