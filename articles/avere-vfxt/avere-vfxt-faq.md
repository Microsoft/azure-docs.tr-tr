---
title: SSS-Azure için avere vFXT
description: Azure için avere vFXT hakkında sık sorulan sorular
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: rohogue
ms.openlocfilehash: 7839530463ea834ed5f8ae2ae06854985a77c673
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847360"
---
# <a name="avere-vfxt-for-azure-faq"></a>Azure için Avere vFXT hakkında SSS

Bu makalede, Azure için avere vFXT 'nin gereksinimlerinize uygun olup olmadığına karar vermenize yardımcı olabilecek sorular yanıtlanmaktadır. Avere vFXT hakkında temel bilgileri verir ve diğer Azure bileşenleriyle ve dış satıcıların ürünleriyle nasıl çalıştığını açıklar.

## <a name="general"></a>Genel

### <a name="what-is-avere-vfxt-for-azure"></a>Azure için Avere vFXT nedir?

Azure için avere vFXT, kritik iş yüklerini verimli bir şekilde işlemek için Azure işlem 'da etkin verileri önbelleğe alan yüksek performanslı bir dosya sistemidir.

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT bir depolama çözümüdür mi?

Hayır. Avere vFXT, EMC veya NetApp NAS veya bir Azure Blob kapsayıcısı gibi depolama ortamlarına bağlanan bir dosya sistemi *önbelleğidir* . Avere vFXT istemcilerden gelen veri isteklerini kolaylaştırır ve ölçek ve zaman içinde performansı artırmak için hizmet verdiği verileri önbelleğe alır. Avere vFXT, verileri depolamaz. Bu, arkasında depolanan veri miktarı hakkında bilgi içermez.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT bir çözüm mi?

Avere vFXT, sık erişimli ve seyrek katmanlar arasında verileri otomatik olarak katmanlamaz.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Nasıl yaparım? avere vFXT için bir ortamın doğru olup olmadığını öğrenin.

Bu soruyu düşünmenin en iyi yolu, "iş yükü önbelleklenebilir mi?" sorusunu sormaktır. Diğer bir deyişle, iş yükünün okuma/yazma oranına sahip olması gerekir mi? Örneğin, 80/20 veya 70/30 okuma/yazma işlemleri.

Çok sayıda Azure sanal makinesi üzerinde çalışan dosya tabanlı bir analitik işlem hattına sahipseniz ve aşağıdaki koşullardan birini veya daha fazlasını karşılıyorsa Azure için avere vFXT 'yi düşünün:

* Genel performans, uzun dosya erişim süreleri (gereksinimlere bağlı olarak on dakika veya saniye) nedeniyle yavaş veya tutarsız olur. Bu gecikme, müşteri tarafından kabul edilemez.

* İşleme için gereken veriler bir WAN ortamının en sonunda bulunur ve bu verilerin kalıcı olarak taşınması pratik bir şekilde yapılır. Veriler farklı bir Azure bölgesinde veya bir müşteri veri merkezinde olabilir.

