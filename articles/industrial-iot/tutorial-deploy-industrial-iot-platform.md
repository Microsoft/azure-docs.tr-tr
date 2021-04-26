---
title: Azure endüstriyel IoT platformunu dağıtma
description: Bu öğreticide, IIoT platformunu dağıtmayı öğreneceksiniz.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787900"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Öğretici: Azure endüstriyel IoT platformunu dağıtma

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> * IIoT platformunun ana bileşenleri hakkında
> * Farklı yükleme türleri hakkında
> * Endüstriyel IoT platformunu dağıtma

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure aboneliğinin oluşturulması gerekir
- [Git](https://git-scm.com/downloads) 'i indir
- Kimlik doğrulaması için kullanılan Azure Active Directory (AAD) uygulama kayıtları, kiracı genelinde yönetici onayı sağlamak için genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi hakları gerektirir (daha fazla seçenek için aşağıya bakın)
- Dağıtım için desteklenen işletim sistemleri Windows, Linux ve Mac
- IoT Edge, Windows 10 IoT Enterprise LTSC ve Ubuntu Linux 16.08/18.04 LTS Linux destekler

## <a name="main-components"></a>Ana bileşenler

- En düşük Bağımlılıklar: IoT Hub, Cosmos DB, Service Bus, Olay Hub 'ı Key Vault, depolama
- Standart Bağımlılıklar: minimum + SignalR hizmeti, AAD uygulama kayıtları, cihaz sağlama hizmeti, Time Series Insights, çalışma kitabı, Log Analytics Application Insights
- Mikro hizmetler: App Service planı, App Service
- Kullanıcı arabirimi (Web uygulaması): App Service planı (mikro hizmetlerle paylaşılan), App Service
- Simülasyon: sanal makine, sanal ağ, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>Yükleme türleri

- En az: en düşük bağımlılıklar
- Yerel: en düşük ve standart bağımlılıklar
- Hizmetler: yerel ve mikro hizmetler
- Simülasyon: en düşük bağımlılıklar ve simülasyon bileşenleri
- Uygulama: hizmetler ve Kullanıcı arabirimi
- Tümü (varsayılan): uygulama ve benzetim

## <a name="deployment"></a>Dağıtım

1. IIoT platformunun dağıtımına başlamak için depoyu komut isteminden veya terminalden kopyalayın.

    Git kopyası https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. Kılavuzlu dağıtımı başlatın, betik Azure hesabı, aboneliği, hedef kaynak ve grup ve uygulama adı gibi gerekli bilgileri toplar.

Windows'da:
    ```
    .\deploy
    ```

Linux veya Mac üzerinde:
    ```
    ./deploy.sh
    ```

3. Mikro hizmetler ve Kullanıcı arabirimi kimlik doğrulaması gerektiren Web uygulamalardır, bu, AAD 'de üç uygulama kaydı gerektirir. Gerekli haklar eksikse, iki olası çözüm vardır:

- AAD yöneticisinden uygulama için kiracı genelinde yönetici onayı vermesini isteyin
- AAD Yöneticisi AAD uygulamalarını oluşturabilir. Dağıt/Scripts klasörü, AAD kaydını dağıtımdan ayrı olarak gerçekleştirmek için AAD-register.ps1 betiğini içerir. Betiğin çıkışı, dağıtımın parçası olarak kullanılacak ilgili bilgileri içeren bir dosyadır ve-aadConfig bağımsız değişkeni kullanılarak aynı klasördeki deploy.ps1 betiğe geçirilmesi gerekir.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

[Azure Kubernetes Service (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md) için hazırlama, geri alma, ölçekleme ve esnekliği gerektiren üretim dağıtımları için platform dağıtılabilir

Başvur
- [Azure endüstriyel IoT platformunu dağıtma](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Hepsini bir arada dağıtma](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Platformu AKS 'e dağıtma](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Sonraki adımlar
Artık IIoT platformunu dağıttığınıza göre, bileşenlerin yapılandırılmasını nasıl özelleştireceğinizi öğrenebilirsiniz:

> [!div class="nextstepaction"]
> [Bileşenlerin yapılandırmasını özelleştirme](tutorial-configure-industrial-iot-components.md)
