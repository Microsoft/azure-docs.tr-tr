---
title: RHEL 'de Azure sanal makinelerinde (VM) IBM DB2 HADR 'yi ayarlama | Microsoft Docs
description: Azure sanal makinelerinde (VM) RHEL 'de IBM DB2 LUW üzerinde yüksek kullanılabilirlik sağlayın.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: 5487b90172788c08a4383a32462ea5a85c1763ee
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099671"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server üzerinde Azure VM’lerindeki IBM Db2 LUW’a yönelik yüksek kullanılabilirlik

[Yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) yapılandırmasında](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) LINUX, UNIX ve Windows (LUW) Için IBM DB2, birincil veritabanı örneği çalıştıran bir düğümden ve ikincil bir veritabanı örneği çalıştıran en az bir düğümden oluşur. Birincil veritabanı örneğindeki değişiklikler, yapılandırmanıza bağlı olarak, eş zamanlı olarak veya zaman uyumsuz olarak ikincil bir veritabanı örneğine çoğaltılır. 

Bu makalede, Azure sanal makinelerini (VM) dağıtma ve yapılandırma, küme çerçevesini yüklemek ve HADR yapılandırması ile IBM DB2 LUW yüklemesi açıklanmaktadır. 

Makalede, HADR veya SAP yazılım yüklemesi ile IBM DB2 LUW yükleme ve yapılandırma ele alınmaktadır. Bu görevleri gerçekleştirmenize yardımcı olmak için SAP ve IBM yükleme kılavuzlarına başvurular sağlıyoruz. Bu makale, Azure ortamına özgü bölümlere odaklanır. 

Desteklenen IBM DB2 sürümleri, SAP Note [1928533]' de belgelendiği gibi 10,5 ve üzeri sürümlerde bulunur.

Yüklemeye başlamadan önce, aşağıdaki SAP notları ve belgelerine bakın:

| SAP Note | Açıklama |
| --- | --- |
| [1928533] | Azure 'da SAP uygulamaları: Desteklenen Ürünler ve Azure VM türleri |
| [2015553] | Azure 'da SAP: Destek önkoşulları |
| [2178632] | Azure 'da SAP için anahtar izleme ölçümleri |
| [2191498] | Azure ile Linux üzerinde SAP: Gelişmiş izleme |
| [2243692] | Azure 'da Linux (IaaS) VM: SAP lisans sorunları |
| [2002167] | Red Hat Enterprise Linux 7. x: Yükleme ve yükseltme |
| [2694118] | Azure 'da HA eklentisi Red Hat Enterprise Linux |
| [1999351] | SAP için gelişmiş Azure izleme sorunlarını giderme |
| [2233094] | DB6: Azure 'da Linux, UNIX ve Windows için IBM DB2 kullanan SAP uygulamaları-ek bilgiler |
| [1612105] | DB6: HADR ile DB2 hakkında SSS |


