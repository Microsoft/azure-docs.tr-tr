---
title: Azure dosyaları dağıtımını planlama | Microsoft Docs
description: Azure dosyaları dağıtımının planlanmasını anlayın. Azure dosya paylaşımından doğrudan bağlama yapabilir veya Azure dosya paylaşımından Azure Dosya Eşitleme ile önbelleğe yazabilirsiniz.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: be7e5b1f9721cc65c2f9b371becf8b4c82fb37b4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759778"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Dosyaları dağıtımı planlama
[Azure dosyaları](storage-files-introduction.md) , iki ana şekilde dağıtılabilir: doğrudan sunucusuz Azure dosya paylaşımlarını bağlayarak veya Azure dosya eşitleme kullanarak şirket içi Azure dosya paylaşımlarını önbelleğe alarak. Seçtiğiniz dağıtım seçeneği, dağıtımınız için planlarken göz önünde bulundurmanız gereken şeyleri değiştirir. 

- **Azure dosya paylaşımının doğrudan bağlanması**: Azure dosyaları sunucu ileti bloğu (SMB) veya ağ dosya SISTEMI (NFS) erişimi SAĞLADıĞıNDAN, işletim sisteminde bulunan standart SMB veya NFS Istemcilerini kullanarak Azure dosya paylaşımlarını şirket içinde veya bulutta bağlayabilirsiniz. Azure dosya paylaşımları sunucusuz olduğundan, üretim senaryolarına yönelik dağıtım, bir dosya sunucusu veya NAS cihazının yönetilmesini gerektirmez. Bu, yazılım düzeltme ekleri uygulamanız veya fiziksel diskleri takas etmeniz gerekmediği anlamına gelir. 

- **Şirket Içi Azure dosya paylaşımını Azure dosya eşitleme Ile önbelleğe alma**: Azure dosya eşitleme, kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirirken şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu mümkün tutmaya olanak sağlar. Azure Dosya Eşitleme, şirket içi (veya bulut) Windows Server 'ı Azure SMB dosya paylaşımınızın hızlı önbelleğine dönüştürür. 

Bu makalede öncelikle bir Azure dosya paylaşımının dağıtımı, şirket içi veya bulut istemcisi tarafından doğrudan bağlanması için dağıtım konuları ele alınmaktadır. Azure Dosya Eşitleme dağıtımı planlamak için, bkz. [Azure dosya eşitleme dağıtımı planlama](../file-sync/file-sync-planning.md).

## <a name="available-protocols"></a>Kullanılabilir protokoller

Azure dosyaları, dosya paylaşımlarınız, SMB ve ağ dosya sistemi (NFS) bağlanırken kullanılabilecek iki protokol sunar. Bu protokollerle ilgili ayrıntılar için bkz. [Azure dosya paylaşma protokolleri](storage-files-compare-protocols.md).

> [!IMPORTANT]
> Bu makalenin içeriğinin çoğu yalnızca SMB paylaşımları için geçerlidir. NFS paylaşımları için geçerli olan herhangi bir şey, özellikle geçerli olur.

## <a name="management-concepts"></a>Yönetim kavramları
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Azure dosya paylaşımlarını depolama hesaplarına dağıttığınızda şunları öneririz:

- Azure dosya paylaşımlarını yalnızca diğer Azure dosya paylaşımlarına sahip depolama hesaplarına dağıtma. Azure dosya paylaşımları ve BLOB kapsayıcıları gibi depolama kaynakları depolama hesabının sınırlarını paylaştığı için GPv2 depolama hesapları karma amaçlı depolama hesaplarına izin veriyor olsa da, kaynakları birlikte karıştırarak, daha sonra performans sorunlarını gidermek daha zor olabilir. 

- Azure dosya paylaşımları dağıtımında bir depolama hesabının ıOPS kısıtlamalarına dikkat edin. İdeal olarak, 1:1 dosya paylaşımlarını depolama hesaplarıyla eşlersiniz, ancak bu, hem kuruluşunuzdan hem de Azure 'dan çeşitli sınırlar ve kısıtlamalar nedeniyle her zaman mümkün olmayabilir. Tek bir depolama hesabında yalnızca bir dosya paylaşımının dağıtılması mümkün olmadığında, en son hangi Paylaşımların etkin olacağını ve en yoğun dosya paylaşımlarının aynı depolama hesabına birlikte yerleştirmemesini sağlamak için hangi paylaşımların daha az etkin olacağını düşünün.

