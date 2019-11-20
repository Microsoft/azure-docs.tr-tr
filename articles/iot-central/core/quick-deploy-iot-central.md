---
title: Azure IoT Central uygulaması oluşturma | Microsoft Docs
description: Yeni bir Azure IoT Central uygulaması oluşturun. Uygulama şablonu kullanarak bir Deneme veya Kullandıkça Öde uygulaması oluşturun.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 5cb6beca56cac94ca05671cdf34c45913a710c39
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957849"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central uygulaması oluşturma

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

_Oluşturucu_ olarak, Azure IoT Central kullanıcı arabirimini kullanarak Microsoft Azure IoT Central uygulamanızı tanımlayabilirsiniz. Bu hızlı başlangıçta, örnek bir _cihaz şablonu_Içeren bir Azure IoT Central uygulamasının nasıl oluşturulacağı gösterilmektedir. Oluşturduğunuz uygulama herhangi bir önizleme özelliği kullanmaz.

## <a name="create-an-application"></a>Uygulama oluşturma

[Azure IoT Central Build](https://aka.ms/iotcentral) Web sitesine gidin. Sonra bir Microsoft kişisel, iş veya okul hesabıyla oturum açın.

Önizleme özellikleri etkinken Azure IoT Central uygulaması oluşturmaya başlamak için **Oluştur**' u seçin. Bu bağlantı sizi **IoT uygulamanızı derleme** sayfasına götürür.

![Azure IoT Central derleme sayfası](media/quick-deploy-iot-central/iotcentralcreate.png)

Ardından **özel uygulama**' yı seçin.

Yeni bir Azure IoT Central uygulaması oluşturmak için:

1. Azure IoT Central, seçtiğiniz uygulama şablonuna göre otomatik olarak bir uygulama adı önerir. Bu adı kabul edebilir veya **contoso IoT**gibi kendi kolay uygulama adınızı girebilirsiniz. Azure IoT Central, uygulama adına göre sizin için benzersiz bir URL de oluşturur. İsterseniz bu URL önekini daha kolay bir şekilde değiştirebilirsiniz.

1. Önizleme özelliklerini kullanmayan bir uygulama şablonu seçin. Uygulama şablonu, başlamanıza yardımcı olacak cihaz şablonları ve panolar gibi önceden tanımlanmış öğeleri içerebilir.

    | Uygulama şablonu | Açıklama |
    | -------------------- | ----------- |
    | Contoso Örneği       | Bir Soğutmalı Otomat için önceden oluşturulmuş cihaz şablonunu içeren bir uygulama oluşturur. Azure IoT Central'ı incelemeye başlamak için bu şablonu kullanın. |
    | Devkits Örneği       | MXChip veya Raspberry Pi cihazını bağlamak amacıyla sizin için hazırlanmış cihaz şablonlarıyla bir uygulama oluşturur. Bu cihazlardan birini deneyen bir cihaz gelişticisiyseniz bu şablonu kullanın. |
    | Özel uygulama   | Kendi cihaz şablonlarınız ve cihazlarınızla doldurabileceğiniz boş bir uygulama oluşturur. |

1. Ödeme planını seçin:
   - **7 günlük ücretsiz deneme** uygulamaları, süresi dolmadan yedi gün boyunca ücretsizdir. Bu kişiler, süresi dolmadan önce herhangi bir zamanda **Kullandıkça Öde** 'ye dönüştürülebilirler. Bir **deneme** uygulaması oluşturursanız, iletişim bilgilerinizi girmeniz ve Microsoft 'tan bilgi ve ipuçları alıp almamanız gerekir.
   - **Kullandıkça Öde** uygulamaları, ilk beş cihaz ücretsiz olarak cihaz başına ücretlendirilir. **Kullandıkça Öde** uygulaması oluşturursanız, *dizininizi*, *Azure aboneliğinizi*ve *bölgenizi*seçmeniz gerekir:
        - *Dizin* , uygulamanızı oluşturmak için Azure ACTIVE DIRECTORY (ad). Kullanıcı kimliklerini, kimlik bilgilerini ve diğer kuruluş bilgilerini içerir. Azure AD yoksa, bir Azure aboneliği oluşturduğunuzda sizin için bir tane oluşturulur.
        - *Azure Aboneliği*, Azure hizmetlerinin örneklerini oluşturmanıza olanak tanır. IoT Central aboneliğinizdeki kaynakları sağlar. Azure aboneliğiniz yoksa, [Azure kayıt sayfasında](https://aka.ms/createazuresubscription) bir abonelik oluşturabilirsiniz. Azure aboneliğini oluşturduktan sonra, **uygulama oluşturma** sayfasına geri gidin. Yeni aboneliğiniz **Azure Aboneliği** açılan listesinde gösterilir.
        - *Bölge* , uygulamanızı oluşturmak istediğiniz fiziksel konum veya [Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) . Genellikle en iyi performansı elde etmek için cihazlarınıza fiziksel olarak en yakın bölgeyi seçmeniz gerekir. Azure IoT Central 'ın [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) sayfasında kullanılabildiği bölgeleri görebilirsiniz. Bir bölge seçtiğinizde, uygulamanızı daha sonra farklı bir bölgeye taşıyamazsınız.

        [Azure IoT Central fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/iot-central/), fiyatlar hakkında daha fazla bilgi edinin.

1. Daha önce seçtiğiniz ödeme planı için gereken ek bilgileri, 1. adımda girin.

1. Sayfanın alt kısmında **Oluştur** ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir IoT Central uygulaması oluşturdunuz. Önerilen sonraki adım:

> [!div class="nextstepaction"]
> [Azure IoT Central uygulamanızda yeni bir cihaz türü tanımlama](./tutorial-define-device-type.md)
