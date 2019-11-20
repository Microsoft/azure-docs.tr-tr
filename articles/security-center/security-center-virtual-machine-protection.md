---
title: Azure Güvenlik Merkezi 'nde makinelerinizi ve uygulamalarınızı koruyun
description: Bu belge, güvenlik merkezi 'ndeki, sanal makinelerinizi ve bilgisayarlarınızı ve Web uygulamalarınızı ve App Service ortamlarınızı korumanıza yardımcı olan önerilere yöneliktir.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 803e64c9df1b52a33a1b50714f77b005032bf200
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686346"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde makinelerinizi ve uygulamalarınızı koruma
Azure Güvenlik Merkezi, Azure kaynaklarınızın, Azure olmayan sunucularınızın ve sanal makinelerinizin güvenlik durumunu analiz eder. Güvenlik Merkezi olası güvenlik açıklarını belirlediğinde, gerekli denetimleri yapılandırma sürecinde size kılavuzluk eden öneriler oluşturur. Öneriler Azure Kaynak türleri için geçerlidir: sanal makineler (VM 'Ler) ve bilgisayarlar, uygulamalar, ağ, SQL ve kimlik ve erişim.

Bu makalede, makineler ve uygulamalar için uygulanan öneriler ele alınmaktadır.

## <a name="monitoring-security-health"></a>Güvenlik durumunu izleme
Kaynaklarınızın güvenlik durumunu **Güvenlik Merkezi-genel bakış** panosunda izleyebilirsiniz. **Kaynaklar** bölümü, her bir kaynak türü için tanımlanan sorun sayısını ve güvenlik durumunu sağlar.

**Öneriler**' i seçerek tüm sorunların bir listesini görebilirsiniz. Önerilerin nasıl uygulanacağı hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde güvenlik önerilerini uygulama](security-center-recommendations.md).

Işlem ve uygulama hizmetleri önerilerinin tüm listesi için bkz. [öneriler](security-center-virtual-machine-protection.md#compute-and-app-recs).

Devam etmek için, **kaynaklar** ' ın altında veya güvenlik merkezi ana menüsünde **işlem & uygulamalar** ' ı seçin.
![Güvenlik Merkezi Pano](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>İşlem ve uygulama hizmetlerini izleme
**İşlem & uygulamalar**altında aşağıdaki sekmeler vardır:

- **Genel Bakış**: Güvenlik Merkezi tarafından belirlenen izleme ve öneriler.
- **Sanal makineler ve bilgisayarlar**: Sanal makinelerinizin, bilgisayarlarınızın ve her birinin geçerli güvenlik durumunun listesi.
- **Cloud Services**: Güvenlik Merkezi tarafından izlenen web ve çalışan rollerinizin listesi.
- **Uygulama hizmetleri**: App Service ortamlarınızın ve geçerli güvenlik durumunun listesi.
- **Kapsayıcılar**: kapsayıcılarınızın listesi ve yapılandırmalarının güvenlik değerlendirmesi.
- **İşlem kaynakları (Önizleme)** : Service Fabric kümeleri ve Olay Hub 'ları gibi işlem kaynaklarınız için önerilerin listesi.

Devam etmek için, **kaynak güvenliği durumu**altında **işlem & uygulamalar** ' ı seçin.

![İşlem](./media/security-center-virtual-machine-recommendations/compute.png)

Her sekmede birden fazla bölüm olabilir ve her bölümde, sorunu çözmek üzere önerilen adımlarla ilgili daha fazla ayrıntı görmek için ayrı ayrı seçenekler belirleyebilirsiniz.

### İzlenmeyen VM 'Ler ve bilgisayarlar<a name="unmonitored-vms-and-computers"></a>
Makine Microsoft Monitoring Agent uzantısını çalıştırmadığından, bir VM veya bilgisayar Güvenlik Merkezi tarafından izlenmeyen. Bir makinede yerel bir aracı zaten yüklü olabilir, örneğin OMS doğrudan Aracısı veya System Center Operations Manager Aracısı. Bu aracılar Güvenlik Merkezi 'nde tam olarak desteklenmediğinden, bu aracıları içeren makineler izlenmeyen olarak tanımlanmıştır. Güvenlik Merkezi’nin tüm özelliklerinden tam olarak faydalanmak için, Microsoft Monitoring Agent uzantısı gereklidir.

Uzantıyı, zaten yüklü olan yerel aracıya ek olarak, izlenmeyen VM veya bilgisayara yükleyebilirsiniz. İki aracıyı da aynı çalışma alanına bağlayarak aynı şekilde yapılandırın. Bu, Güvenlik Merkezi’nin Microsoft Monitoring Agent uzantısıyla etkileşim kurup veri toplamasını sağlar. Microsoft Monitoring Agent uzantısını nasıl yükleyeceğiniz hakkında yönergeler için bkz. [VM uzantısını etkinleştir](../azure-monitor/learn/quick-collect-azurevm.md).

Güvenlik Merkezi’nin otomatik hazırlama için başlatılan VM’leri ve bilgisayarları başarılı bir şekilde izleyememe nedenleri hakkında daha fazla bilgi edinmek için bkz. [Aracı durumu sorunlarını izleme](security-center-troubleshooting-guide.md#mon-agent).

### <a name="recommendations"></a>Öneriler
Bu bölümde, her bir VM ve bilgisayar, Web ve çalışan rolleri, Azure App Service Web Apps ve Güvenlik Merkezi 'nin izlediği Azure App Service Ortamı için bir dizi öneri bulunur. İlk sütunda öneriler listelenmiştir. İkinci sütun, bu öneriden etkilenen Toplam kaynak sayısını gösterir. Üçüncü sütun, sorunun önem derecesini gösterir.

Her öneri, onu seçtikten sonra gerçekleştirebileceğiniz bir dizi eylem içerir. Örneğin, **eksik sistem güncelleştirmeleri**' ni seçerseniz, yamaları eksik olan VM 'ler ve bilgisayar sayısı ve eksik güncelleştirmenin önem derecesi görüntülenir.

**Sistem güncelleştirmelerinin uygulanması** , bir grafik biçiminde, Windows için biri ve Linux için bir grafik biçiminde kritik güncelleştirmelerin bir özetini içerir. İkinci bölümde, aşağıdaki bilgileri içeren bir tablo vardır:

- **AD**: Eksik güncelleştirmenin adıdır.
- **Hayır. VM 'Ler & BILGISAYARLAR**: Bu güncelleştirmenin eksik olduğu VM 'ler ve bilgisayarların toplam sayısı.
- **Güncelleştirme önem derecesi**: belirli bir önerinin önem derecesini açıklar:

    - **Kritik**: anlamlı bir kaynakta (uygulama, sanal makine veya ağ güvenlik grubu) bir güvenlik açığı bulunur ve dikkat edilmesi gerekir.
    - **Önemli**: bir işlemi gerçekleştirmek veya bir güvenlik açığını ortadan kaldırmak için kritik olmayan veya ek adımlar gerekir.
    - **Orta**: bir güvenlik açığına değinilmesi gerekir, ancak hemen ilgilenilmesi gerekmez. (Varsayılan olarak, düşük öneriler sunulmaz ancak bunları görüntülemek istiyorsanız düşük öneriler filtresini kullanabilirsiniz.)


- **DURUM**: Önerinin geçerli durumu:

    - **Açık**: Öneri henüz ele alınmadı.
    - **Devam Ediyor**: Öneri şu anda bu kaynaklara uygulanıyor; sizin bir eylem yapmanıza gerek yok.
    - **Çözüldü**: Öneri daha önce tamamlandı. (Sorun çözüldüğünde girdi soluklaşır).

Öneri ayrıntılarını görüntülemek için listeden eksik güncelleştirmenin adına tıklayın.


> [!NOTE]
> Buradaki güvenlik önerileri, **öneriler** kutucuğunda olanlarla aynıdır. Önerilerin nasıl çözümleneceği hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde güvenlik önerilerini uygulama](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>VM 'Ler ve bilgisayarlar
VM 'Ler ve bilgisayarlar bölümü, tüm VM ve bilgisayar önerilerine genel bir bakış sağlar. Her sütunda bir dizi öneri sunulur.

![VM ve bilgisayar önerileri](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Bu listede gösterilen dört tür simge vardır:

![Azure dışı bilgisayar](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Azure olmayan bilgisayar.

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![Azure klasik VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure klasik VM.


![Çalışma alanından tanımlanan VM 'Ler](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VM'ler yalnızca görüntülenen abonelik parçası olan çalışma alanından tanımlanır. Bu, Bu abonelikteki çalışma alanına rapor veren diğer aboneliklerden gelen VM 'Leri ve Operations Manager doğrudan aracıyla yüklenen ve kaynak KIMLIĞI olmayan VM 'leri içerir.

Her öneri altında görüntülenen simge, dikkat gerektiren VM ve bilgisayarı ve öneri türünü hızlıca tanımanıza yardımcı olur. Ayrıca, listeyi **kaynak türüne** göre ve **önem derecesine**göre aramak için filtreleri de kullanabilirsiniz.

Her VM 'nin güvenlik önerilerinin ayrıntılarına gitmek için VM 'ye tıklayın.
Burada, VM veya bilgisayar için güvenlik ayrıntılarını görürsünüz. En altta, önerilen eylemi ve her bir sorunun önem derecesini görebilirsiniz.
![Bulut hizmetleri](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Bulut hizmetleri
Bulut hizmetleri için, işletim sistemi sürümü güncel olmadığında bir öneri oluşturulur.

![Bulut hizmetleri](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Öneriniz (önceki örnekte bu durum değildir) olan bir senaryoda, işletim sistemi sürümünü güncelleştirmek için önerideki adımları izlemeniz gerekir. Bir güncelleştirme mevcut olduğunda uyarı alırsınız (sorunun önem derecesine bağlı olarak kırmızı veya turuncu). Bu uyarıyı WebRole1 (Web uygulamanızı IIS 'e otomatik olarak dağıtılan Windows Server çalıştırır) veya WorkerRole1 (Web uygulamanızı IIS 'e otomatik olarak dağıtarak Windows Server çalıştırır) satırlarına seçtiğinizde, bu öneriye ilişkin daha fazla ayrıntı görürsünüz.

Bu öneriyle ilgili daha kesin bir açıklama görmek için **AÇIKLAMA** sütunu altındaki **İşletim sistemi sürümünü güncelleştir**’e tıklayın.



![İşletim sistemi sürümünü güncelleştirme](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Uygulama hizmetleri
App Service bilgilerini görüntülemek için aboneliğinizdeki App Service etkinleştirmeniz gerekir. Bu özelliği etkinleştirme hakkında yönergeler için bkz. [Azure Güvenlik Merkezi ile App Service koruma](security-center-app-services.md).
[!NOTE]
> İzleme App Service önizleme aşamasındadır ve yalnızca güvenlik merkezi 'nin standart katmanında kullanılabilir.


**Uygulama hizmetleri**altında, uygulama hizmeti ortamlarınızın listesini ve gerçekleştirilen değerlendirme güvenlik merkezini temel alan sistem durumu özetini bulabilirsiniz.

![Uygulama hizmetleri](./media/security-center-virtual-machine-recommendations/app-services.png)

Bu listede temsil edilen üç tür simge vardır:

![App Services ortamı](./media/security-center-virtual-machine-recommendations/ase.png) Uygulama Hizmetleri ortamı.

![Web uygulaması](./media/security-center-virtual-machine-recommendations/web-app.png) Web uygulaması.

![İşlev uygulaması](./media/security-center-virtual-machine-recommendations/function-app.png) İşlev uygulaması.

1. Bir Web uygulaması seçin. Özet görünümü üç sekmeden açılır:

   - **Öneriler**: Güvenlik Merkezi tarafından gerçekleştirilen değerlendirmelere göre başarısız oldu.
   - **Başarılı değerlendirmeler**: Güvenlik Merkezi tarafından gerçekleştirilen değerlendirmelerin listesi.
   - **Kullanılamayan değerlendirmeler**: bir hata nedeniyle çalıştırımayan değerlendirmelerin listesi veya öneri belirli bir App Service için uygun değil

   **Öneriler** altında seçili Web uygulaması için önerilerin bir listesi ve her bir önerinin önem derecesi listelenmiştir.

   ![Uygulama Hizmetleri önerileri](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Önerinin açıklamasını ve sağlıksız kaynakların, sağlıklı kaynakların ve taranmamış kaynakların bir listesini görmek için bir öneri seçin.

   - **Geçirilen değerlendirmeler** sütununun altında, geçirilen değerlendirmelerin listesi bulunur. Bu değerlendirmelerin önem derecesi her zaman yeşildir.

   - Değerlendirmede bir açıklama, sağlıksız ve sağlıklı kaynakların bir listesi ve taranmamış kaynakların bir listesi için listeden bir geçmiş değerlendirmesi seçin. Sağlıksız kaynaklar için bir sekme bulunur, ancak bu liste, değerlendirme başarılı olduğu için her zaman boştur.

     ![Düzeltme App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri
Güvenlik Merkezi, ölçek kümelerine sahip olup olmadığını otomatik olarak bulur ve Microsoft Monitoring Agent bu ölçek kümelerine yüklemenizi önerir. 

Microsoft Monitoring Agent yüklemek için: 

1. **İzleme aracısını sanal makine ölçek kümesine yükler**önerisini seçin. İzlenmeyen ölçek kümelerinin bir listesini alırsınız.
2. Sağlıksız bir ölçek kümesi seçin. İzleme aracısını mevcut doldurulmuş bir çalışma alanını kullanarak yüklemek veya yeni bir tane oluşturmak için yönergeleri izleyin. Ayarlı değilse, çalışma alanı [fiyatlandırma katmanını](security-center-pricing.md) ayarladığınızdan emin olun.

   ![MMS 'yi yükler](./media/security-center-virtual-machine-recommendations/install-mms.png)

Yeni ölçek kümelerini Microsoft Monitoring Agent otomatik olarak yükleyecek şekilde ayarlamak için:
1. Azure Ilkesi ' ne gidin ve **tanımlar**' a tıklayın.
2. **Windows sanal makine ölçek kümeleri için Log Analytics aracısını dağıtma** ilkesi için arama yapın ve üzerine tıklayın.
3. **Ata**'ya tıklayın.
4. **Kapsamı** ve **Log Analytics çalışma alanını** ayarlayıp **ata**' ya tıklayın.

Microsoft Monitoring Agent yüklemek için tüm mevcut ölçek kümelerini ayarlamak istiyorsanız, Azure Ilkesinde, **Düzeltme** ' ye gidin ve mevcut ilkeyi mevcut ölçek kümelerine uygulayın.


## İşlem ve uygulama önerileri<a name="compute-and-app-recs"></a>
|Kaynak türü|Güvenlik puanı|Öneri|Açıklama|
|----|----|----|----|
|App Service|20|Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Web uygulamalarına erişimi yalnızca HTTPS üzerinden sınırlayın.|
|App Service|20|İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Işlev uygulamalarının erişimini yalnızca HTTPS üzerinden sınırlayın.|
|App Service|5|Uygulama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|App Service|10|Web uygulaması için uzaktan hata ayıklama kapatılmalıdır|Artık kullanmanız gerekmiyorsa Web uygulamaları için hata ayıklamayı kapatın. Uzaktan hata ayıklama, bir İşlev Uygulaması gelen bağlantı noktalarının açılmasını gerektirir.|
|App Service|10|Işlev uygulaması için uzaktan hata ayıklama kapatılmalıdır|Artık kullanmanız gerekmiyorsa İşlev Uygulaması için hata ayıklamayı kapatın. Uzaktan hata ayıklama, bir İşlev Uygulaması gelen bağlantı noktalarının açılmasını gerektirir.|
|App Service|10|Tüm (' * ') kaynaklarının uygulamanıza erişmesine izin verme| WEBSITE_LOAD_CERTIFICATES parametresinin "" olarak belirtilmesine izin vermeyin. Parametresinin ' ' olarak ayarlanması, tüm sertifikaların Web uygulamalarına ait kişisel sertifika deposuna yüklendiği anlamına gelir. Bu, sitenin çalışma zamanında tüm sertifikalara erişmesi gerektiğinden, en az ayrıcalık ilkesinin kötüye kullanılmasına neden olabilir.|
|App Service|20|CORS, her kaynağın Web uygulamalarınıza erişmesine izin vermemelidir|Yalnızca gerekli etki alanlarının Web uygulamanızla etkileşime girmesine izin verin. Çapraz kaynak kaynak paylaşımı (CORS), tüm etki alanlarının Web uygulamanıza erişmesine izin vermemelidir.|
|App Service|20|CORS, her kaynağın İşlev Uygulaması erişmesine izin vermemelidir| Yalnızca gerekli etki alanlarının işlev uygulamanızla etkileşime girmesine izin verin. Çapraz kaynak kaynak paylaşımı (CORS), tüm etki alanlarının işlev uygulamanıza erişmesine izin vermemelidir.|
|İşlem kaynakları (Batch)|1|Toplu Iş hesaplarında ölçüm uyarısı kuralları yapılandırılmalıdır|Batch hesabında ölçüm uyarı kurallarını yapılandırma ve ölçüm havuzunu etkinleştirme Tamamlanan olayları ve havuz silme başlangıç olaylarını silme|
|İşlem kaynakları (Service Fabric)|10|Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır|Istemci kimlik doğrulamasını yalnızca Service Fabric Azure Active Directory aracılığıyla gerçekleştirin.|
|İşlem kaynakları (Otomasyon hesabı)|5|Otomasyon hesabı değişkenleri şifrelenmelidir|Hassas verileri depolarken Otomasyon hesabı değişken varlıklarının şifrelenmesini etkinleştirin.|
|İşlem kaynakları (arama)|5|Arama Hizmetleri için tanılama günlüklerini etkinleştirme denetimi|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|İşlem kaynakları (hizmet veri yolu)|5|Service Bus tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|İşlem kaynakları (Stream Analytics)|5|Azure Stream Analytics tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|İşlem kaynakları (Batch)|5|Toplu Iş hesaplarında tanılama günlüklerini etkinleştirme|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|İşlem kaynakları (Olay Hub 'ı)|5|Olay Hub 'ındaki tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|İşlem kaynakları (Logic Apps)|5|Logic Apps tanılama günlüklerini etkinleştirme|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |
|İşlem kaynakları (Service Fabric)|15|ClusterProtectionLevel özelliğini EncryptAndSign olarak ayarlayın Service Fabric|Service Fabric, birincil küme sertifikası kullanarak düğümden düğüme iletişim için üç koruma düzeyi (None, Sign ve EncryptAndSign) sağlar. Tüm düğümden düğüme mesajların şifrelendiğinden ve dijital olarak imzalandığından emin olmak için koruma düzeyini ayarlayın. |
|İşlem kaynakları (hizmet veri yolu)|1|Service Bus ad alanından RootManageSharedAccessKey hariç tüm yetkilendirme kurallarını kaldır |Service Bus istemcileri, bir ad alanındaki tüm kuyruklara ve konulara erişim sağlayan bir ad alanı düzeyinde erişim ilkesi kullanmamalıdır. En az ayrıcalık güvenlik modeliyle uyum sağlamak için, yalnızca belirli varlığa erişim sağlamak üzere kuyruklar ve konular için varlık düzeyinde erişim ilkeleri oluşturmanız gerekir.|
|İşlem kaynakları (Olay Hub 'ı)|1|RootManageSharedAccessKey hariç tüm yetkilendirme kuralları, Olay Hub 'ı ad alanından kaldırılmalıdır|Olay Hub 'ı istemcileri, bir ad alanındaki tüm kuyruklara ve konulara erişim sağlayan bir ad alanı düzeyinde erişim ilkesi kullanmamalıdır. En az ayrıcalık güvenlik modeliyle uyum sağlamak için, yalnızca belirli varlığa erişim sağlamak üzere kuyruklar ve konular için varlık düzeyinde erişim ilkeleri oluşturmanız gerekir.|
|İşlem kaynakları (Olay Hub 'ı)|5|Olay Hub 'ı varlığındaki yetkilendirme kuralları tanımlanmalıdır|En az ayrıcalıklı erişim sağlamak için Olay Hub 'ı varlığındaki yetkilendirme kurallarını denetleyin.|
|Makine|50|Makinelerinize izleme Aracısı 'nı yükler|Her makinede veri toplamayı, güncelleştirme taramayı, temel taramayı ve Endpoint Protection 'ı etkinleştirmek için Izleme aracısını yükler.|
|Makine|50|Abonelikleriniz için otomatik sağlamayı ve veri toplamayı etkinleştirin |Aboneliklerinize eklenen her makinede veri toplamayı, güncelleştirme taramayı, temel taramayı ve Endpoint Protection 'ı etkinleştirmek için aboneliklerinizdeki makineler için otomatik sağlamayı ve veri toplamayı etkinleştirin.|
|Makine|40|Makinelerinizdeki izleme Aracısı sistem durumu sorunlarını çözün|Tam Güvenlik Merkezi koruması için, sorun giderme kılavuzundaki yönergeleri izleyerek makinelerinizdeki izleme Aracısı sorunlarını çözün| 
|Makine|40|Makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözün|Tam Güvenlik Merkezi koruması için, sorun giderme kılavuzundaki yönergeleri izleyerek makinelerinizdeki izleme Aracısı sorunlarını çözün.|
|Makine|40|Makinelerinizde eksik tarama verilerinin sorunlarını giderme|Sanal makinelerde ve bilgisayarlarda eksik tarama verilerinde sorun giderin. Makinelerinizde eksik tarama verileri, güncelleştirme taraması, temel tarama ve eksik Endpoint Protection çözüm taraması gibi eksik güvenlik değerlendirmelerine neden olur.|
|Makine|40|Sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir|Windows ve Linux sanal makinelerinizi ve bilgisayarlarınızı güvenli hale getirmek için eksik sistem güvenliği ve kritik güncelleştirmeleri yükler
|Makine|15|Web uygulaması güvenlik duvarı ekleme| Web uygulamalarınızı güvenli hale getirmek için bir Web uygulaması güvenlik duvarı (WAF) çözümü dağıtın. |
|Makine|40|Bulut hizmeti rolleriniz için işletim sistemi sürümünü güncelleştirme|Bulut hizmeti rolleriniz için işletim sistemi (OS) sürümünü, işletim sistemi aileniz için kullanılabilen en son sürüme güncelleştirin.|
|Makine|35|Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıklarını, saldırılara karşı korumak için düzeltin.|
|Makine|35|Kapsayıcılarınızdaki güvenlik yapılandırmasındaki güvenlik açıklarını düzeltin|Programları saldırılara karşı korumak için Docker yüklü makinelerde güvenlik yapılandırmasındaki güvenlik açıklarını düzeltin.|
|Makine|25|Uyarlamalı uygulama denetimlerini etkinleştir|Azure 'da bulunan VM 'leriniz üzerinde hangi uygulamaların çalıştırılabileceği denetlemek için uygulama denetimini etkinleştirin. Bu, VM 'lerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, her bir VM 'de çalışan uygulamaları çözümlemek için makine öğrenimini kullanır ve bu zekası kullanarak izin verme kuralları uygulamanıza yardımcı olur. Bu özellik, uygulama izin verme kurallarını yapılandırma ve sürdürme sürecini basitleştirir.|
|Makine|20|Makinelerinize Endpoint Protection çözümünü yükler|Sanal makinelerinize ve güvenlik açıklarına karşı korumak için bir uç nokta koruma çözümü yüklersiniz.|
|Makine|20|Sistem güncelleştirmelerini uygulamak için makinelerinizi yeniden başlatın|Sistem güncelleştirmelerini uygulamak ve makinenin güvenlik açıklarına karşı güvenliğini sağlamak için makinelerinizi yeniden başlatın.|
|Makine|15|Disk şifrelemesi sanal makinelere uygulanmalıdır|Hem Windows hem de Linux sanal makineleri için Azure disk şifrelemesi 'ni kullanarak sanal makine disklerinizi şifreleyin. Azure disk şifrelemesi (ADE), Windows 'un sektör standardı BitLocker özelliği ve Linux 'un DM-Crypt özelliğini kullanır ve verilerinizi korumanıza ve korumanıza yardımcı olmak için işletim sistemi ve veri diski şifrelemesi sağlar ve kurumsal güvenlik ve uyumluluğunuzu karşılamanıza yardımcı olur Müşteri Azure Anahtar Kasası 'ndaki taahhütler. Uyumluluk ve güvenlik gereksiniminize, kısa ömürlü (yerel olarak bağlanmış geçici) disk şifrelemesi de dahil olmak üzere, şifreleme anahtarlarınızı kullanarak veri uçtan uca şifrelemeyi gerektirdiğinde Azure disk şifrelemesi ' ni kullanın. Alternatif olarak, varsayılan olarak yönetilen diskler varsayılan olarak, şifreleme anahtarlarının Azure 'da Microsoft tarafından yönetilen anahtarlar olduğu Azure Depolama Hizmeti Şifrelemesi kullanılarak şifrelenir. Bu, uyumluluk ve güvenlik gereksinimlerinizi karşılıyorsa, gereksinimlerinizi karşılamak için varsayılan yönetilen disk şifrelemesi üzerinden yararlanabilirsiniz.|
|Makine|30|Sanal makinelerinize bir güvenlik açığı değerlendirme çözümü yüklemesi|Sanal makinelerinize bir güvenlik açığı değerlendirme çözümü yüklemesi|
|Makine|15|Web uygulaması güvenlik duvarı ekleme| Web uygulamalarınızı güvenli hale getirmek için bir Web uygulaması güvenlik duvarı (WAF) çözümü dağıtın. |
|Makine|30|Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir|Bir güvenlik açığı değerlendirmesi 3. taraf çözümü dağıtılan sanal makineler, uygulama ve işletim sistemi açıklarına karşı sürekli olarak değerlendirilir. Bu güvenlik açıkları bulunduğunda, önerinin bir parçası olarak daha fazla bilgi için kullanılabilir.|
|Makine|30|Sanal makinelerinize bir güvenlik açığı değerlendirme çözümü yüklemesi|Sanal makinelerinize bir güvenlik açığı değerlendirme çözümü yüklemesi|
|Makine|1|Sanal makinelerin yeni Azurerd kaynaklarına geçirilmesi gerekir|Sanal makineleriniz için Azure Resource Manager kullanarak: daha güçlü erişim denetimi (RBAC), daha iyi denetim, Kaynak Yöneticisi tabanlı dağıtım ve idare, yönetilen kimliklere erişim, gizli dizi için anahtar kasasına erişim, Daha kolay güvenlik yönetimi için Azure AD tabanlı kimlik doğrulaması ve Etiketler ve kaynak grupları desteği. |
|Makine|30|Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir|Bir güvenlik açığı değerlendirmesi 3. taraf çözümü dağıtılan sanal makineler, uygulama ve işletim sistemi açıklarına karşı sürekli olarak değerlendirilir. Bu güvenlik açıkları bulunduğunda, önerinin bir parçası olarak daha fazla bilgi için kullanılabilir.|
|Sanal makine ölçek kümesi |4|Sanal makine ölçek kümelerindeki tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirin ve bir yıla kadar saklayın. Bu, araştırma amaçları için etkinlik izlerini yeniden oluşturmayı sağlar. Bu, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye girerse yararlıdır.|
|Sanal makine ölçek kümesi|35|Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir|Saldırılara karşı korumak için sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıklarını düzeltin. |
|Sanal makine ölçek kümesi|5|Sanal makine ölçek kümelerinde Endpoint Protection sistem durumu başarısızlıklarını düzelt|Sanal makine ölçek kümelerinizi tehdit ve güvenlik açıklarına karşı korumak için Endpoint Protection sistem durumu başarısızlıklarını düzeltin. |
|Sanal makine ölçek kümesi|10|Endpoint Protection 'ın sanal makinelere yüklenmesi gerekir|Sanal makine ölçek kümelerinizi tehdit ve güvenlik açıklarına karşı korumak için bir uç nokta koruma çözümü yüklemek. |
|Sanal makine ölçek kümesi|40|Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir|Windows ve Linux sanal makine ölçek kümelerinizin güvenliğini sağlamak için eksik sistem güvenliği ve kritik güncelleştirmeleri yükler. |
 





## <a name="next-steps"></a>Sonraki adımlar
Diğer Azure kaynak türlerine uygulanan öneriler hakkında daha fazla bilgi edinmek için aşağıdakilere bakın:


* [Azure Güvenlik Merkezi'nde makinelerinizi ve uygulamalarınızı koruma](security-center-virtual-machine-protection.md)
* [Azure Güvenlik Merkezi'nde kimliği ve erişimi izleme](security-center-identity-access.md)
* [Azure Güvenlik Merkezi'nde ağınızı koruma](security-center-network-recommendations.md)
* [Azure Güvenlik Merkezi 'nde Azure SQL hizmetinizi koruma](security-center-sql-service-recommendations.md)

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.

