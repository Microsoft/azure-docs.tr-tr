---
title: Azure Service Fabric kümesini yükseltme
description: Azure Service Fabric kümesinin sürümünü veya yapılandırmasını yükseltme hakkında bilgi edinin.  Bu makalede küme güncelleştirme modunu ayarlama, sertifika yükseltme, uygulama bağlantı noktaları ekleme, işletim sistemi düzeltme ekleri yapma ve yükseltmeler gerçekleştirildiğinde bekleyebileceğiniz özellikler açıklanmaktadır.
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
ms.openlocfilehash: 161c720fbcc9370aaf273b241e88a7184f47371b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013313"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Azure Service Fabric kümesini yükseltme ve güncelleştirme

Tüm modern bir sistem için, ürününüzün uzun süreli başarısını sağlamak üzere yükselme için tasarlanmaya yönelik bir anahtar vardır. Azure Service Fabric kümesi, sahip olduğunuz ancak kısmen Microsoft tarafından yönetilen bir kaynaktır. Bu makalede, otomatik olarak yönetilen ve neleri yapılandırabileceğiniz açıklanmaktadır.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Kümenizde çalışan yapı sürümünü denetleme

Kümenizin, her zaman [desteklenen bir yapı sürümü](service-fabric-versions.md) çalıştırdığından emin olun. Service Fabric 'in yeni bir sürümünün yayınlanmasıyla ilgili olarak, önceki sürüm, bu tarihten en az 60 günden sonra destek sonuna kadar işaretlenir. Yeni yayınlar Service Fabric ekip blogu ' na duyurulur. Yeni sürüm daha sonra seçmek üzere kullanılabilir.

14 gün önce Kümenizin çalıştığı sürümden önce, kümenizi bir uyarı sistem durumuna yerleştiren bir sistem durumu olayı oluşturulur. Küme, desteklenen bir Fabric sürümüne yükseltene kadar uyarı durumunda kalır.

