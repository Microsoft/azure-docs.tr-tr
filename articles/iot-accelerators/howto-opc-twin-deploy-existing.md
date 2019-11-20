---
title: Bir OPC Ikizi modülünü var olan bir Azure projesine dağıtma | Microsoft Docs
description: Bu makalede, OPC Ikizi 'in mevcut bir projeye nasıl dağıtılacağı açıklanır. Dağıtım hatalarıyla ilgili sorunları nasıl giderebileceğinizi de öğrenebilirsiniz.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824113"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>OPC Ikizi 'yi mevcut bir projeye dağıtma

OPC Ikizi modülü IoT Edge üzerinde çalışır ve OPC Ikizi ve kayıt defteri Hizmetleri için birkaç Edge hizmeti sağlar.

OPC Ikizi mikro hizmeti, fabrika ve OPC UA sunucu cihazları arasındaki iletişimi OPC Ikizi IoT Edge modülü aracılığıyla kolaylaştırır. Mikro hizmet, REST API aracılığıyla OPC UA hizmetlerini (tarama, okuma, yazma ve yürütme) kullanıma sunar. 

OPC UA cihaz kayıt defteri mikro hizmeti, kayıtlı OPC UA uygulamalarına ve bunların uç noktalarına erişim sağlar. İşleçler ve Yöneticiler yeni OPC UA uygulamalarını kaydedebilir ve kaydını silip bunların uç noktaları da dahil olmak üzere mevcut olanlara gözatabilir. Uygulama ve uç nokta yönetimine ek olarak, kayıt defteri hizmeti de kayıtlı OPC Ikizi IoT Edge modüllerini kataloglandırır. Hizmet API 'SI, sınır modülü işlevlerini (örneğin, sunucu bulmayı (tarama hizmetleri) başlatma veya durdurma ya da OPC Ikizi mikro hizmeti kullanılarak erişilebilen yeni uç nokta TWINS 'i etkinleştirerek denetlemenizi sağlar.

Modülün çekirdeği gözetmen kimliğidir. Gözetmen, ilgili OPC UA kayıt defteri API 'SI kullanılarak etkinleştirilen OPC UA sunucu uç noktalarına karşılık gelen Endpoint ikizi 'ı yönetir. Bu uç nokta TWINS, bulutta çalışan OPC Ikizi mikro hizmetinden, yönetilen uç noktaya durum bilgisi olan güvenli bir kanaldan gönderilen OPC UA ikili iletilerine alınan OPC UA JSON 'u çevirir. Gözetmen Ayrıca, işlem için OPC UA cihaz ekleme hizmeti 'ne cihaz bulma olayları gönderen bulma hizmetleri sağlar ve bu olaylar OPC UA kayıt defterine güncelleştirmeler ile sonuçlanır.  Bu makalede, OPC Ikizi modülünü var olan bir projeye nasıl dağıtabileceğiniz gösterilmektedir.

