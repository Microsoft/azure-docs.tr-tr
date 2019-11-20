---
title: Azure Container Registry-roller ve izinler
description: Azure Container Registry 'deki kaynaklara yönelik ayrıntılı izinler sağlamak için Azure rol tabanlı erişim denetimi (RBAC) ve kimlik ve erişim yönetimi (ıAM) kullanın.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: 69104cdaeb4abfc15e2ac4209e1ddbc610656c13
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793976"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller ve izinler

Azure Container Registry hizmeti, bir Azure Container Registry 'ye farklı düzeylerde izinler sağlayan bir dizi Azure rolünü destekler. Azure [rol tabanlı erişim denetimi](../role-based-access-control/index.yml) 'ni (RBAC) kullanarak, bir kayıt defteriyle etkileşimde bulunmak gereken kullanıcılara veya hizmet sorumlularına belirli izinler atayın.

| Rol/Izin       | [Erişim Kaynak Yöneticisi](#access-resource-manager) | [Kayıt Defteri Oluştur/Sil](#create-and-delete-registry) | [Görüntü gönder](#push-image) | [Çekme resmi](#pull-image) | [Görüntü verilerini sil](#delete-image-data) | [İlkeleri Değiştir](#change-policies) |   [Görüntüleri imzala](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Sahip | X | X | X | X | X | X |  |  
| Katılımcı | X | X | X |  X | X | X |  |  
| Okuyucu | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| Acrimageimzalayan |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Kullanıcıları ve Hizmetleri ayırt etme

Her zaman izin uygulandığında en iyi yöntem, bir kişiye veya hizmete bir görevi gerçekleştirmek için en sınırlı sayıda izin sağlamaktır. Aşağıdaki izin kümeleri, insanların ve gözetimsiz hizmetler tarafından kullanılabilen bir özellik kümesini temsil eder.

### <a name="cicd-solutions"></a>CI/CD çözümleri

CI/CD çözümlerinde `docker build` komutları otomatikleştirmede `docker push` yetenekler gerekir. Bu gözetimsiz hizmet senaryolarında **Acrpush** rolünü atamayı öneririz. Bu rol, daha geniş **katkıda bulunan** rolünün aksine, hesabın diğer kayıt defteri işlemlerini gerçekleştirmesini veya Azure Resource Manager erişmesini önler.

### <a name="container-host-nodes"></a>Kapsayıcı konak düğümleri

Benzer şekilde, Kapsayıcılarınızı çalıştıran düğümlerin **Acrpull** rolü olması gerekir, ancak **okuyucu** becerileri gerektirmemelidir.

### <a name="visual-studio-code-docker-extension"></a>Docker uzantısını Visual Studio Code

Visual Studio Code [Docker uzantısı](https://code.visualstudio.com/docs/azure/docker)gibi araçlar için, kullanılabilir Azure Container kayıt defterlerini listelemek üzere ek kaynak sağlayıcısı erişimi gerekir. Bu durumda, kullanıcılarınıza **okuyucu** veya **katkıda bulunan** rolü erişimi sağlayın. Bu roller `docker pull`, `docker push`, `az acr list`, `az acr build`ve diğer özellikleri sağlar. 

## <a name="access-resource-manager"></a>Erişim Kaynak Yöneticisi

[Azure CLI](/cli/azure/)ile Azure Portal ve kayıt defteri yönetimi için Azure Resource Manager erişim gerekir. Örneğin, `az acr list` komutunu kullanarak kayıt defterlerinin listesini almak için, bu izin kümesine ihtiyacınız vardır. 

## <a name="create-and-delete-registry"></a>Kayıt defteri oluştur ve Sil

Azure Container Registry oluşturma ve silme özelliği.

## <a name="push-image"></a>Görüntü gönder

Bir görüntüyü `docker push` veya bir hele grafiği gibi başka bir [desteklenen yapıtı](container-registry-image-formats.md) bir kayıt defterine gönderebilirsiniz. Yetkili kimliği kullanarak kayıt defteriyle [kimlik doğrulaması](container-registry-authentication.md) gerektirir. 

## <a name="pull-image"></a>Çekme resmi

Bir kayıt defterinden, karantinaya alınmamış bir görüntü `docker pull` veya Held grafik gibi başka bir [desteklenen yapıt](container-registry-image-formats.md) çekebilirsiniz. Yetkili kimliği kullanarak kayıt defteriyle [kimlik doğrulaması](container-registry-authentication.md) gerektirir.

## <a name="delete-image-data"></a>Görüntü verilerini sil

[Kapsayıcı görüntülerini silme](container-registry-delete.md)veya HELI grafikleri gibi desteklenen diğer [yapıtları](container-registry-image-formats.md) bir kayıt defterinden silme özelliği.

## <a name="change-policies"></a>İlkeleri Değiştir

Bir kayıt defterinde ilkeleri yapılandırma özelliği. , Görüntü Temizleme, karantina etkinleştirme ve görüntü imzalama dahil olmak üzere ilkeler.

## <a name="sign-images"></a>Görüntüleri imzala

Genellikle bir hizmet sorumlusu kullanan otomatik bir işleme atanan görüntüleri imzalama özelliği. Bu izin, bir kayıt defterine güvenilir bir görüntü göndermeye olanak tanımak için genellikle [anında iletme görüntüsüyle](#push-image) birleştirilir. Ayrıntılar için bkz. [Azure Container Registry içerik güveni](container-registry-content-trust.md).

## <a name="next-steps"></a>Sonraki adımlar

* Azure Identity 'a [Azure Portal](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md)veya DIĞER Azure araçlarını kullanarak RBAC rolleri atama hakkında daha fazla bilgi edinin.

* Azure Container Registry için [kimlik doğrulama seçenekleri](container-registry-authentication.md) hakkında bilgi edinin.
