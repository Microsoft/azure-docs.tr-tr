---
title: Azure Service Fabric kümelerini yükseltme
description: Azure Service Fabric kümenizi güncelleştirme seçenekleri hakkında bilgi edinin
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731126"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Azure Service Fabric kümelerini yükseltme ve güncelleştirme

Azure Service Fabric kümesi, sahip olduğunuz bir kaynaktır, ancak kısmen Microsoft tarafından yönetilir. Bu makalede, Azure Service Fabric kümenizin ne zaman ve nasıl güncelleştirileceğini açıklayan seçenekler açıklanmaktadır.

## <a name="automatic-versus-manual-upgrades"></a>Otomatik ve el ile yükseltmelere karşı

Service Fabric kümenizin her zaman [desteklenen bir çalışma zamanı sürümünü](service-fabric-versions.md)çalıştırdığından emin olmak önemlidir. Microsoft yeni bir Service Fabric sürümü duyurusunu her seferinde, bu tarihten en az 60 günden sonra, önceki sürüm *destek sonuna* kadar işaretlenir. Yeni yayınlar [Service Fabric ekip blogundan](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)duyurulur.

Kümenizin çalışma süresinin dolmasıyla önce on dört gün önce, kümenizi bir *Uyarı* sistem durumuna geçirir bir sistem durumu olayı oluşturulur. Küme, desteklenen bir çalışma zamanı sürümüne yükseltene kadar uyarı durumunda kalır.

Kümenizi, Microsoft tarafından yayımlandıklarında otomatik Service Fabric yükseltmelerini alacak şekilde ayarlayabilir veya şu anda Desteklenen sürümlerin bir listesinden el ile seçebilirsiniz. Bu seçenekler, Service Fabric kümesi kaynağınızın **Yapı yükseltmeleri** bölümünde bulunur.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Azure portal ' de küme kaynağınızın ' doku yükseltmeleri ' bölümünde otomatik veya el Ile yükseltmeler ' i seçin.":::

Ayrıca, küme yükseltme kipini ayarlayabilir ve [Kaynak Yöneticisi şablonu kullanarak](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)bir çalışma zamanı sürümü seçebilirsiniz.

