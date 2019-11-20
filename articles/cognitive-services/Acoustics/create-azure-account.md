---
title: Project Acoustics Azure Batch hesabı kurulumu
titlesuffix: Azure Cognitive Services
description: Bu nasıl yapılır, Project Acoustics Unity ve Unreal Engine tümleştirmeleri ile kullanmak üzere Azure Batch hesabı ayarlamayı açıklar.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855089"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Project Acoustics Azure Batch hesabı kurulumu
Bu nasıl yapılır, Project Acoustics Unity ve Unreal Engine tümleştirmeleri ile kullanmak üzere Azure Batch hesabı ayarlamayı açıklar.

## <a name="get-an-azure-subscription"></a>Azure aboneliği edinin
Batch ve Storage hesapları ayarlamadan önce bir [Azure aboneliği](https://azure.microsoft.com/free/) gereklidir. İlk kez kaydoluyorsanız Azure, birkaç zaman sınırlı boş kaynak ve $200 kredi sağlar.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch ve depolama hesapları oluşturma
Sonra, Azure Batch ve ilişkili Azure depolama hesaplarınızı ayarlamak için [Bu yönergeleri](https://docs.microsoft.com/azure/batch/batch-account-create-portal) izleyin.

Toplu Iş ve depolama hesapları için varsayılan seçenekleri belirleyin:
  
  ![Yeni hesap seçeneklerinin Azure Batch varsayılan ayarları gösteren ekran görüntüsü](media/new-batch-account-create.png)

  ![Azure depolama yeni hesap seçeneklerinin varsayılan ayarları gösteren ekran görüntüsü](media/batch-storage-account-create.png)

Azure 'un hesapları dağıtması birkaç dakika sürer. Portalın sağ üst köşesinde bir tamamlanma bildirimi arayın.
  
  ![Azure hesabı dağıtılan bildirim ekran görüntüsü](media/batch-accounts-deploy-notification.png)

Hesaplarınız artık panonuzda görünür olmalıdır.
  
  ![Batch ve Storage hesabını gösteren Azure portal panonun ekran görüntüsü](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Azure kimlik bilgileriyle Acoustics hazırlama Kullanıcı arabirimini ayarlama
Panodaki Batch hesabı bağlantısına tıklayın ve ardından Batch hesabı sayfasındaki **anahtarlar** bağlantısına tıklayarak kimlik bilgilerinizi erişin.
  
  ![Anahtarlar sayfasına bağlantı içeren Azure Batch hesabının ekran görüntüsü vurgulanmış](media/batch-access-keys.png)

  ![Erişim anahtarlarına sahip Azure Batch hesap anahtarları sayfasının ekran görüntüsü](media/batch-keys-info.png)

Azure depolama hesabınızın kimlik bilgilerine erişmek için sayfadaki **depolama hesabı** bağlantısına tıklayın.
  
  ![Erişim anahtarları içeren Azure depolama hesabı anahtarları sayfasının ekran görüntüsü](media/storage-keys-info.png)

Bu kimlik bilgilerini [Unity hazırlama eklentisine](unity-baking.md) veya [Unreal hazırlama eklentisine](unreal-baking.md)girin.

## <a name="node-types-and-region-support"></a>Düğüm türleri ve bölge desteği
Project Acoustics, tüm Azure bölgelerinde desteklenmeyen Fsv2 ve H serisi işlem için iyileştirilmiş Azure VM düğümlerini gerektirir. Batch hesabınız için doğru konumu seçtiğinizden emin olmak için lütfen [Bu tabloyu](https://azure.microsoft.com/global-infrastructure/services) kontrol edin.
![Bölgeye göre Azure sanal makinelerini gösteren ekran görüntüsü](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Kotayı yükseltme
Azure Batch hesapları, hesap oluşturma sırasında 20 işlem çekirdekleri ile sağlanır. Acoustics iş yükünüzü sahnenin araştırma noktası sayısına kadar çok sayıda düğümde paralel hale getirmek olanak sağladığından daha hızlı bir şekilde bu sınırı artırmak istiyoruz. Azure Batch portalı sayfanızdaki **Kota** bağlantısına tıklayıp, ardından **Istek kotası artışına**tıkladığınızda bir kota artışı isteyebilirsiniz:

![Azure kota sayfasının ekran görüntüsü](media/azure-quotas.png)

## <a name="next-steps"></a>Sonraki adımlar
* Project Acoustics eklentisini [Unity](unity-integration.md) veya [gerçek olmayan](unreal-integration.md) projenizden tümleştirin