| Belgeler | 
| --- |
| [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux için gereken tüm SAP notlarına sahiptir |
| [Linux 'TA SAP Için Azure sanal makineleri planlama ve uygulama][planning-guide] Kılavuzu |
| [Linux 'TA SAP Için Azure sanal makineleri dağıtımı][deployment-guide] (Bu makale) |
| [Linux 'TA SAP Için Azure sanal makineler veritabanı yönetim sistemi (DBMS) dağıtımı][dbms-guide] |
| [Azure planlama ve dağıtım denetim listesi üzerinde SAP iş yükü][azr-sap-plancheck] |
| [Red Hat Enterprise Linux 7 için yüksek kullanılabilirlik eklentisi 'ne genel bakış][rhel-ha-addon] |
| [Yüksek kullanılabilirlik eklentisi Yönetimi][rhel-ha-admin] |
| [Yüksek kullanılabilirlik eklentisi başvurusu][rhel-ha-ref] |
| [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-küme üyesi olarak Microsoft Azure Sanal Makineler][rhel-azr-supp]
| [Microsoft Azure üzerinde Red Hat Enterprise Linux 7,4 (ve üzeri) yüksek kullanılabilirlik kümesi yükleme ve yapılandırma][rhel-azr-inst]
| [IBM DB2 Azure sanal makineleri SAP iş yükü için DBMS dağıtımı][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR 10,5][db2-hadr-10.5] |
| [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkesi-bir kümede Linux, Unix ve Windows için IBM DB2 yönetimi][rhel-db2-supp]



## <a name="overview"></a>Genel Bakış
Yüksek kullanılabilirlik elde etmek için, HADR ile IBM DB2 LUW, [Azure kullanılabilirlik kümesine](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) veya [Azure kullanılabilirlik alanları](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)göre dağıtılan en az iki Azure sanal makinesine yüklenir. 

Aşağıdaki grafiklerde iki veritabanı sunucusu Azure VM kurulumu görüntülenir. Veritabanı sunucusu Azure VM 'lerinin her ikisi de kendi depolamasına sahiptir ve çalışır. HADR 'de, Azure VM 'lerinden birindeki bir veritabanı örneği, birincil örnek rolüne sahiptir. Tüm istemciler birincil örneğe bağlanır. Veritabanı işlemlerinde yapılan tüm değişiklikler, DB2 işlem günlüğünde yerel olarak kalıcı hale getirilir. İşlem günlüğü kayıtları yerel olarak kalıcı olduğundan, kayıtlar TCP/IP aracılığıyla ikinci veritabanı sunucusundaki veritabanı örneğine, bekleme sunucusuna veya bekleme örneğine aktarılır. Bekleme örneği, aktarılan işlem günlüğü kayıtlarını ileri alarak yerel veritabanını güncelleştirir. Bu şekilde, bekleme sunucusu birincil sunucuyla eşitlenmiş olarak tutulur.

HADR yalnızca bir çoğaltma işlevidir. Hata algılaması yoktur ve otomatik yük devretme veya yük devretme tesislerini içermez. Bir veritabanı yöneticisi tarafından el ile başlatılan veya bekleyen sunucuya aktarma yapılmalıdır. Otomatik bir ele ve hata algılamayı başarmak için, Linux Paceoluşturucu kümeleme özelliğini kullanabilirsiniz. Paceyapıcısı iki veritabanı sunucusu örneğini izler. Birincil veritabanı sunucu örneği kilitlenirse, pacemaker bekleyen sunucu tarafından *Otomatik* bir HADR tarafından başlatılır. Paceyapıcısı Ayrıca sanal IP adresinin yeni birincil sunucuya atanmasını sağlar.

![IBM DB2 yüksek kullanılabilirliğe genel bakış](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

SAP uygulama sunucularının birincil veritabanına bağlanmasını sağlamak için bir sanal ana bilgisayar adına ve sanal IP adresine sahip olmanız gerekir. Yük devretme durumunda, SAP uygulama sunucuları yeni birincil veritabanı örneğine bağlanır. Bir Azure ortamında, bir sanal IP adresini IBM DB2 için gerekli olacak şekilde kullanmak için bir [Azure yük dengeleyici](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) gereklidir. 

IBM DB2 LUW ile HADR ve Paceyapıcısı 'ın yüksek oranda kullanılabilir bir SAP sistem kurulumuna nasıl uyduğunu tam olarak anlamanıza yardımcı olmak için aşağıdaki görüntüde IBM DB2 veritabanına dayalı bir SAP sisteminin yüksek kullanılabilirliğe sahip bir kurulumuna genel bakış sunulmaktadır. Bu makalede yalnızca IBM DB2 ele alınmaktadır, ancak SAP sisteminin diğer bileşenlerinin nasıl ayarlanacağı hakkında diğer makalelere başvurular sağlanmaktadır.

![IBM DB2 yüksek kullanılabilirlik tam ortamına genel bakış](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Gerekli adımlara üst düzey genel bakış
Bir IBM DB2 yapılandırması dağıtmak için aşağıdaki adımları izlemeniz gerekir:

  + Ortamınızı planlayın.
  + VM 'Leri dağıtın.
  + RHEL Linux 'u güncelleştirin ve dosya sistemlerini yapılandırın.
  + Pacemaker 'ı yükleyip yapılandırın.
  + [GlusterFS kümesi][glusterfs] veya [Azure NetApp Files][anf-rhel] ayarlama
  + [Yoks/ers ayrı bir kümeye][ascs-ha-rhel]yüklenir.
  + Dağıtılmış/yüksek kullanılabilirlik seçeneği (SWPM) ile IBM DB2 veritabanını yükler.
  + İkincil bir veritabanı düğümü ve örneği yükleyip oluşturun ve HADR 'yi yapılandırın.
  + HADR 'nin çalıştığını doğrulayın.
  + IBM DB2 'yi denetlemek için Paceoluşturucu yapılandırmasını uygulayın.
  + Azure Load Balancer yapılandırın.
  + Birincil ve iletişim kutusu uygulama sunucularını yükler.
  + SAP uygulama sunucularının yapılandırmasını denetleyip uyarlayın.
  + Yük devretme ve yük testi gerçekleştirme.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR ile IBM DB2 LUW barındırmak için Azure altyapısını planlayın

Dağıtımı yürütmeden önce planlama işlemini doldurun. Planlama, Azure 'da HADR ile bir DB2 yapılandırmasına dağıtım temelini oluşturur. IDB db2 LUW planlamasının parçası olması gereken anahtar öğeleri (SAP ortamının veritabanı bölümü) aşağıdaki tabloda listelenmiştir:

| Konu | Kısa açıklama |
| --- | --- |
| Azure kaynak gruplarını tanımlama | VM, VNet, Azure Load Balancer ve diğer kaynakları dağıttığınız kaynak grupları. Mevcut veya yeni olabilir. |
| Sanal ağ/alt ağ tanımı | IBM DB2 ve Azure Load Balancer VM 'lerinin dağıtıldığı yer. Var olan veya yeni oluşturulmuş olabilir. |
| IBM DB2 LUW barındıran sanal makineler | VM boyutu, depolama, ağ, IP adresi. |
| IBM DB2 veritabanı için sanal ana bilgisayar adı ve sanal IP| SAP uygulama sunucularının bağlantısında kullanılan sanal IP veya ana bilgisayar adı. **DB-vırt-hostname**, **DB-vırt-IP**. |
| Azure ile sınırlama | Bölünmüş beyinden kaçınmaya yönelik Yöntem engellenir. |
| Azure Load Balancer | Temel veya standart (önerilir) kullanımı, DB2 veritabanı için yoklama bağlantı noktası (öneri 62500) **araştırma-bağlantı noktasıdır**. |
| Ad çözümlemesi| Ad çözümlemenin ortamda nasıl çalıştığı. DNS hizmeti önemle önerilir. Yerel ana bilgisayarlar dosyası kullanılabilir. |
    
Azure 'da Linux Paceyapıcısı hakkında daha fazla bilgi için bkz. [Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux dağıtım

IBM DB2 LUW için kaynak Aracısı Red Hat Enterprise Linux Server HA eklentisi 'ne dahildir. Bu belgede açıklanan kurulum için SAP Red Hat Enterprise Linux kullanmanız gerekir. Azure Marketi, yeni Azure sanal makinelerini dağıtmak için kullanabileceğiniz SAP veya daha yüksek Red Hat Enterprise Linux 7,4 için bir görüntü içerir. Azure VM marketi 'nde bir VM görüntüsü seçtiğinizde, Azure Marketi aracılığıyla, Red Hat tarafından sunulan çeşitli destek veya hizmet modellerinden haberdar olun.

### <a name="hosts-dns-updates"></a>Bilgisayarlarınızı DNS güncelleştirmeleri
Sanal ana bilgisayar adları da dahil olmak üzere tüm konak adlarının listesini oluşturun ve DNS sunucularınızı, konak adı çözümlemesi için doğru IP adresini etkinleştirmek üzere güncelleştirin. Bir DNS sunucusu yoksa veya DNS girdilerini güncelleştirip oluşturamıyoruz, bu senaryoya katılan ayrı VM 'lerin yerel ana bilgisayar dosyalarını kullanmanız gerekir. Ana bilgisayar dosyaları girişleri kullanıyorsanız, girdilerin SAP sistem ortamındaki tüm VM 'lere uygulandığından emin olun. Bununla birlikte, ideal olarak Azure 'a genişleyen DNS 'nizi kullanmanızı öneririz.


### <a name="manual-deployment"></a>El ile dağıtım

Seçilen işletim sisteminin IBM DB2 LUW için IBM/SAP tarafından desteklendiğinden emin olun. Azure VM 'Leri ve DB2 sürümleri için desteklenen işletim sistemi sürümlerinin listesi SAP Note [1928533]' de mevcuttur. Tek bir DB2 sürümüne göre işletim sistemi sürümlerinin listesi SAP ürün kullanılabilirliği matrisinde kullanılabilir. Bu veya Red Hat Enterprise Linux sonraki sürümlerde Azure ile ilgili performans iyileştirmeleri nedeniyle SAP için en az Red Hat Enterprise Linux 7,4 önerilir.

1. Bir kaynak grubu oluşturun veya seçin.
1. Bir sanal ağ ve alt ağ oluşturun veya seçin.
1. Bir Azure kullanılabilirlik kümesi oluşturun veya bir kullanılabilirlik bölgesi dağıtın.
    + Kullanılabilirlik kümesi için, en fazla güncelleştirme etki alanlarını 2 olarak ayarlayın.
1. Sanal makine oluşturun 1.
    + Azure Marketi 'nde SAP görüntüsü için Red Hat Enterprise Linux kullanın.
    + Adım 3 ' te oluşturduğunuz Azure kullanılabilirlik kümesini seçin veya kullanılabilirlik Bölgesi ' ni seçin.
1.  Sanal makine oluştur 2.
    + Azure Marketi 'nde SAP görüntüsü için Red Hat Enterprise Linux kullanın.
    + Adım 3 ' te oluşturduğunuz Azure kullanılabilirlik kümesini seçin veya kullanılabilirlik Bölgesi ' ni (adım 3 ' te ile aynı bölgeyi değil) seçin.
1. Veri disklerini VM 'lere ekleyin ve ardından [IBM DB2 Azure sanal makineleri IÇIN SAP iş yükü için][dbms-db2]bir dosya sistemi kurulumu önerisi ' ne bakın.

## <a name="create-the-pacemaker-cluster"></a>Paceoluşturucu kümesi oluşturma
    
Bu IBM DB2 sunucusu için temel bir Paceoluşturucu kümesi oluşturmak için bkz. [Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama][rhel-pcs-azr]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>IBM DB2 LUW ve SAP ortamını yükler

IBM DB2 LUW 'yu temel alan bir SAP ortamının yüklemesine başlamadan önce, aşağıdaki belgeleri gözden geçirin:

+ Azure belgeleri
+ SAP belgeleri
+ IBM belgeleri

Bu belgenin bağlantıları, bu makalenin giriş bölümünde verilmiştir.

IBM DB2 LUW üzerinde NetWeaver tabanlı uygulamalar yüklemeyle ilgili SAP yükleme kılavuzlarını denetleyin.
SAP [Yükleme Kılavuzu bulucusu][sap-instfind]' nı kullanarak SAP yardım portalındaki kılavuzların bulabilirsiniz.

Aşağıdaki filtreleri ayarlayarak portalda görünen kılavuz sayısını azaltabilirsiniz:
- Yapmak istiyorum: "Yeni bir sistem yüklensin"
- Veritabanım: "Linux, Unix ve Windows için IBM DB2"
- SAP NetWeaver sürümleri, yığın yapılandırması veya işletim sistemi için ek filtreler

#### <a name="red-hat-firewall-rules"></a>Red Hat güvenlik duvarı kuralları
Red Hat Enterprise Linux varsayılan olarak güvenlik duvarı etkinleştirilmiştir. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR ile IBM DB2 LUW ayarlamaya yönelik yükleme ipuçları

Birincil IBM DB2 LUW veritabanı örneğini ayarlamak için:

- Yüksek kullanılabilirlik veya dağıtılmış seçeneğini kullanın.
- SAP yoks/ERS ve veritabanı örneğini yükler.
- Yeni yüklenen veritabanının bir yedeğini alın.

> [!IMPORTANT] 
> Yükleme sırasında ayarlanan "veritabanı Iletişim bağlantı noktası" nı yazın. Her iki veritabanı örneği için de aynı bağlantı noktası numarası olmalıdır.
>![SAP SWPM bağlantı noktası tanımı](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Azure için IBM DB2 HADR ayarları

   Bir Azure pacemaker uçulama Aracısı kullandığınızda, aşağıdaki parametreleri ayarlayın:

   - HADR eş pencere süresi (saniye) (HADR_PEER_WINDOW) = 240  
   - HADR zaman aşımı değeri (HADR_TIMEOUT) = 45

İlk yük devretme/ön yük testi temelinde yukarıdaki parametreleri öneririz. Bu parametre ayarları ile yük devretme ve en uygun işlevsellik için test etmeniz zorunludur. Ayrı ayrı yapılandırmaların değişebildiğinden, parametreler ayarlama gerektirebilir. 

> [!NOTE]
> Normal başlatma ile HADR yapılandırması ile IBM DB2 'e özgü: Birincil veritabanı örneğini başlatabilmeniz için ikincil veya bekleme veritabanı örneğinin çalışır ve çalışıyor olması gerekir.

   
> [!NOTE]
> Azure ve pacemaker 'a özgü yükleme ve yapılandırma için: SAP yazılım sağlama Yöneticisi üzerinden yükleme yordamı sırasında IBM DB2 LUW için yüksek kullanılabilirlik hakkında açık bir soru vardır:
>+ **IBM DB2 pureScale**öğesini seçmeyin.
>+ **Çoklu platformlar IÇIN IBM Tivoli sistem otomasyonu yüklemeyi**seçmeyin.
>+ **Küme yapılandırma dosyalarını üret**' i seçmeyin.
>![SAP SWPM-DB2 HA seçenekleri](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


SAP homojen sistem kopyalama yordamını kullanarak bekleme veritabanı sunucusunu ayarlamak için aşağıdaki adımları yürütün:

1. **Hedef sistemler** **Dağıtılmış veritabanı örneği**> Sistem kopyalama seçeneğini belirleyin. >  > 
1. Bir kopyalama yöntemi olarak, yedek sunucu örneğindeki bir yedeği geri yüklemek için yedekleme kullanabilmeniz için **homojen sistemi** ' ni seçin.
1. Homojen sistem kopyası için veritabanını geri yüklemek üzere çıkış adımına ulaştığınızda yükleyiciden çıkın. Veritabanını birincil ana bilgisayarın yedeğinden geri yükleyin. Sonraki yükleme aşamaları, birincil veritabanı sunucusunda zaten yürütüldü.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>DB2 HADR için Red Hat güvenlik duvarı kuralları
DB2 ve HADR için DB2 arasında trafiğe izin vermek için güvenlik duvarı kuralları ekleme:
+ Veritabanı iletişim bağlantı noktası. Bölümler kullanılıyorsa, bu bağlantı noktalarını da ekleyin.
+ HADR bağlantı noktası (DB2 parametresinin değeri HADR_LOCAL_SVC)
+ Azure araştırma bağlantı noktası
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM DB2 HADR denetimi
Tanıtım amaçları ve bu makalede açıklanan yordamlar için veritabanı SID 'SI **ID2**' dir.

HADR yapılandırıldıktan ve durum eş ve birincil ve bekleme düğümlerine BAĞLıYKEN, aşağıdaki denetimi gerçekleştirin:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>DB2 Paceoluşturucu yapılandırması

Düğüm arızası durumunda otomatik yük devretme için pacemaker kullandığınızda, DB2 örneklerinizi ve Pacedevcinizi uygun şekilde yapılandırmanız gerekir. Bu bölümde bu tür bir yapılandırma açıklanmaktadır.

Aşağıdaki öğelerin ön eki vardır:

- **[A]** : Tüm düğümlere uygulanabilir
- **[1]** : Yalnızca düğüm 1 için geçerlidir 
- **[2]** : Yalnızca düğüm 2 için geçerlidir

**[A]** pacemaker yapılandırması için Önkoşullar:
1. Db2stop ile Kullanıcı DB2\<SID > her iki veritabanı sunucusunu da kapatın.
1. DB2\<SID > User için Shell ortamını */bin/ksh*olarak değiştirin:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker yapılandırması

**[1]** IBM DB2 HADR 'e özgü Paceyapıcısı yapılandırması:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** IBM DB2 kaynakları oluşturma:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** IBM DB2 kaynaklarını başlatın:
* Pacemaker 'ı bakım modundan çıkar.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** küme durumunun tamam olduğundan ve tüm kaynakların başlatıldığından emin olun. Kaynakların üzerinde çalıştığı düğüm önemli değildir.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Çevrimiçi: [az-idb01 az-idb02]

Kaynakların tam listesi:

 rsc_st_azure (stonith: fence_azure_arm):      Başlatıldı az-idb01 Master/bağımlı set: Db2_HADR_ID2-Master [Db2_HADR_ID2] ana şablonlar: [az-idb01] SLA: [az-idb02] kaynak grubu: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (OCF:: sinyal: IPaddr2):       Started az-idb01 nc_db2id2_ID2 (OCF:: sinyal: Azure-lb):      Başlatıldı az-idb01

Daemon durumu: Corosync: etkin/devre dışı pacemaker: etkin/devre dışı pcsd: etkin/etkin
</pre>

> [!IMPORTANT]
> Paceoluşturucu aracını kullanarak pacemaker kümelenmiş DB2 örneğini yönetmeniz gerekir. Db2stop gibi DB2 komutları kullanıyorsanız Paceyapıcısı eylemi kaynak hatası olarak algılar. Bakım yapıyorsanız, düğümleri veya kaynakları bakım moduna alabilirsiniz. Paceyapıcısı izleme kaynaklarını askıya alır ve ardından normal DB2 yönetim komutlarını kullanabilirsiniz.


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer'ı yapılandırma
Azure Load Balancer yapılandırmak için, [Azure Standart Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) 'sunu kullanmanızı ve ardından şunları yapmanızı öneririz.

1. Ön uç IP havuzu oluşturun:

   a. Azure portal, Azure Load Balancer açın, **ön uç IP havuzu**' nu seçin ve ardından **Ekle**' yi seçin.

   b. Yeni ön uç IP havuzunun adını girin (örneğin, **DB2-bağlantı**).

   c. **Atamayı** **statik**olarak AYARLAYıN ve başlangıçta tanımlanan IP adresini **sanal IP** adresi olarak girin.

   d. **Tamam**’ı seçin.

   e. Yeni ön uç IP havuzu oluşturulduktan sonra, havuzun IP adresini aklınızda edin.

1. Arka uç havuzu oluşturma:

   a. Azure portal, Azure Load Balancer açın, **arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

   b. Yeni arka uç havuzunun adını girin (örneğin, **DB2 arka ucu**).

   c. **Sanal makine Ekle**' yi seçin.

   d. Önceki adımda oluşturulan kullanılabilirlik kümesini veya IBM DB2 veritabanını barındıran sanal makineleri seçin.

   e. IBM DB2 kümesinin sanal makinelerini seçin.

   f. **Tamam**’ı seçin.

1. Bir sistem durumu araştırması oluşturun:

   a. Azure portal, Azure Load Balancer açın, **sistem durumu araştırmaları**' nı seçin ve **Ekle**' yi seçin.

   b. Yeni sistem durumu araştırmasının adını girin (örneğin, **DB2-HP**).

   c. Protokol ve bağlantı noktası **62500**olarak **TCP** ' yi seçin. **Aralık** değerini **5**olarak ayarlayın ve **sağlıksız eşik** değerini **2**olarak ayarlayın.

   d. **Tamam**’ı seçin.

1. Yük Dengeleme kurallarını oluşturun:

   a. Azure portal, Azure Load Balancer açın, **Yük Dengeleme kuralları**' nı seçin ve ardından **Ekle**' yi seçin.

   b. Yeni Load Balancer kuralının adını girin (örneğin, **DB2-SID**).

   c. Ön uç IP adresini, arka uç havuzunu ve daha önce oluşturduğunuz sistem durumu araştırmasını (örneğin, **DB2-ön uç**) seçin.

   d. **Protokolü** **TCP**olarak ayarlayın ve bağlantı noktası *veritabanı iletişim bağlantı noktasını*girin.

   e. **Boşta kalma zaman aşımını** 30 dakikaya yükseltin.

   f. **Kayan IP**'yi etkinleştirdiğinizden emin olun.

   g. **Tamam**’ı seçin.

**[A]** araştırma bağlantı noktası için güvenlik duvarı kuralı ekle:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Bağlantı için sanal IP 'yi kullanmak üzere SAP profillerinde değişiklikler yapın
HADR yapılandırmasının birincil örneğine bağlanmak için, SAP uygulama katmanının Azure Load Balancer için tanımladığınız ve yapılandırdığınız sanal IP adresini kullanması gerekir. Aşağıdaki değişiklikler gereklidir:

/sapmnt/\<SID >/profil/default. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID >/Global/DB6/Db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Birincil ve iletişim kutusu uygulama sunucularını yükler

Birincil ve iletişim uygulama sunucularını bir DB2 HADR yapılandırmasına karşı yüklediğinizde, yapılandırma için seçtiğiniz sanal ana bilgisayar adını kullanın. 

DB2 HADR yapılandırmasını oluşturmadan önce yüklemeyi gerçekleştirdiyseniz, önceki bölümde açıklandığı gibi ve SAP Java yığınları için aşağıdaki gibi değişiklikleri yapın.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java ya da Java Stack Systems JDBC URL denetimi

JDBC URL 'sini denetlemek veya güncelleştirmek için J2EE yapılandırma aracını kullanın. J2EE yapılandırma aracı bir grafik araç olduğundan, X sunucusunun yüklü olması gerekir:
 
1. J2EE örneğinin birincil uygulama sunucusunda oturum açın ve yürütün:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. Sol çerçevede **Güvenlik deposu**' nu seçin.
1. Sağ çerçevede, anahtarı `jdbc/pool/\<SAPSID>/url`seçin.
1. JDBC URL 'sindeki ana bilgisayar adını sanal ana bilgisayar adıyla değiştirin.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. **Add (Ekle)** seçeneğini belirleyin.
1. Değişikliklerinizi kaydetmek için sol üst köşedeki disk simgesini seçin.
1. Yapılandırma aracını kapatın.
1. Java örneğini yeniden başlatın.

## <a name="configure-log-archiving-for-hadr-setup"></a>HADR kurulumu için Günlük arşivlemeyi yapılandırma
HADR kurulumu için DB2 Günlük arşivlemeyi yapılandırmak üzere, hem birincil hem de bekleme veritabanını tüm günlük Arşivi konumlarından otomatik günlük alma özelliğine sahip olacak şekilde yapılandırmanızı öneririz. Hem birincil hem de bekleme veritabanının günlük arşivi dosyalarını, veritabanı örneklerinden birinin günlük dosyalarını arşivleyebileceğiniz tüm günlük Arşivi konumlarından alabilmesi gerekir. 

Günlük arşivleme yalnızca birincil veritabanı tarafından gerçekleştirilir. Veritabanı sunucularının HADR rollerini değiştirirseniz veya bir hata oluşursa, yeni birincil veritabanı, günlük arşivlemeden sorumludur. Birden çok günlük arşiv konumu ayarladıysanız günlüklerinizin iki kez arşivlenmesi gerekebilir. Yerel veya uzak bir catch durumunda, arşivlenmiş günlükleri eski birincil sunucudan yeni birincil sunucunun etkin günlük konumuna el ile kopyalamanız de gerekebilir.

Günlüklerin her iki düğümden de yazıldığı ortak bir NFS paylaşımının veya GlusterFS yapılandırılmasını öneririz. NFS paylaşımının veya GlusterFS, yüksek oranda kullanılabilir olmalıdır. 

Aktarımlar için mevcut olan yüksek oranda kullanılabilir NFS paylaşımlarını veya GlusterFS 'yi veya bir profil dizini kullanabilirsiniz. Daha fazla bilgi için bkz.

- [SAP NetWeaver için Red Hat Enterprise Linux’taki Azure VM’leri üzerinde GLusterFS][glusterfs] 
- [SAP uygulamaları için Azure NetApp Files Red Hat Enterprise Linux üzerindeki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik][anf-rhel]
- [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (NFS paylaşımları oluşturmak için)

## <a name="test-the-cluster-setup"></a>Küme kurulumunu test etme

Bu bölüm, DB2 HADR kurulumunuzu nasıl test kullanabileceğinizi açıklar. Her test, *az-idb01* sanal makinesinde IBM DB2 birincil 'ın çalıştığını varsayar. Sudo ayrıcalıklarına veya köküne sahip kullanıcı (önerilmez) kullanılmalıdır.

Tüm test çalışmalarının ilk durumu burada açıklanmıştır: (crm_mon-r veya PC Status)

- **bilgisayar durumu** , yürütme sırasında pacemaker durumunun bir anlık görüntüsüdür 
- **crm_mon-r** , pacemaker durumunun sürekli çıkışı

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Bir SAP sistemindeki özgün durum, aşağıdaki görüntüde gösterildiği gibi, Işlem DBAKOKPIT > Yapılandırma > Genel Bakış ' da belgelenmiştir:

![Dbakokpit-geçiş öncesi](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM DB2 'nin test etme


> [!IMPORTANT] 
> Teste başlamadan önce şunları yaptığınızdan emin olun:
> * Pacemaker başarısız olan eylemlere sahip değil (bilgisayar durumu).
> * Hiçbir konum kısıtlaması yok (geçiş testinin sol üyesi ol)
> * IBM DB2 HADR eşitlemesi çalışıyor. Kullanıcı DB2\<SID 'si > denetle <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Aşağıdaki komutu yürüterek birincil DB2 veritabanını çalıştıran düğümü geçirin:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Geçiş yapıldıktan sonra, CRM durum çıktısı şöyle görünür:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Bir SAP sistemindeki özgün durum, aşağıdaki görüntüde gösterildiği gibi, Işlem DBAKOKPIT > Yapılandırma > Genel Bakış ' da belgelenmiştir:

![Dbakokpit-geçiş sonrası](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

"PCs kaynak taşıma" ile kaynak geçişi konum kısıtlamaları oluşturur. Bu durumda konum kısıtlamaları az-idb01 üzerinde IBM DB2 örneğinin çalıştırılmasını engellemektedir. Konum kısıtlamaları silinmediği takdirde kaynak yeniden başarısız olamaz.

Konum kısıtlama ve bekleme düğümü, az-idb01 tarihinde başlatılır.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
Ve küme durumu şu şekilde değişir:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![Dbakokpit-kaldırılan konum kısıtlama](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Kaynağı *az-idb01* 'e geri geçirin ve konum kısıtlamalarını temizleyin
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **bilgisayar kaynağı taşıma \<RES_NAME > <host>:** Konum kısıtlamaları oluşturur ve ele alınmasına neden olabilir
- **PC kaynağı Temizleme \<RES_NAME >** : Konum kısıtlamalarını temizler
- **bilgisayar kaynağı Temizleme \<RES_NAME >** : Kaynağın tüm hatalarını temizler

### <a name="test-a-manual-takeover"></a>El ile devralmayı test etme

*Az-idb01* node üzerinde Paceyapıcısı hizmetini durdurarak el ile devralmayı test edebilirsiniz:
<pre><code>systemctl stop pacemaker</code></pre>

durum *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Yük devretmeden sonra, *az-idb01*tarihinde hizmeti yeniden başlatabilirsiniz.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>HADR birincil veritabanını çalıştıran düğümde DB2 işlemini sonlandırın

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

DB2 örneği başarısız oluyor ve Paceyapıcısı ana düğümü ve raporu şu durum olarak taşıyacaktır:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Paceyapıcısı, DB2 birincil veritabanı örneğini aynı düğümde yeniden başlatacak veya ikincil veritabanı örneğini çalıştıran düğüme yük devreder ve bir hata bildirilir.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>İkincil veritabanı örneğini çalıştıran düğümdeki DB2 işlemini Sonlandır

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Düğüm, hatalı bir şekilde ifade edildi ve bildirildi
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

DB2 örneği, daha önce atandığı ikincil rolde yeniden başlatılır.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR birincil veritabanı örneğini çalıştıran düğümde db2stop zorlamalı aracılığıyla DB 'yi durdur

As user DB2\<SID > Execute Command db2stop zorlama:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Hata tespit edildi:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

DB2 HADR ikincil veritabanı örneği, birincil role yükseltildi.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>HADR birincil veritabanı örneğini çalıştıran VM 'yi "Durdur" ile çökme

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

Böyle bir durumda, Paceyapıcısı, birincil veritabanı örneğini çalıştıran düğümün yanıt vermediğini algılar.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

Sonraki adım *bölünmüş* bir beyinme durumu olup olmadığını denetlemedir. Kalan düğüm, birincil veritabanı örneğini son çalıştıran düğümün çalıştığını belirledikten sonra, kaynakların yük devretmesi yürütülür.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


Bir çekirdek Panic olayında, başarısız olan düğüm, uçuşlama Aracısı tarafından yeniden başlatılır. Hatalı düğüm yeniden çevrimiçi olduktan sonra, şu şekilde paceoluşturucu kümesini başlatmanız gerekir
<pre><code>sudo pcs cluster start</code></pre> Bu, DB2 örneğini ikincil role başlatır.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Sonraki adımlar
- [SAP NetWeaver için yüksek kullanılabilirliğe sahip mimari ve senaryolar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama][rhel-pcs-azr]