Otomatik yükseltmeler önerilen yükseltme modudur, bu seçenek kümenizin desteklenen bir durumda ve en son düzeltmeler ve özelliklerden faydalarda kalmasını sağlarken, güncelleştirmeleri bir [dalga dağıtım](#wave-deployment-for-automatic-upgrades) stratejisi kullanarak iş yüklerinize en az kesintiye uğratan bir şekilde zamanlamanıza olanak sağlar.

## <a name="wave-deployment-for-automatic-upgrades"></a>Otomatik yükseltmeler için dalga dağıtımı

Dalga dağıtımı sayesinde, iş yükünüze bağlı olarak bir yükseltmenin vade düzeyini seçerek kümenize yükseltmenin kesintiye uğramasını en aza indirmenize olanak sağlayabilirsiniz. Örneğin,   ->    ->  bir çalışma zamanı yükseltmesinin uyumluluğunu üretim iş yüklerinize uygulamadan önce test etmek için çeşitli Service Fabric kümeleriniz için bir test aşaması *Üretim* dalga dağıtımı işlem hattı ayarlayabilirsiniz.

Dalga dağıtımını kabul etmek için kümeniz için aşağıdaki dalga değerlerinden birini belirtin (dağıtım şablonunda):

* **Dalga 0**: yeni bir Service Fabric derlemesi serbest bırakıldıktan sonra kümeler güncelleştirilir. Test/dev kümelerine yöneliktir.
* **Dalga 1**: yeni bir derleme yayımlandıktan sonra kümeler bir hafta (yedi gün) güncelleştirilir. Üretim öncesi/hazırlama kümeleri için tasarlanmıştır.
* **Dalga 2**: yeni bir derleme yayımlandıktan sonra kümeler iki hafta (14 gün) güncelleştirilir. Üretim kümeleri için tasarlanmıştır.

Bir küme yükseltmesi başarısız olursa daha fazla yardım için bağlantılarla e-posta bildirimlerine kaydolabilirsiniz. Kullanmaya başlamak için [otomatik yükseltmeler için bkz. dalga dağıtımı](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) .

## <a name="phases-of-automatic-upgrade"></a>Otomatik yükseltme aşamaları

Microsoft, bir Azure kümesinde çalışan Service Fabric çalışma zamanı kodu ve yapılandırmasını korur. Yazılım için gerektiğinde otomatik olarak izlenen yükseltmeler gerçekleştiririz. Bu yükseltmeler kod, yapılandırma veya her ikisi olabilir. Uygulamalarınızda bu yükseltmelerin etkilerini en aza indirmek için, aşağıdaki aşamalarda gerçekleştirilir:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1. Aşama: bir yükseltme tüm küme sistem durumu ilkeleri kullanılarak gerçekleştirilir

Bu aşamada, yükseltmeler tek seferde bir yükseltme etki alanına devam eder ve kümede çalışan uygulamalar herhangi bir kesinti olmadan çalışmaya devam eder. Küme sistem durumu ilkeleri (düğüm durumu ve uygulama durumu için), yükseltme sırasında ile dağıtılır.

Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınır ve aboneliğin sahibine bir e-posta gönderilir. E-postada aşağıdaki bilgiler bulunur:

* Bir küme yükseltmesini geri almak zorunda olduğumuz bildirim.
* Varsa önerilen düzeltmeleri al eylemleri.
* 2. aşama yürütülene kadar gün sayısı (*n*).

Tüm yükseltmelerin altyapı nedenleriyle başarısız olması durumunda aynı yükseltmeyi birkaç kez yürütmeye çalışırız. E-postanın gönderildiği tarihten itibaren *n* gün sonra 2. aşama 'a devam ediyoruz.

Küme sistem durumu ilkeleri karşılanıyorsa, yükseltme başarılı ve tamamlandı olarak kabul edilir. Bu durum, ilk yükseltme sırasında veya bu aşamada herhangi bir yükseltme yeniden tamamlandıktan kaynaklanabilir. Başarılı bir çalıştırmaya ilişkin e-posta onayı yoktur ve aşırı e-postaların gönderilmesini önleyin. E-posta almak normal işlemlere özel bir durum gösterir. Uygulama kullanılabilirliğini etkilemeden küme yükseltmelerinden çoğunun başarılı olmasını bekliyoruz.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2. Aşama: bir yükseltme yalnızca varsayılan sistem durumu ilkeleri kullanılarak gerçekleştirilir

Bu aşamadaki sistem durumu ilkeleri, yükseltmenin başlangıcında sağlıklı olan uygulamaların sayısı yükseltme sürecinde aynı kalmaya devam edecek şekilde ayarlanır. 1. aşamada, Aşama 2 yükseltmeleri tek seferde bir yükseltme etki alanına devam eder ve kümede çalışan uygulamalar herhangi bir kesinti olmadan çalışmaya devam eder. Küme sistem durumu ilkeleri (düğüm durumunun bir birleşimi ve kümede çalışan tüm uygulamaların sistem durumu), yükseltme sırasında öğesine dağıtılır.

Etkin küme durumu ilkeleri karşılanmazsa, yükseltme geri alınır. Daha sonra aboneliğin sahibine bir e-posta gönderilir. E-postada aşağıdaki bilgiler bulunur:

* Bir küme yükseltmesini geri almak zorunda olduğumuz bildirim.
* Varsa önerilen düzeltmeleri al eylemleri.
* 2. aşama yürütülene kadar gün sayısı (n).

Tüm yükseltmelerin altyapı nedenleriyle başarısız olması durumunda aynı yükseltmeyi birkaç kez yürütmeye çalışırız. Bir anımsatıcı e-postası, n gün önce birkaç gün önce gönderilir. E-postanın gönderildiği tarihten itibaren n gün sonra 3. aşama 'e devam ediyoruz. 2. aşama 'a gönderdiğiniz e-postaların önemli bir şekilde alınması ve düzeltici eylemlerin alınması gerekir.

Küme sistem durumu ilkeleri karşılanıyorsa, yükseltme başarılı ve tamamlandı olarak kabul edilir. Bu, ilk yükseltme sırasında veya bu aşamada herhangi bir yükseltme yeniden tamamlandıktan kaynaklanabilir. Başarılı bir çalıştırmanın e-posta onayı yoktur.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3. Aşama: bir yükseltme, agresif sistem durumu ilkeleri kullanılarak gerçekleştirilir

Bu aşamadaki bu sistem durumu ilkeleri, uygulamaların sistem durumu yerine yükseltmenin tamamlanmasına yöneliktir. Bu aşamada birkaç küme yükseltmesi sona erdir. Kümeniz bu aşamayı alıyorsa, uygulamanız sağlıksız hale gelmiş ve/veya kullanılabilirliği kaybetmesi iyi bir şansınız olur.

Diğer iki aşamaya benzer şekilde, aşama 3 yükseltmeleri aynı anda bir yükseltme etki alanı devam etsin.

Küme sistem durumu ilkeleri karşılanmazsa, yükseltme geri alınır. Tüm yükseltmelerin altyapı nedenleriyle başarısız olması durumunda aynı yükseltmeyi birkaç kez yürütmeye çalışırız. Bundan sonra, küme sabitlenir, böylece artık destek ve/veya yükseltme alınmaz.

Bu bilgileri içeren bir e-posta, sorun düzeltici eylemlerle birlikte abonelik sahibine gönderilir. Aşama 3 ' ün başarısız olduğu bir duruma getirmek için herhangi bir küme beklemeiyoruz.

Küme sistem durumu ilkeleri karşılanıyorsa, yükseltme başarılı ve tamamlandı olarak kabul edilir. Bu, ilk yükseltme sırasında veya bu aşamada herhangi bir yükseltme yeniden tamamlandıktan kaynaklanabilir. Başarılı bir çalıştırmanın e-posta onayı yoktur.

## <a name="custom-policies-for-manual-upgrades"></a>El ile yükseltmelere yönelik özel ilkeler

El ile küme yükseltmeleri için özel ilkeler belirtebilirsiniz. Bu ilkeler, her yeni çalışma zamanı sürümünü seçtiğiniz her seferinde uygulanır ve bu, sistemi kümenizin yükseltmesini başlatma konusunda tetikler. İlkeleri geçersiz kılamazsınız, varsayılanlar kullanılır. Daha fazla bilgi için bkz. [el ile yükseltmeler için özel Ilkeler ayarlama](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades).

## <a name="other-cluster-updates"></a>Diğer küme güncelleştirmeleri

Çalışma zamanının yükseltilmesi dışında, aşağıdaki gibi, kümenizi güncel tutmak için gerçekleştirmeniz gerekebilecek birkaç işlem vardır:

### <a name="managing-certificates"></a>Sertifikaları yönetme

Service Fabric, küme düğümleri arasındaki iletişimleri güvenli hale getirmek ve istemcilerin kimliğini doğrulamak için bir küme oluşturduğunuzda belirttiğiniz [X. 509.440 sunucu sertifikalarını](service-fabric-cluster-security.md) kullanır. [Azure Portal](https://portal.azure.com) veya PowerShell/Azure CLI kullanarak küme ve istemci için sertifika ekleyebilir, güncelleştirebilir veya silebilirsiniz.  Daha fazla bilgi edinmek için [Sertifika Ekle veya Kaldır](service-fabric-cluster-security-update-certs-azure.md) 'ı okuyun

### <a name="opening-application-ports"></a>Uygulama bağlantı noktalarını açma

Düğüm türüyle ilişkili Load Balancer kaynak özelliklerini değiştirerek uygulama bağlantı noktalarını değiştirebilirsiniz. Azure portal kullanabilir ya da PowerShell/Azure CLı kullanabilirsiniz. Daha fazla bilgi için, [bir kümenin açık uygulama bağlantı noktalarını](create-load-balancer-rule.md)okuyun.

### <a name="defining-node-properties"></a>Düğüm özelliklerini tanımlama

Bazen bazı iş yüklerinin kümedeki belirli düğüm türlerinde çalışmasını sağlamak isteyebilirsiniz. Örneğin, bazı iş yükleri GPU 'Lar ya da SSD 'Ler gerektirebilir, diğerleri de olmayabilir. Kümedeki düğüm türlerinin her biri için, küme düğümlerine özel düğüm özellikleri ekleyebilirsiniz. Yerleştirme kısıtlamaları, bir veya daha fazla düğüm özelliği için seçim yapan ayrı hizmetlere eklenmiş deyimlerdir. Yerleştirme kısıtlamaları, hizmetlerin nerede çalışacağını tanımlar.

Yerleştirme kısıtlamalarının, düğüm özelliklerinin kullanımı ve bunların nasıl tanımlanacağı hakkında ayrıntılar için, [düğüm özelliklerini ve yerleştirme kısıtlamalarını](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)okuyun.

### <a name="adding-capacity-metrics"></a>Kapasite ölçümleri ekleme

Düğüm türlerinin her biri için, yük bildirmek üzere uygulamalarınızda kullanmak istediğiniz özel kapasite ölçümlerini ekleyebilirsiniz. Yük raporlamak için kapasite ölçümlerinin kullanımı hakkında daha fazla bilgi için, [küme](service-fabric-cluster-resource-manager-cluster-description.md) ve [ölçümleri ve yük](service-fabric-cluster-resource-manager-metrics.md)hakkındaki bilgileri Kaynak Yöneticisi Service Fabric kümesine bakın.

### <a name="customizing-settings-for-your-cluster"></a>Kümenizin ayarlarını özelleştirme

Küme ve düğüm özelliklerinin güvenilirlik düzeyi gibi birçok farklı yapılandırma ayarı bir kümede özelleştirilebilir. Daha fazla bilgi için [Service Fabric Cluster Fabric ayarlarını](service-fabric-cluster-fabric-settings.md)okuyun.

### <a name="upgrading-os-images-for-cluster-nodes"></a>Küme düğümleri için işletim sistemi görüntülerini yükseltme

Service Fabric kümesi düğümleriniz için otomatik işletim sistemi görüntüsü yükseltmelerini etkinleştirmek en iyi uygulamadır. Bunu yapmak için birkaç küme gereksinimi ve yapılacak adımlar vardır. Başka bir seçenek de, bir Service Fabric kümesinde kapalı kalma süresi olmadan işletim sistemi düzeltme eki uygulamayı otomatikleştiren bir Service Fabric uygulaması olan Patch düzenleme uygulamasını (POA) kullanmaktır. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [Service Fabric kümenizdeki Windows işletim sistemini düzeltme eki](service-fabric-patch-orchestration-application.md)uygulama.

## <a name="next-steps"></a>Sonraki adımlar

* [Service Fabric yükseltmelerini yönetme](service-fabric-cluster-upgrade-version-azure.md)
* [Service Fabric kümesi ayarlarınızı](service-fabric-cluster-fabric-settings.md) özelleştirin
* [Kümenizi ve dışarı ölçeklendirin](service-fabric-cluster-scale-in-out.md)
* [Uygulama yükseltmeleri](service-fabric-application-upgrade.md) hakkında bilgi edinin