- Ortamınızda bulduğunuz GPv2 ve FileStorage hesaplarını dağıtın ve GPv1 ve klasik depolama hesaplarını yükseltin. 

## <a name="identity"></a>Kimlik
Bir Azure dosya paylaşımında erişim sağlamak için dosya paylaşımının kullanıcısının kimliği doğrulanmalıdır ve paylaşıma erişim yetkisi vardır. Bu, dosya paylaşımıyla erişen kullanıcının kimliğine göre yapılır. Azure dosyaları üç ana kimlik sağlayıcısıyla tümleştirilir:
- Şirket **içi Active Directory Domain Services (AD DS veya şirket içi AD DS)**: Azure depolama hesapları, bir Windows Server dosya sunucusu veya NAS cihazında olduğu gibi, müşteriye ait, Active Directory Domain Services etki alanına katılmış olabilir. Bir etki alanı denetleyicisini şirket içinde, bir Azure VM 'de, hatta başka bir bulut sağlayıcısında VM olarak dağıtabilirsiniz; Azure dosyaları, etki alanı denetleyicinizin barındırıldığı yere belirsiz. Bir depolama hesabı etki alanına katıldıktan sonra, Son Kullanıcı BILGISAYARıNDA oturum açtıkları kullanıcı hesabıyla bir dosya paylaşımından bağlanabilir. AD tabanlı kimlik doğrulaması, Kerberos kimlik doğrulama protokolünü kullanır.
- **Azure Active Directory Domain Services (azure AD DS)**: Azure AD DS, Azure kaynakları Için kullanılabilen Microsoft tarafından yönetilen bir etki alanı denetleyicisi sağlar. Depolama hesabınıza Azure AD DS katılma etki alanı, şirkete ait Active Directory etki alanına katılma gibi benzer avantajlar sağlar. Bu dağıtım seçeneği, AD tabanlı izinleri gerektiren uygulama kaldırma ve kaydırma senaryoları için en yararlı seçenektir. Azure AD DS, AD tabanlı kimlik doğrulaması sağladığından, bu seçenek Kerberos kimlik doğrulama protokolünü de kullanır.
- Azure **depolama hesabı anahtarı**: Azure dosya paylaşımları, bir Azure depolama hesabı anahtarıyla de bağlanabilir. Bir dosya payını bu şekilde bağlamak için, depolama hesabı adı Kullanıcı adı olarak kullanılır ve depolama hesabı anahtarı parola olarak kullanılır. Azure dosya paylaşımının bağlanması için depolama hesabı anahtarını kullanmak, bağlı dosya paylaşımının ACL 'Leri olsa bile paylaşımdaki tüm dosya ve klasörler üzerinde tam izinlere sahip olacağı için etkili bir şekilde bir yönetici işlemidir. SMB üzerinden bağlanacak depolama hesabı anahtarını kullanırken, NTLMv2 kimlik doğrulama protokolü kullanılır.

Şirket içi dosya sunucularından geçiş yapmak veya Azure dosyalarında Windows dosya sunucuları veya NAS gereçleri gibi davranmaya yönelik yeni dosya paylaşımları oluşturmak için, depolama hesabınıza **müşterinin sahip Active Directory olduğu** etki alanına katılma, önerilen seçenektir. Depolama hesabınıza şirkete ait bir Active Directory katılma hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları Active Directory genel bakış](storage-files-active-directory-overview.md).

