---
title: Azure Site Recovery kullanarak bir IIS Web uygulaması için olağanüstü durum kurtarmayı ayarlama
description: Azure Site Recovery kullanarak IIS Web grubu sanal makinelerini çoğaltma hakkında bilgi edinin.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 513a0f28fc03cbf24e35112245c9756d5ce00783
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954662"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Çok katmanlı bir IIS tabanlı Web uygulaması için olağanüstü durum kurtarmayı ayarlama

Uygulama yazılımı, bir kuruluştaki iş üretkenlik altyapısıdır. Çeşitli web uygulamaları, bir kuruluşta farklı amaçlar sunabilir. Bordro işleme, finansal uygulamalar ve müşteriye yönelik Web siteleri için kullanılan uygulamalar gibi bazı uygulamalar, bir kuruluş için kritik öneme sahip olabilir. Üretkenlik kaybını engellemek için, kuruluşun bu uygulamaların sürekli olarak çalışır duruma gelmesi önemlidir. Daha da önemlisi, bu uygulamaların sürekli olarak kullanılabilir olması, kuruluşun marka veya görüntüsünün zarar görmesini önlemeye yardımcı olabilir.

Kritik Web uygulamaları genellikle çok katmanlı uygulamalar olarak ayarlanır: Web, veritabanı ve uygulama farklı katmandadır. Uygulamalar, çeşitli katmanlara yayılmaya ek olarak, trafiğin yükünü dengelemek için her katmanda birden çok sunucu da kullanabilir. Ayrıca, çeşitli katmanlar ve Web sunucusu arasındaki eşlemeler statik IP adreslerini temel alabilir. Yük devretmede, özellikle Web sunucusunda birden fazla Web sitesi yapılandırılmışsa, bu eşlemlerden bazılarının güncellenmesi gerekir. Web uygulamaları SSL kullanıyorsa, sertifika bağlamalarını güncelleştirmeniz gerekir.

Çoğaltmayı temel almayan geleneksel kurtarma yöntemleri çeşitli yapılandırma dosyalarını, kayıt defteri ayarlarını, bağlamaları, özel bileşenleri (COM veya .NET), içeriği ve sertifikaları yedeklemeyi içerir. Dosyalar, el ile gerçekleştirilen bir adım kümesi aracılığıyla kurtarılır. Dosyaları yedeklemeye ve el ile kurtarmaya yönelik geleneksel kurtarma yöntemleri, çok sayıda, hataya açıktır ve ölçeklenebilir değildir. Örneğin, sertifikaları yedeklemeyi kolayca unutmanız gerekebilir. Yük devretmeden sonra, hiçbir seçim yapmadan kalır, ancak sunucu için yeni sertifikalar satın alabilirsiniz.

İyi bir olağanüstü durum kurtarma çözümü, karmaşık uygulama mimarileri için modelleme kurtarma planlarını destekler. Ayrıca, Katmanlar arasındaki Uygulama eşlemelerini işlemek için kurtarma planına özelleştirilmiş adımlar ekleyebilmelisiniz. Bir olağanüstü durum varsa, uygulama eşlemeleri, daha düşük bir RTO 'ya yol açmaya yardımcı olan tek tıklamayla, dikkat çekmeye yönelik bir çözüm sağlar.

Bu makalede [Azure Site Recovery](site-recovery-overview.md)kullanarak Internet INFORMATION SERVICES (IIS) tabanlı bir Web uygulamasını nasıl koruyabileceğiniz açıklanır. Makale, üç katmanlı, IIS tabanlı bir Web uygulamasını Azure 'a çoğaltmaya, olağanüstü durum kurtarma detayına ve uygulamanın yükünü Azure 'a nasıl devretireceğinizi gösteren en iyi yöntemleri içerir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki görevleri nasıl yapabileceğinizi öğrendiğinizden emin olun:

