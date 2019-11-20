---
title: Azure Güvenlik Merkezi ile Endpoint Protection sorunlarını yönetme | Microsoft Docs
description: Azure Güvenlik Merkezi 'nde Endpoint Protection sorunlarını yönetmeyi öğrenin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: memildin
ms.openlocfilehash: b1bcce1e94a64253986ad9b1fd77874ba0a39839
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202077"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Azure Güvenlik Merkezi ile Endpoint Protection sorunlarını yönetme
Azure Güvenlik Merkezi, kötü amaçlı yazılımdan koruma sorunlarını izler ve bu durumu Endpoint Protection sorunları dikey penceresinde bildirir. Güvenlik Merkezi, algılanan tehditler ve yetersiz koruma gibi sorunları vurgular ve sanal makinelerinizi (VM) ve bilgisayarlarınızı kötü amaçlı yazılımdan koruma tehditlerine karşı savunmasız hale getirebilir. **Endpoint Protection sorunları**altındaki bilgileri kullanarak, tanımlanan sorunları ele almak için bir plan tanımlayabilirsiniz.

Güvenlik Merkezi aşağıdaki uç nokta koruma sorunlarını bildirir:

- Endpoint Protection, Azure VM 'lerine yüklenmedi – desteklenen bir kötü amaçlı yazılımdan koruma çözümü bu Azure VM 'lerinde yüklü değil.
- Endpoint Protection, Azure dışı bilgisayarlara yüklenmedi: Bu Azure dışı bilgisayarlarda desteklenen bir kötü amaçlı yazılımdan koruma yüklü değildir.
- Endpoint Protection sistem durumu:

  - İmza güncel değil – bu sanal makinelere ve bilgisayarlara bir kötü amaçlı yazılımdan koruma çözümü yüklenir, ancak çözüm en son kötü amaçlı yazılımdan koruma imzalarına sahip değildir.
  - Gerçek zamanlı koruma yok – bu sanal makinelere ve bilgisayarlara bir kötü amaçlı yazılımdan koruma çözümü yüklenir, ancak gerçek zamanlı koruma için yapılandırılmamıştır.   Çözüm desteklenmediğinden, hizmet devre dışı bırakılmış olabilir veya güvenlik merkezi durumu alamıyor olabilir. Desteklenen çözümlerin bir listesi için bkz. [iş ortağı tümleştirmesi](security-center-services.md#endpoint-supported) .
  - Raporlama değil – bir kötü amaçlı yazılımdan koruma çözümü yüklenir, ancak raporlama verileri değildir.
  - Bilinmiyor – kötü amaçlı yazılımdan koruma çözümü yüklendi, ancak durumu bilinmiyor veya bilinmeyen bir hata bildiriyor.

    > [!NOTE]
    > Güvenlik Merkezi ile tümleştirilmiş Endpoint Protection güvenlik çözümlerinin listesi için bkz. [güvenlik çözümlerini tümleştirme](security-center-services.md#endpoint-supported) .
    >
    >

## <a name="implement-the-recommendation"></a>Öneriyi uygulayın
Uç nokta koruma sorunları, güvenlik merkezi 'nde bir öneri olarak sunulur.  Ortamınız kötü amaçlı yazılımdan koruma tehditlerine açık ise, bu öneri **öneriler** altında ve **işlem**altında görüntülenir. **Endpoint Protection sorunları panosunu**görmek Için, işlem iş akışını izlemeniz gerekir.

Bu örnekte, **işlem**kullanacağız.  Azure VM 'lerine ve Azure dışı bilgisayarlara kötü amaçlı yazılımdan koruma yüklemeye bakacağız.

## <a name="install-antimalware-on-azure-vms"></a>Azure VM 'lerine kötü amaçlı yazılım yüklemesi

1. Güvenlik Merkezi ana menüsünde veya **genel bakış**altında **işlem & uygulamalar** ' ı seçin.

   ![Işlem seçin][1]

2. **İşlem**altında **Endpoint Protection sorunları**' nı seçin. **Uç nokta koruma sorunları** panosu açılır.

   ![Endpoint Protection sorunlarını seçin][2]

   Panonun üst kısmında şunları sağlar:

   - Yüklü Endpoint Protection sağlayıcıları-Güvenlik Merkezi tarafından tanımlanan farklı sağlayıcıları listeler.
   - Yüklü Endpoint Protection sistem durumu-yüklü bir uç nokta koruma çözümü olan VM 'lerin ve bilgisayarların sistem durumunu gösterir. Grafik, sağlıklı olan sanal makinelerin ve bilgisayarların sayısını ve yetersiz koruma sayısını gösterir.
   - Algılanan kötü amaçlı yazılım – güvenlik merkezi 'nin algılanan kötü amaçlı yazılım olduğu VM 'lerin ve bilgisayarların sayısını gösterir.
   - Saldırıya uğrayan bilgisayarlar: Güvenlik Merkezi 'nin kötü amaçlı yazılımlara karşı yaptığı saldırıları bildirdiği VM 'lerin ve bilgisayarların sayısını gösterir.

   Panonun en altında, aşağıdaki bilgileri içeren Endpoint Protection sorunlarının bir listesi bulunur:  

   - **Toplam** -sorundan etkilenen sanal makinelerin ve bilgisayarların sayısı.
   - Sorundan etkilenen VM 'lerin ve bilgisayarların sayısını toplayarak bir çubuk. Çubuktaki renkler önceliği belirler:

      - Kırmızı-Yüksek öncelikli ve hemen değinilmesi gerekir
      - Turuncu-orta öncelikli ve en kısa sürede değinilmesi gerekir

3. **Azure VM 'Lerinde Endpoint Protection yüklü değil '** i seçin.

   ![Azure VM 'lerinde Endpoint Protection yüklü değil ' i seçin][3]

4. **Endpoint Protection ' ın altında, Azure VM 'lerde yüklü değil** , kötü amaçlı yazılımdan koruma yüklü olmayan Azure VM 'lerinin bir listesidir.  Kötü amaçlı yazılımdan koruma 'yı listedeki tüm VM 'Lere yüklemeyi seçebilir veya belirli bir VM 'ye tıklayarak kötü amaçlı yazılımdan koruma yüklemek için tek tek VM 'Leri seçebilirsiniz.
5. **Endpoint Protection 'ı seçin**altında kullanmak istediğiniz Endpoint Protection çözümünü seçin. Bu örnekte, **Microsoft kötü amaçlı yazılımdan koruma**' yı seçin.
6. Uç nokta koruma çözümü hakkında ek bilgiler görüntülenir. **Oluştur**’u seçin.

## <a name="install-antimalware-on-non-azure-computers"></a>Azure olmayan bilgisayarlara kötü amaçlı yazılım yüklemesi

1. **Endpoint Protection sorunlarına** geri dönün ve **Azure dışı bilgisayarlarda Endpoint Protection yüklü değil**' i seçin.

   ![Azure dışı bilgisayarlarda Endpoint Protection yüklü değil ' i seçin][4]

2. **Uç nokta koruma altında, Azure dışı bilgisayarlarda yüklü değil**, bir çalışma alanı seçin. Çalışma alanına Filtrelenen bir Azure Izleyici günlüğü arama sorgusu açılır ve kötü amaçlı yazılım eksik bilgisayarları listeler. Daha fazla bilgi için listeden bir bilgisayar seçin.

   ![Azure Izleyici günlükleri araması][5]

Başka bir arama sonucu yalnızca o bilgisayar için filtrelenmiş bilgiler ile açılır.

  ![Azure Izleyici günlükleri araması][6]

> [!NOTE]
> Virüslerin, casus yazılımların ve diğer kötü amaçlı yazılımların tanımlanmasına ve kaldırılmasına yardımcı olmak üzere tüm VM 'Ler ve bilgisayarlar için Endpoint Protection 'ın sağlanması önerilir.
>
>

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, "Install Endpoint Protection" Güvenlik Merkezi önerisi nasıl uygulanır. Azure 'da Microsoft Antimalware 'in etkinleştirilmesi hakkında daha fazla bilgi edinmek için aşağıdaki belgeye bakın:

* [Cloud Services ve sanal makineler Için Microsoft kötü amaçlı yazılımdan koruma](../security/fundamentals/antimalware.md) --Microsoft Antimalware 'i dağıtmayı öğrenin.

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki belgelere bakın:

* [Azure Güvenlik Merkezi 'nde güvenlik Ilkelerini ayarlama](tutorial-security-policy.md) -güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md) -önerilerin Azure kaynaklarınızı korumanıza nasıl yardımcı olduğunu öğrenin.
* [Azure Güvenlik Merkezi 'Nde güvenlik durumu izleme](security-center-monitoring.md) -Azure kaynaklarınızın sistem durumunu izlemeyi öğrenin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md) - İş ortağı çözümlerinizin sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/) -Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini bulun.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
