---
title: Azure portal Azure Arc veri denetleyicisi oluşturma
description: Azure portal Azure Arc veri denetleyicisi oluşturma
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 12d0997e677bcca423f32951e99a6202855104ad
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030878"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Azure portal Azure Arc veri denetleyicisi oluşturma

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Giriş

Azure Arc veri denetleyicisi oluşturmak için Azure portal kullanabilirsiniz.

Oluşturulacak veya yönetilecek kaynak Azure altyapısının dışında olmasına rağmen, Azure Arc için oluşturma deneyimlerinin birçoğu Azure portal başlar. Bu durumlarda Kullanıcı deneyimi deseninin, özellikle de Azure ile ortamınız arasında doğrudan bağlantı olmaması durumunda, daha sonra Azure 'a güvenli bir bağlantı kurmak için ortamınızda indirilebilen ve yürütülebilecek bir betik oluşturmak için Azure portal kullanmaktır. Örneğin, Azure Arc etkin sunucular, [yay özellikli sunucular oluşturmak](../servers/onboard-portal.md)için bu kalıbı izler.

Azure Arc etkin veri Hizmetleri 'nin dolaylı bağlantı modunu kullandığınızda, daha sonra, Kubernetes kümenizde Azure Data Studio indirilebilen ve çalıştırılabilen bir not defteri oluşturmak için Azure portal kullanabilirsiniz. 

Doğrudan bağlanma modunu kullandığınızda, veri denetleyicisini doğrudan Azure portal sağlayabilirsiniz. [Bağlantı modları](connectivity.md)hakkında daha fazla bilgi edinebilirsiniz.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Azure Arc veri denetleyicisi oluşturmak için Azure portal kullanma

Azure portal ve Azure Data Studio kullanarak Azure Arc veri denetleyicisi oluşturmak için aşağıdaki adımları izleyin.

1. İlk olarak [Azure Portal Market](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)' te oturum açın.  Market arama sonuçları ' Azure Arc veri denetleyicisi ' ni gösterecek şekilde filtrelenecektir.
1. İlk adım arama ölçütlerine girmediyseniz. Lütfen arama sonuçlarına girin, ' Azure Arc veri denetleyicisi ' seçeneğine tıklayın.
1. Market 'ten Azure veri denetleyicisi kutucuğunu seçin.
1. **Oluştur** düğmesine tıklayın.
1. Dolaylı bağlantı modunu seçin. [Bağlantı modları ve gereksinimler](./connectivity.md)hakkında daha fazla bilgi edinin. 
1. Azure Arc veri denetleyicisi oluşturma ve Azure Data Studio ve kubectl gibi tüm önkoşul yazılımlarını yüklemeye yönelik gereksinimleri gözden geçirin.
1. **İleri: veri denetleyicisi ayrıntıları** düğmesine tıklayın.
1. Azure portal oluşturduğunuz diğer tüm kaynaklar için yaptığınız gibi bir abonelik, kaynak grubu ve Azure konumu seçin. Bu durumda, seçtiğiniz Azure konumu kaynakla ilgili meta verilerin depolanacağı yeri olacaktır.  Kaynak, seçtiğiniz herhangi bir altyapıda oluşturulur. Azure altyapısında olması gerekmez.
1. Veri denetleyiciniz için bir ad girin.

1. Bir dağıtım yapılandırma profili seçin.
1. **Azure Studio 'Da aç** düğmesine tıklayın.
1. Bir sonraki ekranda, seçimlerinizin ve oluşturulan bir not defterinizin özetini görürsünüz.  Not defterini indirmek için **sağlama Not defterini indir** düğmesine tıklayabilirsiniz.

   > [!IMPORTANT]
   > Azure Red Hat OpenShift veya Red Hat OpenShift kapsayıcı platformunda, veri denetleyicisini oluşturmadan önce güvenlik bağlamı kısıtlamasını uygulamanız gerekir. [OpenShift üzerinde Azure Arc etkin veri Hizmetleri için bir güvenlik bağlamı kısıtlaması uygulama](how-to-apply-security-context-constraint.md)konusundaki yönergeleri izleyin.

1. Azure Data Studio bölümünde Not defterini açın ve üstteki **Tümünü Çalıştır** düğmesine tıklayın.
1. Veri denetleyicisi oluşturma işleminin tamamlanabilmesi için Not defteri 'ndeki komut istemlerini ve yönergeleri izleyin.

## <a name="monitoring-the-creation-status"></a>Oluşturma durumunu izleme

Denetleyicinin oluşturulması tamamlanması birkaç dakika sürer. İlerleme durumunu başka bir Terminal penceresinde aşağıdaki komutlarla izleyebilirsiniz:

> [!NOTE]
>  Aşağıdaki örnek komutlar, ' Arc ' adlı bir veri denetleyicisi ve Kubernetes ad alanı oluşturduğunuzu varsayar.  Farklı bir ad alanı/veri denetleyicisi adı kullandıysanız, ' yay ' değerini adınızla değiştirebilirsiniz.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Ayrıca, aşağıdaki gibi bir komut çalıştırarak belirli bir pod 'un oluşturma durumunu da denetleyebilirsiniz.  Bu, özellikle herhangi bir sorunu gidermek için kullanışlıdır.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Oluşturma sorunlarını giderme

Oluşturma ile herhangi bir sorun yaşarsanız, lütfen [sorun giderme kılavuzuna](troubleshoot-guide.md)bakın.