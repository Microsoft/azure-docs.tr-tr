---
title: Azure IoT çözümleri sitesini kullanma-Azure | Microsoft Docs
description: Çözüm hızlandırıcıyı dağıtmak için AzureIoTSolutions.com Web sitesinin nasıl kullanılacağını açıklar.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: e3c9296a909ba23ee8a3475149911af629a01b1a
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167473"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Çözüm hızlandırıcıyı dağıtmak için azureiotsolutions.com sitesini kullanın

Azure IoT çözüm hızlandırıcılarına [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators)adresinden Azure aboneliğinize dağıtım yapabilirsiniz. AzureIoTSolutions.com, hem Microsoft açık kaynak hem de iş ortağı çözüm hızlandırıcılarını barındırır. Bu çözüm Hızlandırıcılar, [Azure IoT başvuru mimarisi](/azure/architecture/reference-architectures/iot)ile hizalanır. Siteyi kullanarak bir çözüm hızlandırıcıyı tanıtım veya üretim ortamı olarak hızlı bir şekilde dağıtabilirsiniz.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="IoT çözümleri giriş sayfası":::

> [!TIP]
> Dağıtım işlemi üzerinde daha fazla denetime ihtiyacınız varsa, bir çözüm Hızlandırıcısı dağıtmak için CLı 'yi kullanabilirsiniz.

Çözüm hızlandırıcılarını aşağıdaki yapılandırmalarda dağıtabilirsiniz:

* **Standart**: üretim ortamı geliştirmek için genişletilmiş altyapı dağıtımı. Azure Container Service mikro hizmetleri birkaç Azure sanal makinesine dağıtır. Kubernetes mikro hizmetleri tek tek barındıran Docker kapsayıcılarını düzenler.
* **Temel**: bir tanıtım için düşük maliyetli bir sürüm veya bir dağıtımı test etmek için. Tüm mikro hizmetler tek bir Azure sanal makinesine dağıtılır.
* **Yerel**: test ve geliştirme için yerel makine dağıtımı. Bu yaklaşım, mikro hizmetleri yerel bir Docker kapsayıcısına dağıtır ve bulutta IoT Hub, Azure Cosmos DB ve Azure Storage Services 'a bağlanır.

Her çözüm hızlandırıcılarının her biri, IoT Hub, Azure Stream Analytics ve Cosmos DB gibi farklı Azure hizmetleri birleşimini kullanır. Daha fazla bilgi için [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) adresini ziyaret edin ve bir çözüm Hızlandırıcısı seçin.

## <a name="sign-in-at-azureiotsolutionscom"></a>Azureiotsolutions.com adresinde oturum açın

Bir çözüm Hızlandırıcısını dağıtabilmeniz için önce, bir Azure aboneliğiyle ilişkili kimlik bilgilerini kullanarak AzureIoTSolutions.com adresinde oturum açmanız gerekir. Hesabınız birden fazla Microsoft Azure Active Directory (AD) kiracısıyla ilişkiliyse, kullanılacak dizini seçmek için **hesap seçme açılan listesini** kullanabilirsiniz.

Çözüm hızlandırıcılarını dağıtma, kullanıcıları yönetme ve Azure hizmetlerini yönetme izinleriniz, seçili dizindeki rolünüze bağlıdır. Çözüm hızlandırıcılarına ilişkin ortak Azure AD rolleri şunlardır:

* **Genel yönetici**: Azure AD kiracısı başına çok sayıda [genel yönetici](../active-directory/roles/permissions-reference.md) olabilir:

  * Bir Azure AD kiracısı oluşturduğunuzda varsayılan olarak bu kiracının genel yöneticisi olursunuz.
  * Genel yönetici, temel ve standart çözüm hızlandırıcılarını dağıtabilir.

* **Etki alanı kullanıcısı**: Azure AD kiracısı başına çok sayıda etki alanı kullanıcısı olabilir. Bir etki alanı kullanıcısı, temel bir çözüm hızlandırıcıyı dağıtabilir.

* **Konuk Kullanıcı**: Azure AD kiracısı başına çok sayıda Konuk Kullanıcı olabilir. Konuk kullanıcılar, Azure AD kiracısında bir çözüm Hızlandırıcısı dağıtamaz.

Azure AD 'deki kullanıcılar ve roller hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Active Directory Kullanıcı oluşturma](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Kullanıcılara uygulama atama](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Cihazınızı seçin

AzureIoTSolutions.com sitesi, [IoT Için Azure Sertifikalı cihaz kataloğuna](https://devicecatalog.azure.com/)bağlantı sağlar.

Katalog, IoT çözümünüzü oluşturmaya başlamak için çözüm hızlandırıcılarınıza bağlanabilyebilmeniz için yüzlerce sertifikalı IoT donanım cihazını listeler.

![Cihaz Kataloğu](media/iot-accelerators-permissions/devicecatalog.png)

Donanım üreticiniz kullanıyorsanız, IoT için sertifikalı bir programda Microsoft ile ortaklık hakkında bilgi edinmek için **Iş ortağı olun ' a** tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

IoT çözüm hızlandırıcılardan birini denemek için hızlı başlangıç: [bağlı bir fabrika çözümünü deneyin](quickstart-connected-factory-deploy.md).
