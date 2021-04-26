---
title: Azure Arc etkin bir PostgreSQL hiper ölçek sunucu grubunu silme
description: Azure Arc etkin bir Postgres hiper ölçek sunucu grubunu silme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7932ad3b30910e539acfbff2329a03f80a4d1a0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670367"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc etkin bir PostgreSQL hiper ölçek sunucu grubunu silme

Bu belgede, Azure Arc kurulumundan bir sunucu grubunu silme adımları açıklanır.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>Sunucu grubunu sil

Örnek olarak, aşağıdaki kurulumdan _postgres01_ örneğini silmek istiyoruz:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Delete komutunun genel biçimi:
```console
azdata arc postgres server delete -n <server group name>
```
Bu komutu yürüttüğünüzde, sunucu grubunun silinmesini onaylamanız istenecektir. Silme işlemlerini otomatikleştirmek için betikler kullanıyorsanız, onay isteğini atlamak için--zorlama parametresini kullanmanız gerekir. Örneğin, şöyle bir komut çalıştırırsınız: 
```console
azdata arc postgres server delete -n <server group name> --force
```

Delete komutu hakkında daha fazla ayrıntı için şunu çalıştırın:
```console
azdata arc postgres server delete --help
```

### <a name="delete-the-server-group-used-in-this-example"></a>Bu örnekte kullanılan sunucu grubunu sil

```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Kubernetes kalıcı birim taleplerini geri kazanma (PVC)

Bir sunucu grubunu silmek, ilişkili [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)'leri kaldırmaz. Bu tasarım gereğidir. Amacı, örneğin yanlışlıkla silinmesi durumunda kullanıcının veritabanı dosyalarına erişmesine yardımcı olmaktır. PVC’lerin silinmesi zorunlu değildir. Ancak bunun yapılması önerilir. Bu PVC’leri geri kazanmazsanız Kubernetes kümeniz disk alanının tükendiğini düşündüğü için sonunda hatalarla karşılaşırsınız. PVC'leri geri kazanmak için aşağıdaki adımları izleyin:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. sildiğiniz sunucu grubu için PVC 'leri listeleyin

PVC 'leri listelemek için şu komutu çalıştırın:

```console
kubectl get pvc [-n <namespace name>]
```

Bu, özellikle sildiğiniz sunucu grubu için PVC 'lerin listesini döndürür. Örnek:

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Bu sunucu grubu için 8 PVC vardır.

### <a name="2-delete-each-of-the-pvcs"></a>2. her bir PVC 'yi silin

Sildiğiniz sunucu grubunun PostgreSQL hiper ölçek düğümlerinin (düzenleyici ve çalışanlar) her biri için veri ve günlük PVC 'leri silin.

Bu komutun genel biçimi: 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Örnek:

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

Bu kubectl komutlarının her biri, PVC 'nin başarıyla silinmesini doğrulayacaktır. Örnek:

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> Gösterildiği gibi, PVC 'Lerin silinmesinin sonunda, Kubernetes kümenizi hata oluşturacak bir durumda alabilir. Bu hatalardan bazıları, bu depolama sorunu nedeniyle (normal Kubernetes davranışı), Kubernetes kümenizde azdata ile oturum açamıyor olabilir.
>
> Örneğin, günlüklerde şuna benzer iletiler görebilirsiniz:  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>Sonraki adım
[Azure Arc etkin PostgreSQL hiper ölçeği](create-postgresql-hyperscale-server-group.md) oluşturma
