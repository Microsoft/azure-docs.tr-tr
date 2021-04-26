---
title: Azure dosya paylaşımlarına şirket içi NAS geçişi
description: Şirket içi ağa bağlı depolama (NAS) konumundan Azure veri kutusu ile Azure dosya paylaşımlarına dosya geçirmeyi öğrenin.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a8420d23c8bda29290722975ada2acca6733f0e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491703"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>Ağ bağlantılı depolama alanından (NAS) Azure dosya paylaşımlarına geçiş yapmak için veri kutusunu kullanın

Bu geçiş makalesi, NAS ve Azure veri kutusu anahtar sözcüklerini içeren birkaç bir biridir. Bu makalenin senaryonuz için geçerli olup olmadığını denetleyin:

> [!div class="checklist"]
> * Veri kaynağı: ağa bağlı depolama (NAS)
> * Geçiş rotası: NAS &rArr; veri kutusu &rArr; Azure dosya paylaşma
> * Şirket içi dosyaları önbelleğe alma: Hayır, son amaç Azure dosya paylaşımlarını doğrudan bulutta kullanmaktır. Azure Dosya Eşitleme kullanmak için bir plan yoktur.

Senaryonuz farklıysa, [geçiş kılavuzlarındaki tabloya](storage-files-migration-overview.md#migration-guides)bakın.

Bu makale, NAS gerecinizden işlevsel Azure dosya paylaşımlarına geçiş yapmak için gereken planlama, dağıtım ve ağ yapılandırmalarında uçtan uca kılavuzluk eder. Bu kılavuzda toplu veri aktarımı (çevrimdışı veri aktarımı) için Azure veri kutusu kullanılmaktadır.

## <a name="migration-goals"></a>Geçiş hedefleri

Amaç, NAS gerecinizde bulunan paylaşımları Azure 'a taşımaktır ve bunların bir Windows Server gerekmeden kullanabileceğiniz yerel Azure dosya paylaşımları olmasını sağlar. Bu geçişin, geçiş sırasında üretim verilerinin bütünlüğünü ve kullanılabilirliğini garanti eden bir şekilde yapılması gerekir. İkinci olarak, kapalı kalma süresinin en az bir süre içinde tutulması gerekir, böylece normal bakım pencereleri içine sığacak veya yalnızca biraz daha fazla olabilir.

## <a name="migration-overview"></a>Geçişe genel bakış

Geçiş işlemi birkaç aşamadan oluşur. Azure depolama hesaplarını ve dosya paylaşımlarını dağıtmanız, ağı yapılandırmanız, Azure veri kutusunu kullanarak geçirmeniz, RoboCopy aracılığıyla değişikliklerle yakalamalı ve son olarak, yeni oluşturulan Azure dosya paylaşımlarına kullanıcılarınızın bağlantısını kesmeniz gerekir. Aşağıdaki bölümlerde, geçiş işleminin aşamaları ayrıntılı olarak açıklanır.

> [!TIP]
> Bu makaleye geri döndürülürken, kaldığınız yerden geçiş aşamasına geçmek için sağ taraftaki gezintiyi kullanın.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1. Aşama: kaç Azure dosya paylaşımına ihtiyacınız olduğunu belirleme

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2. Aşama: Azure depolama kaynaklarını dağıtma

Bu aşamada, 1. Aşama ' daki eşleme tablosuna başvurun ve bunların içinde doğru sayıda Azure depolama hesabı ve dosya paylaşımı sağlamak için bunu kullanın.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>3. Aşama: kaç Azure veri kutusu gereçini gerektiğini belirleme

Bu adımı yalnızca önceki aşamayı tamamladığınızda başlatın. Azure depolama kaynaklarınızın (depolama hesapları ve dosya paylaşımları) Şu anda oluşturulması gerekir. Veri kutusu Siparişiniz sırasında, veri kutusu 'nun verileri taşımakta olduğu depolama hesaplarına belirtmeniz gerekir.

Bu aşamada, geçiş planının sonuçlarını önceki aşamasından, kullanılabilir veri kutusu seçeneklerinin sınırlarına eşlemeniz gerekir. Bu noktalar, hangi veri kutusu seçeneklerini belirlemeniz gerektiğine ve NAS paylaşımlarınızı Azure dosya paylaşımlarına taşımak için kaç tane ihtiyacınız olacağını bir plan yapmanıza yardımcı olur.

Ne tür cihazlara ihtiyacınız olduğunu öğrenmek için şu önemli limitleri göz önünde bulundurun:

* Herhangi bir Azure veri kutusu, verileri en fazla 10 depolama hesabına taşıyabilir. 
* Her bir veri kutusu seçeneği kendi kullanılabilir kapasitesine sahiptir. Bkz. [veri kutusu seçenekleri](#databox-options).

Oluşturmaya karar verdiğiniz depolama hesabı sayısı ve her birindeki paylaşımlar için geçiş planınıza danışın. Ardından, NAS 'inizdeki paylaşımların her birinin boyutuna bakın. Bu bilgileri birleştirmek, hangi gerecin hangi depolama hesaplarına veri göndermesi gerektiğini iyileştirmenize ve karar vermenize olanak tanır. İki veri kutusu cihazı, dosyaları aynı depolama hesabına taşıyabilir, ancak tek bir dosya paylaşımının içeriğini 2 veri kutularından bölemezsiniz.

### <a name="databox-options"></a>Veri kutusu seçenekleri

Standart geçiş için, bu üç DataBox seçeneğinin bir veya birleşimi seçilmelidir: 

* Veri kutusu diskleri Microsoft, en fazla 40 TiB için bir kapasiteye sahip bir ve en fazla beş SSD diski gönderecek. Şifreleme ve dosya sistemi ek yükü nedeniyle kullanılabilir kapasite yaklaşık %20 daha azdır. Daha fazla bilgi için bkz. [databox diskler belgeleri](../../databox/data-box-disk-overview.md).
* Veri kutusu bu en yaygın seçenektir. Bir NAS ile benzer şekilde çalışarak bir ruggekanlı DataBox gereci size gönderilir. Kullanılabilir 80 TiB kapasitesi vardır. Daha fazla bilgi için bkz. [databox belgeleri](../../databox/data-box-overview.md).
* Veri kutusu ağır bu seçenek, 1 PiB kapasitesine sahip bir NAS 'a benzer bir şekilde çalışma ve bir, bir NAS 'a benzer bir ruggekanlı veri kutusu gereci sunar. Şifreleme ve dosya sistemi ek yükü nedeniyle kullanılabilir kapasite yaklaşık %20 daha azdır. Daha fazla bilgi için bkz. [veri kutusu ağır belgeleri](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-temporary-windows-server"></a>4. Aşama: geçici bir Windows Server sağlama

Azure Data Box 'larınızın gelmesini beklerken, RoboCopy işlerini çalıştırmak için ihtiyacınız olacak bir veya daha fazla Windows sunucusunu zaten dağıtabilirsiniz. 

- Bu sunucuların ilk kullanımı, dosyaları DataBox üzerine kopyalayacaktır.
- Bu sunucuların ikinci kullanımı, veri kutusu aktarımdayken, NAS gereci üzerinde gerçekleşen değişikliklerle yakalayacak. Bu yaklaşım kaynak tarafta kapalı kalma süresini en düşük düzeyde tutar.

RoboCopy işlerinizin çalışma hızı, temel olarak şu etkenlere bağlıdır:

* Kaynak ve hedef depolamada ıOPS
* aralarında kullanılabilir ağ bant genişliği </br> Sorun giderme bölümünde daha fazla ayrıntı bulun: [IOPS ve bant genişliği Konuları](#iops-and-bandwidth-considerations)
* bir ad alanındaki dosyaları ve klasörleri hızlı bir şekilde işleyebilme </br> Sorun giderme bölümünde daha fazla ayrıntı bulun: [işleme hızı](#processing-speed)
* RoboCopy çalıştırmaları arasındaki değişiklik sayısı </br> Sorun giderme bölümünde daha fazla ayrıntı bulun: [gereksiz çalışmalarından kaçının](#avoid-unnecessary-work)

Geçici Windows Server 'larınız için sağladığınız RAM ve iş parçacığı sayısı üzerinde karar verirken başvurulan ayrıntıları göz önünde bulundurmanız önemlidir.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>5. Aşama: Azure dosya paylaşımlarını kullanmaya hazırlanma

Zaman kazanmak için, veri kutusunun gelmesi beklenirken bu aşamaya devam etmelisiniz. Bu aşamadaki bilgilerle, Azure 'daki ve Azure 'un dışındaki sunucularınızın ve kullanıcılarınızın Azure dosya paylaşımlarınızı kullanacak şekilde nasıl etkinleştirileceğini seçebileceksiniz. En kritik kararlar şunlardır:

- **Ağ iletişimi:** Ağlarınızın SMB trafiğini yönlendirmenize olanak tanır.
- **Kimlik doğrulama:** Kerberos kimlik doğrulaması için Azure depolama hesaplarını yapılandırın. Depolama hesabınıza katılan AdConnect ve etki alanı, uygulamalarınızın ve kullanıcılarınızın kimlik doğrulaması için AD kimliklerini kullanmasına izin verir
- **Yetkilendirme:** Her Azure dosya paylaşımının Share-Level ACL 'Leri, AD kullanıcılarının ve grupların belirli bir paylaşıma ve bir Azure dosya paylaşımında erişmesine izin verir, yerel NTFS ACL 'Leri de alınır. Dosya ve klasör ACL 'Lerine dayalı yetkilendirme, daha sonra şirket içi SMB paylaşımlarında olduğu gibi çalışacaktır.
- **İş sürekliliği:** Azure dosya paylaşımlarının mevcut bir ortamla tümleştirilmesi, genellikle var olan paylaşım adreslerini korur. Zaten DFS ad alanları kullanmıyorsanız, bunu ortamınızda oluşturmayı düşünün. Kullanıcılarınızın ve betiklerinizin kullanımını, değişmeden tutabileceksiniz. DFS-Namespace hedeflerini, geçişten sonra Azure dosya paylaşımlarına yönlendirerek, SMB için ad alanı yönlendirme hizmeti olarak DFS-N ' y i kullanabilirsiniz.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Bu video, Azure dosya paylaşımlarını doğrudan bilgi çalışanları ve uygulamaları için beş basit adımda güvenli bir şekilde kullanıma sunma kılavuzudur.</br>
        Video, bazı konular için adanmış belgelere başvurur:

* [Kimliğe genel bakış](storage-files-active-directory-overview.md)
* [Etki alanına bir depolama hesabına ekleme](storage-files-identity-auth-active-directory-enable.md)
* [Azure dosya paylaşımları için ağa genel bakış](storage-files-networking-overview.md)
* [Ortak ve özel uç noktaları yapılandırma](storage-files-networking-endpoints.md)
* [S2S VPN yapılandırma](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN 'yi yapılandırma](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN yapılandırma](storage-files-configure-p2s-vpn-linux.md)
* [DNS iletmeyi yapılandırma](storage-files-networking-dns.md)
* [DFS 'yi yapılandırma-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>6. Aşama: dosyaları veri formunuza kopyalama

Veri Kutusağınız geldiğinde, veri kutusunu NAS gerecinize bir görüş satırı olarak ayarlamanız gerekir. Sipariş ettiğiniz veri kutusu türü için kurulum belgelerini izleyin.

* [Data Box’ı ayarlama](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk’i ayarlama](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy’yi ayarlama](../../databox/data-box-heavy-quickstart-portal.md)

Veri kutusu türüne bağlı olarak, sizin için kullanılabilen veri kutusu kopyalama araçları vardır. Bu noktada, dosyaları veri kutusuna tam uygunluk ile kopyalamadıklarından Azure dosya paylaşımlarına geçiş yapılması önerilmez. Bunun yerine RoboCopy kullanın.

Veri kutusun ulaştığı zaman, verileri sıralama sırasında belirttiğiniz her depolama hesabı için önceden sağlanmış SMB paylaşımlarına sahip olur.

* Dosyalarınız Premium bir Azure dosya paylaşımında yer alıyorsa Premium "dosya depolaması" depolama hesabı başına bir SMB paylaşımıyla karşılaşacaktır.
* Dosyalarınız standart bir depolama hesabına gidirsa standart (GPv1 ve GPv2) depolama hesabı başına üç SMB paylaşımı olacaktır. Geçişiniz için yalnızca ile biten dosya paylaşımları geçerlidir `_AzFiles` . Herhangi bir blok ve Sayfa Blobu paylaşımını yoksayın.

Azure veri kutusu belgelerindeki adımları izleyin:

1. [Data Box'a bağlanma](../../databox/data-box-deploy-copy-data.md)
1. Data Box'a veri kopyalama
1. [Veri kutusunu Azure 'a ayrılmaya hazırlayın](../../databox/data-box-deploy-picked-up.md)

Bağlantılı veri kutusu belgeleri bir RoboCopy komutu belirtir. Ancak, komut tam dosya ve klasör uygunluk kalitesini korumak için uygun değildir. Bunun yerine bu komutu kullanın:

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* Bireysel RoboCopy bayraklarının ayrıntıları hakkında daha fazla bilgi edinmek için yaklaşan [Robocopy bölümündeki](#robocopy)tabloya bakın.
* İş parçacığı sayısını uygun şekilde boyutlandırma `/MT:n` , Robocopy hızını iyileştirme ve Robocopy 'yi veri merkezinizde iyi bir komşu hale getirme hakkında daha fazla bilgi edinmek Için [Robocopy sorun giderme bölümüne](#troubleshoot)göz atın.


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>7. Aşama: NAS 'ınızdan yakalama RoboCopy

Veri kutusu, tüm dosya ve klasörleri planlı Azure dosya paylaşımlarına yerleştirdiğini raporladıktan sonra bu aşamaya devam edebilirsiniz.
Yalnızca veri kutusu kopyası başlatıldıktan sonra NAS 'daki veriler değiştiyse, bir catch RoboCopy gerekir. Arşivleme amacıyla bir paylaşımın kullanıldığı bazı senaryolarda, geçiş tamamlanana kadar NAS 'inizdeki paylaşımdaki değişiklikleri durdurabilirsiniz. Ayrıca, geçiş sırasında, NAS paylaşımlarını salt okunurdur olarak ayarlayarak iş gereksinimlerinize sahip olabilirsiniz.

Geçiş sırasında bir paylaşımın okuma-yazma olması gereken ve yalnızca küçük bir kapalı kalma süresi ile artışlarını devralarak, bu catch RoboCopy adımının, Kullanıcı yükünü doğrudan Azure dosya paylaşımında devretmek için önemli olacağı durumlar.

Bu adımda, paylaşımlarınızın veri kutusu üzerinde olduğu zamandan beri, bulut paylaşımlarınızı, NAS 'inizdeki en son değişikliklerle yakalamak için RoboCopy işlerini çalıştıracaksınız.
Bu catch RoboCopy, NAS paylaşımlarınız üzerinde gerçekleşen dalgalanma miktarına bağlı olarak hızlı bir şekilde bitebileceği veya biraz zaman alabilir.

İlk yerel kopyayı Windows Server hedef klasörünüze çalıştırın:

1. NAS gerecinizdeki ilk konumu belirler.
1. Eşleşen Azure dosya paylaşımının kimliğini belirler.
1. Azure dosya paylaşımından geçici Windows sunucunuza yerel ağ sürücüsü olarak bağlama
1. RoboCopy kullanarak kopyayı, açıklandığı gibi başlatın

### <a name="mounting-an-azure-file-share"></a>Azure dosya paylaşımından bağlama

RoboCopy kullanabilmeniz için, Azure dosya paylaşımının SMB üzerinden erişilebilir olması gerekir. En kolay yol, paylaşılan bir ağ sürücüsü olarak, RoboCopy için kullanmayı planladığınız Windows Server 'a bağlamadır. 

> [!IMPORTANT]
> Bir Azure dosya paylaşımını yerel bir Windows sunucusuna başarıyla bağlayabilmeniz için, şu aşamayı tamamlamış olmanız gerekir: Azure dosya paylaşımlarını kullanmaya hazırlanma!

Hazırladıktan sonra, [Windows nasıl yapılır makalesindeki bir Azure dosya paylaşımının kullanılması](storage-how-to-use-files-windows.md) ve için NAS yakalama Robocopy 'ni başlatmak istediğiniz Azure dosya paylaşımından bağlama konusunu gözden geçirin.

### <a name="robocopy"></a>RoboCopy

Aşağıdaki RoboCopy komutu, yalnızca NAS depolama alanınızı Azure dosya paylaşımınıza olan farkları (güncelleştirilmiş dosya ve klasörler) kopyalayacaktır. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> RoboCopy üretim ortamınızı etkileiyorsa, çok sayıda hatayı raporlar veya beklendiği kadar hızlı ilerliyorsa [sorun giderme bölümüne göz](#troubleshoot) atın.

### <a name="user-cut-over"></a>Kullanıcı tarafından kesilen

RoboCopy komutunu ilk kez çalıştırdığınızda, kullanıcılarınız ve uygulamalarınız hala NAS 'daki dosyalara erişiyor ve bu dosyaları değiştirebilir. Bu, RoboCopy bir dizin işledi, bir sonrakine geçer ve ardından kaynak konumdaki (NAS) bir Kullanıcı, bu geçerli RoboCopy çalıştırmasında işlenmeyecek bir dosyayı ekler, değiştirir veya siler. Bu beklenen bir davranıştır.

İlk çalıştırma, verilerin toplu olarak Azure dosya paylaşımınıza taşınması ile ilgilidir. Bu ilk kopya biraz zaman alabilir. RoboCopy hızlarını neyin etkileyebilecek hakkında daha fazla bilgi edinmek için [sorun giderme bölümüne](#troubleshoot) göz atın.

İlk çalıştırma tamamlandıktan sonra, komutu yeniden çalıştırın.

Aynı paylaşıma yönelik olarak RoboCopy çalıştırdığınızda, yalnızca son çalıştırmasından bu yana gerçekleşen değişiklikleri taşıması gerektiğinden daha hızlı tamamlanır. Yinelenen işleri aynı paylaşımda çalıştırabilirsiniz.

Kapalı kalma süresini kabul ediyorsanız, NAS tabanlı paylaşımlarınız için Kullanıcı erişimini kaldırmanız gerekir. Bunu, kullanıcıların dosya ve klasör yapısını ve içeriğini değiştirmesini önleyen herhangi bir adımla yapabilirsiniz. DFS-Namespace, mevcut olmayan bir konuma işaret etmek veya paylaşımdaki kök ACL 'Leri değiştirmek örneğidir.

Bir son RoboCopy Round çalıştırın. Bu işlem, kaçırılmış olabilecek tüm değişiklikleri seçer.
Bu son adımın ne kadar süreceği, RoboCopy taramasının hızına bağlıdır. Önceki çalıştırmanın ne kadar sürdüğünü ölçerek süreyi tahmin edebilirsiniz (kapalı kalma süresine eşittir).

Windows Server klasöründe bir paylaşma oluşturun ve büyük olasılıkla DFS-N dağıtımınızı bunu işaret etmek üzere ayarlayın. Aynı paylaşma düzeyi izinlerini, NAS SMB paylaşımınızda olduğu gibi ayarladığınızdan emin olun. Kurumsal sınıf etki alanına katılmış bir NAS olsaydıysanız, kullanıcılar Active Directory ' de mevcut olduğu için Kullanıcı SID 'Leri otomatik olarak eşleştirecektir ve RoboCopy dosya ve meta verileri tam uygunlukta kopyalar. NAS 'inizdeki yerel kullanıcıları kullandıysanız, bu kullanıcıları Windows Server yerel kullanıcıları olarak yeniden oluşturmanız ve Windows Server 'a taşınan mevcut SID RoboCopy 'nin yeni, Windows Server yerel kullanıcılarınızın SID 'Lerine eşlenmesi gerekir.

Paylaşımı/paylaşım gruplarını ortak bir köke veya birime geçirmeyi tamamladınız. (1. aşama ile eşleştirmeye bağlı olarak)

Bu kopyaların birkaçını paralel olarak çalıştırmayı deneyebilirsiniz. Tek seferde bir Azure dosya paylaşımının kapsamını işlemeyi öneririz.

## <a name="troubleshoot"></a>Sorun giderme

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure dosya paylaşımları hakkında daha fazla bilgi bulabilirsiniz. Aşağıdaki makalelerde gelişmiş seçenekler, en iyi uygulamalar ve ayrıca sorun giderme yardımı da yer almanıza yardımcı olur. Bu makaleler, [Azure dosya paylaşma belgelerini](storage-files-introduction.md) uygun şekilde bağlar.

* [Geçiş işlemine genel bakış](storage-files-migration-overview.md)
* [Microsoft Azure Depolama izleme, tanılama ve sorun giderme](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Doğrudan erişim için ağ konusunda dikkat edilmesi gerekenler](storage-files-networking-overview.md)
* [Yedekleme: Azure dosya paylaşımının anlık görüntüleri](storage-snapshots-files.md)
