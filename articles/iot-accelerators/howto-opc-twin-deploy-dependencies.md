---
title: Azure 'da OPC Ikizi bulut bağımlılıklarını dağıtma | Microsoft Docs
description: Bu makalede, yerel geliştirme ve hata ayıklama yapmak için gereken OPC Ikizi Azure bağımlılıklarının nasıl dağıtılacağı açıklanır.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646480"
---
# <a name="deploying-dependencies-for-local-development"></a>Yerel geliştirme için bağımlılıkları dağıtma

> [!IMPORTANT]
> Bu makaleyi güncelleştirdiğimiz sürece, en güncel içerik için bkz. [Azure endüstriyel IoT](https://azure.github.io/Industrial-IoT/) .

Bu makalede, yalnızca yerel geliştirme ve hata ayıklama işlemleri için gereken Azure platform hizmetlerinin nasıl dağıtılacağı açıklanır.   Sonunda, yerel geliştirme ve hata ayıklama için ihtiyaç duyduğunuz her şeyi içeren bir kaynak grubunuz dağıtılır.

## <a name="deploy-azure-platform-services"></a>Azure platform hizmetleri 'ni dağıtma

1. PowerShell ve [Azurerd PowerShell](/powershell/azure/azurerm/install-azurerm-ps) uzantılarının yüklü olduğundan emin olun.  Bir komut istemi veya Terminal açın ve şunu çalıştırın:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Dağıtımınız için kaynak grubuna bir ad atamak için istemleri izleyin.  Betik yalnızca Azure aboneliğinizdeki bu kaynak grubuna yönelik bağımlılıkları dağıtır ancak mikro hizmetlere uygulanmaz.  Betik Ayrıca Azure AD 'ye bir uygulama kaydeder.  Bu, OAUTH tabanlı kimlik doğrulamasını desteklemek için gereklidir.  Dağıtım birkaç dakika sürebilir.

3. Betik tamamlandıktan sonra. env dosyasını kaydetmeyi seçebilirsiniz.  . Env ortam dosyası, geliştirme makinenizde çalıştırmak istediğiniz tüm hizmetlerin ve araçların yapılandırma dosyasıdır.  

## <a name="troubleshooting-deployment-failures"></a>Dağıtım hatalarıyla ilgili sorunları giderme

### <a name="resource-group-name"></a>Kaynak grubu adı

Kısa ve basit kaynak grubu adı kullandığınızdan emin olun.  Ad, kaynak adlandırma gereksinimleriyle uyumlu olması gereken şekilde kaynakları adlandırmak için de kullanılır.  

### <a name="azure-active-directory-ad-registration"></a>Azure Active Directory (AD) kaydı

Dağıtım betiği Azure AD uygulamalarını Azure AD 'ye kaydetmeye çalışır.  Seçili Azure AD kiracısına olan haklara bağlı olarak, bu başarısız olabilir. Üç seçenek vardır:

1. Kiracılar listesinden bir Azure AD kiracısı seçerseniz, betiği yeniden başlatın ve listeden farklı bir tane seçin.
2. Alternatif olarak, özel bir Azure AD kiracısı dağıtın, betiği yeniden başlatın ve kullanmayı seçin.
3. Kimlik doğrulaması olmadan devam edin.  Mikro hizmetlerinizi yerel olarak çalıştırdığınız için bu kabul edilebilir, ancak üretim ortamlarını taklit etmez.  

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC Ikizi hizmetlerini mevcut bir projeye başarıyla dağıttığınıza göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [OPC Ikizi modüllerinin nasıl dağıtılacağı hakkında bilgi edinin](howto-opc-twin-deploy-modules.md)