---
title: Azure Arc etkin sunucularını planlamak ve dağıtmak
description: Azure 'da önemli güvenlik, yönetim ve izleme özellikleri yapılandırmasını basitleştirmek için Azure Arc etkin sunucularına çok sayıda makinenin nasıl etkinleştirileceğini öğrenin.
ms.date: 04/21/2021
ms.topic: conceptual
ms.openlocfilehash: e3f8fe410da56f627ceab5f17c980f2daa1a262c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831987"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Yay özellikli sunucuları planlayın ve dağıtın

Bir BT Altyapısı hizmetinin veya iş uygulamasının dağıtımı, tüm şirketler için zorluk sergilemektir. Bunu iyi şekilde yürütmek ve hoş geldiniz sürprizlerini ve planlanmamış maliyetleri önlemek için, mümkün olduğunca uygun olduğundan emin olmak için kapsamlı bir plan yapmanız gerekir. Azure Arc etkin sunucularının herhangi bir ölçekte dağıtılmasını planlamak için, görevleri başarıyla tamamlaması için karşılanması gereken tasarım ve dağıtım ölçütünü kapsamalıdır.

Dağıtımın sorunsuz bir şekilde devam edebilmesi için planınızın açık bir şekilde anlaşılmaları gerekir:

* Roller ve sorumluluklar.
* Ağ ve sistem gereksinimlerini karşıladığından emin olmak için fiziksel sunucuların veya sanal makinelerin envanterini çıkarın.
* Başarılı dağıtımı ve devam eden yönetimi etkinleştirmek için gereken yetenek kümesi ve eğitim.
* Kabul ölçütleri ve başarısını nasıl izlediğiniz.
* Dağıtımları otomatik hale getirmek için kullanılacak araçlar veya Yöntemler.
* Gecikme, kesintiler vb. önlemek için riskler ve risk azaltma planları.
* Dağıtım sırasında kesintiye uğramaktan kaçının.
* Önemli bir sorun oluştuğunda ilerletme yolu nedir?

Bu makalenin amacı, ortamınızdaki birden çok üretim fiziksel sunucusunda veya sanal makinelerde Azure Arc etkin sunucularının başarılı bir dağıtımına hazırlandığınızdan emin olunması sağlamaktır.

## <a name="prerequisites"></a>Önkoşullar

