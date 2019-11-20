---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 3e1e1266122ebcccc0149ca8e0421577becd6708
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036796"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>Kullanım DıŞı Kapsayıcı hizmeti sık sorulan sorular

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

## <a name="orchestrators"></a>Düzenleyiciler

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Azure Container Service’te hangi kapsayıcı düzenleyicileri desteklenir? 

Açık kaynak DC/OS, Docker Swarm ve Kubernetes desteği mevcuttur. Daha fazla bilgi için bkz. [Genel Bakış](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Docker Swarm modu destekleniyor mu? 

Swarm modu, şu anda desteklenmiyor ancak hizmet yol haritası kapsamında. 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service, Windows kapsayıcılarını destekliyor mu?  

Şu anda Linux kapsayıcıları tüm düzenleyicilerle desteklenmektedir. Kubernetes ile Windows kapsayıcıları desteği önizleme aşamasındadır.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Azure Container Service için önerilen belirli bir düzenleyici var mı? 
Genellikle belirli bir düzenleyici önerilmemektedir. Desteklenen düzenleyicilerden biriyle deneyiminiz varsa, bu deneyimi Azure Container Service’e uygulayabilirsiniz. Ancak, veri eğilimleri DC/OS’nin Büyük Veri ve IoT iş yükleri için üretimde kanıtlandığını, Kubernetes’in bulut yerel iş yükleri için uygun olduğunu, Docker Swarm’ın ise Docker araçları ve kolay öğrenme eğrisi konusunda ünlü olduğunu ortaya koymaktadır.

Senaryonuza bağlı olarak, diğer Azure hizmetleriyle de özel kapsayıcı çözümleri oluşturup yönetebilirsiniz. Bu hizmetler [Sanal Makine](../articles/virtual-machines/linux/overview.md), [Service Fabric](../articles/service-fabric/service-fabric-overview.md), [Web Apps](../articles/app-service/overview.md) ve [Batch](../articles/batch/batch-technical-overview.md) hizmetleridir.  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure Container Service ile ACS Altyapısı arasındaki fark nedir? 
Azure Container Service; Azure portalı, Azure komut satırı araçları ve Azure API’lerindeki özelliklere sahip SLA destekli bir Azure hizmetidir. Hizmet, çok az sayıda yapılandırma seçeneği ile standart kapsayıcı düzenleme araçlarını çalıştıran kümeleri hızlıca uygulayıp yönetmenizi sağlar. 

[ACS Altyapısı](https://github.com/Azure/acs-engine), yetkili kullanıcıların her düzeyde küme yapılandırmasını özelleştirmesine olanak tanıya bir açık kaynak projesidir. Hem altyapı hem de yazılım yapılandırmasını değiştirmeye yönelik bu özellik, ACS Altyapısı için SLA sunulmadığı anlamına gelir. Destek, resmi Microsoft kanalları yerine GitHub üzerinde açık kaynak projesi üzerinden gerçekleştirilir. 

Ek ayrıntılar için lütfen [kapsayıcılar için destek ilkemize](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers) bakın.

## <a name="cluster-management"></a>Küme yönetimi

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Kümem için nasıl SSH anahtarları oluşturabilirim?

Kümeniz için Linux sanal makinelerinde kimlik doğrulamasına yönelik bir SSH RSA genel ve özel anahtar çifti oluşturmak için, işletim sisteminizde standart araçları kullanabilirsiniz. Adımlar için [OS X ve Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) veya [Windows](../articles/virtual-machines/linux/ssh-from-windows.md) yönergelerine bakın. 

Bir kapsayıcı hizmeti kümesini dağıtmak için [Azure CLI komutlarını](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) kullanırsanız kümenize yönelik SSH anahtarları otomatik olarak oluşturulabilir.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Kubernetes kümem için nasıl hizmet sorumlusu oluşturabilirim?

Azure Container Service’te bir Kubernetes kümesi oluşturmak için ayrıca bir Azure Active Directory hizmet sorumlusu kimliği ve parolası gereklidir. Daha fazla bilgi için bkz. [Bir Kubernetes kümesi için hizmet sorumlusu hakkında](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md).

Bir Kubernetes kümesini dağıtmak için [Azure CLI komutlarını](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) kullanırsanız kümenize yönelik hizmet sorumlusu kimlik bilgileri otomatik olarak oluşturulabilir.

### <a name="how-large-a-cluster-can-i-create"></a>Ne kadar büyük bir küme oluşturabilirim?
1, 3 veya 5 ana düğüm içeren bir küme oluşturabilirsiniz. En fazla 100 aracı düğümü seçebilirsiniz.

> [!IMPORTANT]
> Daha büyük kümeler için ve düğümlerinize yönelik seçtiğiniz VM boyutuna bağlı olarak, aboneliğinizdeki çekirdek kotasını artırmanız gerekebilir. Bir kota artışı istemek için ücretsiz olarak [çevrimiçi müşteri destek isteği](../articles/azure-supportability/how-to-create-azure-support-request.md) açın. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/) kullanıyorsanız, yalnızca sınırlı sayıda Azure işlem çekirdeği kullanabilirsiniz.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Bir küme oluşturulduktan sonra ana sunucu sayısını nasıl artırabilirim? 
Küme oluşturulduktan sonra ana sunucu sayısı sabittir ve değiştirilemez. Küme oluşturma sırasında, yüksek kullanılabilirlik için birden çok ana düğüm seçmeniz en uygunudur.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Bir küme oluşturulduktan sonra aracı sayısını nasıl artırabilirim? 
Azure portalını veya komut satırı araçlarını kullanarak kümedeki aracı sayısını ölçeklendirebilirsiniz. Bkz. [Azure Container Service kümesini ölçeklendirme](../articles/container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Ana sunucu ve aracılarımın URL'leri nelerdir? 
Azure Container Service’teki küme kaynaklarının URL’leri, belirttiğiniz DNS adı ön ekine ve dağıtım için seçtiğiniz Azure bölgesinin adına bağlıdır. Örneğin, ana düğümünün tam etki alanı adı (FQDN) şu biçimdedir:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Azure portalı, Azure Kaynak Gezgini veya diğer Azure araçlarında kümeniz için yaygın olarak kullanılan URL’leri bulabilirsiniz.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Kümemde hangi orchestrator sürümünün çalıştığını nasıl öğrenirim?

* DC/OS: [Mesosphere belgelerine](https://docs.mesosphere.com/1.7/usage/cli/command-reference/) bakın
* Docker Sısınma: 
          `docker version`'i çalıştırın.
* Kubernetes: 
          `kubectl version`'i çalıştırın.

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Dağıtımdan sonra orchestrator’ı nasıl yükseltirim?

Şu anda, Azure Container Service kümenizde dağıttığınız orchestrator sürümünü yükseltmek için araçlar sağlamamaktadır. Kapsayıcı Hizmeti sonraki bir sürümü destekliyorsa, yeni bir küme dağıtabilirsiniz. Başka bir seçenek, bir kümeyi yerinde yükseltmek üzere kullanılabilir olmaları durumunda orchestrator’a özgü araçlar kullanmaktır. Örneğin, bkz. [DC/OS Yükseltme](http://docs.mesosphere.com/1.12/installing/production/upgrading).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Kümemin SSH bağlantı dizesini nerede bulabilirim?

Bağlantı dizesini Azure portalında veya Azure komut satırı araçlarını kullanarak bulabilirsiniz. 

1. Portalda, küme dağıtımı için kaynak grubuna gidin.  

2. **Genel Bakış**’a ve **Temel Bileşenler** altındaki **Dağıtımlar** bağlantısına tıklayın. 

3. **Dağıtım geçmişi** dikey penceresinde, adı **microsoft-acs** ile başlayıp dağıtım tarihiyle biten dağıtıma tıklayın. Örnek: microsoft-acs-201701310000.  

4. **Özet** sayfasındaki **Çıktılar** altında çeşitli küme bağlantıları sağlanır. **SSHMaster0**, kapsayıcı hizmeti kümenizdeki birinci ana sunucuya bir SSH bağlantı dizesi sağlar. 

Daha önce belirtildiği gibi, ana sunucunun FQDN'sini bulmak için Azure araçlarını da kullanabilirsiniz. Ana sunucunun FQDN’sini ve kümeyi oluştururken belirttiğiniz kullanıcı adını kullanarak ana sunucuyla SSH bağlantısı oluşturun. Örneğin:

```bash
ssh userName@masterFQDN –A –p 22 
```

Daha fazla bilgi için bkz. [Azure Container Service kümesine bağlanma](../articles/container-service/kubernetes/container-service-connect.md).

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>DNS ad çözümlemem Windows’da çalışmıyor. Ne yapmalıyım?

Windows’da, düzeltmeleri halen etkin olarak kaldırılmakta olan bazı bilinen DNS sorunları vardır. Ortamınızın bundan yararlanabilmesi için lütfen en güncel acs-engine ve Windows sürümünü kullandığınızdan ([KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) ve [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848) yüklü şekilde) emin olun. Aksi takdirde lütfen azaltma adımları için aşağıdaki tabloya bakın:

| DNS Belirtisi | Geçici çözüm  |
|-------------|-------------|
|İş yükü kapsayıcısı kararsız olduğunda ve çöktüğünde ağ ad alanı temizlenir | Etkilenen hizmetleri yeniden dağıtın |
| Hizmet VIP erişimi bozuk | Her zaman bir normal (ayrılıklı olmayan) bir podun çalıştırılmasını sağlamak için bir [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) yapılandırın |
|Kapsayıcının çalıştırılmakta olduğu düğüm kullanılamaz duruma gelirse DNS sorguları başarısız olarak "negatif önbellek girişi" ile sonuçlanabilir | Etkilenen kapsayıcıların içinde aşağıdaki komutu çalıştırın: <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> Bu da sorunu çözmezse DNS önbelleğe almayı tamamen devre dışı bırakmayı deneyin: <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Service hakkında [daha fazla bilgi edinin](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
* [Portal](../articles/container-service/dcos-swarm/container-service-deployment.md)’ı veya [Azure CLI](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) aracını kullanarak bir kapsayıcı hizmeti kümesi dağıtın.