> [!NOTE]
> Dağıtım ayrıntıları ve yönergeleri hakkında daha fazla bilgi için bkz. GitHub [deposu](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Ön koşullar

PowerShell ve [Azurerd PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) uzantılarının yüklü olduğundan emin olun. Daha önce yapmadıysanız, bu GitHub deposunu kopyalayın. PowerShell 'de aşağıdaki komutları çalıştırın:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Endüstriyel IoT hizmetlerini Azure 'a dağıtma

1. PowerShell oturumunuzda şunu çalıştırın:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Dağıtım kaynak grubuna bir ad ve Web sitesine bir ad atamak için istemleri izleyin.   Betik, mikro hizmetleri ve bunların Azure platformu bağımlılıklarını Azure aboneliğinizdeki kaynak grubuna dağıtır.  Betik Ayrıca, OAUTH tabanlı kimlik doğrulamasını desteklemek üzere Azure Active Directory (AAD) kiracınıza bir uygulama kaydeder.  Dağıtım birkaç dakika sürer.  Çözüm başarıyla dağıtıldıktan sonra neleri görebileceğinize bir örnek:

   ![Endüstriyel IoT OPC Ikizi mevcut projeye dağıt](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Çıktı, genel uç noktanın URL 'sini içerir. 

3. Betik başarıyla tamamlandıktan sonra `.env` dosyasını kaydetmek isteyip istemediğinizi seçin.  Bulut uç noktasına konsol veya geliştirme ve hata ayıklama için modülleri dağıtma gibi araçları kullanarak bağlanmak istiyorsanız `.env` ortam dosyasına ihtiyacınız vardır.

## <a name="troubleshooting-deployment-failures"></a>Dağıtım hatalarıyla ilgili sorunları giderme

### <a name="resource-group-name"></a>Kaynak grubu adı

Kısa ve basit kaynak grubu adı kullandığınızdan emin olun.  Ad, kaynak adlandırma gereksinimleriyle uyumlu olması gereken şekilde kaynakları adlandırmak için de kullanılır.  

### <a name="website-name-already-in-use"></a>Web sitesi adı zaten kullanımda

Web sitesinin adı zaten kullanımda olabilir.  Bu hatayla karşılaşırsanız, farklı bir uygulama adı kullanmanız gerekir.

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) kaydı

Dağıtım betiği, Azure Active Directory iki AAD uygulamasını kaydetmeye çalışır.  Seçili AAD kiracısına olan haklara bağlı olarak, dağıtım başarısız olabilir. İki seçenek vardır:

1. Kiracılar listesinden bir AAD kiracısı seçerseniz, betiği yeniden başlatın ve listeden farklı bir tane seçin.
2. Alternatif olarak, özel bir AAD kiracısını başka bir abonelikte dağıtın, betiği yeniden başlatın ve kullanmayı seçin.

> [!WARNING]
> Kimlik doğrulaması olmadan HIÇBIR şekilde devam edin.  Bunu tercih ederseniz, herkes Internet 'ten kimliği doğrulanmamış olan OPC Ikizi uç noktalarınıza erişebilir.   Her zaman ["yerel" dağıtım seçeneğini](howto-opc-twin-deploy-dependencies.md) seçerek Tires 'yi başlatabilirsiniz.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Hepsi bir adet endüstriyel IoT Hizmetleri tanıtımı dağıtma

Yalnızca hizmet ve bağımlılıklar yerine, hepsi bir arada tanıtım de dağıtabilirsiniz.  Tek bir demo üç OPC UA sunucusu, OPC Ikizi modülünü, tüm mikro hizmetleri ve örnek bir Web uygulamasını içerir.  Tanıtım amacıyla tasarlanmıştır.

1. Deponun bir kopyasına sahip olduğunuzdan emin olun (yukarıya bakın). Deponun kökünde bir PowerShell istemi açın ve şunu çalıştırın:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Kaynak grubuna ve bir ada Web sitesine yeni bir ad atamak için istemleri izleyin.  Başarılı bir şekilde dağıtıldıktan sonra, betik web uygulaması uç noktasının URL 'sini görüntüler.

## <a name="deployment-script-options"></a>Dağıtım betiği seçenekleri

Betik aşağıdaki parametreleri alır:

```powershell
-type
```

Dağıtım türü (VM, yerel, tanıtım)

```powershell
-resourceGroupName
```

Var olan veya yeni bir kaynak grubunun adı olabilir.

```powershell
-subscriptionId
```

İsteğe bağlı, kaynakların dağıtılacağı abonelik KIMLIĞI.

```powershell
-subscriptionName
```

Ya da abonelik adı.

```powershell
-resourceGroupLocation
```

İsteğe bağlı, bir kaynak grubu konumu. Belirtilmişse, bu konumda yeni bir kaynak grubu oluşturmaya çalışır.

```powershell
-aadApplicationName
```

Kayıt yapılacak AAD uygulaması için bir ad.

```powershell
-tenantId
```

Kullanılacak AAD kiracısı.

```powershell
-credentials
```

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC Ikizi 'i mevcut bir projeye dağıtmayı öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [OPC UA Client ve OPC UA PLC ile güvenli iletişim](howto-opc-vault-secure.md)
