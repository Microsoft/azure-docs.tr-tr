---
title: Dış kimliklere self servis kaydolma-Azure AD
description: Self Servis kaydolma etkinleştirerek, dış kullanıcıların uygulamalarınıza kaydolmalarına nasıl izin vereceğinizi öğrenin. Self Servis kaydolma Kullanıcı akışını özelleştirerek kişiselleştirilmiş bir kaydolma deneyimi oluşturun.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13023ef93cabcf46924cc2cc76dc2d868c4a1ddd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653577"
---
# <a name="self-service-sign-up"></a>Self servis kaydolma

Bir uygulamayı dış kullanıcılarla paylaşırken, uygulamaya erişmesi gereken her zaman önceden bilmiyor olabilirsiniz. Doğrudan kişilere davetiye göndermeye alternatif olarak, dış kullanıcıların Self Servis kaydolma 'yı etkinleştirerek belirli uygulamalara kaydolmalarına izin verebilirsiniz. Self Servis kaydolma Kullanıcı akışını özelleştirerek kişiselleştirilmiş bir kaydolma deneyimi oluşturabilirsiniz. Örneğin, Azure AD veya sosyal kimlik sağlayıcılarına kaydolmak ve kaydolma işlemi sırasında Kullanıcı hakkında bilgi toplamak için seçenekler sağlayabilirsiniz.

> [!NOTE]
> Kullanıcı akışlarını, kuruluşunuz tarafından oluşturulan uygulamalarla ilişkilendirebilirsiniz. Kullanıcı akışları, SharePoint veya takımlar gibi Microsoft uygulamaları için kullanılamaz.

## <a name="user-flow-for-self-service-sign-up"></a>Self Servis kaydolma için Kullanıcı akışı

Self Servis kaydolma Kullanıcı akışı, dış kullanıcılarınız için paylaşmak istediğiniz uygulama aracılığıyla bir kaydolma deneyimi oluşturur. Kullanıcı akışı, bir veya daha fazla uygulamalarınızdan ilişkilendirilebilir. İlk olarak, kiracınız için self servis kaydolma özelliğini etkinleştireceksiniz ve dış kullanıcıların oturum açma için kullanmasına izin vermek istediğiniz kimlik sağlayıcılarıyla federasyona sahip olacaksınız. Daha sonra kaydolma Kullanıcı akışını oluşturup özelleştirecek ve uygulamalarınızı buna atamanız gerekir.
Kullanıcı akış ayarlarını, kullanıcının uygulama için nasıl kaydolduğunda denetlemek için yapılandırabilirsiniz:

- Facebook gibi sosyal hesaplar veya Azure AD hesapları gibi oturum açma için kullanılan hesap türleri
- Kullanıcı kaydolmadan toplanacak öznitelikler, örneğin ilk adı, posta kodu veya bir yerden ülke/bölge

Bir Kullanıcı uygulamanızda oturum açmak istediğinde bir Web, mobil, masaüstü veya tek sayfalı uygulama (SPA) olduğunda, uygulama Kullanıcı akışı tarafından belirtilen uç noktaya bir yetkilendirme isteği başlatır. Kullanıcı akışı kullanıcının deneyimini tanımlar ve denetler. Kullanıcı kaydolma Kullanıcı akışını tamamladığında, Azure AD bir belirteç oluşturur ve kullanıcıyı uygulamanıza yeniden yönlendirir. Kayıt tamamlandıktan sonra, dizinde Kullanıcı için bir Konuk hesabı temin edilir. Birden çok uygulama aynı kullanıcı akışını kullanabilir.

## <a name="example-of-self-service-sign-up"></a>Self Servis kaydolma örneği

Aşağıdaki örnek, sosyal kimlik sağlayıcılarını Konuk kullanıcılar için self servis kaydolma özellikleri ile Azure AD 'ye nasıl sunduğumuz gösterilmektedir.  
Woodgrove, Woodgrove uygulamasını açan bir iş ortağı. Bunlar bir Tedarikçi hesabına kaydolmalarına karar verirse, bu nedenle self servis kaydolma akışını başlatan Tedarikçi hesabınızı ıste ' yi seçer.

![Self Servis kaydolma başlangıç sayfası örneği](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Kaydolmak için seçtikleri e-postaları kullanırlar.

![Oturum açma için Facebook seçimini gösteren örnek](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD, Woodgrove iş ortağının Facebook hesabını kullanarak bir ilişki oluşturur ve kaydolduktan sonra Kullanıcı için yeni bir Konuk hesabı oluşturur.

Woodgrove Kullanıcı hakkında ad, iş adı, iş kaydı kodu, telefon numarası gibi daha fazla bilgi edinmek istiyor.

![Kullanıcı kaydolma özniteliklerini gösteren örnek](media/self-service-sign-up-overview/example-enter-user-attributes.png)

Kullanıcı bilgileri girer, kaydolma akışına devam eder ve ihtiyaç duydukları kaynaklara erişim sağlar.

![Oturum açan kullanıcıyı gösteren örnek](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Sonraki adımlar

 Ayrıntılar için bkz. [bir uygulamaya self servis kaydolma ekleme](self-service-sign-up-user-flow.md).