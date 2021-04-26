---
title: CSP portalından Azure IoT Central uygulamaları oluşturma ve yönetme | Microsoft Docs
description: Bir CSP olarak, müşteriniz adına bir Azure IoT Central uygulaması oluşturma.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: f3293ada549351cc7273847cde48c0531f06f028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675816"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>CSP portalından Azure IoT Central uygulaması oluşturma ve yönetme

Microsoft Bulut çözümü sağlayıcısı (CSP) programı bir Microsoft satıcı programıdır. Amaç, tüm Microsoft ticari çevrimiçi hizmetlerini yeniden satmak için tek seferlik bir programla kanal iş ortaklarımızı sağlamaktır. [Bulut çözümü sağlayıcısı programı](https://partner.microsoft.com/cloud-solution-provider)hakkında daha fazla bilgi edinin.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Bir CSP olarak, [Microsoft Iş Ortağı Merkezi](https://partnercenter.microsoft.com/partner/home)aracılığıyla müşterileriniz adına Microsoft Azure IoT Central uygulamaları oluşturabilir ve yönetebilirsiniz. Azure IoT Central uygulamaları, diğer CSP ile yönetilen Azure hizmetlerinde olduğu gibi CSP 'Ler tarafından müşteriler adına oluşturulduğunda, CSP 'Ler müşteriler için faturalandırmayı yönetir. Azure IoT Central ücreti, Microsoft Iş Ortağı Merkezi 'nde toplam faturanızda görünür.

Başlamak için Microsoft Iş ortağı portalı 'nda hesabınızda oturum açın ve Azure IoT Central uygulaması oluşturmak istediğiniz müşteriyi seçin. Sol gezinti çubuğunda müşteri için hizmet yönetimi ' ne gidin.

![Microsoft Iş Ortağı Merkezi, müşteri görünümü](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central, yönetmek için kullanılabilen bir hizmet olarak listelenir. Yeni uygulamalar oluşturmak veya bu müşteri için mevcut uygulamaları yönetmek üzere sayfadaki Azure IoT Central bağlantısını seçin.

![Azure IoT Central yönetilecek kullanılabilir](media/howto-create-and-manage-applications-csp/image2.png)

Azure IoT Central uygulama Yöneticisi sayfasını kullanabilirsiniz. Azure IoT Central, Microsoft Iş Ortağı Merkezi 'nden gelen ve söz konusu müşteriyi yönetmek için aldığınız bağlamı korur. Bu bildirim, uygulama Yöneticisi sayfasının üstbilgisinde görüntülenir. Buradan, bu müşteri için daha önce oluşturduğunuz mevcut bir uygulamaya gidebilir veya müşteri için yeni bir uygulama oluşturabilirsiniz.

![CSP 'Ler için yönetici oluşturma](media/howto-create-and-manage-applications-csp/image3.png)

Azure IoT Central uygulaması oluşturmak için soldaki menüden **Oluştur** ' u seçin. Sektör şablonlarından birini seçin veya sıfırdan bir uygulama oluşturmak için **özel uygulama** ' yı seçin. Bu işlem, uygulama oluşturma sayfasını yükler. Bu sayfadaki tüm alanları tamamlayıp **Oluştur**' u seçmeniz gerekir. Aşağıdaki alanların her biri hakkında daha fazla bilgi bulabilirsiniz.

!["Derleme" düğmesinin seçili olduğu "IoT uygulamanızı derleme" sayfasını gösteren ekran görüntüsü.](media/howto-create-and-manage-applications-csp/image4.png)

![CSP 'Ler için uygulama sayfası oluştur](media/howto-create-and-manage-applications-csp/image4-1.png)

![CSP faturalandırma bilgileri için uygulama sayfası oluşturma](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Fiyatlandırma planı

Yalnızca bir CSP olarak standart fiyatlandırma planı kullanan uygulamalar oluşturabilirsiniz. Müşterinizin Azure IoT Central göstermek için, ücretsiz fiyatlandırma planını ayrı olarak kullanan bir uygulama oluşturabilirsiniz. [Azure IoT Central fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/iot-central/)ücretsiz ve standart fiyatlandırma planları hakkında daha fazla bilgi edinin.

Yalnızca bir CSP olarak standart fiyatlandırma planı kullanan uygulamalar oluşturabilirsiniz. Müşterinizin Azure IoT Central göstermek için, ücretsiz fiyatlandırma planını ayrı olarak kullanan bir uygulama oluşturabilirsiniz. [Azure IoT Central fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/iot-central/)ücretsiz ve standart fiyatlandırma planları hakkında daha fazla bilgi edinin.

## <a name="application-name"></a>Uygulama adı

Uygulamanızın adı, **Uygulama Yöneticisi** sayfasında ve her bir Azure IoT Central uygulaması içinde görüntülenir. Azure IoT Central uygulamanız için herhangi bir ad seçebilirsiniz. Sizin ve kuruluşunuzdaki diğer diğerlerine anlamlı bir ad seçin.

## <a name="application-url"></a>Uygulama URL'si

Uygulama URL 'SI, uygulamanızın bağlantıdır. Bir yer işaretini tarayıcınızda kaydedebilir veya başkalarıyla paylaşabilirsiniz.

Uygulamanızın adını girdiğinizde, uygulama URL 'niz otomatik olarak oluşturulur. İsterseniz, uygulamanız için farklı bir URL seçebilirsiniz. Her Azure IoT Central URL 'SI Azure IoT Central içinde benzersiz olmalıdır. Seçtiğiniz URL zaten alınmışsa bir hata iletisi görürsünüz.

## <a name="directory"></a>Dizin

Azure IoT Central, Microsoft Iş ortağı portalında seçtiğiniz müşteriyi yönetmek için aldığınız bağlamdan, Dizin alanında o müşteri için yalnızca Azure Active Directory kiracısını görürsünüz. 

Azure Active Directory Kiracı Kullanıcı kimliklerini, kimlik bilgilerini ve diğer kuruluş bilgilerini içerir. Birden çok Azure aboneliği, tek bir Azure Active Directory kiracısıyla ilişkilendirilebilir.

Daha fazla bilgi için bkz. [Azure Active Directory](../../active-directory/index.yml).

## <a name="azure-subscription"></a>Azure aboneliği

Azure aboneliği, Azure hizmetlerinin örneklerini oluşturmanıza olanak sağlar. Azure IoT Central, erişiminiz olan müşterinin tüm Azure aboneliklerini otomatik olarak bulur ve bunları **uygulama oluştur** sayfasında bir açılan pencerede görüntüler. Yeni bir Azure IoT Central uygulaması oluşturmak için bir Azure aboneliği seçin.

Azure aboneliğiniz yoksa, Microsoft Iş Ortağı Merkezi ' nde bir tane oluşturabilirsiniz. Azure aboneliğini oluşturduktan sonra, **Uygulama Oluştur** sayfasına geri gidin. Yeni aboneliğiniz **Azure Aboneliği** açılan listesinde gösterilir.

Daha fazla bilgi için bkz. [Azure abonelikleri](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Konum

**Konum** , uygulamayı oluşturmak Istediğiniz [Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) konumudur. Genellikle en iyi performansı elde etmek için cihazlarınıza fiziksel olarak en yakın konumu seçmeniz gerekir. Şu anda **Avustralya**, **Asya Pasifik**, **Avrupa**, **Birleşik Devletler**, **Birleşik Krallık** ve **Japonya** coğrafi graflarını IoT Central bir uygulama oluşturabilirsiniz. Bir konum seçtikten sonra uygulamanızı daha sonra farklı bir konuma taşıyamazsınız.

## <a name="application-template"></a>Uygulama şablonu

Uygulamanız için kullanmak istediğiniz uygulama şablonunu seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir Azure IoT Central uygulamasının CSP olarak nasıl oluşturulacağını öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Uygulamanızı yönetme](howto-administer.md)