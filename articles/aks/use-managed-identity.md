---
title: Azure Kubernetes hizmetinde Yönetilen kimlikler kullanma
description: Azure Kubernetes hizmeti 'nde (AKS) yönetilen kimlikleri nasıl kullanacağınızı öğrenin
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 16a1f3b8f9ef036bcdc9af122243d313186f99f1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885328"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Önizleme-Azure Kubernetes hizmetinde Yönetilen kimlikler kullanma

Şu anda Azure Kubernetes hizmeti (AKS) kümesi (özellikle Kubernetes bulut sağlayıcısı), Azure 'da yük dengeleyiciler ve yönetilen diskler gibi ek kaynaklar oluşturmak için bir *hizmet sorumlusu* gerektirir. Bir hizmet sorumlusu sağlamanız gerekir ya da AKS sizin adınıza bir tane oluşturuyor. Hizmet sorumluları genellikle sona erme tarihi vardır. Kümeler, kümenin çalışmasını sağlamak için hizmet sorumlusunun yenilenmesi gereken bir duruma ulaşır. Hizmet sorumlularını yönetmek karmaşıklık ekler.

*Yönetilen kimlikler* temelde hizmet sorumluları etrafında bir sarmalayıcıdır ve yönetimini daha kolay hale getirir. Daha fazla bilgi edinmek için [Azure kaynakları için Yönetilen kimlikler](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)hakkında bilgi edinin.

AKS iki yönetilen kimlik oluşturur:

- **Sistem tarafından atanan yönetilen kimlik**: Kubernetes bulut sağlayıcısının kullanıcı adına Azure kaynakları oluşturmak için kullandığı kimlik. Sistem tarafından atanan kimliğin yaşam döngüsü, kümeyle bağlantılıdır. Küme silindiğinde kimlik silinir.
- **Kullanıcı tarafından atanan yönetilen kimlik**: kümede yetkilendirme için kullanılan kimlik. Örneğin, Kullanıcı tarafından atanan kimlik, erişim denetimi kayıtlarını (ACRs) kullanmak üzere AKS yetkilendirmek veya kubelet 'in Azure 'dan meta verileri almasına yetki vermek için kullanılır.

Bu önizleme döneminde bir hizmet sorumlusu hala gereklidir. İzleme, sanal düğümler, Azure Ilkesi ve HTTP uygulama yönlendirme gibi eklentilerin yetkilendirmesi için kullanılır. İş, hizmet sorumlusu adı 'nda (SPN) eklentilerin bağımlılığını kaldırmak için çalışır. Sonuç olarak, AKS 'teki bir SPN 'nin gereksinimi tamamen kaldırılır.

> [!IMPORTANT]
> AKS Önizleme özellikleri self servis, kabul etme esasına göre sunulmaktadır. Önizlemeler "olduğu gibi" ve "kullanılabildiği gibi" verilmiştir ve hizmet düzeyi anlaşmalarından ve sınırlı garantiden çıkarılır. AKS önizlemeleri, müşteri desteğiyle ilgili olarak en iyi çaba kapsamında kısmen ele alınmıştır. Bu nedenle, bu özellikler üretim kullanımı için tasarlanmamıştır. Daha fazla bilgi için aşağıdaki destek makalelerine bakın:
>
> - [AKS destek Ilkeleri](support-policies.md)
> - [Azure desteği SSS](faq.md)

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki kaynakların yüklü olması gerekir:

- Azure CLı, sürüm 2.0.70 veya üzeri
- Aks-Preview 0.4.14 uzantısı

Aks-Preview 0.4.14 uzantısını veya üstünü yüklemek için aşağıdaki Azure CLı komutlarını kullanın:

```azurecli
az extension update --name aks-preview
az extension list
```

> [!CAUTION]
> Bir aboneliği bir abonelik üzerinde kaydettikten sonra, şu anda bu özelliğin kaydını silemezsiniz. Bazı Önizleme özelliklerini etkinleştirdiğinizde, varsayılanlar daha sonra abonelikte oluşturulan tüm AKS kümeleri için kullanılabilir. Üretim aboneliklerinde Önizleme özelliklerini etkinleştirmeyin. Bunun yerine, Önizleme özelliklerini test etmek ve geri bildirim toplamak için ayrı bir abonelik kullanın.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Durumun **kayıtlı**olarak gösterilmesi birkaç dakika sürebilir. [Az Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) komutunu kullanarak kayıt durumunu kontrol edebilirsiniz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Durum kayıtlı olarak görünüyorsa, [az Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) komutunu kullanarak `Microsoft.ContainerService` kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Yönetilen kimliklerle bir AKS kümesi oluşturma

Artık aşağıdaki CLı komutlarını kullanarak, yönetilen kimliklerle bir AKS kümesi oluşturabilirsiniz.

İlk olarak, bir Azure Kaynak grubu oluşturun:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ardından, bir AKS kümesi oluşturun:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Son olarak, kümeye erişmek için kimlik bilgilerini alın:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Küme birkaç dakika içinde oluşturulacak. Böylece, uygulama iş yüklerinizi yeni kümeye dağıtabilir ve hizmet sorumlusu tabanlı AKS kümelerinde yaptığınız gibi etkileşimde bulunabilirsiniz.

> [!IMPORTANT]
>
> - Yönetilen kimlikleri olan AKS kümeleri yalnızca kümenin oluşturulması sırasında etkinleştirilebilir.
> - Mevcut AKS kümeleri, yönetilen kimlikleri etkinleştirmek için güncelleştirilemez veya yükseltilemez.