* [Bir sanal makineyi Azure 'a çoğaltma](vmware-azure-tutorial.md)
* [Kurtarma ağını tasarlama](site-recovery-network-design.md)
* [Azure 'a yük devretme testi yapın](site-recovery-test-failover-to-azure.md)
* [Azure 'a yük devretme](site-recovery-failover.md)
* [Bir etki alanı denetleyicisini çoğaltma](site-recovery-active-directory.md)
* [SQL Server çoğaltma](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Dağıtım desenleri
IIS tabanlı bir Web uygulaması, genellikle aşağıdaki dağıtım desenlerinden birini izler:

**Dağıtım kalıbı 1**

Uygulama Isteği yönlendirme (ARR), IIS sunucusu ve SQL Server bir IIS tabanlı Web grubu.

![Üç katmanı olan IIS tabanlı bir Web grubunun diyagramı](./media/site-recovery-iis/deployment-pattern1.png)

**Dağıtım kalıbı 2**

ARR, IIS sunucusu, bir uygulama sunucusu ve SQL Server olan IIS tabanlı bir Web grubu.

![Dört katmanı olan IIS tabanlı bir Web grubunun diyagramı](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery desteği

Bu makaledeki örneklerde, Windows Server 2012 R2 Enterprise üzerinde IIS 7,5 ile VMware sanal makinelerini kullanırız. Site Recovery çoğaltma uygulamaya özgü olmadığından, bu makaledeki önerilerin aşağıdaki tabloda listelenen senaryolarda ve farklı IIS sürümleri için uygulanması beklenmektedir.

### <a name="source-and-target"></a>Kaynak ve hedef

Senaryo | İkincil siteye | Azure’a
--- | --- | ---
Hyper-V | Yes | Yes
VMware | Yes | Yes
Fiziksel sunucu | Hayır | Yes
Azure|NA|Yes

## <a name="replicate-virtual-machines"></a>Sanal makineleri çoğaltma

Tüm IIS Web grubu sanal makinelerini Azure 'a Çoğaltmaya başlamak için [Site Recovery 'de Azure 'a yük devretme testi](site-recovery-test-failover-to-azure.md)' nde yer alan yönergeleri izleyin.

Statik bir IP adresi kullanıyorsanız, sanal makinenin geçirmesine istediğiniz IP adresini belirtebilirsiniz. IP adresini ayarlamak için, **hedef ıp** > **işlem ve ağ ayarları** ' na gidin.

![Site Recovery Işlem ve ağ bölmesinde hedef IP 'nin nasıl ayarlanacağını gösteren ekran görüntüsü](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Kurtarma planı oluşturma
Kurtarma planı, yük devretme sırasında çok katmanlı bir uygulamadaki çeşitli katmanların sıralamasını destekler. Sıralama, uygulama tutarlılığını sürdürmenize yardımcı olur. Çok katmanlı bir Web uygulaması için bir kurtarma planı oluşturduğunuzda [Site Recovery kullanarak kurtarma planı oluşturma](site-recovery-create-recovery-plans.md)bölümünde açıklanan adımları uygulayın.

### <a name="add-virtual-machines-to-failover-groups"></a>Yük devretme gruplarına sanal makineler ekleme
Tipik bir çok katmanlı IIS Web uygulaması aşağıdaki bileşenlerden oluşur:
* SQL sanal makineleri olan bir veritabanı katmanı.
* IIS sunucusundan ve uygulama katmanından oluşan Web katmanı. 

Katmana bağlı olarak farklı gruplara sanal makineler ekleyin:

1. Kurtarma planı oluşturun. Veritabanı katmanı sanal makinelerini Grup 1 ' in altına ekleyin. Bu, veritabanı katmanı sanal makinelerinin en son kapanmasını ve ilk olarak alınmasını sağlar.
1. Uygulama katmanı sanal makinelerini Grup 2 ' ye ekleyin. Bu, veritabanı katmanı oluşturulduktan sonra uygulama katmanı sanal makinelerinin kullanılabilmesini sağlar.
1. Web katmanı sanal makinelerini Grup 3 ' te ekleyin. Bu, uygulama katmanı oluşturulduktan sonra Web katmanı sanal makinelerinin kullanılabilmesini sağlar.
1. Grup 4 ' te yük dengeleme sanal makineleri ekleyin. Bu, Web katmanı oluşturulduktan sonra Yük Dengeleme sanal makinelerinin alınmasını sağlar.

Daha fazla bilgi için bkz. [Kurtarma planını özelleştirme](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Kurtarma planına bir komut dosyası ekleyin
IIS Web grubunun düzgün çalışması için, Azure sanal makinelerinde yük devretme sonrası veya yük devretme testi sırasında bazı işlemler yapmanız gerekebilir. Bazı yük devretme sonrası işlemleri otomatik hale getirebilirsiniz. Örneğin, DNS girişini güncelleştirebilir, bir site bağlamasını değiştirebilir veya ilgili betikleri kurtarma planına ekleyerek bir bağlantı dizesini değiştirebilirsiniz. Bir [Kurtarma PLANıNA VMM betiği ekleme](site-recovery-how-to-add-vmmscript.md) bir betiği kullanarak otomatikleştirilmiş görevlerin nasıl ayarlanacağını açıklar.

#### <a name="dns-update"></a>DNS güncelleştirmesi
DNS dinamik DNS güncelleştirmesi için yapılandırılmışsa, sanal makineler genellikle DNS 'yi yeni IP adresiyle güncelleştirir. DNS 'yi sanal makinelerin yeni IP adresleriyle güncelleştirmek üzere açık bir adım eklemek istiyorsanız, DNS 'deki IP 'yi kurtarma planı gruplarında yük devretme sonrası eylem olarak [güncelleştirmek üzere bir komut dosyası](https://aka.ms/asr-dns-update) ekleyin.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Uygulamanın Web. config dosyasındaki bağlantı dizesi
Bağlantı dizesi, Web sitesinin iletişim kurduğu veritabanını belirtir. Bağlantı dizesi, veritabanı sanal makinesinin adını taşıyorsa yük devretme sonrası başka bir adım gerekmez. Uygulama veritabanıyla otomatik olarak iletişim kurabilir. Ayrıca, veritabanı sanal makinesi için IP adresi korunursa bağlantı dizesinin güncelleştirilmesi gerekmez. 

Bağlantı dizesi bir IP adresi kullanarak veritabanı sanal makinesine başvuruyorsa, bunun yük devretme sonrası güncelleştirilmesi gerekir. Örneğin, aşağıdaki bağlantı dizesi veritabanına 127.0.1.2 IP adresiyle işaret eder:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Web katmanındaki bağlantı dizesini güncelleştirmek için, kurtarma planında Grup 3 ' den sonra bir [IIS bağlantı güncelleştirme betiği](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) ekleyin.

#### <a name="site-bindings-for-the-application"></a>Uygulama için site bağlamaları
Her site bağlama bilgilerinden oluşur. Bağlama bilgileri, bağlamanın türünü, IIS sunucusunun site isteklerini dinlediği IP adresini, bağlantı noktası numarasını ve site için ana bilgisayar adlarını içerir. Yük devretme sırasında kendileriyle ilişkili IP adresinde bir değişiklik varsa, bu bağlamaları güncelleştirmeniz gerekebilir.

> [!NOTE]
>
> Site bağlamayı **atanmamış**olarak ayarlarsanız, bu bağlama sonrası yük devretmeyi güncelleştirmeniz gerekmez. Ayrıca, bir siteyle ilişkilendirilmiş IP adresi yük devretme sonrası değiştirildiyse, site bağlamayı güncelleştirmeniz gerekmez. (IP adresini bekletme, birincil ve kurtarma sitelerine atanan ağ mimarisine ve alt ağlara bağlıdır. Bunları güncelleştirmek, kuruluşunuz için uygun olmayabilir.)

![SSL bağlamasını ayarlamayı gösteren ekran görüntüsü](./media/site-recovery-iis/sslbinding.png)

IP adresini bir siteyle ilişkilendirdiyseniz, tüm site bağlamalarını yeni IP adresiyle güncelleştirin. Site bağlamalarını değiştirmek için kurtarma planında Grup 3 ' den sonra bir [IIS Web katmanı güncelleştirme betiği](https://aka.ms/asr-web-tier-update-runbook-classic) ekleyin.

#### <a name="update-the-load-balancer-ip-address"></a>Yük dengeleyici IP adresini güncelleştirme
Bir ARR sanal makineniz varsa, IP adresini güncelleştirmek için, Grup 4 ' den sonra bir [IIS ARR yük devretme betiği](https://aka.ms/asr-iis-arrtier-failover-script-classic) ekleyin.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>HTTPS bağlantısı için SSL sertifikası bağlama
Web sitesi, Web sunucusu ile kullanıcının tarayıcısı arasında güvenli bir iletişim sağlamaya yardımcı olan ilişkili bir SSL sertifikasına sahip olabilir. Web sitesinde bir HTTPS bağlantısı varsa ve aynı zamanda bir SSL sertifikası bağlaması olan IIS sunucusunun IP adresine ilişkili bir HTTPS site bağlaması varsa, sertifika için IIS sanal makinesinin IP adresine yönelik yeni bir site bağlaması eklemeniz gerekir.

SSL sertifikası bu bileşenlere göre verilebilir:

* Web sitesinin tam etki alanı adı.
* Sunucunun adı.
* Etki alanı adı için bir joker karakter sertifikası.  
* Bir IP adresi. SSL sertifikası IIS sunucusunun IP adresine karşı verildiyse, Azure sitesindeki IIS sunucusunun IP adresine karşı başka bir SSL sertifikasının verilmesi gerekir. Bu sertifika için ek bir SSL bağlamasının oluşturulması gerekir. Bu nedenle, IP adresine karşı verilen bir SSL sertifikası kullanmanızı öneririz. Bu seçenek daha az yaygın olarak kullanılır ve yeni sertifika yetkilisi/tarayıcı Forum değişikliklerine göre yakında kullanım dışı olacaktır.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Web katmanı ve uygulama katmanı arasındaki bağımlılığı güncelleştirme
Sanal makinelerin IP adresini temel alan uygulamaya özgü bir bağımlılığı varsa, bu bağımlılık yük devretmesini güncelleştirmeniz gerekir.

## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

1. Azure portal, kurtarma hizmetleri kasanızı seçin.
2. IIS Web grubu için oluşturduğunuz kurtarma planını seçin.
3. **Yük Devretme Testi**'ni seçin.
4. Yük devretme testi işlemini başlatmak için kurtarma noktasını ve Azure sanal ağını seçin.
5. İkincil ortam çalışır duruma geldiğinde, doğrulamaları yapabilirsiniz.
6. Doğrulamalar tamamlandığında, test yük devretme ortamını temizlemek için **doğrulama Tamam**' ı seçin.

Daha fazla bilgi için bkz. [Azure 'a yük devretmeyi test etme Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Yük devretme çalıştırma

1. Azure portal, kurtarma hizmetleri kasanızı seçin.
1. IIS Web grubu için oluşturduğunuz kurtarma planını seçin.
1. **Yük devretme**'yi seçin.
1. Yük devretme işlemini başlatmak için kurtarma noktasını seçin.

Daha fazla bilgi için bkz. [Site Recovery 'de yük devretme](site-recovery-failover.md).

## <a name="next-steps"></a>Sonraki adımlar
* Site Recovery kullanarak [diğer uygulamaları çoğaltma](site-recovery-workload.md) hakkında daha fazla bilgi edinin.
