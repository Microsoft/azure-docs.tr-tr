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
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824107"
---
# <a name="deploying-dependencies-for-local-development"></a>Yerel geliştirme için bağımlılıkları dağıtma

Bu makalede, yalnızca yerel geliştirme ve hata ayıklama işlemleri için gereken Azure platform hizmetlerinin nasıl dağıtılacağı açıklanır.   Sonunda, yerel geliştirme ve hata ayıklama için ihtiyaç duyduğunuz her şeyi içeren bir kaynak grubunuz dağıtılır.

## <a name="deploy-azure-platform-services"></a>Azure platform hizmetleri 'ni dağıtma

1. PowerShell ve [Azurerd PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) uzantılarının yüklü olduğundan emin olun.  Bir komut istemi veya Terminal açın ve şunu çalıştırın:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Dağıtımınız için kaynak grubuna bir ad atamak için istemleri izleyin.  Betik yalnızca Azure aboneliğinizdeki bu kaynak grubuna yönelik bağımlılıkları dağıtır ancak mikro hizmetlere uygulanmaz.  Betik Ayrıca Azure Active Directory bir uygulama kaydeder.  Bu, OAUTH tabanlı kimlik doğrulamasını desteklemek için gereklidir.  Dağıtım birkaç dakika sürebilir.

3. Betik tamamlandıktan sonra. env dosyasını kaydetmeyi seçebilirsiniz.  . Env ortam dosyası, geliştirme makinenizde çalıştırmak istediğiniz tüm hizmetlerin ve araçların yapılandırma dosyasıdır.  

## <a name="troubleshooting-deployment-failures"></a>Dağıtım hatalarıyla ilgili sorunları giderme

### <a name="resource-group-name"></a>Kaynak grubu adı

Kısa ve basit kaynak grubu adı kullandığınızdan emin olun.  Ad, kaynak adlandırma gereksinimleriyle uyumlu olması gereken şekilde kaynakları adlandırmak için de kullanılır.  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) kaydı

Dağıtım betiği, Azure Active Directory AAD uygulamalarını kaydetmeye çalışır.  Seçili AAD kiracısına olan haklara bağlı olarak, bu başarısız olabilir.   Üç seçenek vardır:

1. Kiracılar listesinden bir AAD kiracısı seçerseniz, betiği yeniden başlatın ve listeden farklı bir tane seçin.
2. Alternatif olarak, özel bir AAD kiracısı dağıtın, betiği yeniden başlatın ve kullanmayı seçin.
3. Kimlik doğrulaması olmadan devam edin.  Mikro hizmetlerinizi yerel olarak çalıştırdığınız için bu kabul edilebilir, ancak üretim ortamlarını taklit etmez.  

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC Ikizi hizmetlerini mevcut bir projeye başarıyla dağıttığınıza göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [OPC Ikizi modüllerinin nasıl dağıtılacağı hakkında bilgi edinin](howto-opc-twin-deploy-modules.md)
