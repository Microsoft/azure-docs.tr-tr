---
title: CloudSimple-Portal tarafından Azure VMware çözümüne erişin
description: Azure portalından CloudSimple portalıyla VMware Çözümüne nasıl erişileceği açıklanır
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0aa7a9a1f19a9d4fb2c555b08753e0b57c657974
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895164"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Azure portal CloudSimple Portal tarafından kullanılan VMware çözümüne erişin

Çoklu oturum açma, CloudSimple portalına erişim için desteklenir. Azure portal oturum açtıktan sonra CloudSimple portalına yeniden oturum açmadan erişebilirsiniz. CloudSimple portalına ilk kez eriştiğinizde [Cloudsimple hizmet Yetkilendirme](#consent-to-cloudsimple-service-authorization-application) uygulamasını yetkilendirmeniz istenir.  Yetkilendirme, tek seferlik bir işlemdir.

## <a name="before-you-begin"></a>Başlamadan önce

Yerleşik **sahibi** ve **katkıda bulunan** rollerine sahip kullanıcılar cloudsimple portalına erişebilir.  Rollerin, CloudSimple hizmeti 'nin dağıtıldığı kaynak grubunda yapılandırılması gerekir.  Roller, CloudSimple hizmeti nesnesinde de yapılandırılabilir.  Rolünüzü denetleme hakkında daha fazla bilgi için bkz. [rol atamalarını görüntüleme](../role-based-access-control/check-access.md) makalesi. Yalnızca yerleşik **sahibi** ve **katkıda bulunan** rollerine sahip kullanıcılar cloudsimple portalına erişebilir.  Rolün abonelikte yapılandırılması gerekir.  Rolünüzü denetleme hakkında daha fazla bilgi için bkz. [rol atamalarını görüntüleme](../role-based-access-control/check-access.md) makalesi.

Özel roller kullanıyorsanız, rol altında aşağıdaki işlemlerden herhangi birine sahip olmalıdır ```Actions``` .  Özel roller hakkında daha fazla bilgi için bkz. [Azure özel rolleri](../role-based-access-control/custom-roles.md).  İşlemlerden herhangi biri bir parçasıysa ```NotActions``` , Kullanıcı CloudSimple portalına erişemez.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple portalına erişim

1. **Tüm Hizmetler**’i seçin.

2. **Cloudsimple Hizmetleri** için arama yapın.

3. Özel bulutunuzu oluşturmak istediğiniz CloudSimple hizmetini seçin.

4. **Genel bakış** sayfasında **Cloudsimple portalına git ' e** tıklayın.  CloudSimple portalına ilk kez Azure portal erişiyorsanız, [Cloudsimple hizmet Yetkilendirme](#consent-to-cloudsimple-service-authorization-application) uygulamasına yetki vermeniz istenir. 

    ![CloudSimple portalını Başlat](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Doğrudan Azure portal özel bir bulut işlemi seçerseniz (özel bir bulut oluşturma veya genişletme gibi), CloudSimple portalı belirtilen sayfada açılır.

CloudSimple portalında yan menüden **giriş** ' i seçerek özel bulutlarınız hakkında özet bilgiler görüntüleyin. Özel bulutlarınızın kaynakları ve kapasitesi, dikkat gerektiren uyarılarla ve görevlerle birlikte gösterilir. Ortak görevler için sayfanın üst kısmındaki adlandırılmış simgeler ' e tıklayın.

![Giriş sayfası](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>CloudSimple hizmet Yetkilendirme uygulamasına izin verme

CloudSimple portalının ilk kez Azure portal başlatılması, CloudSimple hizmet Yetkilendirme uygulaması için izin vermenizi gerektirir.  İstenen izinleri vermek ve CloudSimple portalına erişmek için **kabul et** ' i seçin.

![CloudSimple hizmet yetkilendirmesi-Yöneticiler için onay](media/cloudsimple-azure-consent.png)

Genel yönetici ayrıcalıklarınız varsa, kuruluşunuza izin verebilirsiniz.  **Kuruluşunuz adına onay verin**’i seçin.

![CloudSimple hizmet yetkilendirmesi onayı-genel yönetici](media/cloudsimple-azure-consent-global-admin.png)

İzinleriniz CloudSimple portalına erişime izin vermezse, gerekli izinleri vermek için kiracınızın genel yöneticisine başvurun.  Genel yönetici, kuruluşunuzun adına izin verebilir.

![CloudSimple hizmet yetkilendirmesi onayı-Yöneticiler gerektirir](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bulut oluşturmayı](./create-private-cloud.md) öğrenin
* [Özel bir bulut ortamını yapılandırmayı](quickstart-create-private-cloud.md) öğrenin