* Makineleriniz bağlı makine Aracısı için [desteklenen bir işletim sistemi](agent-overview.md#supported-operating-systems) çalıştırır.
* Makineleriniz, şirket içi ağınızdan veya diğer bulut ortamlarınızdan doğrudan veya bir ara sunucu aracılığıyla Azure 'daki kaynaklara bağlantı sağlar.
* Yay etkin sunucular bağlı makine aracısını yüklemek ve yapılandırmak için makinelerde yükseltilmiş (yani, bir yönetici veya kök olarak) bir hesap.
* Makineleri eklemek için, **Azure bağlı makine ekleme** rolünün bir üyesidir.
* Bir makineyi okumak, değiştirmek ve silmek için, **Azure bağlı makine kaynak yöneticisi** rolünün bir üyesi olursunuz.

## <a name="pilot"></a>Pilot

Tüm üretim makinelerine dağıtım yapmadan önce, bu dağıtım işlemini ortamınıza göre benimsemeden önce değerlendirerek başlayın. Pilot için, şirketlerin iş yürütme yeteneği açısından önemli olmayan makinelerin temsili bir örneklemesini belirler. Pilot uygulamayı çalıştırmaya yetecek kadar zaman izin verdiğinizden emin olmak ve etkisini değerlendirmek için en az 30 gün önerilir.

Pilot 'ın kapsamını ve ayrıntılarını açıklayan resmi bir plan oluşturun. Aşağıda, başlamanıza yardımcı olmak için planın neleri içermesi gerektiğine ilişkin bir örnek verilmiştir.

* Hedefler-bir pilot uygulamanın gerekli olduğu kararına yol gösteren iş ve teknik sürücüleri açıklar.
* Seçim ölçütleri-çözümün bir pilot aracılığıyla ne şekilde gösterilolacağını seçmek için kullanılan ölçütü belirtir.
* Kapsam-çözüm bileşenleri, beklenen zamanlama, pilot süre ve hedeflenecek makine sayısı dahil ancak bunlarla sınırlı olmamak üzere pilot 'un kapsamını açıklar.
* Başarı ölçütleri ve ölçümleri-pilot 'ın başarı ölçütlerini ve başarı düzeyini belirlemek için kullanılan belirli ölçüleri tanımlayın.
* Eğitim planı-pilot sırasında Azure ve BT Hizmetleri ile ilgili eğitim sistemi mühendisleri, Yöneticiler vb. için plan açıklanmıştır.
* Geçiş planı-pilot 'dan üretime geçişe kılavuzluk etmek için kullanılan stratejiyi ve kriterleri açıklar.
* Geri alma-dağıtım öncesi durumuna bir pilot geri dönme yordamlarını açıklar.
* Riskler-pilot yürütmek ve üretim dağıtımıyla ilişkilendirmek için tüm belirtilen riskleri listeleyin.

## <a name="phase-1-build-a-foundation"></a>1. Aşama: temel derleme

Bu aşamada, sistem mühendisleri veya yöneticileri, makinelerinizi yay özellikli sunucular ve diğer Azure hizmetleriyle yönetim için etkinleştirmeden önce, kuruluşlarınızın Azure aboneliğindeki temel özellikleri etkinleştirir.

|Görev |Ayrıntı |Süre |
|-----|-------|---------|
| [Kaynak grubu oluşturma](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Yalnızca yay özellikli sunucuları dahil etmek ve bu kaynakların yönetimini ve izlemesini sağlamak için ayrılmış bir kaynak grubu. | Bir saat |
| Makineleri düzenlemeye yardımcı olmak için [Etiketler](../../azure-resource-manager/management/tag-resources.md) uygulayın. | Yay özellikli sunucularınızı yönetmenin karmaşıklığını azaltmaya ve yönetim kararları almayı basitleştirmeye yardımcı olabilecek, BT hizalanmış bir [etiketleme stratejisini](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) değerlendirin ve geliştirin. | Bir günde |
| [Azure Izleyici günlüklerini](../../azure-monitor/logs/data-platform-logs.md) tasarlama ve dağıtma | Kuruluşunuzun, karma sunuculardan ve makinelerden toplanan günlük verilerini depolamak için mevcut bir Log Analytics veya başka bir çalışma alanı uygulayıp uygulamamı kullanacağını belirleme [Tasarım ve dağıtım konularını](../../azure-monitor/logs/design-logs-deployment.md) değerlendirin. <sup>1</sup> | Bir günde |
| [Azure ilke](../../governance/policy/overview.md) Idare planı geliştirme | Azure Ilkesi ile abonelik veya kaynak grubu kapsamında Karma sunucular ve makineler için nasıl idare uygulayacağınızı öğrenin. | Bir günde |
| [Rol tabanlı erişim denetimini](../../role-based-access-control/overview.md) (RBAC) yapılandırma | Yay etkin sunucuları yönetme ve diğer Azure hizmetlerinden ve çözümlerinden verileri görüntüleme yeteneği olan kimlerin erişimi olduğunu denetlemek için bir erişim planı geliştirin. | Bir günde |
| Log Analytics Aracısı zaten yüklü olan makineleri tanımla | Mevcut Log Analytics Aracısı dağıtımlarının uzantı tarafından yönetilen aracıya dönüştürülmesini desteklemek için [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) aşağıdaki günlük sorgusunu çalıştırın:<br> Sinyal <br> TimeGenerated > önce &#124; (30D) <br> &#124; burada ResourceType = = "Machines" ve (ComputerEnvironment = = "Azure dışı") <br> Bilgisayar, ResourceProvider, ResourceType, ComputerEnvironment tarafından &#124; özetleme | Bir saat |

<sup>1</sup> Log Analytics çalışma alanı tasarımınızı değerlendirme kapsamında önemli bir göz önünde bulundurmanız, Azure Güvenlik Merkezi ve Azure Sentinel 'in yanı sıra Güncelleştirme Yönetimi ve değişiklik izleme ve envanter özelliğini desteklemek Için Azure Otomasyonu ile tümleştirmedir. Kuruluşunuzda zaten bir Otomasyon hesabı varsa ve bir Log Analytics çalışma alanıyla bağlantılı olan yönetim özelliklerini etkinleştirdiyseniz, yönetim işlemlerini merkezileştirip kolaylaştırabilir ve maliyeti en aza indirmek için, mevcut kaynakları kullanarak bir yinelenen hesap, çalışma alanı, vb. oluşturabilirsiniz.

## <a name="phase-2-deploy-arc-enabled-servers"></a>2. Aşama: yay etkin sunucularını dağıtma

Daha sonra, yay etkin sunucular bağlı makine aracısına hazırlık ve dağıtmaya göre 1. aşamada bulunan temele ekliyoruz.

|Görev |Ayrıntı |Süre |
|-----|-------|---------|
| Önceden tanımlanmış yükleme betiğini indirin | Otomatik dağıtım gereksinimlerinizi desteklemek için bağlı makine aracısının ölçekli dağıtımı için önceden tanımlanmış yükleme betiğini gözden geçirin ve özelleştirin.<br><br> Örnek ölçek ekleme kaynakları:<br><br> <ul><li> [Temel dağıtım betiği ölçeklendirin](onboard-service-principal.md)</ul></li> <ul><li>[Windows Server VM 'Leri VMware vSphere ölçekli ekleme](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Linux sanal makinelerini VMware vSphere ölçekli ekleme](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Anerişilebilir kullanarak, ölçeklendirerek AWS EC2 örnekleri ekleme](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[PowerShell uzaktan iletişimini kullanarak ölçekli dağıtım](./onboard-powershell.md) (yalnızca Windows)</ul></li>| Gereksinimlere bağlı olarak bir veya daha fazla gün, kurumsal süreçler (örneğin, değişiklik ve Release Management) ve kullanılan otomasyon yöntemi. |
| [Hizmet sorumlusu oluşturma](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Makineleri etkileşimli olmayan Azure PowerShell veya portaldan kullanarak bağlamak için bir hizmet sorumlusu oluşturun.| Bir saat |
| Bağlı makine aracısını hedef sunucularınız ve makinelerinize dağıtın |Komut dosyalarını sunucularınıza dağıtmak ve Azure 'a bağlamak için Otomasyon aracınızı kullanın.| Yayın planınıza bağlı olarak bir veya daha fazla gün ve aşamalı bir dağıtım takip ediyorsanız. |

## <a name="phase-3-manage-and-operate"></a>3. Aşama: yönetme ve çalıştırma

3. aşama, Yöneticiler veya sistem mühendisleri, bağlı makine aracısını ve makineyi yaşam döngüsü sırasında yönetmek ve çalıştırmak için el ile gerçekleştirilen görevlerin otomatikleştirilmesini sağlar.

|Görev |Ayrıntı |Süre |
|-----|-------|---------|
|Kaynak Durumu Uyarısı oluşturma |Bir sunucu, 15 dakikadan daha uzun bir süre boyunca Azure 'a sinyal göndermeyi sonlandırmışsa, bu, çevrimdışı olduğu, ağ bağlantısının engellendiği veya aracının çalışmadığı anlamına gelebilir. Bu olayları nasıl yanıtlayacağınızı ve araştırıp [kaynak durumu uyarıları](../..//service-health/resource-health-alert-monitor-guide.md) kullanarak, ne zaman başladığınızı öğrenmek için bir plan geliştirin.<br><br> Uyarıyı yapılandırırken şunları belirtin:<br> **Kaynak türü**  =  **Azure Arc etkin sunucular**<br> **Geçerli kaynak durumu**  =  **Kullanılamıyor**<br> **Önceki kaynak durumu**  =  **Kullanılabilir** | Bir saat |
|Azure Advisor uyarısı oluşturma | En iyi deneyim ve en son güvenlik ve hata düzeltmeleri için, Azure Arc etkin sunucu aracısını güncel tutmanız önerilir. Güncel olmayan aracılar bir [Azure Advisor uyarısıyla](../../advisor/advisor-alerts-portal.md)tanımlanacaktır.<br><br> Uyarıyı yapılandırırken şunları belirtin:<br> **Öneri türü**  =  **Azure bağlı makine aracısının en son sürümüne yükseltin** | Bir saat |
|Aboneliğinize veya kaynak grubu kapsamınızda [Azure Ilkeleri atama](../../governance/policy/assign-policy-portal.md) |**Etkinleştirme VM'ler için Azure izleyici** [ilkesini](../../azure-monitor/vm/vminsights-enable-policy.md) (ve gereksinimlerinizi karşılayan diğerlerini) abonelik veya kaynak grubu kapsamına atayın. Azure Ilkesi, ortamınızda VM öngörüleri için gereken aracıları yükleyen ilke tanımlarını atamanıza olanak tanır.| Değişir |
|[Yay özellikli sunucularınız için Güncelleştirme Yönetimi etkinleştirme](../../automation/update-management/enable-from-automation-account.md) |Windows ve Linux sanal makineleriniz için, Arc etkin sunucularla kayıtlı işletim sistemi güncelleştirmelerini yönetmek üzere Azure Otomasyonu 'nda Güncelleştirme Yönetimi yapılandırın. | 15 dakika |

## <a name="next-steps"></a>Sonraki adımlar

* Sorun giderme bilgileri, [bağlı makine Aracısı sorunlarını giderme kılavuzunda](troubleshoot-agent-onboard.md)bulunabilir.

* Azure Otomasyonu [Durum Yapılandırması](../../automation/automation-dsc-overview.md) ve desteklenen DIĞER [Azure VM uzantıları](manage-vm-extensions.md)gibi diğer Azure hizmetleriyle dağıtımı basitleştirmeye nasıl basitleştireceğinizi öğrenin.