* Önemli sayıda istemci, verileri (örneğin, yüksek performanslı bilgi işlem (HPC) kümesinde ister. Çok sayıda eşzamanlı istek gecikme süresini artırabilir.

* Müşteri, Azure sanal makinelerinde geçerli işlem hattını "olduğu gibi" çalıştırmak istiyor ve ölçeklenebilirlik için POSIX tabanlı bir paylaşılan depolama (veya önbelleğe alma) çözümüne ihtiyaç duyuyor. Azure için avere vFXT kullanarak, Azure Blob depolamaya yerel çağrılar yapmak üzere iş ardışık düzenini yeniden mimarmeniz gerekmez.

* HPC uygulamanız NFSv3 istemcilerini temel alır. (Bazı durumlarda, SMB 2,1 istemcilerini kullanabilir, ancak performans sınırlıdır.)

Aşağıdaki diyagram, bu sorunun yanıtını basitleştirir. İş akışınız sağ üst köşede, avere önbellek çözümünün ortamınız için doğru olması olasıdır.

![Avere vFXT için binlerce istemci içeren okuma ağır yükün daha uygun olduğunu gösteren diyagram](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>İstemci ölçeğinde avere vFXT çözümü en mantıklı midir?

Avere vFXT önbellek çözümü yüzlerce, binlerce veya on binlerce işlem çekirdeğini işleyecek şekilde oluşturulmuştur. Hafif çalışma çalıştıran birkaç makineniz varsa, avere vFXT doğru çözüm değildir.

Tipik avere vFXT müşterileri, yaklaşık 1.000 CPU çekirdekleri ile başlayan yoğun iş yüklerini çalıştırır. Bu ortamlar 50.000 çekirdek veya daha fazlası kadar büyük olabilir. Avere vFXT ölçeklenebilir olduğundan, daha fazla verimlilik veya daha fazla ıOPS gerektirecek şekilde büyürken bu iş yüklerini desteklemek için düğüm ekleyebilirsiniz.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Avere vFXT ortam deposu ne kadar veri olabilir?

Avere vFXT bir önbelleğidir. Verileri özel olarak depolamaz. Önbelleğe alınmış verileri depolamak için RAM ve SSD 'lerin bir birleşimini kullanır. Veriler, arka uç depolama sisteminde (örneğin, bir NetApp NAS sistemi veya bir blob kapsayıcısı) kalıcı olarak depolanır. Avere vFXT sisteminde, arkasında depolanan veri miktarı hakkında bilgi yok. Avere vFXT yalnızca istemcilerin talep aldığı verilerin alt kümesini önbelleğe alır.  

### <a name="what-regions-are-supported"></a>Hangi bölgeler destekleniyor?

Azure için avere vfxt, bağımsız bölgeleri hariç tüm bölgelerde desteklenir (Çin, Almanya). Kullanmak istediğiniz bölgenin büyük miktarda bilgi işlem çekirdeğini ve avere vFXT kümesini oluşturmak için gereken sanal makine örneklerini destekleyebileceği emin olun.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Avere vFXT ile ilgili yardım almak Nasıl yaparım??

Özelleştirilmiş bir destek grubu, Azure için avere vFXT ile yardım sağlar. Azure portal bir destek bileti açmak için [sisteminizle ilgili yardım alın](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) bölümündeki yönergeleri izleyin.

### <a name="is-avere-vfxt-highly-available"></a>Avere vFXT yüksek oranda kullanılabilir mi?

Evet, avere vFXT özel olarak bir HA çözümü olarak çalışır.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Azure için avere vFXT, diğer bulut hizmetlerini de destekliyor mu?

Evet, müşteriler avere vFXT kümesi ile birden fazla bulut sağlayıcısı kullanabilir. AWS S3 standart demetlerini, Google Cloud Services standart demetlerini ve Azure Blob kapsayıcıları destekler.

> [!NOTE] 
> Yazılım ücreti, AWS veya Google bulutu 'nda avere vFXT kullanımı için geçerlidir, ancak Azure ile kullanılamaz.

## <a name="technical-compute"></a>Teknik: Işlem

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Bir avere vFXT ortamının "nasıl göründüğünü" tanımlayabilir misiniz?

Avere vFXT, birden çok Azure sanal makinesi tarafından oluşturulan kümelenmiş bir gereç. Bir Python kitaplığı, küme oluşturma, silme ve değiştirme işlemini gerçekleştirir. Daha fazla bilgi edinmek için [Azure Için avere vFXT nedir?](avere-vfxt-overview.md) makalesini okuyun.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Avere vFXT ne tür Azure sanal makineleri üzerinde çalışıyor?  

Azure kümesi için bir avere vFXT Microsoft Azure sanal makineler E32s_v3 kullanır.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.

-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Avere vFXT ortamı ölçeklendirsin mi?

Avere vFXT kümesi, üç sanal makine düğümü kadar küçük veya 24 düğüm kadar büyük olabilir. Dokuz taneden fazla düğüm kümesine ihtiyaç duymadıysanız planlama konusunda yardım almak için Azure teknik desteği 'ne başvurun. Daha büyük sayıda düğüm daha büyük bir dağıtım mimarisi gerektirir.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Avere vFXT ortamı "otomatik ölçeklendirme" mı?

Hayır. Küme boyutunu yukarı ve aşağı ölçeklendirebilirsiniz, ancak küme düğümlerini ekleme veya kaldırma işlemi el ile gerçekleştirilen bir adımdır.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Avere vFXT kümesini sanal makine ölçek kümesi olarak çalıştırabilir miyim?

Avere vFXT, bir sanal makine ölçek kümesinin dağıtımını desteklemez. Çeşitli yerleşik kullanılabilirlik desteği mekanizmaları yalnızca bir kümeye katılan atomik VM 'Ler için tasarlanmıştır.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Düşük öncelikli VM 'lerde avere vFXT kümesini çalıştırabilir miyim?

Hayır, sistem temeldeki kararlı bir sanal makine kümesi gerektirir.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Avere vFXT kümesini kapsayıcılarda çalıştırabilir miyim?

Hayır, avere vFXT bağımsız bir uygulama olarak dağıtılmalıdır.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Avere vFXT VM 'Leri işlem kotamla karşı mı?

Evet. Kümeyi desteklemek için bölgede yeterli kotayı kullandığınızdan emin olun.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Avere vFXT kümesi makinelerini farklı kullanılabilirlik bölgelerinde çalıştırabilir miyim?

Hayır. Avere vFXT içindeki yüksek kullanılabilirlik modeli, farklı kullanılabilirlik bölgelerinde bulunan bireysel avere vFXT kümesi üyelerini desteklemez.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Avere vFXT sanal makinelerini kopyalayabilir miyim?

Hayır, avere vFXT kümesinde düğüm eklemek veya kaldırmak için desteklenen Python betiğini kullanmanız gerekir. Daha fazla bilgi için [avere vFXT kümesini yönetme](avere-vfxt-manage-cluster.md)makalesini okuyun.  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Kendi yerel ortammda çalıştırabileceğiniz yazılımın bir "VM" sürümü var mı?

Hayır, sistem kümelenmiş gereç olarak sunulur ve belirli sanal makine türlerinde test edilir. Bu kısıtlama, müşterilerin tipik bir avere vFXT iş akışının yüksek performanslı gereksinimlerini destekleyebilen bir sistem oluşturmaktan kaçınmasına yardımcı olur.

## <a name="technical-disks"></a>Teknik: diskler

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Azure VM 'Leri için ne tür diskler destekleniyor?

Azure için avere vFXT, 1 TB veya 4 TB Premium SSD yapılandırması kullanabilir. Premium SSD yapılandırması birden çok yönetilen disk olarak dağıtılabilir.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Küme, yönetilmeyen diskleri destekliyor mu?

Hayır, küme yönetilen diskler gerektirir.

### <a name="does-the-system-support-local-attached-ssds"></a>Sistem yerel (ekli) SSD 'yi destekliyor mu?

Azure için avere vFXT Şu anda yerel SSD 'leri desteklemiyor. Avere vFXT için kullanılan disklerin kapatılıp yeniden başlatılması gerekir, ancak bu yapılandırmadaki yerel ekli SSD 'Ler yalnızca sonlandırılabilir.

### <a name="does-the-system-support-ultra-ssds"></a>Sistem Ultra SSD 'leri destekliyor mu?

Hayır, sistem yalnızca Premium SSD yapılandırmasını destekler.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Premium SSD 'larımı ayırabilir ve daha sonra kullanım arasındaki önbellek içeriğini korumak için yeniden eklenebilir miyim?

SSD 'Lerin ayrılması ve yeniden iliştirme desteklenmez. Kaynaktaki meta veriler veya dosya içerikleri, veri bütünlüğü sorunlarına neden olabilecek kullanımları arasında değişmiş olabilir.

### <a name="does-the-system-encrypt-the-cache"></a>Sistem önbelleği şifreliyor mu?

Veriler, diskler arasında şeritlenir ancak şifrelenmez. Ancak, disklerin kendisi şifrelenebilir. Daha fazla bilgi için bkz. [Azure 'da sanal makinelerde Ilkeleri güvenli hale getirme ve kullanma](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Teknik: ağ Iletişimi

### <a name="what-network-is-recommended"></a>Hangi ağ önerilir?

Avere vFXT ile şirket içi depolama kullanıyorsanız, 1 GB/sn veya daha iyi bir ağ bağlantısına sahip olmanız gerekir. Az miktarda veriniz varsa ve işleri çalıştırmadan önce verileri buluta kopyalamak istiyorsanız VPN bağlantısı yeterince olabilir. 

> [!TIP]
> Ağ bağlantısı ne kadar yavaşsa, başlangıçtaki soğuk okuma sayısı daha yavaş olur. Yavaş okumalar iş işlem hattının gecikmesini arttırır.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Avere vFXT 'yi işlem kümemdeki farklı bir sanal ağda çalıştırabilir miyim?

Evet, avere vFXT sisteminizi farklı bir sanal ağda oluşturabilirsiniz. Ayrıntılar için [avere vFXT sisteminizin planını](avere-vfxt-deploy-plan.md) okuyun.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Avere vFXT kendi alt ağını gerektiriyor mu?

Evet. Avere vFXT tamamen yüksek kullanılabilirlik (HA) kümesi olarak çalışır ve çalışması için birden çok IP adresi gerekir. Küme kendi alt ağdaysa, IP adresi çakışmaları riskinden kaçınabilirsiniz, bu da yükleme ve normal işlem sorunlarına neden olabilir. Kümenin alt ağı, hiçbir IP adresi çakıştığına sahip olan sanal ağ içinde olabilir.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Avere vFXT 'yi InfiniBand üzerinde çalıştırabilir miyim?

Hayır, avere vFXT yalnızca Ethernet/IP kullanır.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Avere vFXT 'den şirket içi NAS ortamlarıma erişim Nasıl yaparım??

Avere vFXT ortamı, bir ağ geçidi ya da VPN (ve geri) üzerinden bir ağ geçidi veya VPN aracılığıyla yönlendirilmiş erişim gerektirdiğinden diğer tüm Azure VM 'leri gibidir. Ortamınızda kullanılabiliyorsa Azure ExpressRoute bağlantısı kullanmayı göz önünde bulundurun.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Avere vFXT için bant genişliği gereksinimleri nelerdir?

Genel bant genişliği gereksinimi iki etkene bağlıdır:

* Kaynaktan istenen veri miktarı
* İlk veri yükleme sırasında istemci sistemin gecikme süresi toleransı  

Gecikme süresine duyarlı ortamlar için en az 1 Gbps bağlantı hızına sahip bir fiber çözüm kullanmanız gerekir. Varsa ExpressRoute kullanın.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Avere vFXT 'yi genel IP adresleriyle çalıştırabilir miyim?

Hayır, avere vFXT, en iyi yöntemlerle güvenliği sağlanmış bir ağ ortamında işletilyöneliktir.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Internet erişimini Kümemin sanal ağından kısıtlayabilirim miyim?

Genel olarak, gerektiğinde sanal ağınızda ek güvenlik yapılandırabilirsiniz, ancak bazı kısıtlamalar kümenin çalışmasını etkileyebilir.

Örneğin, sanal ağınızdan giden Internet erişiminin kısıtlanması, Ayrıca, Azurecı 'ye açıkça erişim izni veren bir kural eklemediğiniz takdirde küme için sorunlara neden olur. Bu durum [GitHub 'daki ek belgelerde](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md)açıklanmaktadır.

Özelleştirilmiş güvenlik konusunda yardım için, [sisteminizle ilgili yardım alın](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)bölümünde açıklandığı gibi desteğe başvurun.

## <a name="technical-back-end-storage-core-filers"></a>Teknik: arka uç depolama (çekirdek dosyasıları)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Tek bir avere vFXT ortamı kaç adet çekirdek filerdestekler?

Bir avere vFXT kümesi en fazla 20 çekirdekli filers 'yi destekler.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Avere vFXT ortamı verileri nasıl saklar?

Avere vFXT depolama değildir. Bu, çekirdek filers adlı birden çok depolama hedefi ile verileri okuyan ve yazan bir önbelleğidir. Avere vfxt 'de Premium SSD disklerinde depolanan veriler geçicidir ve sonunda arka uç çekirdek dosyalayıcı depolama alanına temizlenir.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Hangi çekirdek dosyasıları avere vFXT destekler?

Genel koşullarda, Azure için avere vFXT, çekirdek filers olarak aşağıdaki sistemleri destekler:

* Dell EMC Isilon (OneFS 7,1, 7,2, 8,0 ve 8,1) 
* NetApp ONTAP (kümelenmiş Mod 9,4, 9,3, 9,2, 9.1 P1, 8.0-8.3) ve (7-Mod 7. *, 8.0-8.3)

  > [!NOTE]
  > Azure NetApp Files Şu anda desteklenmiyor.

* Azure Blob kapsayıcıları (yalnızca yerel olarak yedekli depolama)
* AWS S3 demetleri
* Google bulut demetleri

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Neden avere vFXT tüm NFS dosyası'leri destekliyor?

Tüm NFS platformları aynı IETF standartlarını karşılıyorsa, ancak her bir uygulama kendi kendine sahiptir. Bu ayrıntılar, avere vFXT 'nin depolama sistemiyle nasıl etkileşime gireceğini etkiler. Desteklenen sistemler Market 'teki en yaygın kullanılan platformlardır.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT özel nesne depolamayı (SwiftStack gibi) destekler mi?

Avere vFXT özel nesne depolamayı desteklemez.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Destek kapsamında belirli bir depolama ürününü nasıl alabilirim?

Destek, alanındaki talep miktarına göre belirlenir. Bir NAS çözümünü desteklemek için yeterli sayıda gelir tabanlı istek varsa, bunu kabul edeceğiz. Azure desteği aracılığıyla istek yapın.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Azure Blob depolamayı bir çekirdek filmi olarak kullanabilir miyim?

Evet, Azure için avere vFXT, bir Blok Blobu kapsayıcısını bulut çekirdeği filsi olarak kullanabilir.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Blob çekirdeği filmidir için depolama hesabı gereksinimleri nelerdir?

Depolama hesabınızın bir genel amaçlı v2 (GPv2) hesabı olması ve yalnızca yerel olarak yedekli depolama için yapılandırılmış olması gerekir. Coğrafi olarak yedekli depolama ve bölge yedekli depolama desteklenmez.

### <a name="can-i-use-archive-blob-storage"></a>Arşiv BLOB depolama alanını kullanabilir miyim?

Hayır. Arşiv depolaması için hizmet düzeyi sözleşmesi (SLA), avere vFXT sisteminin gerçek zamanlı dizin ve dosya erişim gereksinimleriyle uyumlu değildir.

### <a name="can-i-use-cool-blob-storage"></a>Cool blob Storage kullanabilir miyim?

Seyrek Erişimli Katman blob depolaması, genellikle Azure Core Filer için bir avere vFXT için önerilmez. Cool Tier, daha düşük depolama maliyetleri sunar (Daha fazla ayrıntı için bkz. [Blok Blobu fiyatlandırması](<https://azure.microsoft.com/pricing/details/storage/blobs/>) .) Veriler sıklıkla erişilip değiştirilmeyecektir veya siliniyorsa, lütfen etkin katmanı kullanmayı düşünün.

[Erişim katmanları](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) , bir vFXT Core filsi olarak Seyrek Erişimli Katman depolaması kullanmanın ne zaman yararlı olabileceğini hakkında daha fazla bilgi sağlar.

### <a name="how-do-i-encrypt-the-blob-container"></a>Blob kapsayıcısını şifrelemek Nasıl yaparım? mı?

Blob şifrelemesini Azure 'da (tercih edilen) ya da avere vfxt Core dosyalayıcı düzeyinde yapılandırabilirsiniz.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Blob çekirdeği filmi için kendi şifreleme anahtarımı kullanabilir miyim?

Varsayılan olarak, veriler Azure blob, tablo ve kuyruk depolama için Microsoft tarafından yönetilen anahtarlar ve ayrıca Azure dosyaları aracılığıyla şifrelenir. BLOB depolama ve Azure dosyaları için şifreleme için kendi anahtarınızı getirebilirsiniz. Avere vFXT şifrelemesini kullanmayı seçerseniz, avere tarafından oluşturulan anahtarı kullanmanız ve yerel olarak depolamanız gerekir.

## <a name="purchasing"></a>Satın alma

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Azure lisanslama için avere vFXT al Nasıl yaparım??

Azure lisansına yönelik bir avere vFXT 'nin alınması, Azure Marketi aracılığıyla kolay bir işlemdir. Bir Azure hesabı için kaydolun ve ardından bir avere vFXT kümesi oluşturmak için [avere vfxt kümesini dağıtma](avere-vfxt-deploy.md) bölümündeki yönergeleri izleyin.

### <a name="how-much-does-avere-vfxt-cost"></a>VFXT maliyeti ne kadar avere?

Azure 'da avere vFXT kümelerini kullanmak için ek bir lisans ücreti yoktur. Müşteriler, depolama ve diğer Azure tüketim ücretlerine göre sorumludur.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Avere vFXT sanal makineleri düşük öncelikli olarak çalıştırılabilir mi?

Hayır, avere vFXT kümeleri "Always On" hizmetini gerektirir. Gerekli olmadığında kümeler kapatılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure için avere vFXT 'yi kullanmaya başlamak için, kendi sisteminizi nasıl planlayıp dağıtacağınızı öğrenmek için şu makaleleri okuyun:

* [Avere vFXT sisteminizi planlama](avere-vfxt-deploy-plan.md)
* [Dağıtıma genel bakış](avere-vfxt-deploy-overview.md)
* [Avere vFXT kümesi oluşturmaya hazırlanma](avere-vfxt-prereqs.md)
* [Avere vFXT kümesini dağıtma](avere-vfxt-deploy.md)

Avere vFXT için yetenekler ve kullanım örnekleri hakkında daha fazla bilgi edinmek için [Azure Için avere vfxt](https://azure.microsoft.com/services/storage/avere-vfxt/)adresini ziyaret edin.
