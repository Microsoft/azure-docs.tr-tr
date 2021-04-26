---
title: Konak kotasını isteyin ve Azure VMware çözümünü etkinleştirin
description: Konak kotası/kapasitesi isteme ve Azure VMware Çözüm kaynak sağlayıcısını etkinleştirme hakkında bilgi edinin. Ayrıca, var olan bir Azure VMware çözümü özel bulutunda daha fazla ana bilgisayar isteyebilirsiniz.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653180"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Konak kotasını isteyin ve Azure VMware çözümünü etkinleştirin

Bu nasıl yapılır, konak kotası/kapasitesi isteme ve hizmeti etkinleştiren [Azure VMware Çözüm](introduction.md) kaynak sağlayıcısını etkinleştirme hakkında bilgi edineceksiniz. Azure VMware çözümünü etkinleştirebilmeniz için önce, konaklarınızın ayrılmış olması için bir destek bileti göndermeniz gerekir. Mevcut bir Azure VMware çözümü özel bulutunuz varsa ve daha fazla ana bilgisayar istiyorsanız, aynı süreci takip edersiniz.

>[!IMPORTANT]
>Ana bilgisayarların istenen sayıya bağlı olarak ayrılması birkaç gün sürebilir.  Bu nedenle, sağlama için gerekenleri isteyin, böylece bir kota artışı genellikle istemeniz gerekmez.


Genel işlem basittir ve iki adımdan oluşur:
- [EA müşterileri](#request-host-quota-for-ea-customers) veya [CSP müşterileri](#request-host-quota-for-csp-customers) için ek konak kotası/kapasitesi isteyin 
- Microsoft. AVS kaynak sağlayıcısını etkinleştirin

## <a name="eligibility-criteria"></a>Uygunluk ölçütleri

Azure aboneliğinde bir Azure hesabınızın olması gerekir. Azure aboneliğinin aşağıdaki ölçütlerden biriyle izlenmesi gerekir:

- Microsoft ile [Azure Kurumsal Anlaşma (EA)](../cost-management-billing/manage/ea-portal-agreements.md) kapsamındaki bir abonelik.
- Mevcut CSP Azure 'da bir bulut çözümü sağlayıcısı (CSP) tarafından yönetilen abonelik veya bir Azure planı.

## <a name="request-host-quota-for-ea-customers"></a>EA müşterileri için konak kotası iste

1. Azure portal, **Yardım + Destek** altında, **[Yeni bir destek isteği](https://rc.portal.azure.com/#create/Microsoft.Support)** oluşturun ve bilet için aşağıdaki bilgileri sağlayın:
   - **Sorun türü:** Teknik
   - **Abonelik:** Aboneliğinizi seçin
   - **Hizmet:** Tüm hizmetler Azure VMware çözümüne >
   - **Kaynak:** Genel soru 
   - **Özet:** Kapasiteye ihtiyaç duyuyor
   - **Sorun türü:** Kapasite yönetimi sorunları
   - **Sorun alt türü:** Ek konak kotası/kapasitesi için müşteri Isteği

1. Destek bileti **açıklamasında** , **Ayrıntılar** sekmesinde şunları belirtin:

   - POC veya üretim 
   - Bölge Adı
   - Ana bilgisayar sayısı
   - Diğer ayrıntılar

   >[!NOTE]
   >Azure VMware çözümü, özel bulutunuzu ve artıklığı N + 1 ana bilgisayarlarını çalıştırmak için en az üç ana bilgisayar önerir. 

1. İsteği göndermek için **gözden geçir + oluştur** ' u seçin.


## <a name="request-host-quota-for-csp-customers"></a>CSP müşterileri için konak kotası iste 

CSP 'Ler, müşterileri için Azure VMware çözümünü etkinleştirmek üzere [Microsoft Iş ortağı merkezini](https://partner.microsoft.com) kullanmalıdır. Bu makalede, iş ortakları için satın alma yordamını göstermek üzere bir örnek olarak [CSP Azure planı](/partner-center/azure-plan-lp) kullanılmaktadır.

Iş ortağı merkezinden **yönetici adına** (Aobo) yordamını kullanarak Azure Portal erişin.

>[!IMPORTANT] 
>Azure VMware çözüm hizmeti bir çoklu kiracı gerekli değil. Bunu gerektiren barındırma iş ortakları desteklenmez. 

1. CSP Azure planını yapılandırın:

   1. **Iş Ortağı Merkezi**'Nde, **müşteriler** alanına erişmek için **CSP** 'yi seçin.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft Iş ortağı merkezi müşterileri alanı" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Müşteriyi seçin ve ardından **Ürün Ekle**' yi seçin.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft İş Ortağı Merkezi" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. **Azure planı** ' nı seçin ve ardından **Sepete Ekle**' yi seçin. 
   
   1. Müşterinizin Azure planı aboneliğinin genel kurulumunu gözden geçirin ve son yapın. Daha fazla bilgi için bkz. [Microsoft Iş Ortağı Merkezi belgeleri](/partner-center/azure-plan-manage).

1. Azure planını yapılandırdıktan ve abonelik için gereken [Azure RBAC izinlerine](/partner-center/azure-plan-manage) sahip olduğunuzda, Azure plan aboneliğiniz için kota isteyebilirsiniz. 

   1. **Şirket adına yönetici** (Aobo) yordamını kullanarak [Microsoft iş ortağı merkezi](https://partner.microsoft.com) 'nden Azure Portal erişin.
   
   1. **Müşteriler** alanına erişmek için **CSP** 'yi seçin.
   
   1. Müşteri ayrıntıları ' nı genişletin ve **Microsoft Azure yönetim portalı**' yi seçin.
   
   1. Azure portal, **Yardım + Destek** altında, **[Yeni bir destek isteği](https://rc.portal.azure.com/#create/Microsoft.Support)** oluşturun ve bilet için aşağıdaki bilgileri sağlayın:
      - **Sorun türü:** Teknik
      - **Abonelik:** Aboneliğinizi seçin
      - **Hizmet:** Tüm hizmetler Azure VMware çözümüne >
      - **Kaynak:** Genel soru 
      - **Özet:** Kapasiteye ihtiyaç duyuyor
      - **Sorun türü:** Kapasite yönetimi sorunları
      - **Sorun alt türü:** Ek konak kotası/kapasitesi için müşteri Isteği
   
   1. Destek bileti **açıklamasında** , **Ayrıntılar** sekmesinde şunları belirtin:
   
      - POC veya üretim 
      - Bölge Adı
      - Ana bilgisayar sayısı
      - Diğer ayrıntılar
      - Birden çok müşteriyi barındırmak için tasarlanmıştır mı?
   
      >[!NOTE]
      >Azure VMware çözümü, özel bulutunuzu ve artıklığı N + 1 ana bilgisayarlarını çalıştırmak için en az üç ana bilgisayar önerir. 
   
   1. İsteği göndermek için **gözden geçir + oluştur** ' u seçin.

## <a name="register-the-microsoftavs-resource-provider"></a>**Microsoft. AVS** kaynak sağlayıcısını kaydetme

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Sonraki adımlar

Kaynağı etkinleştirdikten ve uygun ağ yerine, [özel bir bulut oluşturabilirsiniz](tutorial-create-private-cloud.md).
