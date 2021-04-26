---
title: Azure VMware çözümü için vSphere kimlik bilgilerini sıfırlama
description: Azure VMware çözümünüz özel bulutunuz için vSphere kimlik bilgilerini sıfırlamayı ve HCX bağlayıcısının en son vSphere kimlik bilgilerine sahip olduğundan emin olun.
ms.topic: how-to
ms.date: 03/31/2021
ms.openlocfilehash: 793b79e42a0adbca54804d1b66102736aff22d7a
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109110"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Azure VMware çözümü için vSphere kimlik bilgilerini sıfırlama

Bu makalede, Azure VMware çözümünüz özel bulutunuz için vCenter Server ve NSX-T Yöneticisi kimlik bilgilerini sıfırlamaya yönelik adımları adım adım inceleyeceğiz. Bu, HCX bağlayıcısının en son vCenter Server kimlik bilgilerine sahip olduğundan emin olmanızı sağlar.

Bu nasıl yapılır bilgisine ek olarak, [vCenter CloudAdmin & NSX-T yönetici parolasını sıfırlamaya](https://youtu.be/cK1qY3knj88)yönelik videoyu da görüntüleyebilirsiniz.

## <a name="reset-your-azure-vmware-solution-credentials"></a>Azure VMware Çözüm kimlik bilgilerinizi sıfırlayın

 İlk olarak Azure Vmsizin çözüm bileşenleri kimlik bilgileri sıfırlanalım. VCenter Server CloudAdmin ve NSX-T yönetici kimlik bilgilerinizin süresi dolmaz; Ancak, bu hesaplar için yeni parolalar oluşturmak üzere aşağıdaki adımları izleyebilirsiniz.

> [!NOTE]
> HCX, Vetorchestrator, vRealizae Operations Manager veya VMware ufuk gibi bağlı hizmetler için CloudAdmin kimlik bilgilerinizi kullanırsanız, parolanızı güncelleştirdikten sonra bağlantılarınız çalışmayı durduracaktır.  Parola döndürmeyi başlatmadan önce bu hizmetler durdurulmalıdır.  Bunun başarısız olması, bu hizmetler eski kimlik bilgilerinizi kullanarak sürekli olarak çağrı yapabilmeleri için vCenter CloudAdmin ve NSX-T yönetici hesaplarınızdaki geçici kilitlerin oluşmasına neden olabilir.  Bağlı hizmetler için ayrı hesaplar ayarlama hakkında daha fazla bilgi için bkz. [erişim ve kimlik kavramları](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Azure portal, bir Azure Cloud Shell oturumu açın.

2. VCenter CloudAdmin parolanızı güncelleştirmek için aşağıdaki komutu çalıştırın.  {SubscriptionID}, {ResourceGroup} ve {PrivateCloudName} değerlerini CloudAdmin hesabının ait olduğu özel bulutun gerçek değerleriyle değiştirmeniz gerekir.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. NSX-T yönetici parolanızı güncelleştirmek için aşağıdaki komutu çalıştırın. {SubscriptionID}, {ResourceGroup} ve {PrivateCloudName} değerlerini NSX-T yönetici hesabının ait olduğu özel bulutun gerçek değerleriyle değiştirmeniz gerekir.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>HCX bağlayıcısının en son vCenter Server kimlik bilgilerine sahip olduğundan emin olun

Kimlik bilgilerinizi sıfırladıktan sonra, HCX bağlayıcısının güncelleştirilmiş kimlik bilgilerine sahip olduğundan emin olmak için aşağıdaki adımları izleyin.

1. Parolanız değiştirildikten sonra, https://{HCX bağlayıcı gerecinin IP 'sini kullanarak şirket içi HCX Bağlayıcısı Web arabirimine gidin: 443. 443 numaralı bağlantı noktasını kullandığınızdan emin olun. Yeni kimlik bilgilerinizi kullanarak oturum açın.

2. VMware HCX panosunda **site eşleştirme**' yi seçin.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Site eşleştirmesi vurgulanmış şekilde VMware HCX panosunun ekran görüntüsü.":::
 
3. Azure VMware çözümüne doğru bağlantıyı (birden fazla varsa) seçin ve **Bağlantıyı Düzenle**' yi seçin.
 
4. Yeni vCenter Server CloudAdmin Kullanıcı kimlik bilgilerini sağlayın ve kimlik bilgilerini kaydeden **Düzenle**' yi seçin. Kaydetme işleminin başarılı olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure VMware çözümü için vCenter Server ve NSX-T Yöneticisi kimlik bilgilerini sıfırlamayı artık kapsadığınıza göre, şunları öğrenmek isteyebilirsiniz:

- [Azure VMware çözümünde NSX ağ bileşenlerini yapılandırma](configure-nsx-network-components-azure-portal.md).
- [Azure VMware Çözüm VM 'Lerinin yaşam döngüsü yönetimi](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Azure VMware çözümünü kullanarak sanal makinelerin olağanüstü durum kurtarma dağıtımı](disaster-recovery-for-virtual-machines.md).
