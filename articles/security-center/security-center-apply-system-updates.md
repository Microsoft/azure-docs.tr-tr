---
title: Azure Güvenlik Merkezi 'nde sistem güncelleştirmelerini uygulama | Microsoft Docs
description: Bu belgede, Azure Güvenlik Merkezi önerilerini **uygulama** ve **sistem güncelleştirmelerinden sonra yeniden başlatma**işlemlerinin nasıl uygulanacağı gösterilmektedir.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 1688e85c6e6ed57892ccdffdf0813c8628127cc5
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202456"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde sistem güncelleştirmelerini uygulama
Azure Güvenlik Merkezi, eksik işletim sistemi güncelleştirmeleri için günlük Windows ve Linux sanal makinelerini (VM 'Ler) ve bilgisayarları izler. Güvenlik Merkezi, bir Windows bilgisayarda hangi hizmetin yapılandırıldığına bağlı olarak Windows Update veya Windows Server Update Services (WSUS) ' dan kullanılabilen güvenlik ve kritik güncelleştirmeler listesini alır. Güvenlik Merkezi, Linux sistemlerinde en son güncelleştirmeleri de denetler. VM 'niz veya bilgisayarınızda bir sistem güncelleştirmesi eksikse, Güvenlik Merkezi, sistem güncelleştirmelerini uygulamanızı önerir.

## <a name="implement-the-recommendation"></a>Öneriyi uygulayın
Sistem güncelleştirmelerini Uygula, güvenlik merkezi 'nde bir öneri olarak sunulur. VM 'niz veya bilgisayarınızda bir sistem güncelleştirmesi eksikse, bu öneri **öneriler** altında ve **işlem**altında görüntülenir.  Öneriyi seçmek, **sistem güncelleştirmelerini Uygula** panosunu açar.

Bu örnekte, **işlem**kullanacağız.

1. Güvenlik Merkezi ana menüsünde **işlem** ' ı seçin.

   ![Işlem seçin][1]

2. **İşlem**altında **eksik sistem güncelleştirmeleri**' ni seçin. **Sistem güncelleştirmelerini Uygula** panosu açılır.

   ![Sistem güncelleştirmeleri panosunu Uygula][2]

   Panonun üst kısmında şunları sağlar:

    - Windows ve Linux VM 'lerinin ve sistem güncelleştirmelerinin eksik olduğu bilgisayarların toplam sayısı.
    - VM 'lerde ve bilgisayarlarınızda eksik olan kritik güncelleştirmelerin toplam sayısı.
    - VM 'lerde ve bilgisayarlarınızda eksik olan güvenlik güncelleştirmelerinin toplam sayısı.

   Panonun en altında, VM 'Lerde ve bilgisayarlarınızda eksik olan tüm güncelleştirmeler ve eksik güncelleştirmenin önem derecesi listelenir.  Liste aşağıdakileri içerir:

    - ADA Eksik güncelleştirmenin adı.
    - EŞLEŞEN. VM 'Ler & BILGISAYARLAR: Bu güncelleştirme eksik olan sanal makinelerin ve bilgisayarların toplam sayısı.
    - DURUMUNDA Önerinin geçerli durumu:

      - Açın Öneri henüz sağlanmadı.
      - Devam ediyor: Öneri şu anda bu kaynaklara uygulanıyor ve sizin için herhangi bir eylem gerekmiyor.
      - Çözülüyor Öneri zaten tamamlandı. (Sorun çözüldüğünde girdi soluklaşır).

    - ININ Bu belirli bir önerinin önem derecesini açıklar:

      - Geniş Anlamlı bir kaynak (uygulama, sanal makine veya ağ güvenlik grubu) ile bir güvenlik açığı bulunur ve dikkat edilmesi gerekir.
      - Uzunlukta Bir işlemi gerçekleştirmek veya bir güvenlik açığını ortadan kaldırmak için kritik olmayan veya ek adımlar gerekir.
      - Zayıf Bir güvenlik açığına değinilmesi gerekir, ancak hemen ilgilenilmesi gerekmez. (Varsayılan olarak, düşük öneriler sunulmaz ancak bunları görüntülemek istiyorsanız düşük öneriler filtresini kullanabilirsiniz.)

3. Ayrıntıları görüntülemek için listeden eksik bir güncelleştirme seçin.

   ![Eksik güvenlik güncelleştirmesi][3]

4. Üst şeritte **arama** simgesini seçin.  Azure Izleyici günlükleri arama sorgusu, güncelleştirmenin eksik olduğu bilgisayarlara filtrelenmiş olarak açılır.

   ![Azure Izleyici günlükleri araması][4]

5. Daha fazla bilgi için listeden bir bilgisayar seçin. Başka bir arama sonucu yalnızca o bilgisayar için filtrelenmiş bilgiler ile açılır.

    ![Azure Izleyici günlükleri araması][5]

## <a name="next-steps"></a>Sonraki adımlar
Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md) -önerilerin Azure kaynaklarınızı korumanıza nasıl yardımcı olduğunu öğrenin.
* [Azure Güvenlik Merkezi 'Nde güvenlik durumu izleme](security-center-monitoring.md) -Azure kaynaklarınızın sistem durumunu izlemeyi öğrenin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md) - İş ortağı çözümlerinizin sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/) -Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini bulun.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
