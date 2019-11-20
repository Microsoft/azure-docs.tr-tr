---
title: Azure Container Instances bir emptyDir birimi bağlama
description: Azure Container Instances bir kapsayıcı grubundaki kapsayıcılar arasında veri paylaşmak için emptyDir birimini nasıl bağlayacağınızı öğrenin
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 0dbe26ff1e00e1912cfd63e8383695ca794dd037
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325456"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Azure Container Instances bir emptyDir birimi bağlama

Azure Container Instances bir kapsayıcı grubundaki kapsayıcılar arasında veri paylaşmak için *Emptydir* birimini nasıl bağlayacağınızı öğrenin.

> [!NOTE]
> Bir *Emptydir* birimi bağlamak Şu anda Linux kapsayıcılarıyla kısıtlıdır. Tüm özellikleri Windows kapsayıcılarına getirmek için çalıştık, ancak geçerli platform farklarını [genel bakışta](container-instances-overview.md#linux-and-windows-containers)bulabilirsiniz.

## <a name="emptydir-volume"></a>emptyDir birimi

*Emptydir* birimi bir kapsayıcı grubundaki her bir kapsayıcıya erişilebilen yazılabilir bir dizin sağlar. Gruptaki kapsayıcılar birimdeki aynı dosyaları okuyabilir ve yazabilir ve her kapsayıcıda aynı veya farklı yollar kullanılarak bağlanabilir.

Bir *Emptydir* birimi için bazı örnekler kullanılmıştır:

* Karalama alanı
* Uzun süre çalışan görevler sırasında işaret noktası oluşturma
* Bir sepet kapsayıcısı tarafından alınan ve uygulama kapsayıcısı tarafından sunulan verileri depolayın

*Emptydir* birimindeki veriler, kapsayıcı Kilitlenmelerinde kalıcı hale getirilir. Ancak yeniden başlatılan kapsayıcılar, verileri bir *Emptydir* biriminde kalıcı hale getirmek için garanti edilmez.

## <a name="mount-an-emptydir-volume"></a>EmptyDir birimi bağlama

Bir boş dizin birimini bir kapsayıcı örneğine bağlamak için bir [Azure Resource Manager şablonu](/azure/templates/microsoft.containerinstance/containergroups)kullanarak dağıtmanız gerekir.

Önce, `volumes` diziyi şablonun kapsayıcı grubu `properties` bölümünde doldurun. Ardından, `volumeMounts` *Dizin birimini bağlamak* istediğiniz kapsayıcı grubundaki her bir kapsayıcı için, diziyi `properties` kapsayıcı tanımının bölümünde doldurun.

Örneğin, aşağıdaki Kaynak Yöneticisi şablonu, her biri *Emptydir* birimini bağlayan iki kapsayıcıyı içeren bir kapsayıcı grubu oluşturur:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Azure Resource Manager şablonuyla kapsayıcı örneği dağıtımına bir örnek görmek için bkz. [Azure Container Instances birden çok Kapsayıcılı grupları dağıtma](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Instances diğer birim türlerini nasıl bağlayacağınızı öğrenin:

* [Azure kapsayıcı durumlarda bir Azure dosya paylaşımını bağlama](container-instances-volume-azure-files.md)
* [Azure Container Instances bir gitRepo birimi bağlama](container-instances-volume-gitrepo.md)
* [Azure Container Instances bir gizli birimi bağlama](container-instances-volume-secret.md)