Kümenizi, Microsoft tarafından yayımlandıklarında otomatik yapı yükseltmeleri alacak şekilde ayarlayabilir veya kümenizin açık olmasını istediğiniz desteklenen bir yapı sürümünü seçebilirsiniz.  Daha fazla bilgi edinmek için, [kümenizin Service Fabric sürümünü yükseltin](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Otomatik yükseltmeler sırasında yapı yükseltme davranışı
Microsoft, bir Azure kümesinde çalışan yapı kodunu ve yapılandırmayı korur. Yazılım için gerektiğinde otomatik olarak izlenen yükseltmeler gerçekleştiririz. Bu yükseltmeler kod, yapılandırma veya her ikisi olabilir. Bu yükseltmeler nedeniyle uygulamanızın hiçbir etkisi veya en az etkisi olmadığından emin olmak için, yükseltmeleri aşağıdaki aşamalarda gerçekleştirdik:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1\. Aşama: bir yükseltme tüm küme sistem durumu ilkeleri kullanılarak gerçekleştirilir
Bu aşamada, yükseltmeler tek seferde bir yükseltme etki alanına devam eder ve kümede çalışan uygulamalar herhangi bir kesinti olmadan çalışmaya devam eder. Küme sistem durumu ilkeleri (düğüm durumunun bir birleşimi ve kümede çalışan tüm uygulamaların sistem durumu), yükseltme sırasında öğesine dağıtılır.

Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınır. Daha sonra aboneliğin sahibine bir e-posta gönderilir. E-postada aşağıdaki bilgiler bulunur:

* Bir küme yükseltmesini geri almak zorunda olduğumuz bildirim.
* Varsa önerilen düzeltmeleri al eylemleri.
* 2\. aşama yürütülene kadar gün sayısı (n).

Tüm yükseltmelerin altyapı nedenleriyle başarısız olması durumunda aynı yükseltmeyi birkaç kez yürütmeye çalışırız. E-postanın gönderildiği tarihten itibaren n gün sonra 2. Aşama ' a devam ediyoruz.

Küme sistem durumu ilkeleri karşılanıyorsa, yükseltme başarılı ve tamamlandı olarak kabul edilir. Bu, ilk yükseltme sırasında veya bu aşamada herhangi bir yükseltme yeniden tamamlandıktan kaynaklanabilir. Başarılı bir çalıştırmanın e-posta onayı yoktur. Bu, çok sayıda e-posta göndermekten kaçınmaktır. bir e-postanın alınması, normal için bir özel durum olarak görülenmelidir. Uygulama kullanılabilirliğini etkilemeden küme yükseltmelerinden çoğunun başarılı olmasını bekliyoruz.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2\. Aşama: bir yükseltme yalnızca varsayılan sistem durumu ilkeleri kullanılarak gerçekleştirilir
Bu aşamadaki sistem durumu ilkeleri, yükseltmenin başlangıcında sağlıklı olan uygulamaların sayısı yükseltme işlemi süresince aynı kalır ve bu şekilde ayarlanır. 1\. aşamada, Aşama 2 yükseltmeleri tek seferde bir yükseltme etki alanına devam eder ve kümede çalışan uygulamalar herhangi bir kesinti olmadan çalışmaya devam eder. Küme sistem durumu ilkeleri (düğüm durumunun bir birleşimi ve kümede çalışan tüm uygulamaların sistem durumu), yükseltme süresince ' a dağıtılır.

Etkin küme durumu ilkeleri karşılanmazsa, yükseltme geri alınır. Daha sonra aboneliğin sahibine bir e-posta gönderilir. E-postada aşağıdaki bilgiler bulunur:

* Bir küme yükseltmesini geri almak zorunda olduğumuz bildirim.
* Varsa önerilen düzeltmeleri al eylemleri.
* 2\. aşama yürütülene kadar gün sayısı (n).

Tüm yükseltmelerin altyapı nedenleriyle başarısız olması durumunda aynı yükseltmeyi birkaç kez yürütmeye çalışırız. Bir anımsatıcı e-postası, n gün önce birkaç gün önce gönderilir. E-postanın gönderildiği tarihten itibaren n gün sonra 3. aşama 'e devam ediyoruz. 2\. aşama 'a gönderdiğiniz e-postaların önemli bir şekilde alınması ve düzeltici eylemlerin alınması gerekir.

Küme sistem durumu ilkeleri karşılanıyorsa, yükseltme başarılı ve tamamlandı olarak kabul edilir. Bu, ilk yükseltme sırasında veya bu aşamada herhangi bir yükseltme yeniden tamamlandıktan kaynaklanabilir. Başarılı bir çalıştırmanın e-posta onayı yoktur.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3\. Aşama: bir yükseltme, agresif sistem durumu ilkeleri kullanılarak gerçekleştirilir
Bu aşamadaki bu sistem durumu ilkeleri, uygulamaların sistem durumu yerine yükseltmenin tamamlanmasına yöneliktir. Bu aşamada birkaç küme yükseltmesi sona erdir. Kümeniz bu aşamayı alıyorsa, uygulamanız sağlıksız hale gelmiş ve/veya kullanılabilirliği yitiriyor olabilir.

Diğer iki aşamaya benzer şekilde, aşama 3 yükseltmeleri aynı anda bir yükseltme etki alanı devam etsin.

Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınır. Tüm yükseltmelerin altyapı nedenleriyle başarısız olması durumunda aynı yükseltmeyi birkaç kez yürütmeye çalışırız. Bundan sonra, küme sabitlenir, böylece artık destek ve/veya yükseltme alınmaz.

Bu bilgileri içeren bir e-posta, sorun düzeltici eylemlerle birlikte abonelik sahibine gönderilir. Aşama 3 ' ün başarısız olduğu bir duruma getirmek için herhangi bir küme beklemeiyoruz.

Küme sistem durumu ilkeleri karşılanıyorsa, yükseltme başarılı ve tamamlandı olarak kabul edilir. Bu, ilk yükseltme sırasında veya bu aşamada herhangi bir yükseltme yeniden tamamlandıktan kaynaklanabilir. Başarılı bir çalıştırmanın e-posta onayı yoktur.

## <a name="manage-certificates"></a>Sertifikaları yönetme
Service Fabric, küme düğümleri arasındaki iletişimleri güvenli hale getirmek ve istemcilerin kimliğini doğrulamak için bir küme oluşturduğunuzda belirttiğiniz [X. 509.440 sunucu sertifikalarını](service-fabric-cluster-security.md) kullanır. [Azure Portal](https://portal.azure.com) veya PowerShell/Azure CLI kullanarak küme ve istemci için sertifika ekleyebilir, güncelleştirebilir veya silebilirsiniz.  Daha fazla bilgi edinmek için [Sertifika Ekle veya Kaldır](service-fabric-cluster-security-update-certs-azure.md) 'ı okuyun

## <a name="open-application-ports"></a>Açık uygulama bağlantı noktaları
Düğüm türüyle ilişkili Load Balancer kaynak özelliklerini değiştirerek uygulama bağlantı noktalarını değiştirebilirsiniz. Azure portal kullanabilir ya da PowerShell/Azure CLı kullanabilirsiniz. Daha fazla bilgi için, [bir kümenin açık uygulama bağlantı noktalarını](create-load-balancer-rule.md)okuyun.

## <a name="define-node-properties"></a>Düğüm özelliklerini tanımla
Bazen bazı iş yüklerinin kümedeki belirli düğüm türlerinde çalışmasını sağlamak isteyebilirsiniz. Örneğin, bazı iş yükleri GPU 'Lar ya da SSD 'Ler gerektirebilir, diğerleri de olmayabilir. Kümedeki düğüm türlerinin her biri için, küme düğümlerine özel düğüm özellikleri ekleyebilirsiniz. Yerleştirme kısıtlamaları, bir veya daha fazla düğüm özelliği için seçim yapan ayrı hizmetlere eklenmiş deyimlerdir. Yerleştirme kısıtlamaları, hizmetlerin nerede çalışacağını tanımlar.

Yerleştirme kısıtlamalarının, düğüm özelliklerinin kullanımı ve bunların nasıl tanımlanacağı hakkında ayrıntılar için, [düğüm özelliklerini ve yerleştirme kısıtlamalarını](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)okuyun.

## <a name="add-capacity-metrics"></a>Kapasite ölçümleri ekleme
Düğüm türlerinin her biri için, yük bildirmek üzere uygulamalarınızda kullanmak istediğiniz özel kapasite ölçümlerini ekleyebilirsiniz. Yük raporlamak için kapasite ölçümlerinin kullanımı hakkında daha fazla bilgi için, [küme](service-fabric-cluster-resource-manager-cluster-description.md) ve [ölçümleri ve yük](service-fabric-cluster-resource-manager-metrics.md)hakkındaki bilgileri Kaynak Yöneticisi Service Fabric kümesine bakın.

## <a name="set-health-policies-for-automatic-upgrades"></a>Otomatik yükseltmeler için sistem durumu ilkeleri ayarlama
Doku yükseltme için özel sistem durumu ilkeleri belirtebilirsiniz. Kümenizi otomatik yapı yükseltmeleri olarak ayarladıysanız, bu ilkeler otomatik doku yükseltmelerinden 1. aşamasına uygulanır.
Kümenizi El Ile doku yükseltmeleri için ayarladıysanız, bu ilkeler, kümeinizdeki doku yükseltmesini başlatma amacıyla sistemi tetikleyen yeni bir sürüm seçtiğinizde uygulanır. İlkeleri geçersiz kılamazsınız, varsayılanlar kullanılır.

Gelişmiş yükseltme ayarlarını seçerek özel sistem durumu ilkelerini belirtebilir veya "yapı yükseltme" dikey penceresindeki geçerli ayarları gözden geçirebilirsiniz. Nasıl yapılır hakkında aşağıdaki resmi gözden geçirin. 

![Özel sistem durumu ilkelerini yönetme][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Kümeniz için doku ayarlarını özelleştirme
Küme ve düğüm özelliklerinin güvenilirlik düzeyi gibi birçok farklı yapılandırma ayarı bir kümede özelleştirilebilir. Daha fazla bilgi için [Service Fabric Cluster Fabric ayarlarını](service-fabric-cluster-fabric-settings.md)okuyun.

## <a name="patch-the-os-in-the-cluster-nodes"></a>Küme düğümlerinde işletim sistemini yama
Düzeltme Eki düzenleme uygulaması (POA), kapalı kalma süresi olmadan bir Service Fabric kümesinde işletim sistemi düzeltme eki uygulamayı otomatikleştiren Service Fabric bir uygulamadır. [Windows Için düzeltme eki düzenleme uygulaması](service-fabric-patch-orchestration-application.md) , her zaman kullanılabilir durumda tutulması sırasında düzeltme eklerini düzenli olarak yüklemek üzere kümenize dağıtılabilir.


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
