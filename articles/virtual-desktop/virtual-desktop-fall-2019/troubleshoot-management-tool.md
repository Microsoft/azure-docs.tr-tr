---
title: Windows sanal masaüstü (klasik) yönetim aracı-Azure
description: Windows sanal masaüstü (klasik) yönetim aracı ile ilgili sorunları giderme.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 430ab72a3eb0cd41d707ff0eb5d10d8b97162d53
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444250"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>Windows sanal masaüstü (klasik) yönetim aracında sorun giderme

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir.

Bu makalede, Windows sanal masaüstü yönetim aracı dağıtıldığında oluşabilecek sorunlar ve bunları nasıl gidereceğiniz anlatılmaktadır.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Hata: yönetim aracı hizmetleri yapılandırıldı ancak otomatik kurulum başarısız oluyor

Yönetim aracı için Hizmetleri başarıyla ayarlarken, ancak otomatik kurulum başarısız olursa şu hata iletisini görürsünüz:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Bu genellikle aşağıdaki iki şeyi gösterir:

- Kullanıcının abonelikleri ve genel Yöneticisi kiracı düzeyinde sahip izinleri vardır, ancak Azure 'da oturum açamaz.
- Kullanıcının hesap ayarlarında Multi-Factor Authentication etkin.

Bu hatayı onarmak için:

1. Azure Active Directory Kullanıcı asıl adı için oluşturduğunuz kullanıcının "katkıda bulunan" abonelik düzeyine sahip olduğundan emin olun.
2. Hesap ayarlarını denetlemek ve Multi-Factor Authentication 'ın açık olmadığından emin olmak için UPN hesabıyla <portal.azure.com> oturum açın. Açık ise kapatın.
3. Windows sanal masaüstü onay sayfasını ziyaret edin ve sunucu ve istemci uygulamalarının izin olduğundan emin olun.
4. Sorun devam ederse, [Yönetim aracını dağıtma](manage-resources-using-ui.md) öğreticisini gözden geçirin ve aracı yeniden dağıtın.

## <a name="error-job-with-specified-id-already-exists"></a>Hata: belirtilen KIMLIĞE sahip Iş zaten var

Kullanıcılarınız "belirtilen KIMLIĞE sahip Iş zaten var" hata iletisini görürse, şablon dağıtıldığında "uygulama adı" parametresinde benzersiz bir ad sağlamadıkları için.

Bunu onarmak için, yönetim aracını "uygulama adı" parametresi doldurulmuş olarak yeniden dağıtın.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Yönetim Aracı açılırken gecikmeli onay istemi

Yönetim aracını dağıtırken, onay istemi hemen açılmayabilir. Bu nedenle, Azure Web App Service 'in yüklenmesi normalden uzun sürüyor. İstem, Azure Web 'den yükleme yapıldıktan sonra görünmelidir.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Kullanıcı Doğu ABD bölgesinde yönetim aracını dağıtabmaz

Bir müşteri, bölgeyi Doğu ABD olarak ayarlarsa, yönetim aracını dağıtabamazlar.

Bu hatayı onarmak için yönetim aracını farklı bir bölgede dağıtın. Aracı farklı bir bölgede yeniden dağıtmak Kullanıcı deneyimini etkilemeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

- [Sorun gidermeye genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview-2019.md)konularında bilgi edinin.
- [Uzak Masaüstü Hizmetleri Için ARM şablonlarındaki](https://github.com/Azure/RDS-Templates/blob/master/README.md)Windows Sanal Masaüstü araçlarıyla ilgili sorunları nasıl bildirebileceğinizi öğrenin.
- Yönetim aracını dağıtmayı öğrenmek için bkz. [Yönetim Aracı dağıtma](manage-resources-using-ui.md).