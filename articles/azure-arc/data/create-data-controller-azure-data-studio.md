---
title: Azure Data Studio veri denetleyicisi oluşturma
description: Azure Data Studio veri denetleyicisi oluşturma
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 6e61c8819e7ccd868ec92458cff69c37f9277d80
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029633"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Azure Data Studio veri denetleyicisi oluşturma

Dağıtım Sihirbazı ve Not defterleri aracılığıyla Azure Data Studio kullanarak bir veri denetleyicisi oluşturabilirsiniz.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Geçerli zamanda, bu makalede açıklanan yöntemi kullanarak bir veri denetleyicisi oluşturabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Bir Kubernetes kümesine erişmeniz ve kubeconfig dosyanızı, dağıtmak istediğiniz Kubernetes kümesine işaret etmek üzere yapılandırmış olmanız gerekir.
- **Azure Arc** ve adlı Azure Data Studio uzantıları **Azure Data Studio** dahil [istemci araçlarını yüklemeniz](install-client-tools.md) gerekir **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- Azure Data Studio 'de Azure 'da oturum açmanız gerekir.  Bunu yapmak için: CTRL/Command + SHIFT + P yazarak komut metni penceresini açın ve **Azure** yazın.  **Azure: oturum aç '** ı seçin.   Panelde, bir Azure hesabı eklemek için sağ üst köşedeki + simgesine tıklayın.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Azure Arc veri denetleyicisi oluşturmak için Dağıtım Sihirbazı 'nı kullanma

Dağıtım sihirbazını kullanarak bir Azure Arc veri denetleyicisi oluşturmak için bu adımları izleyin.

1. Azure Data Studio, sol gezinti bölmesinde Bağlantılar sekmesine tıklayın.
1. Bağlantılar panelinin üstündeki **...** düğmesine tıklayın ve **yeni dağıtım...** seçeneğini belirleyin.
1. Yeni dağıtım sihirbazında, **Azure Arc veri denetleyicisi**' ni seçin ve ardından alttaki **Seç** düğmesine tıklayın.
1. Önkoşul araçlarının kullanılabilir olduğundan ve gerekli sürümleri karşıladığından emin olun. **Ileri ' ye tıklayın**.
1. Varsayılan kubeconfig dosyasını kullanın veya başka bir tane seçin.  **İleri**’ye tıklayın.
1. Bir Kubernetes küme bağlamı seçin. **İleri**’ye tıklayın.
1. Hedef Kubernetes kümenize bağlı olarak bir dağıtım yapılandırma profili seçin. **Ileri ' ye tıklayın**.
1. Azure Red Hat OpenShift veya Red Hat OpenShift kapsayıcı platformu kullanıyorsanız güvenlik bağlamı kısıtlamalarını uygulayın. [OpenShift üzerinde Azure Arc etkin veri Hizmetleri için bir güvenlik bağlamı kısıtlaması uygulama](how-to-apply-security-context-constraint.md)konusundaki yönergeleri izleyin.

   >[!IMPORTANT]
   >Azure Red Hat OpenShift veya Red Hat OpenShift kapsayıcı platformunda, veri denetleyicisini oluşturmadan önce güvenlik bağlamı kısıtlamasını uygulamanız gerekir.

1. İstediğiniz aboneliği ve kaynak grubunu seçin.
1. Bir Azure konumu seçin.
   
   Burada seçilen Azure konumu, veri denetleyicisi ve yönettiği veritabanı örnekleri hakkındaki *meta verilerin* depolanacağı Azure konumudur. Veri denetleyicisi ve veritabanı örnekleri, aslında Kubernetes kümenizde oluşturulacak her yerde oluşturulur.
   
   İşiniz bittiğinde **İleri**' ye tıklayın.

1. Veri denetleyicisi ve veri denetleyicisinin oluşturulacağı ad alanı için bir ad girin.

    Veri denetleyicisi ve ad alanı adı, Kubernetes kümesinde, [Kubernetes adlandırma kurallarına](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)uyması gereken özel bir kaynak oluşturmak için kullanılır.
    
    Ad alanı zaten varsa, ad alanı zaten başka Kubernetes nesneleri içermiyorsa, vb. kullanılır.  Ad alanı yoksa, ad alanını oluşturma girişimi yapılır.  Bir Kubernetes kümesinde ad alanı oluşturmak için Kubernetes Küme Yöneticisi ayrıcalıkları gerekir.  Kubernetes Küme Yöneticisi ayrıcalıklarınız yoksa, bu Sihirbazı tamamlamadan önce bir Kubernetes Yöneticisi tarafından gerçekleştirilmesi gereken Kubernetes [-Native Araçları ' nı kullanarak bir veri denetleyicisi oluşturma makalesini kullanarak](./create-data-controller-using-kubernetes-native-tools.md) , Kubernetes küme yöneticinizden ilk birkaç adımı gerçekleştirmesini isteyin.


1. Veri denetleyicisinin dağıtılacağı depolama sınıfını seçin. 
1.  Bir Kullanıcı adı ve parola girin ve veri denetleyicisi yönetici kullanıcı hesabı için parolayı onaylayın. **İleri**’ye tıklayın.

1. Dağıtım yapılandırmasını gözden geçirin.
1. Dağıtım talimatlarını gözden geçirmek veya depolama sınıfı adları veya hizmet türleri gibi gerekli değişiklikleri yapmak için, istenen yapılandırmayı veya **betiği not defterine** dağıtmak üzere **Dağıt** ' a tıklayın. Not defterinin en üstünde **Tümünü Çalıştır** ' a tıklayın.

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
