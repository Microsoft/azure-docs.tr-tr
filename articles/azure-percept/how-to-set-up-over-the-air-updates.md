---
title: Kablosuz güncelleştirmeleri dağıtmak için Azure IoT Hub ayarlama
description: Azure IoT Hub 'yi-Air üzerinde Azure Percept DK 'ye güncelleştirme dağıtmak üzere nasıl yapılandıracağınızı öğrenin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064129"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Azure IoT Hub, Azure Percept DK 'nize hava güncelleştirmeleri dağıtmak için nasıl ayarlanır

Havayolu arası güncelleştirmeleri kullanarak Azure Percept DK 'yi güvenli ve güncel tutun. Birkaç basit adımda Azure ortamınızı IoT Hub için cihaz güncelleştirmesiyle ayarlayacaksınız ve en son güncelleştirmeleri Azure Percept DK 'nize dağıtabileceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- [Azure aboneliği](https://azure.microsoft.com/free/)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md): Dev Kit 'i bir Wi-Fi ağına bağladınız, bir IoT Hub oluşturdunuz ve geliştirme setinizi IoT Hub bağladınız

## <a name="create-a-device-update-account"></a>Cihaz güncelleştirme hesabı oluştur

1. [Azure Portal](https://portal.azure.com) gidin ve Azure Percept Ile kullandığınız Azure hesabıyla oturum açın.

1. Sayfanın üst kısmındaki arama çubuğunda **IoT Hub Için cihaz güncelleştirmesi** girin.

1. Arama çubuğunda göründüğünde **IoT Hub Için cihaz güncelleştirme** ' yi seçin.

1. Sayfanın sol üst kısmındaki **+ Ekle** düğmesine tıklayın.

1. Azure Percept aygıtınızla ilişkili **Azure aboneliğini** ve **kaynak grubunu** ve IoT Hub seçin.

1. Cihaz güncelleştirme hesabınız için bir **ad** ve **konum** belirtin.

1. Ayrıntıları gözden geçirin ve **gözden geçir + oluştur**' u seçin.

1. Dağıtım tamamlandıktan sonra **Kaynağa Git ' e** tıklayın.

## <a name="create-a-device-update-instance"></a>Cihaz güncelleştirme örneği oluşturma

1. IoT Hub kaynağı için cihaz güncelleştirmeniz ' nda, **örnek yönetimi** altında **örnekler** ' e tıklayın.

1. **+ Oluştur**' a tıklayın, bir örnek adı belirtin ve Azure Percept aygıtınızla ilişkili IoT Hub seçin. Bu işlem birkaç dakika sürebilir.

1. **Oluştur**’a tıklayın.

## <a name="configure-iot-hub"></a>IoT Hub Yapılandır

1. Örnek Yönetimi **örnekleri** sayfasında, cihaz güncelleştirme örneğinizin **başarılı** durumuna geçmesinin tamamlanmasını bekleyin. Durumu güncelleştirmek için **Yenile** simgesine tıklayın.

1. Sizin için oluşturulan örneği seçin ve **IoT Hub Yapılandır**' a tıklayın. Sol bölmede, **Bu değişiklikleri yapmak için kabul ediyorum** ' u seçin ve **Güncelleştir**' e tıklayın.

1. İşlemin başarıyla tamamlanmasını bekleyin.

## <a name="configure-access-control-roles"></a>Erişim denetimi rollerini yapılandırma

Son adım, kullanıcılara güncelleştirme yayımlamak ve dağıtmak için izin vermenizi sağlayacak.

1. IoT Hub kaynağı için cihaz güncelleştirmeniz ' nda, **erişim denetimi (IAM)** öğesine tıklayın.

1. **+ Ekle** ' ye tıklayın ve ardından **rol ataması Ekle**' yi seçin.

1. **Rol** Için, **Cihaz Güncelleştirme Yöneticisi**' ni seçin. **Kullanıcı, Grup veya hizmet ilkesi** seçmek **için erişim atama** için. **Seç** için hesabınızı veya güncelleştirmeleri dağıtmaya yönelik kişinin hesabını seçin. **Kaydet**’e tıklayın.

> [!TIP]
> Kuruluşunuzda daha fazla kişiye erişim vermek istiyorsanız, bu adımı yineleyebilir ve bu kullanıcıların her birini bir **Cihaz Güncelleştirme Yöneticisi** yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Hub için cihaz güncelleştirmesini kullanarak [Azure Percept Dev Kit 'i daha sonra kablosuz olarak güncelleştirmeye](./how-to-update-over-the-air.md) hazırsınız.