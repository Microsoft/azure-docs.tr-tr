---
title: Azure Arc etkin SQL yönetilen örneğini silme
description: Azure Arc etkin SQL yönetilen örneğini silme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90942166"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Azure Arc etkin SQL yönetilen örneğini silme
Bu makalede, Azure Arc etkin bir SQL yönetilen örneğini nasıl silebileceğinizi açıklanmaktadır.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Mevcut Azure Arc etkin SQL yönetilen örneklerini görüntüle
SQL yönetilen örnekleri görüntülemek için aşağıdaki komutu çalıştırın:

```console
azdata arc sql mi list
```

Çıktı şuna benzer görünmelidir:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Azure Arc etkin SQL yönetilen örneğini silme
SQL yönetilen örneğini silmek için şu komutu çalıştırın:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

Çıktı şuna benzer görünmelidir:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes kalıcı birim taleplerini geri kazanma (PVC)

Bir SQL yönetilen örneğinin silinmesi ilişkili [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)'yi kaldırmaz. Bu tasarım gereğidir. Amacı, örneğin yanlışlıkla silinmesi durumunda kullanıcının veritabanı dosyalarına erişmesine yardımcı olmaktır. PVC’lerin silinmesi zorunlu değildir. Ancak bunun yapılması önerilir. Bu PVC 'yi geri kazanmıyorsanız, Kubernetes kümeniz disk alanının dışında, sonunda hatalarla karşılaşırsınız. PVC'leri geri kazanmak için aşağıdaki adımları izleyin:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. sildiğiniz sunucu grubu için PVC 'leri listeleyin
PVC 'leri listelemek için aşağıdaki komutu çalıştırın:
```console
kubectl get pvc
```

Aşağıdaki örnekte, sildiğiniz SQL yönetilen örneklerine yönelik PVC 'ler olduğunu fark edersiniz.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. her bir PVC 'yi silin
Sildiğiniz her SQL yönetilen örnek için veri ve günlük PVC 'leri silin.
Bu komutun genel biçimi: 
```console
kubectl delete pvc <name of pvc>
```

Örnek:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Bu kubectl komutlarının her biri, PVC 'nin başarıyla silinmesini doğrulayacaktır. Örnek:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Gösterildiği gibi, PVC 'Lerin silinmesinin sonunda, Kubernetes kümenizi hata oluşturacak bir durumda alabilir. Bu hatalardan bazıları, bu depolama sorunu nedeniyle (normal Kubernetes davranışı), Kubernetes kümenizde azdata ile oturum açamıyor olabilir.
>
> Örneğin, günlüklerde şuna benzer iletiler görebilirsiniz:  
> - Ek açıklamalar: microsoft.com/ignore-pod-health: true  
> - Durum: başarısız  
> - Neden: çıkarıldı  
> - İleti: düğüm kaynakta azalmıştır: geçici-depolama. Kapsayıcı denetleyicisi, 0 isteğini aşan 16372Ki kullanıyor.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Arc ETKIN SQL yönetilen örneği özellikleri ve](managed-instance-features.md) özellikleri hakkında daha fazla bilgi edinin

[Veri denetleyicisi oluşturarak başlayın](create-data-controller.md)

Zaten bir veri denetleyicisi oluşturulmuş mı? [Azure Arc etkin SQL yönetilen örneği oluşturma](create-sql-managed-instance.md)