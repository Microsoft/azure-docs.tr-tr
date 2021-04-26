---
title: Azure Arc etkin veri Hizmetleri-bilinen sorunlar
description: Bilinen en son sorunlar
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029514"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Bilinen sorunlar-Azure Arc etkin veri Hizmetleri (Önizleme)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Mart 2021

### <a name="data-controller"></a>Veri denetleyicisi

- Azure portal ile doğrudan bağlanma modunda bir veri denetleyicisi oluşturabilirsiniz. Diğer Azure Arc etkin veri Hizmetleri araçlarıyla dağıtım desteklenmez. Özellikle, bu yayın sırasında aşağıdaki araçlardan herhangi biriyle doğrudan bağlanma modunda bir veri denetleyicisi dağıtamazsınız.
   - Azure Data Studio
   - Azure Data CLı ( `azdata` )
   - Kubernetes yerel araçları

   [Azure Arc Data Controller 'ı dağıtma | Doğrudan bağlanma modu](deploy-data-controller-direct-mode.md) , portalda veri denetleyicisinin nasıl oluşturulacağını açıklar. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc etkin PostgreSQL hiper ölçeği

- Doğrudan bağlanma modu için etkinleştirilen bir yay veri denetleyicisinde Azure Arc etkin bir Postgres hiper ölçek sunucu grubu dağıtmak desteklenmez.
- `--extensions`Ek uzantıları etkinleştirmek üzere bir sunucu grubu yapılandırması düzenlenirken parametreye geçersiz bir değer geçirilme, etkinleştirilmiş uzantıların listesini sunucu grubunun oluşturma zamanında yanlış olarak sıfırlar ve kullanıcının ek uzantı oluşturmasını engeller. Bu durumda, tek yapmanız gereken tek geçici çözüm, sunucu grubunu silip yeniden dağıtmanıza olanak sağlar.

## <a name="february-2021"></a>Şubat 2021

### <a name="data-controller"></a>Veri denetleyicisi

- Doğrudan bağlantı kümesi modu devre dışı

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc etkin PostgreSQL hiper ölçeği

- Zaman içinde bir noktaya geri yükleme, şu anda NFS depolamada desteklenmez.
- Pg_cron uzantısını aynı anda etkinleştirip yapılandırmak mümkün değildir. Bunun için iki komut kullanmanız gerekir. Bunu etkinleştirmek için bir komut ve onu yapılandırmak için bir komut. 

   Örnek:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   İlk komut, sunucu grubunun yeniden başlatılmasını gerektirir. Bu nedenle, ikinci komutu yürütmeden önce, sunucu grubunun durumunun güncelleştirmeden başlamaya geçirildiğinden emin olun. Yeniden başlatma tamamlanmadan önce ikinci komutu çalıştırırsanız, başarısız olur. Böyle bir durum söz konusu olduğunda birkaç dakika bekleyip ikinci komutu yeniden yürütün.

## <a name="introduced-prior-to-february-2021"></a>Şubat 2021 ' den önce sunulmuştur

### <a name="data-controller"></a>Veri denetleyicisi

- Azure Kubernetes Service (AKS) üzerinde Kubernetes sürüm 1.19. x desteklenmez.
- Kubernetes 1,19 üzerinde `containerd` desteklenmez.
- Azure 'daki veri denetleyicisi kaynağı şu anda bir Azure kaynağıdır. Silme gibi tüm güncelleştirmeler Kubernetes kümesine geri yayılmaz.
- Örnek adları 13 karakterden fazla olamaz
- Azure Arc veri denetleyicisi veya veritabanı örnekleri için yerinde yükseltme yok.
- Arc özellikli veri hizmetleri kapsayıcı görüntüleri imzalanmadı.  Kubernetes düğümlerinizi, imzasız kapsayıcı görüntülerine çekilmesine izin verecek şekilde yapılandırmanız gerekebilir.  Örneğin, kapsayıcı çalışma zamanı olarak Docker kullanıyorsanız, DOCKER_CONTENT_TRUST = 0 ortam değişkenini ayarlayabilir ve yeniden başlatabilirsiniz.  Diğer kapsayıcı çalışma zamanları [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration)'te olduğu gibi benzer seçeneklere sahiptir.
- Azure portal Azure Arc etkin SQL yönetilen örnekleri veya PostgreSQL hiper ölçek sunucu grupları oluşturulamıyor.
- Şimdilik, NFS kullanıyorsanız, `allowRunAsRoot` `true` Azure Arc veri denetleyicisini oluşturmadan önce dağıtım profili dosyanızda öğesini olarak ayarlamanız gerekir.
- Yalnızca SQL ve PostgreSQL oturum açma kimlik doğrulaması.  Azure Active Directory veya Active Directory desteği yok.
- Openshıft üzerinde bir veri denetleyicisi oluşturmak için gevşek güvenlik kısıtlamaları gerekir.  Ayrıntılar için belgelere bakın.
- Azure Arc veri denetleyicisi ve veritabanı örnekleri ile Azure Stack Hub üzerinde Azure Kubernetes hizmeti (AKS) altyapısı kullanıyorsanız, daha yeni bir Kubernetes sürümüne yükseltme desteklenmez. Kubernetes kümesini yükseltmeden önce Azure Arc veri denetleyicisi 'ni ve tüm veritabanı örneklerini kaldırın.
- [Birden çok kullanılabilirlik bölgesini](../../aks/availability-zones.md) kapsayan aks kümeleri, Azure Arc etkin veri Hizmetleri için şu anda desteklenmiyor. Bu sorundan kaçınmak için, Azure portal ' de AKS kümesi oluşturduğunuzda, bölgelerin kullanılabildiği bir bölgeyi seçerseniz seçim denetiminden tüm bölgeleri temizleyin. Aşağıdaki resme bakın:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Hiçbirini belirtmek için her bir bölgenin onay kutularını temizleyin.":::


## <a name="next-steps"></a>Sonraki adımlar

> **Yalnızca bir şeyi denemek mi istiyorsunuz?**  
> AKS, AWS elastik Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) veya bir Azure VM 'de [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ile hızlı bir şekilde çalışmaya başlayın.

- [İstemci araçları 'nı yükler](install-client-tools.md)
- [Azure Arc veri denetleyicisi oluşturma](create-data-controller.md) (önce istemci araçlarının yüklenmesini gerektirir)
- Azure [Arc üzerinde Azure SQL yönetilen örneği oluşturma](create-sql-managed-instance.md) (önce Azure Arc veri denetleyicisinin oluşturulmasını gerektirir)
- [Azure Arc 'Da PostgreSQL Için Azure veritabanı hiper ölçek sunucu grubu oluşturma](create-postgresql-hyperscale-server-group.md) (önce Azure Arc veri denetleyicisi oluşturulmasını gerektirir)
- [Azure hizmetleri için kaynak sağlayıcıları](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Sürüm notları-Azure Arc etkin veri Hizmetleri (Önizleme)](release-notes.md)
