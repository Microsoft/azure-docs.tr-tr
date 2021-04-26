---
title: Azure Dosya Eşitleme Linux geçişi
description: Linux sunucu konumundan Azure Dosya Eşitleme ve Azure dosya paylaşımlarına sahip karma bulut dağıtımına dosya geçirmeyi öğrenin.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564263"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Azure Dosya Eşitleme ile Linux 'tan karma bulut dağıtımına geçiş

Bu geçiş makalesi, NFS ve Azure Dosya Eşitleme anahtar kelimeleriyle ilgili birkaç tane biridir. Bu makalenin senaryonuz için geçerli olup olmadığını denetleyin:

> [!div class="checklist"]
> * Veri kaynağı: ağa bağlı depolama (NAS)
> * Geçiş rotası: SAMBA &rArr; Windows Server 2012R2 veya sonraki bir sürüme sahip Linux Server, &rArr; Azure dosya paylaşımıyla eşitleniyor
> * Şirket içi dosyaları önbelleğe alma: Evet, son hedef, Azure Dosya Eşitleme bir dağıtımdır.

Senaryonuz farklıysa, [geçiş kılavuzlarındaki tabloya](storage-files-migration-overview.md#migration-guides)bakın.

Azure Dosya Eşitleme, doğrudan bağlı depolama (DAS) ile Windows Server örneklerinde çalışmaktadır. Linux istemcilerinden ve bu bilgisayarlardan veya bir uzak sunucu Ileti bloğu (SMB) paylaşımına veya ağ dosya sistemi (NFS) paylaşımlarına eşitlemeyi desteklemez.

Sonuç olarak, dosya hizmetlerinizi karma dağıtıma dönüştürmek, Windows Server 'a geçiş yapmayı zorunlu kılar. Bu makale, böyle bir geçişin planlanması ve yürütülmesi sırasında size rehberlik eder.

## <a name="migration-goals"></a>Geçiş hedefleri

Amaç, Linux Samba sunucunuzda bulunan paylaşımları bir Windows Server örneğine taşımaktır. Karma bulut dağıtımı için Azure Dosya Eşitleme kullanın. Bu geçişin, geçiş sırasında üretim verilerinin bütünlüğünü ve kullanılabilirliğini garanti eden bir şekilde yapılması gerekir. İkinci olarak, kapalı kalma süresinin en az bir süre içinde tutulması gerekir, böylece normal bakım pencereleri içine sığacak veya yalnızca biraz daha fazla olabilir.

## <a name="migration-overview"></a>Geçişe genel bakış

Azure dosyaları [geçişine genel bakış makalesinde](storage-files-migration-overview.md)belirtildiği gibi, doğru kopyalama aracını ve yaklaşımı kullanmak önemlidir. Linux Samba sunucunuz, SMB paylaşımlarını doğrudan yerel ağınızda kullanıma sunmaktadır. Windows Server 'da yerleşik olan Robocopy, dosyalarınızı bu geçiş senaryosunda taşımanın en iyi yoludur.

Linux sunucunuzda Samba çalıştırmıyorsanız ve Windows Server 'da klasörleri karma dağıtıma geçirmek istiyorsanız Robocopy yerine Linux kopyalama araçlarını kullanabilirsiniz. Kopyalama araclarınızın uygunluk özelliklerini unutmayın. Bir kopyalama aracında neleri aradığınızı öğrenmek için geçişe genel bakış makalesindeki [Geçiş temelleri bölümüne](storage-files-migration-overview.md#migration-basics) bakın.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1. Aşama: kaç Azure dosya paylaşımına ihtiyacınız olduğunu belirleme

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>2. Aşama: şirket içinde uygun bir Windows Server örneği sağlama

* Bir sanal makine veya fiziksel sunucu olarak Windows Server 2019 örneği oluşturun. Windows Server 2012 R2 en düşük gereksinimdir. Bir Windows Server yük devretme kümesi de desteklenir.
* Doğrudan bağlı depolama alanı (DAS) sağlayın veya ekleyin. Ağa bağlı depolama (NAS) desteklenmiyor.

  Azure Dosya Eşitleme [bulut katmanlama](storage-sync-cloud-tiering-overview.md) özelliğini kullanıyorsanız, sağladığınız depolama alanı, Linux Samba sunucunuzda kullanmakta olduğunuz miktardan daha küçük olabilir. 

Sağladığınız depolama alanı miktarı, Linux Samba sunucunuzda kullanmakta olduğunuz miktardan daha küçük olabilir. Bu yapılandırma seçeneği, Azure dosya eşitleme [bulut katmanlama](storage-sync-cloud-tiering-overview.md) özelliğini de kullanmanızı gerektirir. Ancak, daha sonraki bir aşamada dosyalarınızı daha büyük Linux Samba sunucu alanından daha küçük Windows Server birimine kopyaladığınızda, toplu işlerle çalışmanız gerekir:

  1. Diske sığan bir dosya kümesini taşıyın.
  2. Dosya eşitlemeye ve bulut katmanlamasına izin verin.
  3. Birimde daha fazla boş alan oluşturulduğunda, sonraki dosya toplu iş ile devam edin. Alternatif olarak, yeni anahtarın kullanımı için yaklaşan [Robocopy bölümünde](#phase-7-robocopy) Robocopy komutunu gözden geçirin `/LFSM` . Kullanmak `/LFSM` Robocopy işlerinizin önemli ölçüde basitleşmesini sağlayabilir, ancak buna bağlı olabileceğiniz diğer Robocopy anahtarlarıyla uyumlu değildir.
    
  Dosyalarınızın Linux Samba sunucusunda kaplayacağı Windows Server örneğindeki eşdeğer alanı sağlayarak bu toplu işleme yaklaşımına engel olabilirsiniz. Windows üzerinde Yinelenenleri kaldırmayı etkinleştirmeyi düşünün. Bu yüksek miktarda depolama alanını Windows Server örneğinize kalıcı olarak kaydetmek istemiyorsanız, geçişten sonra ve bulut katmanlama ilkelerini ayarlamadan önce birim boyutunu azaltabilirsiniz. Bu, Azure dosya paylaşımlarınızın daha küçük bir şirket içi önbelleği oluşturur.

Dağıttığınız Windows Server örneğinin kaynak yapılandırması (işlem ve RAM), genellikle eşittabileceğiniz öğe sayısına (dosya ve klasör) bağlıdır. Herhangi bir endişeniz varsa daha yüksek performanslı bir yapılandırma ile devam etmenizi öneririz.

[Bir Windows Server örneğinin, eşitlenmesi gereken öğe sayısına (dosya ve klasör) göre nasıl boyutlandıralınacağını öğrenin.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Daha önce bağlanılan makalede, sunucu belleği (RAM) için bir aralığa sahip bir tablo sunulmaktadır. Sunucunuz için daha küçük bir sayıya yönlendirebilirsiniz, ancak ilk eşitlemenin önemli ölçüde daha uzun sürme süresini tahmin edebilirsiniz.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3. Aşama: Azure Dosya Eşitleme bulut kaynağını dağıtma

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4. Aşama: Azure depolama kaynaklarını dağıtma

Bu aşamada, 1. Aşama ' daki eşleme tablosuna başvurun ve bunların içinde doğru sayıda Azure depolama hesabı ve dosya paylaşımı sağlamak için bunu kullanın.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5. Aşama: Azure Dosya Eşitleme aracısını dağıtma

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>6. Aşama: Windows Server dağıtımında Azure Dosya Eşitleme yapılandırma

Kayıtlı şirket içi Windows Server Örneğiniz, bu işlem için önceden hazırlanmalıdır ve internet 'e bağlanmalıdır.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Bulut katmanlama, yerel sunucunun bulutta depolankıyasla daha az depolama kapasitesine sahip olmasına izin veren Azure Dosya Eşitleme özelliğidir, ancak tam ad alanı kullanılabilir. Yerel olarak ilginç veriler de hızlı erişim performansı için yerel olarak önbelleğe alınır. Bulut katmanlaması, her bir Azure Dosya Eşitleme sunucu uç noktası için isteğe bağlı bir özelliktir.

> [!WARNING]
> Windows Server birimlerinizde Linux Samba sunucusunda kullanılan verilerinize kıyasla daha az depolama alanı sağladıysanız, bulut katmanlaması zorunludur. Bulut katmanlamayı açmazsanız, sunucunuz tüm dosyaları depolamak için alan boşaltmaz. Geçiş için geçici olarak, bir birim için yüzde 99 boş alana kadar katmanlama ilkenizi ayarlayın. Geçiş tamamlandıktan sonra bulut katmanlama ayarlarınıza döndiğinizden emin olun ve ilkeyi uzun dönem için daha kullanışlı bir düzeye ayarlayın.

Eşitleme grubu oluşturma adımlarını ve eşitleme için yapılandırılması gereken tüm Azure dosya paylaşımları ve sunucu konumları için sunucu uç noktası olarak eşleşen sunucu klasörü ekleme adımlarını yineleyin.

Tüm sunucu uç noktaları oluşturulduktan sonra eşitleme çalışır. Bir test dosyası oluşturabilir ve sunucu konumunuzda bağlı Azure dosya paylaşımında (eşitleme grubundaki bulut uç noktasında açıklandığı gibi) eşitlemeyi görebilirsiniz.

Her iki konum, sunucu klasörleri ve Azure dosya paylaşımları, başka türlü boş ve veri bekliyor. Bir sonraki adımda, dosyaları buluta taşımak için Azure Dosya Eşitleme Windows Server örneğine kopyalamak için başlarsınız. Bulut katmanlamayı etkinleştirdiyseniz, yerel birimlerde kapasiteniz tükenirse sunucu dosyaları katmana başlar.

## <a name="phase-7-robocopy"></a>7. Aşama: Robocopy

Temel geçiş yaklaşımı, dosyaları kopyalamak ve eşitleme yapmak için Azure Dosya Eşitleme kullanmak için Robocopy kullanmaktır.

İlk yerel kopyayı Windows Server hedef klasörünüze çalıştırın:

1. Linux Samba sunucunuzdaki ilk konumu belirler.
1. Üzerinde zaten yapılandırılmış Azure Dosya Eşitleme Windows Server örneğinde eşleşen klasörü belirler.
1. Robocopy kullanarak kopyayı başlatın.

Aşağıdaki Robocopy komutu, Linux Samba sunucunuzun depolama alanındaki dosyaları Windows Server hedef klasörünüze kopyalayacaktır. Windows Server onu Azure dosya paylaşımlarıyla eşitler. 

Windows Server Örneğiniz Linux Samba sunucusuna düşenden daha az depolama alanı sağladıysanız, bulut katmanlaması yapılandırmış olursunuz. Yerel Windows Server birimi tam aldığından, [bulut katmanlaması](storage-sync-cloud-tiering-overview.md) başlar ve zaten eşitlenmiş olan dosyaları ve katman dosyalarını başarıyla eşitledi. Bulut katmanlaması, Linux Samba sunucusundan kopyalamaya devam etmek için yeterli alan oluşturacak. Bulut katmanlaması, bir süre sonra, bir birim için yüzde 99 boş alanın ilkesine ulaşmak üzere disk alanını nasıl eşitlediğini görmek için saatte bir kez denetler.

Robocopy, bulut ve katmana yerel olarak eşitlenebilmeniz için dosyaları daha hızlı taşıdığından, yerel disk alanının tükenmesine yol açabilir. Robocopy daha sonra başarısız olur. Paylaşımlar üzerinde sorunu önleyen bir sırada çalışmanız önerilir. Örneğin, aynı zamanda tüm paylaşımlar için Robocopy işlerini başlatmayabilirsiniz. Ya da Windows Server örneğindeki geçerli boş alan miktarına uygun olan paylaşımları taşımayı düşünün. Robocopy işiniz başarısız olursa, aşağıdaki yansıtma/Temizleme seçeneğini kullandığınız sürece komutu her zaman yeniden çalıştırabilirsiniz:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>8. Aşama: Kullanıcı tarafından kesilen

Robocopy komutunu ilk kez çalıştırdığınızda, kullanıcılarınız ve uygulamalarınız Linux Samba sunucusundaki dosyalara erişmeye devam eder ve büyük olasılıkla bunları değiştiriyor. Robocopy bir dizini işledi ve bir sonrakine geçer ve ardından kaynak konumdaki (Linux) bir Kullanıcı bu geçerli Robocopy çalıştırmasında işlenmeyecek bir dosyayı ekler, değiştirir veya siler. Bu beklenen bir davranıştır.

İlk çalıştırma, Azure Dosya Eşitleme aracılığıyla verilerin toplu olarak Windows Server Örneğiniz ve buluta taşınması ile ilgilidir. Bu ilk kopya, şu değere bağlı olarak uzun zaman alabilir:

* İndirme bant genişliğiniz.
* Karşıya yükleme bant genişliği.
* Yerel ağ hızı ve Robocopy iş parçacıklarının sayısının ne kadar en iyi şekilde eşleştiğini.
* Robocopy ve Azure Dosya Eşitleme işlemesi gereken öğelerin (dosya ve klasör) sayısı.

İlk çalıştırma tamamlandıktan sonra, komutu yeniden çalıştırın.

İkinci kez daha hızlı tamamlanır, çünkü yalnızca son çalıştırmasından bu yana gerçekleşen değişiklikleri taşıması gerekir. Bu saniye boyunca, yeni değişiklikleri Çalıştır hala birikmeye devam edebilir.

Belirli bir konum için bir Robocopy işlemini tamamlamaya yönelik sürenin süresinin, kapalı kalma süresi için kabul edilebilir bir pencere içinde olması karşılanana kadar bu işlemi tekrarlayın.

Kapalı kalma süresini kabul ediyorsanız ve Linux konumunu çevrimdışına almak için hazırlandıysanız, paylaşılan kökündeki ACL 'Leri, kullanıcıların artık konuma erişememesi için değiştirebilirsiniz. Ya da içeriğin Linux sunucunuzdaki bu klasörde değiştirilmesini engelleyen herhangi bir uygun adımı izleyebilirsiniz.

Bir son Robocopy Round çalıştırın. Bu, kaçırılmış olabilecek tüm değişiklikleri seçer. Bu son adımın ne kadar süreceği, Robocopy taramasının hızına bağlıdır. Önceki çalıştırmanın ne kadar sürdüğünü ölçerek süreyi tahmin edebilirsiniz (kapalı kalma süresine eşittir).

Windows Server klasöründe bir paylaşma oluşturun ve büyük olasılıkla DFS-N dağıtımınızı bunu işaret etmek üzere ayarlayın. Linux Samba sunucu SMB paylaşımlarınız ile aynı paylaşım düzeyi izinlerini ayarladığınızdan emin olun. Linux Samba sunucunuzda yerel kullanıcılar kullandıysanız, bu kullanıcıları Windows Server yerel kullanıcıları olarak yeniden oluşturmanız gerekir. Ayrıca, Robocopy 'nin üzerine taşınan mevcut SID 'Leri Windows Server Örneğiniz için yeni Windows Server yerel kullanıcılarınızın SID 'Lerine eşlemeniz gerekir. Active Directory hesapları ve ACL 'Leri kullandıysanız, Robocopy bunları olduğu gibi taşır ve başka bir eylem gerekmez.

Bir paylaşımı veya bir paylaşım grubunu ortak bir köke veya birime geçirmeyi tamamladınız (1. aşama, eşlemelerinize bağlı olarak).

Bu kopyaların birkaçını paralel olarak çalıştırmayı deneyebilirsiniz. Tek seferde bir Azure dosya paylaşımının kapsamını işlemeyi öneririz.

> [!WARNING]
> Linux Samba sunucunuzdaki tüm verileri Windows Server örneğine taşıdıktan ve geçiş işlemi tamamlandıktan sonra, Azure portal *Tüm*  eşitleme gruplarına geri dönün. Bulut katmanlama birimi için boş alan yüzdesini, önbellek kullanımının yüzde 20 ' si için daha uygun bir değere ayarlayın. 

Bulut katmanlama birimindeki boş alan ilkesi, büyük olasılıkla birden çok sunucu uç noktası ile eşitlenmesi olan bir birim düzeyinde çalışır. Bir sunucu uç noktasında boş alanı ayarlamayı unutursanız, eşitleme en kısıtlayıcı kuralı uygulamaya devam eder ve boş disk alanını yüzde 99 ' de tutmaya çalışır. Yerel önbellek daha sonra beklediği gibi gerçekleştirilemeyebilir. Amacınız, yalnızca nadiren erişilen arşiv verileri içeren bir birim için ad alanına sahip olduğunda ve diğer bir senaryo için depolama alanının geri kalanını ayırdıysanız, performans kabul edilebilir olabilir.

## <a name="troubleshoot"></a>Sorun giderme

En yaygın sorun, Robocopy komutunun Windows Server tarafında **tam birimle** başarısız olmasına neden olur. Bulut katmanlaması, eşitlenen yerel Windows Server diskinden içerik boşaltmak için saatte bir kez davranır. Amaç, birim üzerinde yüzde 99 boş alana ulaşmaktır.

Eşitleme ilerleme durumu ve bulut katmanlaması için disk alanının serbest olmasına izin verin. Windows Server 'da dosya Gezgini ' nde bunu gözlemleyebilirsiniz.

Windows Server Örneğiniz yeterli kullanılabilir kapasiteye sahip olduğunda, komutu yeniden çalıştırmak sorunu çözer. Bu durumla karşılaşdığınızda hiçbir şey kesilmez ve güvenle ileri doğru gezinebilirsiniz. Komutu yeniden çalıştırmanın bir nedeni tek sonucudur.

Sorun giderme Azure Dosya Eşitleme sorunları için aşağıdaki bölümdeki bağlantıyı denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure dosya paylaşımları ve Azure Dosya Eşitleme hakkında daha fazla bilgi bulabilirsiniz. Aşağıdaki makalelerde gelişmiş seçenekler, en iyi uygulamalar ve sorun giderme yardımı bulunur. Bu makaleler, [Azure dosya paylaşma belgelerini](storage-files-introduction.md) uygun şekilde bağlar.

* [Azure Dosya Eşitleme genel bakış](./storage-sync-files-planning.md)
* [Azure Dosya Eşitleme dağıtım kılavuzu](./storage-how-to-create-file-share.md)
* [Azure Dosyalar Eşitleme sorunlarını giderme](storage-sync-files-troubleshoot.md)