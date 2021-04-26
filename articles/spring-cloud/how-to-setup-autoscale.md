---
title: Mikro hizmet uygulamaları için otomatik ölçeklendirmeyi ayarlama
description: Bu makalede, Microsoft Azure portal veya Azure CLı kullanarak uygulamalarınız için otomatik ölçeklendirme ayarlarının nasıl ayarlanacağı açıklanır.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: eaa4a219a1020639c930f8a07bfcaa1c45d0cef8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878772"
---
# <a name="set-up-autoscale-for-microservice-applications"></a>Mikro hizmet uygulamaları için otomatik ölçeklendirmeyi ayarlama

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Otomatik ölçeklendirme, mikro hizmet uygulamalarının talep değiştiğinde en iyi şekilde çalışmasına yardımcı olan Azure Spring Cloud 'ın yerleşik bir özelliğidir. Bu, sanal CPU 'ların, belleğin ve uygulama örneklerinin sayısını değiştirmeyi içerir. Bu makalede, Microsoft Azure portal veya Azure CLı kullanarak uygulamalarınız için otomatik ölçeklendirme ayarlarının nasıl ayarlanacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Bu yordamları izlemek için şunlar gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* Dağıtılmış bir Azure yay bulut hizmeti örneği. Kullanmaya başlamak için [Azure CLI aracılığıyla uygulama dağıtma hızlı](./spring-cloud-quickstart.md) başlangıcını izleyin.
* Hizmet örneğiniz için en az bir uygulama zaten oluşturulmuş.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Azure portal otomatik ölçeklendirme sayfasına gidin

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure Spring Cloud **genel bakış** sayfasına gidin.
3. Hizmetinizi içeren kaynak grubunu seçin.
4. Sol gezinti bölmesindeki menüde **Ayarlar** ' ın altında **uygulamalar** sekmesini seçin.
5. Otomatik ölçeklendirmeyi ayarlamak istediğiniz uygulamayı seçin. Bu örnekte, **demo** adlı uygulamayı seçin. Daha sonra uygulamanın **genel bakış** sayfasını görmeniz gerekir.
6. Sol gezinti bölmesindeki menüdeki **Ayarlar** ' ın altındaki **Ölçek Genişletme** sekmesine gidin.
7. Otomatik ölçeklendirmeyi ayarlamak istediğiniz dağıtımı seçin. Aşağıdaki bölümde gösterilen otomatik ölçeklendirme seçeneklerini görmeniz gerekir.


![Otomatik ölçeklendirme menüsü](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Azure portal uygulamanızın otomatik ölçeklendirme ayarlarını ayarlayın

Otomatik ölçeklendirme talep yönetimi için iki seçenek vardır:

* El ile ölçeklendirme: sabit bir örnek sayısını tutar. Standart katmanda, en fazla 500 örneğe ölçeklendirebilirsiniz. Bu değer, mikro hizmet uygulamasının ayrı çalışan örneklerinin sayısını değiştirir.
* Özel otomatik ölçeklendirme: herhangi bir ölçümlere göre herhangi bir zamanlamaya göre ölçeklendirir.

Azure portal nasıl ölçeklendirmek istediğinizi seçin.  Aşağıdaki şekilde, **özel otomatik ölçeklendirme** seçeneği ve mod ayarları gösterilmektedir.

![Özel otomatik ölçeklendirme](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Azure CLı 'de uygulamanız için otomatik ölçeklendirme ayarları ayarlama
Azure CLı kullanarak otomatik ölçeklendirme modlarını de ayarlayabilirsiniz.  Aşağıdaki komutlar bir otomatik ölçeklendirme ayarı ve bir otomatik ölçeklendirme kuralı oluşturur.

* Otomatik ölçeklendirme ayarı oluştur
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Otomatik ölçeklendirme kuralı oluştur
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Standart katmana yükseltme

Temel katmandadır ve bu limitlerden biri veya daha fazlası ile kısıtlanıyor ise Standart katmana yükseltebilirsiniz. Bunu yapmak için, önce *Standart* katman sütununu ve **Yükselt** düğmesine tıklayarak **fiyatlandırma** katmanı menüsüne gidin.

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft Azure otomatik ölçeklendirmeyi genel bakış](../azure-monitor/autoscale/autoscale-overview.md)
* [Azure CLı Izleme otomatik ölçeklendirme](/cli/azure/monitor/autoscale)
