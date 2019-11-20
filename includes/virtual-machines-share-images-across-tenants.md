---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903694"
---
Ancak, Azure kiracınızın dışındaki görüntüleri, ölçeklendirerek paylaşmak istiyorsanız, paylaşmayı kolaylaştırmak için bir uygulama kaydı oluşturmanız gerekir.  Bir uygulama kaydının kullanılması, şunun gibi daha karmaşık paylaşım senaryolarına olanak sağlayabilir: 

* Bir şirket diğeri edindiğinde paylaşılan görüntüleri yönetme ve Azure altyapısı ayrı kiracılar arasında yayılır. 
* Azure Iş ortakları, Azure altyapısını müşterileri adına yönetir. Görüntülerin özelleştirilmesi iş ortakları kiracısı içinde yapılır, ancak altyapı dağıtımları müşterinin kiracısında gerçekleşecektir. 


## <a name="create-the-app-registration"></a>Uygulama kaydını oluşturma

Görüntü Galerisi kaynaklarını paylaşmak için her iki kiracı tarafından kullanılacak bir uygulama kaydı oluşturun.
1. [Azure portal uygulama kayıtları (Önizleme)](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)öğesini açın.    
1. Sayfanın üst kısmındaki menüden **Yeni kayıt** ' ı seçin.
1. **Ad**alanına *myGalleryApp*yazın.
1. **Desteklenen hesap türleri**' nde, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si**içinde *https://www.microsoft.com* yazın ve ardından **Kaydet**' i seçin. Uygulama kaydı oluşturulduktan sonra genel bakış sayfası açılır.
1. Genel Bakış sayfasında, **uygulama (istemci) kimliğini** kopyalayın ve daha sonra kullanmak üzere kaydedin.   
1. **Sertifikalar & sertifikalar**' ı seçin ve ardından **yeni istemci parolası**' nı seçin.
1. **Açıklama**' da *paylaşılan görüntü Galerisi çapraz kiracı uygulama gizli*dizisi yazın.
1. **Süre sonu**' nde, varsayılan değeri **1 yıl olarak** bırakın ve ardından **Ekle**' yi seçin.
1. Gizli dizi değerini kopyalayın ve güvenli bir yere kaydedin. Sayfadan ayrıldıktan sonra bunu alamazsınız.


Paylaşılan görüntü galerisini kullanmak için uygulama kaydı iznini verin.
1. Azure portal, başka bir kiracıyla paylaşmak istediğiniz paylaşılan görüntü Galerisi ' ni seçin.
1. **Erişim denetimi Seç (IAM)** seçeneğini belirleyin ve **rol ataması Ekle** altında *Ekle*' yi seçin. 
1. **Rol**altında **okuyucu**' yı seçin.
1. **Erişime ata:** ' nın altında, bunu **Azure AD kullanıcısı, Grup veya hizmet sorumlusu**olarak bırakın.
1. **Seç**' ın altında *myGalleryApp* yazın ve listede gösterdiği zaman seçin. İşiniz bittiğinde **Kaydet**' i seçin.


## <a name="give-tenant-2-access"></a>Kiracı 2 erişimi verme

Bir tarayıcı kullanarak oturum açmayı isteyerek kiracı 2 ' ye uygulamaya erişim izni verin. *\<TENANT2 ıd >* , görüntü galerinizi paylaşmak istediğiniz KIRACıNıN kiracı kimliğiyle değiştirin. *\<uygulaması (istemci) kimliği >* , oluşturduğunuz uygulama KAYDıNıN uygulama kimliğiyle değiştirin. Değişiklikleri yapmayı tamamladığınızda, URL 'YI bir tarayıcıya yapıştırın ve kiracı 2 ' de oturum açmak için oturum açma istemlerini izleyin.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

[Azure Portal](https://portal.azure.com) kiracı 2 olarak oturum açın ve uygulama kaydını VM oluşturmak istediğiniz kaynak grubuna erişim izni verin.

1. Kaynak grubunu seçin ve ardından **erişim denetimi (IAM)** seçeneğini belirleyin. **Rol ataması Ekle** altında **Ekle**' yi seçin. 
1. **Rol**altında **katkıda bulunan**yazın.
1. **Erişime ata:** ' nın altında, bunu **Azure AD kullanıcısı, Grup veya hizmet sorumlusu**olarak bırakın.
1. Tür *MyGalleryApp* **seçin** altında, listede gösterildiği zaman seçin. İşiniz bittiğinde **Kaydet**' i seçin.

> [!NOTE]
> Farklı bir görüntü sürümü oluşturmak için aynı yönetilen görüntüyü kullanabilmeniz için görüntü sürümünün oluşturulması ve çoğaltılması tamamen bitmesini beklemeniz gerekir.

