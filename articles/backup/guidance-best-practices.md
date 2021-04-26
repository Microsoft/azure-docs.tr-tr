---
title: Rehberlik ve en iyi deneyimler
description: Buluta ve şirket içi iş yükünü buluta yedeklemeye yönelik en iyi yöntemleri ve Kılavuzu bulun
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 14476533cf896434182e1d63f89c6a1279b36362
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519072"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Buluta ve şirket içi iş yüklerini buluta yedekleyin

## <a name="introduction"></a>Giriş

Azure Backup, Azure 'daki veri varlıklarınızı, sıfır altyapı gerektiren basit, güvenli ve düşük maliyetli bir çözüm aracılığıyla büyük ölçüde korur. Bu, çok çeşitli iş yükleri için Azure's yerleşik veri koruma çözümüdür. Bu, bulutta çalışan görev açısından kritik iş yüklerinizi korumanıza yardımcı olur ve yedeklemelerinizin tüm yedeklemelerinizde her zaman kullanılabilir ve yönetilebilir olmasını sağlar.

### <a name="intended-audience"></a>Hedef kitle

Bu makaleye yönelik birincil hedef kitle, BT ve uygulama yöneticileri ve Azure 'un yerleşik veri koruma teknolojisi özellikleri, Azure Backup ve dağıtımlarınızı daha iyi koruyan çözümlerin verimli bir şekilde nasıl uygulanacağı hakkında bilgi edinmek isteyen büyük ve orta ölçekli kuruluşların uygulayıcılarıdır. Makalede, temel Azure teknolojileri, veri koruma kavramları hakkında bilgi sahibi olduğunuz ve bir yedekleme çözümüyle çalışırken deneyim sahibi olduğunuz varsayılmaktadır. Bu makalede ele alınan yönergeler, Azure 'da sağlanan desenleri kullanarak yedekleme çözümünüzü tasarlamayı kolaylaştırabilir ve bilinen kaynaklı tehlikelerin önüne geçmez.

### <a name="how-this-article-is-organized"></a>Bu makale nasıl düzenlenir

Azure 'da altyapı ve uygulamaları korumaya başlamak çok kolay olsa da, temel alınan Azure kaynaklarının doğru şekilde kurulduğundan ve en iyi şekilde kullanıldığından emin olduğunuzda, zaman değeri olarak hızlandıraseçebilirsiniz. Bu makalede, Azure Backup dağıtımınızı en iyi şekilde yapılandırmaya yönelik tasarım konularına ve kılavuza ilişkin kısa bir genel bakış ele alınmaktadır. Çekirdek bileşenleri (örneğin, kurtarma hizmetleri Kasası, yedekleme Ilkesi) ve kavramları (örneğin, idare) ve bunların özelliklerini ve bunların ayrıntılı ürün belgelerinin bağlantılarıyla nasıl düşündüğünü inceler.

## <a name="architecture"></a>Mimari

![Azure Backup mimarisi](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>İş yükleri

Azure Backup çeşitli iş yükleri (Şirket içi ve bulut) için veri korumasına izin vermez. Azure 'da güvenli ve güvenilir bir yerleşik veri koruma mekanizmasıdır. Herhangi bir yönetim ek yükü olmadan, birden çok iş yükü genelinde korumasını sorunsuz bir şekilde ölçeklendirebilir. Bunun yanı sıra, bunu etkinleştirmek için (PowerShell, CLı, Azure Resource Manager şablonları ve REST API 'Leri aracılığıyla) birden çok Automation kanalı vardır.)

* **Ölçeklenebilir, dayanıklı ve güvenli depolama-** Azure Backup, yerleşik güvenlik ve yüksek kullanılabilirlik özellikleriyle güvenilir BLOB depolama kullanır. Yedekleme verileriniz için LRS, GRS veya RA-GRS depolama alanları ' nı seçebilirsiniz.  