Azure dosya paylaşımlarınıza erişmek için depolama hesabı anahtarını kullanmayı düşünüyorsanız, hizmet uç noktalarının [ağ](#networking) bölümünde açıklandığı gibi kullanılması önerilir.

## <a name="networking"></a>Ağ
Azure dosya paylaşımlarına, depolama hesabının genel uç noktası aracılığıyla her yerden erişilebilir. Bu, bir kullanıcının oturum açma kimliği tarafından yetkilendirilmiş istekler gibi kimliği doğrulanmış isteklerin Azure içinden veya dışından güvenli bir şekilde kaynaklanabilmesi anlamına gelir. Birçok müşteri ortamında, Azure VM 'lerinden gelen Mount başarılı olsa bile, şirket içi iş istasyonunuzda Azure dosya paylaşımının ilk bağlanması başarısız olur. Bunun nedeni, SMB 'nin iletişim kurmak için kullandığı bağlantı noktasını pek çok kuruluşun ve Internet hizmet sağlayıcısının (ISS) engellemesini, bağlantı noktası 445 ' dir. 445 numaralı bağlantı noktasından erişime izin veren veya erişimi engelleyen ISP'lerin özetini görmek için [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)'e gidin.

Azure dosya paylaşımınıza erişimi engellemeyi kaldırmak için iki ana seçeneğiniz vardır:

- Kuruluşunuzun şirket içi ağı için 445 bağlantı noktasını engellemeyi kaldırın. Azure dosya paylaşımlarına yalnızca, SMB 3,0 ve FileREST API gibi internet güvenli protokolleri kullanılarak genel uç nokta aracılığıyla dışarıdan erişilebilir. Bu, kuruluşunuzun giden bağlantı noktası kurallarını değiştirmenin ötesinde gelişmiş ağ yapılandırması gerektirmediğinden bu yana şirket içi sunucudan Azure dosya paylaşımınıza erişmenin en kolay yoludur, ancak SMB 1,0 gibi eski ve kullanımdan kaldırılmış olan SMB protokolünün sürümlerini kaldırmanızı öneririz. Bunun nasıl yapılacağını öğrenmek için bkz. [Windows/Windows Server 'ın güvenliğini sağlama](storage-how-to-use-files-windows.md#securing-windowswindows-server) ve [Linux güvenliğini sağlama](storage-how-to-use-files-linux.md#securing-linux).

- Azure dosya paylaşımlarına bir ExpressRoute veya VPN bağlantısı üzerinden erişin. Azure dosya paylaşımınıza bir ağ tüneli üzerinden eriştiğinizde, SMB trafiği kuruluş sınırınızda geçiş yaptığından, Azure dosya paylaşımınızı şirket içi dosya paylaşımında bağlayabilirsiniz.   

Technical perspektifinden, Azure dosya paylaşımlarınızı genel uç nokta aracılığıyla bağlamak çok daha kolay olsa da, çoğu müşterinin Azure dosya paylaşımlarını bir ExpressRoute veya VPN bağlantısı üzerinden bağlamaya karar veririz. Bu seçeneklerle bağlama, hem SMB hem de NFS paylaşımlarında mümkündür. Bunu yapmak için, ortamınız için aşağıdakileri yapılandırmanız gerekir:  

- **ExpressRoute, siteden siteye veya noktadan sıteye VPN kullanarak ağ tüneli**: bir sanal ağa tünel oluşturma, 445 bağlantı noktası engellense bile şirket içi Azure dosya paylaşımlarına erişim sağlar.
- **Özel uç** noktalar: özel uç noktalar, depolama hesabınıza sanal ağın adres alanından ayrılmış bir IP adresi sağlar. Bu, Azure depolama kümelerinin sahip olduğu tüm IP adresi aralıklarına kadar şirket içi ağları açmaya gerek kalmadan ağ tünelini sağlar. 
- **DNS iletimi**: ŞIRKET içi DNS 'nizi, `storageaccount.file.core.windows.net` Özel uç noktalarınızın IP adresini çözümlemek üzere depolama hesabınızın adını (genel bulut bölgeleri için) çözümlemek üzere yapılandırın.

Azure dosya paylaşımının dağıtımıyla ilişkili ağı planlamak için bkz. [Azure dosyaları ağ iletişimi konuları](storage-files-networking-overview.md).

## <a name="encryption"></a>Şifreleme
Azure dosyaları iki farklı şifreleme türünü destekler: Azure dosya paylaşımında bağlama/erişim sırasında kullanılan şifrelemeyle ve bekleyen şifreleme ile ilgili olarak, diskte depolandığında verilerin nasıl şifrelendiğine ilişkin şifreleme ile ilişkili olan geçişte şifreleme. 

### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme

> [!IMPORTANT]
> Bu bölüm, SMB paylaşımları için iletim ayrıntılarının şifrelemesini anlatmaktadır. NFS paylaşımları ile aktarım sırasında şifreleme ile ilgili ayrıntılar için bkz. [güvenlik](storage-files-compare-protocols.md#security).

Varsayılan olarak, tüm Azure depolama hesaplarının geçiş etkin olarak şifrelenmesi vardır. Bu, SMB üzerinden bir dosya paylaşımının bağladığınızda veya dosyayı dosya paylaşımından (Azure portal, PowerShell/CLı veya Azure SDK 'Ları aracılığıyla) eriştiğinizde, Azure dosyaları yalnızca şifreleme veya HTTPS ile SMB 3.0 + ile yapılırsa bağlantıya izin verir. SMB 3,0 ' i veya SMB 3,0 ' i destekleyen ancak SMB 'yi destekleyen istemcileri desteklemeyen istemciler, aktarım sırasında şifreleme etkinse Azure dosya paylaşımının bağlanabilmesi mümkün olmayacaktır. Şifreleme ile SMB 3,0 ' i destekleyen işletim sistemleri hakkında daha fazla bilgi için bkz. [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)ve [Linux](storage-how-to-use-files-linux.md)için ayrıntılı Belgelerimiz. PowerShell, CLı ve SDK 'ların tüm geçerli sürümleri HTTPS 'yi destekler.  

Azure depolama hesabı için iletim sırasında şifrelemeyi devre dışı bırakabilirsiniz. Şifreleme devre dışı bırakıldığında, Azure dosyaları da SMB 2,1, şifrelemeden SMB 3,0 ve HTTP üzerinden şifrelenmemiş dosya API çağrıları sağlar. Geçiş sırasında şifrelemeyi devre dışı bırakmak için birincil neden, Windows Server 2008 R2 veya daha eski Linux dağıtımı gibi eski bir işletim sisteminde çalıştırılması gereken eski bir uygulamayı desteklemedir. Azure dosyaları yalnızca Azure dosya paylaşımıyla aynı Azure bölgesi içinde SMB 2,1 bağlantılarına izin verir; Azure dosya paylaşımının Azure bölgesinin dışında bir SMB 2,1 istemcisi, örneğin şirket içi veya farklı bir Azure bölgesinde, dosya paylaşımıyla erişemeyecektir.

Geçiş sırasında verilerin şifrelenmesini güvence altına almanız önemle önerilir.

Aktarım sırasında şifreleme hakkında daha fazla bilgi için bkz. [Azure depolama 'da güvenli aktarım gerektirme](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Veri koruma
Azure dosyaları, verilerinizin yedeklenmesini, kurtarılabilir ve güvenlik tehditlerine karşı korunmasını sağlamaya yönelik çok katmanlı bir yaklaşıma sahiptir.

### <a name="soft-delete"></a>Geçici silme
Dosya paylaşımları için geçici silme (Önizleme), yanlışlıkla silindiğinde dosya paylaşımınızı kurtarmanıza olanak sağlayan bir depolama hesabı düzeyi ayarıdır. Bir dosya paylaşımının silindiği zaman kalıcı olarak silinmeyeceği yerine geçici olarak silinen bir duruma geçer. Geçici olarak silinen verilerin kalıcı olarak silinmeden önce kurtarılabilir kalma süresini yapılandırabilir ve bu bekletme döneminde her zaman paylaşımdan geri alma işlemini geri alabilirsiniz. 

Çoğu dosya paylaşımı için geçici silme özelliğini etkinleştirmenizi öneririz. Paylaşma silmenin yaygın olduğu ve beklenildiği bir iş akışınız varsa, kısa bir bekletme dönemine sahip olmak veya hiç geçici silme özelliği etkinleştirilmemiş olabilir.

Geçici silme hakkında daha fazla bilgi için bkz. [yanlışlıkla veri silmeyi engelleme](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Azure dosya paylaşımınızı, paylaşımınızın salt okunurdur, paylaşılan [anlık görüntüleri](./storage-snapshots-files.md)aracılığıyla yedekleyebilirsiniz. Anlık görüntüler artımlı, yani yalnızca önceki anlık görüntüden beri değiştiği kadar veri içerir. Dosya paylaşımında en fazla 200 anlık görüntü alabilir ve bunları 10 yıla kadar koruyabilirsiniz. Bu anlık görüntüleri, PowerShell veya komut satırı arabirimi (CLı) aracılığıyla Azure portal el ile alabilir ya da [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)kullanabilirsiniz. Anlık görüntüler dosya paylaşımınızda saklanır, yani dosya paylaşımınızı silerseniz anlık görüntülerinizin de silinmesi gerekir. Anlık görüntü yedeklemelerinizi yanlışlıkla silinmeye karşı korumak için, paylaşımınızda geçici silme özelliğinin etkinleştirildiğinden emin olun.

[Azure dosya paylaşımları için Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) , anlık görüntülerin zamanlamasını ve bekletilmesini işler. Babalar ve oğul-son (GFS) özellikleri, her biri kendi ayrı saklama süresine sahip günlük, haftalık, aylık ve yıllık anlık görüntüleri gerçekleştirebileceğiniz anlamına gelir. Azure Backup Ayrıca, içindeki herhangi bir dosya paylaşımının yedekleme için yapılandırıldığı anda, geçici silme işlemini ve bir depolama hesabında silme kilidi alır. Son olarak, Azure Backup müşterilerin yedeklemelerinin birleştirilmiş bir görünümüne sahip olmasını sağlayan belirli önemli izleme ve uyarı özellikleri sağlar.

Azure Backup kullanarak Azure portal hem öğe düzeyinde hem de paylaşma düzeyinde geri yüklemeler gerçekleştirebilirsiniz. Yapmanız gereken tek şey, geri yükleme noktasını (belirli bir anlık görüntü), varsa belirli bir dosyayı veya dizini ve ardından geri yüklemeyi istediğiniz konumu (orijinal veya alternatif) seçmeniz gerekir. Yedekleme hizmeti, anlık görüntü verilerinin kopyalanmasını işler ve portalda geri yükleme ilerleme durumunu gösterir.

Yedekleme hakkında daha fazla bilgi için bkz. [Azure dosya paylaşma yedeklemesi hakkında](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-defender-for-azure-files"></a>Azure dosyaları için Azure Defender 
Azure depolama için Azure Defender (eski adıyla Azure Storage için Gelişmiş tehdit koruması), depolama hesabınızda anormal etkinlik algıladığında uyarılar sağlayan ek bir güvenlik zekası katmanı sağlar. Örneğin, olağan dışı erişim girişimleri. Ayrıca, kötü amaçlı yazılım karması saygınlığı analizini çalıştırır ve bilinen kötü amaçlı yazılımlara uyar. Azure Güvenlik Merkezi 'ni kullanarak bir abonelik veya depolama hesabı düzeyinde Azure Defender 'ı yapılandırabilirsiniz. 

Daha fazla bilgi için bkz. [depolama Için Azure Defender 'A giriş](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Depolama katmanları
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standart dosya paylaşımlarının 100 TiB 'ye kadar yayılmasını sağlar
Varsayılan olarak, standart dosya paylaşımları yalnızca 5 TiB 'ye yayılabilir, ancak paylaşım limitini 100 TiB olarak artırabilirsiniz. Paylaşım sınırınızı nasıl artıracağınızı öğrenmek için bkz. [büyük dosya paylaşımlarını etkinleştirme ve oluşturma](storage-files-how-to-create-large-file-share.md).


#### <a name="limitations"></a>Sınırlamalar
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Yedeklilik
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Geçiş
Çoğu durumda, kuruluşunuz için bir ağ yeni dosya paylaşımının kurulması ve bunun yerine, mevcut bir dosya paylaşımının şirket içi bir dosya sunucusundan veya NAS cihazından Azure dosyalarına geçirilmesi gerekir. Senaryonuza yönelik doğru geçiş stratejisi ve aracının seçilmesi, geçişinizin başarısı için önemlidir. 

[Geçişe genel bakış makalesi](storage-files-migration-overview.md) , temel bilgileri kısaca kapsar ve senaryonuzu kapsayan geçiş kılavuzlarını size yönlendiren bir tablo içerir.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Dosya Eşitleme dağıtımı için planlama yapma](../file-sync/file-sync-planning.md)
* [Azure dosyalarını dağıtma](./storage-how-to-create-file-share.md)
* [Azure Dosya Eşitleme dağıtma](../file-sync/file-sync-deployment-guide.md)
* [Senaryonuza yönelik geçiş kılavuzunu bulmak için geçişe genel bakış makalesine göz atın](storage-files-migration-overview.md)