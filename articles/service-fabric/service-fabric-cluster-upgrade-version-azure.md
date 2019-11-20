---
title: Bir kümenin Azure Service Fabric sürümünü yükseltme
description: Küme güncelleştirme modunu ayarlama, sertifika yükseltme, uygulama bağlantı noktaları ekleme, işletim sistemi düzeltme ekleri yapma gibi bir Service Fabric kümesini çalıştıran Service Fabric kodu ve/veya yapılandırmayı yükseltin. Yükseltmeler gerçekleştirildiğinde ne bekleyebiliriz?
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: 067bf654928240ffe9651d4acf1933a81e631146
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013352"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Bir kümenin Service Fabric sürümünü yükseltme

Tüm modern bir sistem için, ürününüzün uzun süreli başarısını sağlamak üzere yükselme için tasarlanmaya yönelik bir anahtar vardır. Azure Service Fabric kümesi, sahip olduğunuz ancak kısmen Microsoft tarafından yönetilen bir kaynaktır. Bu makalede, Azure kümenizde çalışan Service Fabric sürümünün nasıl yükseltileceği açıklanır.

Kümenizi, Microsoft tarafından yayımlandıklarında otomatik yapı yükseltmeleri alacak şekilde ayarlayabilir veya kümenizin açık olmasını istediğiniz desteklenen bir yapı sürümünü seçebilirsiniz.

Bu, portalda "upgradeMode" küme yapılandırmasını ayarlayarak veya dinamik bir kümede oluşturma sırasında veya sonrasında Kaynak Yöneticisi kullanarak yapabilirsiniz 

> [!NOTE]
> Kümenizin, her zaman desteklenen bir yapı sürümü çalıştırdığından emin olun. Service Fabric 'in yeni bir sürümünün yayınlanmasıyla ilgili olarak, önceki sürüm, bu tarihten en az 60 günden sonra destek sonuna kadar işaretlenir. Yeni yayınlar [Service Fabric ekip blogu '](https://blogs.msdn.microsoft.com/azureservicefabric/)na duyurulur. Yeni sürüm daha sonra seçmek üzere kullanılabilir. 
> 
> 

14 gün önce Kümenizin çalıştığı sürümden önce, kümenizi bir uyarı sistem durumuna yerleştiren bir sistem durumu olayı oluşturulur. Küme, desteklenen bir Fabric sürümüne yükseltene kadar uyarı durumunda kalır.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Azure portal yükseltme modunu ayarlama
Kümeyi oluştururken kümeyi otomatik veya el ile ayarlayabilirsiniz.

![Create_Manualmode][Create_Manualmode]

Yönetim deneyimini kullanarak, canlı bir kümede kümesini otomatik veya el ile ayarlayabilirsiniz. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Portal aracılığıyla manuel moda ayarlanmış bir kümedeki yeni bir sürüme yükseltme.
Yeni bir sürüme yükseltmek için, tek yapmanız gereken, açılan listeden kullanılabilir sürümü seçip kaydetmeniz gerekir. Doku yükseltme otomatik olarak açılır. Küme sistem durumu ilkeleri (düğüm durumunun bir birleşimi ve kümede çalışan tüm uygulamaların sistem durumu), yükseltme sırasında öğesine dağıtılır.

Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınır. Bu özel durum ilkelerini ayarlama hakkında daha fazla bilgi için bu belgeyi aşağı kaydırın. 

Geri alma işlemine neden olan sorunları düzelttikten sonra, daha önce yaptığınız adımları izleyerek yükseltmeyi yeniden başlatmanız gerekir.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak yükseltme modunu ayarlama
"UpgradeMode" yapılandırmasını Microsoft. ServiceFabric/kümeler kaynak tanımına ekleyin ve "clusterCodeVersion" değerini aşağıda gösterildiği gibi desteklenen yapı sürümlerinden birine ayarlayın ve ardından şablonu dağıtın. "UpgradeMode" için geçerli değerler "Manual" veya "Automatic"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Bir Kaynak Yöneticisi şablonu aracılığıyla el Ile moduna ayarlanmış bir kümedeki yeni bir sürüme yükseltme.
Küme El Ile modundayken, yeni bir sürüme yükseltmek için, "clusterCodeVersion" öğesini desteklenen bir sürüm olarak değiştirin ve dağıtın. Şablon dağıtımı, doku yükseltmesiyle otomatik olarak açılır. Küme sistem durumu ilkeleri (düğüm durumunun bir birleşimi ve kümede çalışan tüm uygulamaların sistem durumu), yükseltme sırasında öğesine dağıtılır.

Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınır.  

Geri alma işlemine neden olan sorunları düzelttikten sonra, daha önce yaptığınız adımları izleyerek yükseltmeyi yeniden başlatmanız gerekir.

## <a name="set-custom-health-polices-for-upgrades"></a>Yükseltmeler için özel durum ilkelerini ayarlama
Doku yükseltme için özel sistem durumu ilkeleri belirtebilirsiniz. Kümenizi otomatik yapı yükseltmeleri olarak ayarladıysanız, bu ilkeler [Otomatik doku yükseltmelerinden 1. aşamasına](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)uygulanır.
Kümenizi El Ile doku yükseltmeleri için ayarladıysanız, bu ilkeler, kümeinizdeki doku yükseltmesini başlatma amacıyla sistemi tetikleyen yeni bir sürüm seçtiğinizde uygulanır. İlkeleri geçersiz kılamazsınız, varsayılanlar kullanılır.

Gelişmiş yükseltme ayarlarını seçerek özel sistem durumu ilkelerini belirtebilir veya "yapı yükseltme" dikey penceresindeki geçerli ayarları gözden geçirebilirsiniz. Nasıl yapılır hakkında aşağıdaki resmi gözden geçirin. 

![Özel sistem durumu ilkelerini yönetme][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Belirli bir abonelik için tüm ortamlar için kullanılabilir tüm sürümleri listeleyin
Aşağıdaki komutu çalıştırın ve buna benzer bir çıktı almalısınız.

"supportExpiryUtc", belirli bir yayının ne zaman sona ertiğine veya süresinin dolduğunu bildirir. En son sürümde geçerli bir tarih yok; "9999-12-31T23:59:59.9999999" değeri vardır; bu, yalnızca sona erme tarihi henüz ayarlanmamış anlamına gelir.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric kümesi doku ayarlarından](service-fabric-cluster-fabric-settings.md) bazılarını özelleştirmeyi öğrenin
* [Kümenizi ve dışarı ölçeklendirmeyi nasıl ölçeklentireceğinizi](service-fabric-cluster-scale-up-down.md) öğrenin
* [Uygulama yükseltmeleri](service-fabric-application-upgrade.md) hakkında bilgi edinin

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