* **Yerel iş yükü tümleştirmesi-** Azure Backup, aracıları dağıtmak, yeni betikler yazmak veya depolama alanı sağlamak üzere otomasyon veya altyapıyı yönetmeniz gerekmeden Azure Iş yükleri (VM 'Ler, SAP HANA, Azure sanal makinelerinde SQL ve hatta Azure dosyaları) ile yerel tümleştirme sağlar.

### <a name="data-plane"></a>Veri düzlemi

* **Otomatik depolama yönetimi** – Azure Backup, yedekleme verileri arttıkça, yedekleme verileri için depolama hesaplarını sağlamayı ve yönetmeyi otomatikleştirir.

* **Kötü amaçlı silme koruması –** Yedeklemeleri geçici olarak silme yoluyla yedeklemelerinizi silmeye yönelik yanlışlıkla veya kötü amaçlı saldırılara karşı koruma. Silinen yedekleme verileri, ücretsiz olarak 14 gün boyunca depolanır ve bu durumdan kurtarılıp kurtarılmasını sağlar.

* **Güvenli şifrelenmiş yedeklemeler-** Azure Backup, Azure RBAC ve şifreleme gibi Azure platformunun yerleşik güvenlik yeteneklerini kullanarak yedekleme verilerinizin güvenli bir şekilde depolanmasını sağlar.

* **Yedekleme veri yaşam döngüsü yönetimi-** Azure Backup, bekletme ilkelerine uymak için eski yedekleme verilerini otomatik olarak temizler. Ayrıca, işletimsel depolama alanındaki verilerinizi kasa depolamaya dağıtabilirsiniz.

### <a name="management-plane"></a>Yönetim düzlemi

* **Erişim denetimi** – kaults (kurtarma hizmetleri ve yedekleme kasaları), yönetim olanakları sağlar ve Azure Portal, yedekleme merkezi, kasa PANOLARı, SDK, CLI ve hatta REST API 'leri aracılığıyla erişilebilir. Ayrıca, yalnızca yetkili yedekleme yöneticilerine yönelik yedeklemelere erişimi kısıtlama seçeneği sunan bir Azure RBAC sınırıdır.

* **İlke yönetimi** – her bir kasada yer alan Azure Backup ilkeler, yedeklemelerin ne zaman tetiklenmesi gerektiğini ve ne kadar süreyle tutulacağını tanımlar. Ayrıca, bu ilkeleri yönetebilir ve birden çok öğe arasında uygulayabilirsiniz.

* **İzleme ve raporlama** – Azure Backup Log Analytics tümleştirilir ve çalışma kitapları aracılığıyla raporları görme yeteneği sağlar.

* **Anlık görüntü yönetimi** – Azure Backup bazı Azure yerel iş yükleri (VM 'Ler ve Azure dosyaları) için anlık görüntüler alır, bu anlık görüntüleri yönetir ve onlardan hızlı geri yüklemeler sağlar. Bu seçenek verilerinizi özgün depolamaya kurtarmak için geçen süreyi önemli ölçüde azaltır.

## <a name="vault-considerations"></a>Kasa konuları

Azure Backup, yedeklemeleri düzenlemek ve yönetmek için kasalarını (kurtarma hizmetleri ve yedekleme kasaları) kullanır. Ayrıca, yedeklenen verileri depolamak için kasalarını kullanır. Etkin kasa tasarımı, kuruluşların iş önceliklerinizi destekleyecek şekilde Azure 'da yedekleme varlıklarını düzenlemek ve yönetmek için bir yapı kurmalarını sağlar. Bir kasa oluştururken aşağıdaki yönergeleri göz önünde bulundurun:  

### <a name="align-to-subscription-design-strategy"></a>Abonelik tasarım stratejisine Hizala

Kasa bir abonelik kapsamında olduğundan, aboneliklerin belirli uygulamalara veya hizmetlere göre veya uygulama arşiv satırları üzerinde ayrıldığı *uygulama kategorisi stratejisi* gibi abonelik tasarımı stratejisini karşılamak için kasa tasarımınıza uyum yapın. Daha fazla bilgi için [bu makaleye](/azure/cloud-adoption-framework/decision-guides/subscriptions/) bakın.

### <a name="single-or-multiple-vault"></a>Tek veya birden çok kasa

Yedeklemenizi düzenlemek ve yönetmek için tek bir kasa veya birden çok kasa kullanabilirsiniz. Aşağıdaki yönergeleri göz önünde bulundurun:

* İş yüklerinizin tümü tek bir abonelik ve tek bir kaynak tarafından yönetiliyorsa, yedeklemenizi izlemek ve yönetmek için tek bir kasa kullanabilirsiniz.

* İş yükleriniz abonelikler arasında yayıldığında, abonelik başına bir veya daha fazla çoklu kasa oluşturabilirsiniz.
  * Yedekleme merkezi, yedeklemeyle ilgili tüm görevleri yönetmek için tek bir cam bölmenizi sağlar. [Burada daha fazla bilgi edinebilirsiniz]().
  * Çalışma kitabı şablonlarıyla görünümlerinizi özelleştirebilirsiniz. Yedekleme Gezgini, Azure VM 'Leri için bu tür bir şablondur. [Burada daha fazla bilgi edinebilirsiniz](monitor-azure-backup-with-backup-explorer.md).
  * Kasaların tamamında tutarlı ilke gerekiyorsa, yedekleme ilkesini birden çok kasa genelinde yaymak için Azure ilkesini kullanabilirsiniz. Bir yedekleme ilkesini birden çok kasa genelinde yaymak için [' deployifnotexists '](../governance/policy/concepts/effects.md#deployifnotexists) efektini kullanan özel bir [Azure ilke tanımı](../governance/policy/concepts/definition-structure.md) yazabilirsiniz. Ayrıca, bu Azure Ilke tanımını belirli bir kapsama (abonelik veya RG) [atayabilir](../governance/policy/assign-policy-portal.md) , böylece Azure ilke atamasının kapsamındaki tüm kurtarma hizmetleri kasalarına ' yedekleme İlkesi ' kaynağı dağıtılır. Yedekleme ilkesinin ayarları (örneğin, yedekleme sıklığı, bekletme vb.), Kullanıcı tarafından Azure Ilke atamasında parametre olarak belirtilmelidir.

* Kurumsal parmak izi büyüdükçe, aşağıdaki nedenlerden dolayı iş yüklerini abonelikler arasında taşımak isteyebilirsiniz: yedekleme ilkesine göre hizalayın, kasaları birleştirin, düşük yedekliliğe kadar tasarruf edin (GRS 'den LRS 'ye geçiş yapın).  Azure Backup, kurtarma hizmetleri kasasının Azure aboneliklerine veya aynı abonelik içindeki başka bir kaynak grubuna taşınmasını destekler. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Varsayılan ayarları gözden geçir

Kasadaki yedeklemeleri yapılandırmadan önce gereksinimlerinizi karşılayacak depolama çoğaltma türü ve güvenlik ayarları için varsayılan ayarları gözden geçirin.

* *Depolama çoğaltma türü* varsayılan olarak coğrafi olarak yedekli (GRS) olarak ayarlanır. Yedeklemeyi yapılandırdıktan sonra, değiştirme seçeneği devre dışı bırakılır. Ayarları gözden geçirmek ve değiştirmek için [aşağıdaki](backup-create-rs-vault.md#set-storage-redundancy) adımları izleyin.

* Varsayılan olarak *geçici silme* , yeni oluşturulan kasaların yanlışlıkla veya kötü amaçlı silmelerden yedekleme verilerini korumak için etkinleştirilir. Ayarları gözden geçirmek ve değiştirmek için [aşağıdaki](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) adımları izleyin.

* *Çapraz bölge geri yükleme* , Azure sanal makinelerini Azure eşlenmiş bölgesi olan ikincil bir bölgeye geri yüklemenize olanak tanır. Bu seçenek, denetim ve uyumluluk gereksinimlerini karşılamak üzere detayların yanı sıra birincil bölgede bir olağanüstü durum olması durumunda VM 'yi veya diskini geri yüklemeyi sağlar. CRR, herhangi bir GRS Kasası için bir katılım özelliğidir. [Burada daha fazla bilgi edinebilirsiniz](backup-create-rs-vault.md#set-cross-region-restore).

* Kasa tasarımınızı kesinleştirmeden önce, tasarım seçimlerinizi etkileyebilecek veya sınırlayabilecek faktörleri anlamak için [kasa desteği matrisler](backup-support-matrix.md#vault-support) ' nı gözden geçirin.

## <a name="backup-policy-considerations"></a>Yedekleme Ilkesi konuları

Azure Backup Ilkesinin iki bileşeni vardır: *zamanlama* (yedekleme ne zaman) ve *bekletme* (yedeklemenin tutulacağı süre). İlkeyi yedeklenen verilerin türüne, RTO/RPO gereksinimlerine, işlemsel veya mevzuat uyumluluk ihtiyaçlarına ve iş yükü türüne (örneğin, VM, veritabanı, dosyalar) göre tanımlayabilirsiniz. [Burada daha fazla bilgi edinebilirsiniz](backup-architecture.md#backup-policy-essentials).

Yedekleme Ilkesi oluştururken aşağıdaki yönergeleri göz önünde bulundurun:

### <a name="schedule-considerations"></a>Zamanlama konuları

* Tek bir ilke içinde aynı zamanlama başlangıç saati, sıklığı ve bekletme ayarlarını gerektiren VM 'Leri gruplandırmayı düşünün.

* Yedeklemenin zamanlanan başlangıç zamanının yoğun olmayan üretim uygulaması süresi sırasında olduğundan emin olun.

* Yedekleme trafiğini dağıtmak için, günün farklı saatlerinde farklı VM 'Leri yedeklemeyi düşünün ve zamanların çakışmadığından emin olun.

### <a name="retention-considerations"></a>Bekletme konuları

* Kısa süreli saklama "dakika" veya "günlük" olabilir. "Haftalık", "aylık" veya "yıllık" yedekleme noktaları için bekletme, uzun süreli saklama olarak adlandırılır.

* Uzun süreli saklama:
  * Planlanmış (uyumluluk gereksinimleri)-verilerin geçerli zamandan itibaren gerekli yıllar olduğunu biliyorsanız, uzun süreli saklama kullanın.
  * Plansız (isteğe bağlı gereksinim)-önceden bilmiyorsanız, belirli özel bekletme ayarlarıyla isteğe bağlı olarak kullanabilirsiniz (Bu özel saklama ayarları ilke ayarlarından etkilenmez).

* Özel saklama ile isteğe bağlı yedekleme-yedekleme ilkesi aracılığıyla zamanlanmamış bir yedekleme yapmanız gerekiyorsa, isteğe bağlı yedekleme kullanabilirsiniz. Bu, zamanlanmış yedeğine uymayan veya ayrıntılı yedekleme (örneğin, zamanlanan yedeklemenin günde yalnızca bir yedeklemeye izin verdiğinden) yedeklemeleri almak için yararlı olabilir. Zamanlanan ilkede tanımlanan bekletme ilkesinin isteğe bağlı yedeklemeler için uygulanmadığını unutmayın.

### <a name="optimize-backup-policy"></a>Yedekleme Ilkesini iyileştirme

* İş gereksinimleriniz değiştikçe, saklama süresini genişletmeniz veya azaltmanız gerekebilir. Bunu yaptığınızda, aşağıdakileri bekleyebilir:  
  * Bekletme genişletildiğinde, var olan kurtarma noktaları işaretlenir ve yeni ilkeye uygun olarak tutulur.
  * Bekletme azaltıldığında, kurtarma noktaları sonraki temizleme işinde ayıklanmayı işaretlenir ve sonra silinir.
  * En son saklama kuralları tüm bekletme noktaları için geçerlidir (isteğe bağlı saklama noktaları hariç). Bu nedenle, bekletme süresi genişletilmişse (örneğin, 100 gün), yedekleme tamamlandığında (örneğin, 100 günden yedi güne kadar), tüm yedekleme verileri belirtilen son bekletme dönemine (yani 7 gün) göre korunur.

* Azure Backup, *yedeklemelerinizi korumayı ve yönetmeyi durdurma* esnekliği sağlar:
  * *Korumayı durdurun ve yedekleme verilerini koruyun*. Veri kaynağınızı devre dışı bırakırsanız veya kullanımdan çıkardıysanız (VM, uygulama), ancak denetim veya uyumluluk amaçlarıyla verileri tutmanız gerekiyorsa, gelecekteki tüm yedekleme işlerinin veri kaynağınızı korumasını ve yedeklenen kurtarma noktalarını korumasını durdurmak için bu seçeneği kullanabilirsiniz. Daha sonra VM korumasını geri yükleyebilir veya sürdürebilirsiniz.
  * *Korumayı durdurun ve yedekleme verilerini silin*. Bu seçenek, gelecekteki tüm yedekleme işlerinin VM 'nizi korumasını durdurur ve tüm kurtarma noktalarını siler. VM 'yi geri yükleyemeyeceksiniz ve yedeklemeyi yeniden yükle seçeneğini kullanamazsınız.

  * Korumaya devam ederseniz (verilerle durdurulan bir veri kaynağı), saklama kuralları uygulanır. Süresi geçen tüm kurtarma noktaları kaldırılır (zamanlanan zamanda).

* İlke tasarımınızı tamamlamadan önce, tasarım seçimlerinizi etkileyebilecek aşağıdaki faktörlerin farkında olmak önemlidir.
  * Yedekleme ilkesi bir kasadan kapsamlandırılır.
  * İlke başına öğe sayısı için bir sınır vardır (örneğin, 100 VM). Ölçeklemek için, aynı veya farklı zamanlamalarla yinelenen ilkeler oluşturabilirsiniz.
  * Belirli kurtarma noktalarını seçmeli olarak silemezsiniz.
  * Zamanlanmış yedeklemeyi tamamen devre dışı bırakıp veri kaynağını korumalı bir durumda tutabilirsiniz. İlkeyle yapılandırabileceğiniz en az sıklıkta, haftalık olarak zamanlanmış bir yedekleme yapmanız gerekir. Bir alternatif, yedekleme gerçekleştirmek istediğiniz her seferinde verileri koruma ve korumayı etkinleştirme ile korumayı durdurmak, isteğe bağlı bir yedekleme yapmak ve ardından korumayı kapatmak, ancak yedekleme verilerini korur. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Güvenlik konuları

Yedekleme verilerinizi korumanıza ve işinizin güvenlik ihtiyaçlarını karşılamanıza yardımcı olmak için Azure Backup, önemli veri ve sistemlerinizde bilinçli saldırılarına ve uygunsuz kullanım için gizlilik, bütünlük ve kullanılabilirlik sağlar. Azure Backup çözümünüz için aşağıdaki güvenlik yönergelerini göz önünde bulundurun:

### <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme

* Azure rol tabanlı erişim denetimi (Azure RBAC), takımınızda görev ayırma ve yalnızca işlerini gerçekleştirmek için gereken kullanıcılara erişim miktarı verme konusunda ayrıntılı erişim yönetimi sağlar. [Burada daha fazla bilgi edinebilirsiniz](backup-rbac-rs-vault.md).

* Azure Backup, yedekleme yönetim işlemlerini denetlemek için üç yerleşik rol sağlar: yedek katkıda bulunanlar, işleçler ve okuyucular. [Burada daha fazla bilgi edinebilirsiniz](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup, güvenlik açıklarını engellemek, algılamak ve yanıtlamak için hizmette yerleşik olarak bulunan çeşitli güvenlik denetimlerine sahiptir (daha fazla bilgi edinin)

* Kurtarma Hizmetleri kasaları tarafından kullanılan depolama hesapları yalıtılmış ve herhangi bir kötü amaçlı kullanıcı tarafından erişilemez. Erişime yalnızca geri yükleme gibi Azure Backup yönetim işlemleri aracılığıyla izin verilir.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Yoldaki ve bekleyen verilerin şifrelenmesi

Şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur.

* Azure 'da, Azure depolama ile kasa arasındaki yoldaki veriler HTTPS tarafından korunur. Bu veriler Azure ağı içinde kalır.

* Yedekleme verileri Microsoft tarafından yönetilen anahtarlar kullanılarak otomatik olarak şifrelenir. Alternatif olarak, [müşteri tarafından yönetilen anahtarlar](encryption-at-rest-with-cmk.md)olarak da bilinen kendi anahtarlarınızı de kullanabilirsiniz.

* Azure Backup, işletim sistemi/veri disklerinin Azure disk şifrelemesi (ADE) ile şifrelenmiş olduğu Azure VM 'lerinin yedeklenmesini ve geri yüklenmesini destekler. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>İstemeden silme işleminden yedekleme verilerinin korunması

Azure Backup, silme işleminden sonra bile yedekleme verilerini korumaya yardımcı olmak için güvenlik özellikleri sağlar. Geçici silme sayesinde, bir Kullanıcı yedeklemeyi (bir VM, SQL Server veritabanı, Azure dosya paylaşımından SAP HANA veritabanı) silerse, yedekleme verileri 14 ek gün boyunca tutulur ve bu yedekleme öğesinin veri kaybı olmadan kurtarılmasını sağlar. "Geçici silme" durumundaki yedekleme verilerinin ek 14 gün tutulması size herhangi bir ücret vermez. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Şüpheli etkinliğin izlenmesi ve uyarıları

Azure Backup, Azure Backup ilgili olaylara yönelik eylemleri görüntülemek ve yapılandırmak için yerleşik izleme ve uyarı özellikleri sağlar. [Burada daha fazla bilgi edinebilirsiniz](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Karma yedeklemeleri korumaya yardımcı olan güvenlik özellikleri

Azure Backup hizmeti, dosyaları, klasörleri ve birim ya da sistem durumunu şirket içi bir bilgisayardan Azure 'a yedeklemek ve geri yüklemek için Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı 'nı kullanır. MARS artık güvenlik özellikleri sağlıyor: Azure Backup 'den indirdikten sonra karşıya yükleme ve şifre çözme işleminden önce şifrelenecek bir parola, silinen yedekleme verileri, silme tarihinden sonra ek 14 gün boyunca tutulur ve kritik işlem (örn. bir parolayı değiştirme) yalnızca geçerli Azure kimlik bilgilerine sahip kullanıcılar tarafından gerçekleştirilebilir. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Ağ konuları

Azure Backup, iş yükünüze ait verilerin kurtarma hizmetleri kasasına taşınmasını gerektirir. Azure Backup, yedekleme verilerinin istenmeden (örneğin, ağda ortadaki adam saldırısı gibi) korunmasını sağlamak için çeşitli yetenekler sağlar. Aşağıdaki yönergeleri göz önünde bulundurun:

### <a name="internet-connectivity"></a>İnternet bağlantısı

* *Azure VM yedeklemesi* -depolama ve Azure Backup hizmeti arasındaki tüm gerekli iletişim ve veri aktarımı, sanal ağınıza erişime gerek kalmadan Azure ağı içinde gerçekleşir. Bu nedenle, güvenli ağların içine yerleştirilmiş olan Azure VM 'lerinin yedeklenmesi, herhangi bir IP veya FQDN 'ye erişim izni vermemesini gerektirmez.

* Azure VM 'de *SQL Server veritabanları,* Azure vm 'de SAP HANA veritabanları-Azure Backup hizmeti, Azure depolama ve Azure Active Directory bağlantı gerektirir. Bu, Özel uç noktalar kullanılarak veya gerekli genel IP adreslerine veya FQDN 'lere erişim izni vererek elde edilebilir. Gerekli Azure hizmetlerine doğru bağlantının yapılmasına izin verilmemesi, veritabanı bulma, yedeklemeyi yapılandırma, yedeklemeleri gerçekleştirme ve verileri geri yükleme gibi işlemlerde hata oluşmasına yol açabilir. NSG etiketlerini, Azure Güvenlik duvarını ve HTTP proxy 'yi kullanırken tüm Ağ Kılavuzu için bu [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) ve [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) makalelerine bakın.

* *Karma* -MARS (Microsoft Azure Kurtarma Hizmetleri) Aracısı tüm kritik işlemler için ağ erişimi gerektirir-yükleme, yapılandırma, yedekleme ve geri yükleme. MARS Aracısı [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) üzerinden Azure Backup hizmetine, [Özel uç noktaları](install-mars-agent.md#private-endpoints) kullanarak veya [uygun erişim denetimleriyle ara sunucu/güvenlik duvarı](install-mars-agent.md#verify-internet-access)aracılığıyla genel eşleme (eski devreler için kullanılabilir) ve Microsoft eşlemesi aracılığıyla bağlanabilir.

### <a name="private-endpoints-for-azure-backup"></a>Azure Backup için özel uç noktalar

Azure [Özel uç noktası](../private-link/private-endpoint-overview.md) , Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Azure Backup, Özel uç noktaları kullanarak kurtarma hizmetleri Kasalarınızın verilerinizi güvenli bir şekilde yedekleyeve geri yüklemenize olanak tanır.

* Kasa için özel uç noktaları etkinleştirdiğinizde, bunlar yalnızca Azure VM ve MARS Aracısı yedeklemelerindeki SQL ve SAP HANA iş yüklerinin yedeklenmesi ve geri yüklenmesi için kullanılır.  Diğer iş yüklerinin yedeklenmesi için kasayı da kullanabilirsiniz (ancak özel uç noktalar gerektirmez). MARS Aracısı kullanılarak SQL ve SAP HANA iş yükleri ve yedekleme 'nin yedeğinin yanı sıra, Azure VM yedeklemesi durumunda dosya kurtarma gerçekleştirmek için de özel uç noktalar kullanılır. [Burada daha fazla bilgi edinebilirsiniz](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory şu anda özel uç noktaları desteklemez. Bu nedenle, Azure Active Directory için gereken IP 'Ler ve FQDN 'ler, Azure VM 'lerinde veritabanlarının yedeklenmesi sırasında ve MARS Aracısı kullanılarak yedeklendiğinden güvenli ağdan giden erişime izin verilmesi gerekir. Ayrıca, geçerli olduğu şekilde Azure AD 'ye erişim izni vermek için NSG etiketlerini ve Azure Güvenlik Duvarı etiketlerini de kullanabilirsiniz. [Önkoşullar](./private-endpoints.md#before-you-start)hakkında daha fazla bilgi edinin.

## <a name="governance-considerations"></a>İdare ile ilgili önemli noktalar

Azure 'da idare, birincil olarak [Azure ilkesi](../governance/policy/overview.md) ve [Azure maliyet yönetimi](../cost-management-billing/cost-management-billing-overview.md)ile uygulanır. [Azure ilkesi](../governance/policy/overview.md) , kaynaklarınız için kuralları zorlamak üzere ilke tanımları oluşturmanıza, atamanıza ve yönetmenize olanak sağlar. Bu özellik, bu kaynakları kurumsal standartlarınızla uyumlu tutar. [Azure maliyet yönetimi](../cost-management-billing/cost-management-billing-overview.md) , Azure kaynaklarınız ve diğer bulut sağlayıcıları için bulut kullanımını ve harcamalarınızı izlemenize olanak sağlar. Ayrıca, [Azure fiyat Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) ve [Azure Advisor](../advisor/advisor-overview.md)  gibi aşağıdaki araçlar, maliyet yönetimi sürecinde önemli bir rol oynar.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup yerleşik Azure Ilkesi aracılığıyla iki temel senaryoyu destekler

* Yeni oluşturulan iş açısından kritik makinelerin doğru bekletme ayarları ile otomatik olarak yedeklendiğinden emin olun. Azure Backup, bir abonelik veya kaynak grubu içindeki belirli bir konumdaki tüm Azure VM 'lerine atanabilecek yerleşik bir ilke (Azure Ilkesi kullanarak) sağlar. Bu ilke belirli bir kapsama atandığında, bu kapsamda oluşturulan tüm yeni VM 'Ler, aynı konum ve abonelik içindeki mevcut bir kasaya yedekleme için otomatik olarak yapılandırılır. Kullanıcı, yedeklenen sanal makinelerin ilişkilendirilmesi gereken kasayı ve bekletme ilkesini belirtebilir. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-auto-enable-backup.md).

* Yeni oluşturulan kasaların raporları desteklemek için etkinleştirilmiş tanılamayı içerdiğinden emin olun. Genellikle, kasa başına el ile bir tanılama ayarı eklemek çok bir görev olabilir. Ayrıca, bu kasaya ait raporları görüntüleyebilmeniz için oluşturulan yeni kasaların tanılama ayarlarının etkin olması gerekir. Tanılama ayarlarının ölçeğe göre (hedef olarak Log Analytics ile) oluşturulmasını basitleştirmek için, Azure Backup yerleşik bir Azure Ilkesi sağlar. Bu ilke, her abonelik veya kaynak grubundaki tüm kasaları için bir LA tanılama ayarı ekler. Aşağıdaki bölümler, bu ilkenin nasıl kullanılacağına ilişkin yönergeler sağlar. [Burada daha fazla bilgi edinebilirsiniz](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup maliyet konuları

Azure Backup hizmetin özellikleri, maliyetlerinizi etkin bir şekilde yönetme esnekliği sunar ve BCDR (iş sürekliliği ve olağanüstü durum kurtarma) iş Gereksiniminize uygun olmaya devam eder. Aşağıdaki yönergeleri göz önünde bulundurun:

* Çeşitli sızıntıları ayarlayarak maliyeti değerlendirmek ve iyileştirmek için fiyatlandırma hesaplayıcısını kullanın. [Daha fazla bilgi edinin](azure-backup-pricing.md)

* Yedekleme Gezgini: yedekleme, kritik olmayan iş yükleri veya silinen VM 'Lere yönelik yedeklemeleri durdurup yedekleme depolama büyümesini anlamak ve iyileştirmek için yedekleme Gezginini veya yedekleme raporlarını kullanın. Bir yedekleme perspektifinden, tüm bakırsanız toplanan bir görünüm edinebilirsiniz. Bu, yalnızca yedekleme öğelerinizdeki bilgileri değil, yedekleme için yapılandırılmamış kaynakları da içerir. Bu, büyümekte olan önemli verileri korumayı hiç kaçırmayın ve yedeklemeleriniz kritik olmayan iş yükleri veya silinen iş yükleri için iyileştirilmiştir.

* Yedekleme ilkesini iyileştirme
  * İş yükü arşiv türleri temelinde zamanlamayı ve bekletme ayarlarını iyileştirin (örneğin, görev açısından kritik, kritik olmayan)
  * Anında geri yükleme için bekletme ayarlarını iyileştirme
  * Gereksinimleri karşılamak için doğru yedekleme türünü seçin. Azure Backup ' deki iş yüküne göre desteklenen yedekleme türleri (tam, artımlı, günlük, fark) alınıyor.

* Seçmeli yedekleme diskleri: diski çıkar (Önizleme özelliği), kritik verileri seçmeli olarak yedeklemek için verimli ve uygun maliyetli bir seçenek sağlar. Örneğin, bir VM 'ye bağlı olan disklerin geri kalanını yedeklemek istemediğinizde yalnızca bir diski yedekleyin. Bu, birden çok yedekleme çözümünden de yararlanabilirsiniz. Örneğin, veritabanlarınızı veya verilerinizi bir iş yükü yedekleme çözümüyle (Azure VM yedeklemesi 'nde SQL Server veritabanı) yedeklemenizin yanı sıra Seçili diskler için Azure VM düzeyi yedeklemesi kullanmak istediğinizde.

* Azure Backup, Azure VM 'lerinin anlık görüntülerini alır ve bunları kurtarma noktası oluşturmayı artırmak ve geri yükleme işlemlerini hızlandırmak için disklerle birlikte depolar. Bu, anında geri yükleme olarak adlandırılır. Varsayılan olarak, anlık geri yükleme anlık görüntüleri iki gün boyunca tutulur. Bu özellik geri yükleme sürelerini kesip bu anlık görüntülerden geri yükleme işlemine izin verir. Verileri kasadan geri dönüştürmek ve kopyalamak için gereken süreyi azaltır. Sonuç olarak, bu süre boyunca alınan anlık görüntülere karşılık gelen depolama maliyetlerini görürsünüz. [Burada daha fazla bilgi edinebilirsiniz](backup-instant-restore-capability.md#configure-snapshot-retention).

* Azure Backup kasasının depolama çoğaltma türü, varsayılan olarak coğrafi olarak yedekli (GRS) olarak ayarlanmıştır. Öğeler korunduktan sonra bu seçenek değiştirilemez. Coğrafi olarak yedekli depolama (GRS), yerel olarak yedekli depolama (LRS) düzeyinden daha yüksek düzeyde veri dayanıklılığı sağlar ve çapraz bölge geri yükleme ve maliyetlerin daha fazlasını kullanmasına izin verir. Düşük maliyetler ve daha yüksek veri dayanıklılığı arasındaki ücretleri gözden geçirin ve senaryonuza en uygun şeyleri belirleyin. [Daha fazla bilgi edinin](backup-create-rs-vault.md#set-storage-redundancy)

* Hem sanal makinenin hem de VM 'nin içinde çalıştığı iş yükünü koruyorsanız, bu çift korumanın gerekli olup olmadığını denetleyin.

## <a name="monitoring-and-alerting-considerations"></a>İzleme ve uyarı konuları

Bir yedekleme kullanıcısı veya Yöneticisi olarak, tüm yedekleme çözümlerini izleyebilmeniz ve önemli senaryolar hakkında bildirim alabilmesi gerekir. Bu bölümde Azure Backup hizmeti tarafından sunulan izleme ve bildirim özellikleri ayrıntılı olarak verilmiştir.

### <a name="monitoring"></a>İzleme

* Azure Backup, yedekleme, yedekleme, geri yükleme, yedeklemeyi silme gibi işlemler için **yerleşik iş izleme** sağlar. Bu, kasasının kapsamına alınır ve tek bir kasayı izlemek için idealdir. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* İşlemsel etkinlikleri ölçeklendirmeniz gerekiyorsa, **yedekleme Gezgini** tüm yedeklemelerinizin toplanmış bir görünümünü sağlar ve ayrıntılı ayrıntıya gitme analizini ve sorun gidermeyi etkinleştirir. Bu, Azure 'da, kapsayıcı kiracılar, konumlar, abonelikler, kaynak grupları ve kasaların bulunduğu tüm yedekleme genelinde işletimsel etkinlikleri izlemenize yardımcı olacak tek ve merkezi bir konum sunan yerleşik bir Azure Izleyici çalışma kitabıdır. [Burada daha fazla bilgi edinebilirsiniz](monitor-azure-backup-with-backup-explorer.md).
  * Yedekleme için yapılandırılmamış kaynakları belirlemek için kullanın ve büyümekte olan önemli verileri korumayı hiç kaçırmadığınızdan emin olun.
  * Pano, son yedi gün (en fazla) için işlemsel etkinlikler sağlar. Bu verileri saklamanız gerekiyorsa, Excel dosyası olarak dışarı aktarabilir ve koruyabilirsiniz.
  * Azure ışıklı bir Kullanıcı kullanıyorsanız, birden fazla kiracıda daha fazla bilgi görüntüleyerek sınır azaltma izlemeyi etkinleştirebilirsiniz.

* Uzun süreli işlem etkinliklerini korumanız ve görüntülemeniz gerekiyorsa, **raporları** kullanın. Yedekleme yöneticileri için ortak bir gereksinim, uzun bir süre yayılan verilere göre yedeklemeler hakkında öngörüler elde etmek içindir. Böyle bir çözüm için kullanım örnekleri şunları içerir:
  * Tüketilen bulut depolamasını ayırma ve tahmin etme.
  * Yedeklemeler ve geri yüklemeler denetimi.
  * Farklı ayrıntı düzeyi düzeylerinde önemli eğilimleri tanımlama.

* Ayrıca
  * **Log Analytics** çalışma alanına veri (örneğin, işler, ilkeler vb.) gönderebilirsiniz. Bu işlem, Azure izleyici tarafından toplanan diğer izleme verileriyle veri bağıntısını etkinleştirmek için Azure Izleyici günlüklerinin özelliklerini etkinleştirir, birden çok Azure aboneliği ve kiracısından günlük girişlerini analiz için tek bir konumda birleştirir, karmaşık analiz gerçekleştirmek ve günlük girişleri hakkında derin Öngörüler elde etmek için günlük sorgularını kullanın. [Burada daha fazla bilgi edinebilirsiniz](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * Olayları Azure dışında, örneğin bir üçüncü taraf SıEM (güvenlik bilgileri ve olay yönetimi) veya diğer Log Analytics çözümüne göndermek için Olay Hub 'ına veri gönderebilirsiniz. [Burada daha fazla bilgi edinebilirsiniz](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * Günlük verilerinizi denetim, statik analiz veya yedekleme için 90 günden daha uzun bir süre içinde bekletmek istiyorsanız, Azure depolama hesabına veri gönderebilirsiniz. Olaylarınızı yalnızca 90 gün veya daha az süreyle tutmanız gerekiyorsa, etkinlik günlüğü olayları 90 gün boyunca Azure platformunda tutulduğundan bir depolama hesabına arşiv ayarlamanız gerekmez. [Daha fazla bilgi edinin](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Uyarı

* Uyarılar öncelikle ilgili eylemi gerçekleştirmek için bildirim almanın bir yoludur. Yedekleme uyarıları bölümü Azure Backup hizmeti tarafından oluşturulan uyarıları gösterir.

* Azure Backup, e-posta yoluyla hatalara, uyarılara ve kritik işlemlere yönelik **olarak yerleşik bir uyarı** bildirim mekanizması sağlar. Bir uyarı oluşturulduğunda bildirilecek tek e-posta adreslerini veya dağıtım listelerini belirtebilirsiniz. Ayrıca, her bir uyarı için bildirim almayı veya saatlik bir özette grupları seçip bildirim almayı seçebilirsiniz.
  * Bu uyarılar, hizmet tarafından tanımlanır ve sınırlı senaryolar için destek sağlar. yedekleme/geri yükleme hatalarıyla, verileri koruma ve verileri silme ile korumayı durdurma ve bu şekilde devam eder. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Verileri silme ile korumayı durdur gibi bir bozucu işlem gerçekleştirilirse, kurtarma hizmetleri Kasası için **Bildirimler yapılandırılmadığı halde** bir uyarı oluşturulur ve abonelik sahiplerine, yöneticilerine ve ortak yöneticilere e-posta gönderilir.
  * Belirli iş yükleri yüksek sıklıkta başarısız olabilir (örneğin, her 15 dakikada bir SQL Server). Her bir başarısızlık durumunda oluşan uyarılarla başa çıkılamamasını engellemek için uyarılar birleştirilir. [Burada daha fazla bilgi edinebilirsiniz](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * Yerleşik uyarılar özelleştirilemiyor ve Azure portal tanımlanan e-postalarda kısıtlanıyor.

* **Özel uyarılar** (örneğin, başarılı işlerin uyarıları) oluşturmanız gerekiyorsa Log Analytics kullanın. Azure Izleyici 'de, bir Log Analytics çalışma alanında kendi uyarılarınızı oluşturabilirsiniz. Hibrit iş yükleri (DPM/MABS) Ayrıca, LA 'ya veri gönderebilir ve Azure Backup tarafından desteklenen iş yükleri arasında ortak uyarılar sağlamak için LA 'yı kullanabilir.

* Ayrıca, yerleşik kurtarma hizmetleri Kasası **etkinlik günlükleri** aracılığıyla da bildirim alabilirsiniz. Ancak, sınırlı senaryoları destekler ve zamanlanan yedekleme gibi işlemler için uygun değildir ve bu da, etkinlik günlükleriyle daha iyi olan kaynak günlükleriyle daha iyi bir şekilde hizalanır. Bu sınırlamalar hakkında daha fazla bilgi edinmek ve Azure Backup tarafından korunan tüm iş yüklerinizin ölçeğini izlemek ve uyarmak üzere Log Analytics çalışma alanını nasıl kullanabileceğinizi öğrenmek için bu [makaleye](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Backup kullanmaya yönelik başlangıç noktaları olarak aşağıdaki makaleleri okumanızı öneririz:

* [Azure Backup genel bakış](backup-overview.md)
* [Sık Sorulan Sorular](backup-azure-backup-faq.yml)
