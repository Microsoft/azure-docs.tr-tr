---
title: Azure sanal makinelerinde Oracle çözümleri | Microsoft Docs
description: Microsoft Azure 'de Oracle sanal makine görüntülerinin desteklenen yapılandırma ve sınırlamaları hakkında bilgi edinin.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.openlocfilehash: 8bcd45ab1270d478b05b3929d7b8914976612294
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645314"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure Oracle VM görüntüleri ve bunların dağıtımı

Bu makalede, Azure Marketi 'nde Oracle tarafından yayımlanan sanal makine görüntülerini temel alan Oracle çözümleri hakkında bilgiler yer almaktadır. Oracle bulut altyapısıyla platformlar arası uygulama çözümleri hakkında bilgi edinmek istiyorsanız, bkz. [Microsoft Azure ve Oracle bulut altyapısını tümleştirme Oracle uygulama çözümleri](oracle-oci-overview.md).

Şu anda kullanılabilir görüntülerin listesini almak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Haziran 2020 itibariyle aşağıdaki görüntüler kullanılabilir:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Bu görüntüler "kendi lisansını getir" olarak değerlendirilir ve yalnızca bir VM çalıştırılarak oluşan işlem, depolama ve ağ maliyetleri için ücretlendirilirsiniz.  Oracle yazılımını kullanmaya doğru lisanslandığınızı ve Oracle ile geçerli bir destek sözleşmeniz olduğunu kabul edersiniz. Oracle 'ın Şirket içinden Azure 'a lisans taşınabilirlik garantisi vardır. Lisans Taşınabilirliği hakkındaki ayrıntılar için bkz. yayımlanmış [Oracle ve Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Note.

Bireyler, çözümlerini Azure 'da sıfırdan oluşturdukları özel bir görüntüye dayandırın ve şirket içi ortamlarından özel bir görüntüyü karşıya yükleyebilir.

## <a name="oracle-database-vm-images"></a>Oracle Database VM görüntüleri

Oracle, Oracle Linux tabanlı sanal makine görüntülerinde Azure 'da Oracle Database 12,1 ve daha yüksek standart ve Enterprise sürümlerini çalıştırmayı destekler.  Azure 'da Oracle Database üretim iş yükleri için en iyi performans için, VM görüntüsünü düzgün bir şekilde boyutlandırdığınızdan ve Premium SSD veya Ultra SSD yönetilen diskleri kullandığınızdan emin olun. Oracle yayımlanmış VM görüntüsünü kullanarak Azure 'da bir Oracle Database çalışmaya ve çalıştırmaya hızlı bir şekilde nasıl yararlandığınıza ilişkin yönergeler için [Oracle Database hızlı başlangıç kılavuzunu deneyin](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Ekli disk yapılandırma seçenekleri

Eklenen diskler, Azure Blob depolama hizmetini kullanır. Her standart disk, saniyede en fazla 500 giriş/çıkış işlemi (ıOPS) özelliğine sahiptir. Premium disk teklifimiz, yüksek performanslı veritabanı iş yükleri için tercih edilir ve disk başına 5000 IOPS 'yi alabilir. Performans ihtiyaçlarınızı karşılıyorsa tek bir disk kullanabilirsiniz. Ancak, birden çok bağlı disk kullanırsanız, veritabanı verilerini bunlar arasında yaydıysanız ve sonra Oracle otomatik depolama yönetimi (ASM) kullanıyorsanız, etkin ıOPS performansını artırabilirsiniz. Oracle ASM 'e özgü daha fazla bilgi için bkz. [Oracle otomatik depolamaya genel bakış](https://www.oracle.com/technetwork/database/index-100339.html) . Linux Azure VM 'de Oracle ASM 'yi yükleme ve yapılandırma hakkında bir örnek için bkz. [Oracle otomatik depolama yönetimi öğreticisini yükleme ve yapılandırma](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Paylaşılan depolama yapılandırma seçenekleri

Azure NetApp Files, bulutta veritabanları gibi yüksek performanslı iş yüklerini çalıştırmanın temel gereksinimlerini karşılayacak şekilde tasarlanmıştır ve şunları sağlar;

- VM yerel NFS istemcisi veya Oracle dNFS aracılığıyla Oracle iş yüklerini çalıştırmak için Azure yerel paylaşılan NFS depolama hizmeti
- Gerçek dünyada ıOPS taleplerini yansıtan ölçeklenebilir performans katmanları
- Düşük gecikme süresi
- Genellikle görev açısından kritik kurumsal iş yükleri (SAP ve Oracle gibi) tarafından talep edilen yüksek düzeyde kullanılabilirlik, yüksek dayanıklılık ve yönetilebilirlik
- En agresif RTO ve RPO SLA 'lara ulaşmak için hızlı ve verimli yedekleme ve kurtarma

Azure NetApp Files, Azure yerel hizmeti olarak Azure veri merkezi ortamında çalışan tüm Flash sistemlerine®® ONTAP 'ı temel aldığı için bu yetenekler olasıdır. Sonuç olarak, diğer Azure depolama seçenekleri gibi sağlanabilen ve tüketilen ideal bir veritabanı depolama teknolojisidir. Azure NetApp Files NFS birimlerine dağıtım ve erişme hakkında daha fazla bilgi için bkz. [Azure NetApp Files belgeleri](../../../azure-netapp-files/index.yml) . Azure NetApp Files bir Oracle veritabanı çalıştırmak için en iyi yöntem önerileri için [Azure NetApp Files kullanarak Oracle 'ın Azure dağıtımı En Iyi Yöntem Kılavuzu '](https://www.netapp.com/us/media/tr-4780.pdf) na bakın.

## <a name="licensing-oracle-database--software-on-azure"></a>Azure 'da & Yazılım Oracle Database lisanslama

Microsoft Azure, Oracle Database çalıştırmaya yönelik yetkili bir bulut ortamıdır. Oracle Core Factor tablosu, bulutta Oracle veritabanları lisanslandığınızda geçerli değildir. Bunun yerine, Enterprise Edition veritabanları için etkinleştirilmiş Hyper-Threading teknolojisine sahip VM 'Leri kullanırken, hiper iş parçacığı etkinse (ilke belgesinde belirtildiği gibi) iki vCPU 'yu bir Oracle Işlemci lisansına eşdeğer olarak Sayın. İlke ayrıntıları [burada](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)bulunabilir.
Oracle veritabanları genellikle daha yüksek bellek ve GÇ gerektirir. Bu nedenle, bu iş yükleri için [bellek Için Iyileştirilmiş VM 'ler](../../sizes-memory.md) önerilir. İş yüklerinizi daha iyi bir şekilde iyileştirmek için, [kısıtlı çekirdek vCPU 'lar](../../constrained-vcpu.md) yüksek bellek, depolama ve g/ç bant genişliği gerektiren, yüksek çekirdek sayısı olmayan Oracle Database iş yükleri için önerilir.

Oracle yazılımı ve iş yüklerini Şirket içinden Microsoft Azure 'e geçirirken Oracle, [Azure 'Da Oracle](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) 'da belirtildiği gibi lisans taşınabilirliği sağlar

## <a name="high-availability-and-disaster-recovery-considerations"></a>Yüksek kullanılabilirlik ve olağanüstü durum kurtarma konuları

Azure 'da Oracle veritabanları kullanırken, herhangi bir kesinti yaşanmasını önlemek için yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümü uygulamaktan siz sorumlusunuz.

Oracle Database Enterprise Edition için yüksek kullanılabilirlik ve olağanüstü durum kurtarma (Oracle RAC 'ye bağlı olmadan), iki ayrı sanal makinede iki veritabanı bulunan [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)veya [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)kullanılarak Azure 'da elde edilebilir. Her iki sanal makine da, özel kalıcı IP adresi üzerinden bunlara erişebildiklerinden emin olmak için aynı [sanal ağ](https://azure.microsoft.com/documentation/services/virtual-network/) içinde olmalıdır.  Ayrıca, Azure 'un onları ayrı hata etki alanlarına ve yükseltme etki alanlarına yerleştirmesini sağlamak için sanal makineleri aynı Kullanılabilirlik kümesine yerleştirmenizi öneririz. Coğrafi yedeklilik olmasını, iki farklı bölge arasında çoğaltmak üzere iki veritabanını ayarlama ve iki örneği bir VPN Gateway bağlama.

[Azure 'Da Oracle Data Guard 'ı uygulama](configure-oracle-dataguard.md) öğreticisinde, Azure 'da temel kurulum yordamında size adım adım yol gösterilir.  

Oracle Data Guard ile yüksek kullanılabilirlik, bir sanal makinedeki birincil veritabanıyla, başka bir sanal makinedeki ikincil (bekleme) bir veritabanında ve aralarında tek yönlü bir çoğaltmanın ayarlanmasıyla elde edilebilir. Sonuç, veritabanının kopyasına okuma erişimdir. Oracle GoldenGate ile iki veritabanı arasında çift yönlü çoğaltmayı yapılandırabilirsiniz. Bu araçları kullanarak veritabanınız için yüksek kullanılabilirliğe sahip bir çözüm ayarlamayı öğrenmek için Oracle Web sitesindeki [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) ve [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) belgeleri bölümüne bakın. Veritabanının kopyasına okuma/yazma erişimi gerekiyorsa, [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)' ı kullanabilirsiniz.

[Azure 'Da Oracle GoldenGate uygulayan](configure-oracle-golden-gate.md) öğretici, Azure 'daki temel kurulum yordamında size yol gösterir.

Bir HA ve DR çözümünün Azure 'da tasarlanmış olmasının yanı sıra, veritabanınızı geri yüklemek için bir yedekleme stratejisine sahip olmanız gerekir. Öğretici [yedekleme ve kurtarma Oracle Database](./oracle-overview.md) , tutarlı bir yedekleme oluşturmaya yönelik temel yordamda size rehberlik eder.

## <a name="support-for-jd-edwards"></a>JD edi desteği

Oracle Destek notunun [belge kimliği 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)' ne göre, JD edi EnterpriseOne sürüm 9,2 ve üzeri, belirli (MTR) karşılayan **tüm genel bulut tekliflerinde** desteklenir `Minimum Technical Requirements` .  İşletim sistemi ve yazılım uygulama uyumluluğu için MTR belirtimlerini karşılayan özel görüntüler oluşturmanız gerekir.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Oracle WebLogic Server sanal makine teklifleri

Oracle ve Microsoft, Web Logic Server 'ı Azure Uygulama tekliflerine yönelik bir koleksiyon biçiminde Azure Marketi 'ne getirmek için işbirliği yapmıştır.  Bu teklifler, [Oracle WebLogic Server Azure uygulamaları](oracle-weblogic.md)makalesinde açıklanmaktadır.

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server sanal makine görüntüleri

- **Kümeleme yalnızca Enterprise Edition 'da desteklenir.** Yalnızca Oracle WebLogic Server Enterprise sürümü kullanılırken WebLogic kümeleme kullanma lisansına sahip olursunuz. Oracle WebLogic Server Standard sürümüyle kümeleme kullanmayın.
- **UDP çok noktaya yayını desteklenmez.** Azure, çok noktaya yayın veya yayın olmadan UDP tek noktaya yayını destekler. Oracle WebLogic Server, Azure UDP tek noktaya yayın özelliklerine güveniyor. UDP tek noktaya yayın için en iyi sonuçlar için, WebLogic Cluster boyutunun statik tutulmasını veya 10 ' dan fazla yönetilen sunucu olmadan tutulmasını öneririz.
- **Oracle WebLogic Server, T3 erişimi için genel ve özel bağlantı noktalarının aynı olmasını bekler (örneğin, kurumsal Javabeller kullanılırken).** Bir hizmet katmanı (EJB) uygulamasının, *Slwls* adlı bir sanal ağda iki veya daha fazla VM 'den oluşan bir Oracle WebLogic Server kümesinde çalıştığı çok katmanlı bir senaryoyu göz önünde bulundurun. İstemci katmanı, hizmet katmanında EJB çağrısına çalışan basit bir Java programı çalıştıran aynı sanal ağdaki farklı bir alt ağda bulunur. Hizmet katmanının yük dengelenmesi gerektiğinden, Oracle WebLogic Server kümesindeki sanal makineler için ortak yük dengeli bir uç noktanın oluşturulması gerekir. Belirttiğiniz özel bağlantı noktası genel bağlantı noktasından farklıysa (örneğin, 7006:7008), aşağıdaki gibi bir hata oluşur:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Bunun nedeni, tüm uzak T3 erişimidir, Oracle WebLogic Server yük dengeleyici bağlantı noktasını ve WebLogic Managed Server bağlantı noktasının aynı olmasını bekler. Önceki durumda, istemci bağlantı noktası 7006 ' a (yük dengeleyici bağlantı noktası) ve yönetilen sunucu ise 7008 (özel bağlantı noktası) üzerinde dinleme yapıyor. Bu kısıtlama yalnızca, HTTP değil, T3 erişimi için geçerlidir.

   Bu sorundan kaçınmak için aşağıdaki geçici çözümlerden birini kullanın:

- T3 erişimine adanmış yük dengeli uç noktalar için aynı özel ve genel bağlantı noktası numaralarını kullanın.
- Oracle WebLogic Server 'ı başlatırken aşağıdaki JVM parametresini ekleyin:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

İlgili bilgiler için, bkz. KB  makalesi 860340,1 <https://support.oracle.com> .

- **Dinamik kümeleme ve yük dengeleme sınırlamaları.** Oracle WebLogic Server 'da dinamik bir küme kullanmak istediğinizi ve Azure 'da tek ve genel yük dengeli bir uç nokta ile kullanıma sunduğunuzu varsayalım. Bu işlem, yönetilen sunucuların her biri için (bir aralıktan dinamik olarak atanmayan) sabit bir bağlantı noktası numarası kullandığınız ve yöneticinin izlemediği makinelerden daha fazla yönetilen sunucu başlatmadığı sürece yapılabilir. Diğer bir deyişle, sanal makine başına birden fazla yönetilen sunucu yoktur). Yapılandırmanız, sanal makinelerden (birden çok Oracle WebLogic Server örneği aynı sanal makineyi paylaştıkları) daha fazla Oracle WebLogic Servers 'ın başlatılmasına neden olursa, belirli bir bağlantı noktası numarasına bağlamak için bu Oracle WebLogic Servers örneklerinden birden fazla olması mümkün değildir. Bu sanal makinedeki diğerleri başarısız olur.

   Yönetim sunucusunu, yönetilen sunucularına otomatik olarak benzersiz bağlantı noktası numaraları atanacak şekilde yapılandırırsanız, Azure tek bir genel bağlantı noktasından birden çok özel bağlantı noktasına eşlemeyi desteklemediğinden, bu yapılandırma için gerekli olduğu gibi yük dengeleme mümkün değildir.
- **Bir sanal makinedeki birden çok Oracle WebLogic Server örneği.** Dağıtımınızın gereksinimlerine bağlı olarak, sanal makine yeterince büyükse Oracle WebLogic Server 'ın birden fazla örneğini aynı sanal makinede çalıştırmayı düşünebilirsiniz. Örneğin, iki çekirdek içeren orta büyüklükte bir sanal makinede, iki çekirdekli Oracle WebLogic Server örneğini çalıştırmayı seçebilirsiniz. Ancak, yalnızca birden çok Oracle WebLogic Server örneğini çalıştıran tek bir sanal makine kullandıysanız, mimarinize tek hata noktaları getirmekten kaçınmanızı öneririz. En az iki sanal makine kullanmak daha iyi bir yaklaşım olabilir ve her bir sanal makine daha sonra Oracle WebLogic Server 'ın birden fazla örneğini çalıştırabilir. Oracle WebLogic Server 'ın her örneği aynı kümenin bir parçası olmaya devam edebilir. Ancak, Azure yük dengeleyici, yük dengeli sunucuların benzersiz sanal makineler arasında dağıtılmasını gerektirdiğinden, bu tür bir Oracle WebLogic Server dağıtımı tarafından sunulan uç noktaların yükünü dengelemek için Azure 'u kullanmak mümkün değildir.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK sanal makine görüntüleri

- **JDK 6 ve en son 7 güncelleştirme.** Java 'nın en son ortak ve desteklenen sürümü (Şu anda Java 8) kullanmanızı öneririz. Azure, JDK 6 ve 7 görüntülerini da kullanılabilir hale getirir. Bu, henüz JDK 8 ' e yükseltilmeye hazırlanma eski uygulamalar için tasarlanmıştır. Önceki JDK görüntülerine yönelik güncelleştirmeler genel kullanıma sunulmayabilir, ancak Oracle ile Microsoft iş ortaklığı verildiğinde, Azure tarafından sağlanan JDK 6 ve 7 görüntüleri, genellikle Oracle tarafından desteklenen ve yalnızca bir Oracle tarafından desteklenen müşterilerin sunduğu daha yeni bir ortak olmayan güncelleştirme içerir. JDK görüntülerinin yeni sürümleri, güncelleştirilmiş JDK 6 ve 7 sürümleri ile zaman içinde kullanıma sunulacaktır.

   JDK 6 ve 7 görüntülerinde kullanılabilen JDK ve bunlardan türetilmiş sanal makineler ve görüntüler yalnızca Azure 'da kullanılabilir.
- **64 bit JDK.** Oracle WebLogic Server sanal makine görüntüleri ve Azure tarafından sunulan Oracle JDK sanal makine görüntüleri, hem Windows Server hem de JDK 'nin 64 bitlik sürümlerini içerir.

## <a name="next-steps"></a>Sonraki adımlar

Artık Microsoft Azure içindeki sanal makine görüntülerini temel alan geçerli Oracle çözümlerine genel bir bakış sunulmaktadır. Sonraki adımınız, ilk Oracle veritabanınızı Azure 'da dağıtmaktır.

> [!div class="nextstepaction"]
> [Azure 'da Oracle veritabanı oluşturma](oracle-database-quick-create.md)
