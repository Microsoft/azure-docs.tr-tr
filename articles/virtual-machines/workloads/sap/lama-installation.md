---
title: Azure için SAP 'yi kısıtlama Bağlayıcısı | Microsoft Docs
description: SAP sistemlerini, SAP 'yi kullanarak Azure 'da yönetme
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 6521c139463bb0de1e24783bbbdd6a2d3996be6f
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430098"
---
# <a name="sap-lama-connector-for-azure"></a>Azure için SAP LaMa bağlayıcısı

[1877727]: https://launchpad.support.sap.com/#/notes/1877727
[2343511]: https://launchpad.support.sap.com/#/notes/2343511
[2350235]: https://launchpad.support.sap.com/#/notes/2350235
[2562184]: https://launchpad.support.sap.com/#/notes/2562184
[2628497]: https://launchpad.support.sap.com/#/notes/2628497
[2445033]: https://launchpad.support.sap.com/#/notes/2445033
[2815988]: https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Genel destek beyanı: SAP veya Azure Bağlayıcısı için desteğe ihtiyacınız varsa lütfen her zaman bileşen BC-VCM-LVM-HYPERV üzerinde SAP ile bir olay açın.

SAP 'yi almak, birçok müşteri tarafından SAP Yatayı çalıştırmak ve izlemek için kullanılır. SAP 3,0 SP05, varsayılan olarak Azure 'a bir bağlayıcı ile birlikte gönderilir. Bu bağlayıcıyı, sanal makineleri serbest bırakmak ve başlatmak, yönetilen diskleri kopyalamak ve yeniden konumlandırmak ve yönetilen diskleri silmek için kullanabilirsiniz. Bu temel işlemlerle, SAP sistemlerini, SAP 'yi kullanarak yeniden konumlandırmak, kopyalamak, klonlayabilir ve yenileyebilirsiniz.

Bu kılavuzda, SAP 'yi almak için Azure bağlayıcısını ayarlama, uyarlamalı SAP sistemlerini yüklemek için kullanılabilecek sanal makineler oluşturma ve bunların nasıl yapılandırılacağı açıklanmaktadır.

> [!NOTE]
> Bağlayıcı yalnızca SAP-Enterprise sürümünde kullanılabilir

## <a name="resources"></a>Kaynaklar

Aşağıdaki SAP notları, Azure 'da SAP 'nin konusu ile ilgilidir:

| Dekont numarası | Başlık |
| --- | --- |
| [2343511] |SAP manzara yönetimi için Microsoft Azure Bağlayıcısı (bir) |
| [2350235] |SAP yatay yönetimi 3,0-Enterprise Edition |

Ayrıca, [SAP 'Yi almak Için SAP yardım portalını](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE)okuyun.

## <a name="general-remarks"></a>Genel açıklamalar

* Kurulum-> ayarları-> altyapısında *otomatik bağlama noktası oluşturmayı* etkinleştirdiğinizden emin olun  
  SAP, bir sanal makinede SAP Uyarlamalı uzantıları kullanarak birimleri takar, bu ayar etkinleştirilmemişse bağlama noktası mevcut olmalıdır.

* Farklı alt ağ kullan ve yeni VM 'Ler dağıtıldığında "çalmasını" IP adresini önlemek için dinamik IP adresleri kullanmayın ve SAP örnekleri hazırlanmamışsa  
  Bu alt ağda dinamik IP adresi ayırma kullanıyorsanız, bu da SAP 'nin ele alındığı, SAP sisteminin yük devri ile hazırlanması başarısız olabilir. Bir SAP sistemi hazırlanmamışsa, IP adresleri ayrılmamıştır ve diğer sanal makinelere tahsis edilebilir.

* Yönetilen konaklarda oturum açarsanız, dosya sistemlerinin çıkarılmasını engellemediğinizden emin olun  
  Bir Linux sanal makinelerinde oturum açıp çalışma dizinini bağlama noktasındaki bir dizin olarak değiştirirseniz (örneğin,/usr/sap/AH1/ASCS00/exe), birim kaldırılamaz ve bir yeniden konumlandırma ya da hazırlama başarısız olur.

## <a name="set-up-azure-connector-for-sap-lama"></a>SAP için Azure bağlayıcısını ayarlama

Azure Bağlayıcısı, SAP 'yi 3,0 SP05 olarak sevk edilir. SAP 'yi 3,0 için en son destek paketini ve düzeltme ekini yüklemenizi öneririz. Azure Bağlayıcısı Microsoft Azure karşı yetkilendirmek için bir hizmet sorumlusu kullanır. SAP yatay yönetimi (bir hizmet sorumlusu) oluşturmak için bu adımları izleyin.

1. Şuraya gidin: https://portal.azure.com
1. Azure Active Directory dikey penceresini açın
1. Uygulama kayıtları tıklayın
1. Ekle 'ye tıklayın
1. Bir ad girin, uygulama türü "Web uygulaması/API" yi seçin, bir oturum açma URL 'SI girin (örneğin, http: \//localhost) ve Oluştur ' a tıklayın.
1. Oturum açma URL 'SI kullanılmıyor ve geçerli bir URL olabilir
1. Yeni uygulamayı seçin ve Ayarlar sekmesinde anahtarlar ' a tıklayın.
1. Yeni anahtar için bir açıklama girin, "süresiz Expires" seçeneğini belirleyin ve Kaydet ' e tıklayın.
1. Değeri yazın. Hizmet sorumlusu için parola olarak kullanılır
1. Uygulama KIMLIĞINI yazın. Hizmet sorumlusunun Kullanıcı adı olarak kullanılır

Hizmet sorumlusu, varsayılan olarak Azure kaynaklarınıza erişme iznine sahip değildir. Onlara erişim sağlamak için hizmet sorumlusu izinlerine sahip olmanız gerekir.

1. Şuraya gidin: https://portal.azure.com
1. Kaynak grupları dikey penceresini açın
1. Kullanmak istediğiniz kaynak grubunu seçin
1. Erişim denetimi (ıAM) öğesine tıklayın
1. Rol ataması Ekle ' ye tıklayın
1. Rol katılımcısı seçin
1. Yukarıda oluşturduğunuz uygulamanın adını girin
1. Kaydet’e tıklayın.
1. SAP 'de kullanmak istediğiniz tüm kaynak grupları için 3 ile 8 arasındaki adımları yineleyin

SAP bir Web sitesini açın ve altyapıya gidin. Sekme bulut yöneticileri ' ne gidin ve Ekle ' ye tıklayın. Microsoft Azure Bulut Bağdaştırıcısı seçin ve Ileri ' ye tıklayın. Aşağıdaki bilgileri girin:

* Etiket: bağlayıcı örneği için bir ad seçin
* Kullanıcı adı: hizmet sorumlusu uygulama KIMLIĞI
* Parola: hizmet sorumlusu anahtarı/parolası
* URL: varsayılan @no__t tut-0
* İzleme aralığı (saniye): en az 300 olmalıdır
* Abonelik KIMLIĞI: Azure abonelik KIMLIĞI
* Azure Active Directory kiracı KIMLIĞI: Active Directory kiracının KIMLIĞI
* Proxy Konağı: SAP 'nin internet 'e bağlanması için bir proxy olması gerekiyorsa, proxy 'nin ana bilgisayar adı
* Proxy bağlantı noktası: proxy 'nin TCP bağlantı noktası

Girişinizi doğrulamak için test yapılandırması ' na tıklayın. Şunu görmeniz gerekir:

Bağlantı başarılı: Microsoft bulut bağlantısı başarılı oldu. 7 kaynak grubu bulundu (yalnızca 10 grup istendi)

Web sitesinin en altında.

## <a name="provision-a-new-adaptive-sap-system"></a>Yeni bir uyarlamalı SAP sistemi sağlama

Yeni bir sanal makineyi el ile dağıtabilir veya [hızlı başlangıç deposundaki](https://github.com/Azure/azure-quickstart-templates)Azure şablonlarından birini kullanabilirsiniz. [SAP NetWeaver Ass](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver uygulama sunucuları](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)ve [veritabanı](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database)için şablonlar içerir. Ayrıca, bu şablonları bir sistem kopyasının/kopyasının bir parçası olarak yeni konaklar sağlamak için de kullanabilirsiniz.

SAP 'yi kullanarak yönetmek istediğiniz tüm sanal makineler için ayrı bir alt ağ kullanmanızı ve yeni sanal makineler ve SAP örneklerinin hazırlanmasından sonra "çalmasını" IP adresini önlemek için dinamik IP adreslerini kullanmayı öneririz.

> [!NOTE]
> Mümkünse, sanal makineden disk ayırmak için uzun çalışma zamanlarının oluşmasına neden olabilecek tüm sanal makine uzantılarını kaldırın.

Kullanıcı \<hanasıd > adm, \<sapsıd > adm ve grup sapsys 'in aynı KIMLIĞE ve GID 'ye sahip hedef makinede bulunduğundan emin olun veya LDAP kullanın. SAP NetWeaver (A) SCS 'yi çalıştırmak için kullanılması gereken sanal makinelerde NFS Sunucusunu etkinleştirin ve başlatın.

### <a name="manual-deployment"></a>El ile dağıtım

SAP, SAP ana bilgisayar Aracısı kullanılarak sanal makine ile iletişim kurar. Sanal makineleri el ile dağıtır veya hızlı başlangıç deposundan Azure Resource Manager şablonunu kullanmıyorsanız, en son SAP konak aracısını ve SAP Uyarlamalı uzantılarını yüklediğinizden emin olun. Azure için gereken düzeltme eki düzeyleri hakkında daha fazla bilgi için bkz. SAP Note [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Bir Linux sanal makinesinin el ile dağıtılması

SAP Note [2343511]' de listelenen desteklenen işletim sistemlerinden birine sahip yeni bir sanal makine oluşturun. SAP örnekleri için ek IP yapılandırması ekleyin. Her örnek için en az IP adresi gerekir ve bir sanal ana bilgisayar adı kullanılarak yüklenmelidir.

SAP NetWeaver ASCS örneği/sapmnt/\<SAPSıD >,/usr/SAP/\<SAPSıD >,/usr/SAP/Trans ve/usr/SAP/\<sapsıd > adm için disklere ihtiyaç duyuyor. SAP NetWeaver uygulama sunucularında ek disklere gerek yoktur. SAP örneğiyle ilgili her şey, Ass 'de depolanmalıdır ve NFS aracılığıyla aktarmalıdır. Aksi takdirde, şu anda SAP 'yi kullanarak ek uygulama sunucuları eklemek mümkün değildir.

![Linux üzerinde SAP NetWeaver YOKLARı](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>SAP HANA için el ile dağıtım

SAP HANA için desteklenen işletim sistemlerinden birine sahip yeni bir sanal makine oluşturun, SAP Note [2343511]' de listelenmiştir. SAP HANA için bir ek IP yapılandırması ve her bir HANA kiracısı için bir tane ekleyin.

SAP HANA/Hana/Shared,/Hana/Backup,/Hana/Data ve/Hana/log için disklere ihtiyaç duyuyor

![Linux üzerinde SAP HANA](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Linux üzerinde Oracle Database için el ile dağıtım

SAP Note [2343511]bölümünde listelendiği gibi Oracle veritabanları için desteklenen işletim sistemlerinden birine sahip yeni bir sanal makine oluşturun. Oracle veritabanı için bir ek IP yapılandırması ekleyin.

Oracle veritabanı/Oracle,/Home/oraod1 ve/Home/Oracle için disklere ihtiyaç duyuyor

![Linux 'ta Oracle veritabanı](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Microsoft SQL Server için el ile dağıtım

Microsoft SQL Server için desteklenen işletim sistemlerinden birine sahip yeni bir sanal makine oluşturun, SAP Note [2343511]' de listelenmiştir. SQL Server örneği için bir ek IP yapılandırması ekleyin.

SQL Server veritabanı sunucusu, c:\usr\sap. için veritabanı verilerine ve günlük dosyalarına ve disklere yönelik disklere ihtiyaç duyuyor.

![Linux 'ta Oracle veritabanı](media/lama/sap-lama-db-sql.png)

SAP NetWeaver uygulama sunucusunu bir sistem kopyası/kopyalama hedefi olarak veya olarak değiştirmek için kullanmak istediğiniz bir sanal makineye SQL Server için desteklenen bir Microsoft ODBC sürücüsü yüklediğinizden emin olun.

SAP, bir veritabanı örneğini yeniden konumlandırmak için kullanmak istediğiniz bir sanal makineye veya bir sistem kopyalama/Kopyalama hedefi ihtiyaçlarına SQL Server önceden yüklenmiş olarak, SQL Server kendisini yeniden konumlandıramaz.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Azure şablonu kullanarak sanal makine dağıtma

Sanal makinelerin işletim sistemi için [SAP Software Market](https://support.sap.com/swdc) 'ten aşağıdaki en son mevcut arşivleri indirin:

1. SAPCAR 7,21
1. SAP ANA BILGISAYAR ARACıSı 7,21
1. SAP UYARLAMALı UZANTı 1,0 EXT

Ayrıca, [Microsoft Indirme merkezi](https://www.microsoft.com/download) ' nden aşağıdaki bileşenleri indirin

1. Microsoft Visual C++ 2010 yeniden dağıtılabilir paketi (x64) (yalnızca Windows)
1. SQL Server için Microsoft ODBC Sürücüsü (yalnızca SQL Server)

Şablonu dağıtmak için bileşenlerin olması gerekir. Bunları şablon için kullanılabilir yapmanın en kolay yolu, bunları bir Azure depolama hesabına yüklemek ve paylaşılan erişim Imzası (SAS) oluşturmaktır.

Şablonlar aşağıdaki parametrelere sahiptir:

* Sapsystemıd: SAP sistem KIMLIĞI. Disk düzeni oluşturmak için kullanılır (örneğin,/usr/SAP/\<sapsıd >).

* computerName: yeni sanal makinenin bilgisayar adı. Bu parametre Ayrıca, SAP tarafından kullanılır. Bu şablonu, bir sistem kopyasının parçası olarak yeni bir sanal makine sağlamak için kullandığınızda, bu bilgisayar adına sahip ana bilgisayara ulaşılana kadar SAP 'nin kullanılmasına izin bekler.

* osType: dağıtmak istediğiniz işletim sisteminin türü.

* DbType: veritabanının türü. Bu parametre, kaç tane ek IP yapılandırmasının eklenmesi gerektiğini ve disk düzeninin nasıl görüneceğini belirlemede kullanılır.

* sapSystemSize: dağıtmak istediğiniz SAP sisteminin boyutu. Sanal makine örneği türünü ve boyutunu tespit etmek için kullanılır.

* adminUsername: sanal makine için Kullanıcı adı.

* adminPassword: sanal makine için parola. SSH için ortak anahtar da sağlayabilirsiniz.

* sshKeyData: sanal makineler için genel SSH anahtarı. Yalnızca Linux işletim sistemleri için desteklenir.

* SubnetID: kullanmak istediğiniz alt ağın KIMLIĞI.

* deployEmptyTarget: sanal makineyi bir örnek yeniden konumlandırma veya buna benzer bir hedef olarak kullanmak istiyorsanız boş bir hedef dağıtabilirsiniz. Bu durumda, ek disk veya IP yapılandırması eklenmez.

* sapcarLocation:, dağıttığınız işletim sistemiyle eşleşen sapcar uygulamasının konumu. sapcar, diğer parametrelerde sağladığınız arşivleri ayıklamak için kullanılır.

* sapHostAgentArchiveLocation: SAP konak Aracısı Arşivi konumu. SAP konak Aracısı, bu şablon dağıtımının bir parçası olarak dağıtılır.

* sapacExtLocation: SAP Uyarlamalı uzantılarının konumu. SAP Note [2343511] , Azure için gereken en düşük düzeltme eki düzeyini listeler.

* vcRedistLocation: SAP Uyarlamalı uzantılarını yüklemek için gereken VC çalışma zamanının konumu. Bu parametre yalnızca Windows için gereklidir.

* odbcDriverLocation: yüklemek istediğiniz ODBC sürücüsünün konumu. Yalnızca SQL Server için Microsoft ODBC sürücüsü desteklenir.

* sapadmPassword: sapadm kullanıcısının parolası.

* sapadmId: sapadm kullanıcısının Linux Kullanıcı KIMLIĞI. Windows için gerekli değildir.

* Sapsysgıd: sapsys grubunun Linux Grup KIMLIĞI. Windows için gerekli değildir.

* _artifactsLocation: Bu şablon için gereken yapıtların bulunduğu temel URI. Şablon, eşlik eden betikler kullanılarak dağıtıldığında, abonelikte bir özel konum kullanılacaktır ve bu değer otomatik olarak oluşturulur. Şablonu yalnızca GitHub 'dan dağıtmayın.

* _artifactsLocationSasToken: _artifactsLocation öğesine erişmek için gereken sasToken. Şablon, eşlik eden betikler kullanılarak dağıtıldığında, otomatik olarak bir sasToken oluşturulur. Şablonu yalnızca GitHub 'dan dağıtmayın.

### <a name="sap-hana"></a>SAP HANA

Aşağıdaki örneklerde, System ID HN1 ve SAP NetWeaver System with System ID AH1 ile SAP HANA yükletireceğiz varsayılmaktadır. Sanal ana bilgisayar adları, hn1-DB, SAP NetWeaver sistemi tarafından kullanılan HANA kiracısı için AH1-DB, SAP NetWeaver ASCS ve AH1-dı-0 için ilk SAP NetWeaver uygulama sunucusu için AH1-ascs ' dir.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Azure yönetilen diskleri kullanarak SAP HANA için SAP NetWeaver YOKLARı 'nı yükler

SAP yazılım sağlama Yöneticisi 'Ni (SWPM) başlatabilmeniz için, yoks 'nin sanal ana bilgisayar adının IP adresini bağlamanız gerekir. Önerilen yol, sapacext kullanmaktır. IP adresini sapacext kullanarak bağlarsanız, yeniden başlatmadan sonra IP adresini kaldırdığınızdan emin olun.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

SPM 'yi çalıştırın ve *ascs örnek ana bilgisayar adı*için *AH1-ascs* kullanın.

![Linux @ no__t-1 Linux  
Aşağıdaki profil parametresini/usr/SAP/hostctrl/exe/host_profile. adresinde bulunan SAP konak Aracısı profiline ekleyin Daha fazla bilgi için bkz. SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Azure NetAppFiles (ANF) BETA SAP HANA için SAP NetWeaver yoks 'yi yükler

> [!NOTE]
> Bu işlevsellik daha sonra veya GA 'dir. Daha fazla bilgi için SAP Note [2815988] (yalnızca Preview müşterilerine görünür) bölümüne bakın.
Bir SAP olayını bileşen BC-VCM-LVM-HYPERV ' de açın ve Azure NetApp Files önizlemesi için yönetim depolama bağdaştırıcısına katma isteği yapın

ANF, Azure için NFS sağlar. SAP 'nin bu bağlamda, ABAP Merkezi Hizmetleri (ASCS) örneklerinin ve sonraki uygulama sunucuları yüklemesinin oluşturulmasını basitleştirir. Daha önce ASCS örneğinin de NFS sunucusu olarak davranması gerekiyordu ve acosprep/nfs_paths parametresi, SAP Hostagent 'ın host_profile 'e eklenmesi gerekiyordu.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF Şu anda şu bölgelerde kullanılabilir:

Avustralya Doğu, Orta ABD, Doğu ABD, Doğu ABD 2, Kuzey Avrupa, Orta Güney ABD, Batı Avrupa ve Batı ABD 2.

#### <a name="network-requirements"></a>Ağ gereksinimleri

ANF, SAP sunucularıyla aynı VNET 'in bir parçası olması gereken Temsilcili bir alt ağ gerektirir. Bu tür bir yapılandırma için örnek verilmiştir.
Bu ekranda VNET 'in ve ilk alt ağın oluşturulması gösterilmektedir:

![Azure ANF için sanal ağ oluşturma SAP ](media/lama/sap-lama-createvn-50.png)

Sonraki adımda, Microsoft. NetApp/birimleri için Temsilcili alt ağ oluşturulur.

![SAP 'yi temsil eden alt ağ ekle ](media/lama/sap-lama-addsubnet-50.png)

![Alt ağların SAP 'yi Lama listesi ](media/lama/sap-lama-subnets.png)

Artık Azure portal içinde bir NetApp hesabının oluşturulması gerekir:

![SAP 'yi oluşturma NetApp hesabı oluştur ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP 'nin NetApp hesabı oluşturuldu ](media/lama/sap-lama-netappaccount.png)

NetApp hesabında, kapasite havuzu her havuzun disk boyutunu ve türünü belirtir:

![SAP 'yi oluşturma NetApp kapasite Havuzu Oluştur ](media/lama/sap-lama-capacitypool-50.png)

![SAP 'nin NetApp kapasite havuzunu oluşturma ](media/lama/sap-lama-capacitypool-list.png)

NFS birimleri artık tanımlanabilir. Tek bir havuzdaki birden fazla sistem için birimler olacağı için, kendi kendine açıklayan bir adlandırma düzeni seçilmelidir. SID ekleme ilgili birimleri birlikte gruplamak için yardımcı olur. YOKS ve AS örneği için aşağıdaki takmalar gereklidir: */sapmnt/\<SıD @ no__t-2*, */usr/SAP/\<sid @ no__t-5*ve */Home/\<sid @ no__t-8adm*. İsteğe bağlı olarak, */usr/SAP/Trans* , en azından bir yatay 'ın tüm sistemleri tarafından kullanılan merkezi aktarım dizini için gereklidir.

> [!NOTE]
> BETA aşamasında birimlerin adı, abonelik içinde benzersiz olmalıdır.

![SAP 'yi oluşturan birim oluşturma 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP 'yi oluşturma birim oluştur 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP 'yi oluşturma birim oluşturma 3 ](media/lama/sap-lama-createvolume3-80.png)

Bu adımların diğer birimler için de tekrarlanması gerekir.

![Oluşturulan birimlerin SAP 'yi Lama listesi ](media/lama/sap-lama-volumes.png)

Artık bu birimlerin SAP SWPM ile ilk yüklemesinin gerçekleştirileceği sistemlere bağlanması gerekir.

İlk olarak bağlama noktalarının oluşturulması gerekir. Bu durumda, SID AN1 olur, bu nedenle aşağıdaki komutların yürütülmesi gerekir:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Sonraki ANF birimleri aşağıdaki komutlarla birlikte bağlanacaktır:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Bağlama komutları portaldan da türetilebilir. Yerel bağlama noktalarının ayarlanması gerekir.

Doğrulamak için df-h komutunu kullanın.

![SAP yerleştirme noktaları işletim sistemi düzeyi ](media/lama/sap-lama-mounts.png)

Artık SWPM ile yükleme yapılmalıdır.

En az bir AS örneği için aynı adımların gerçekleştirilmesi gerekir.

Başarılı yüklemeden sonra sistem, SAP 'nin içinde keşfedilmiş olmalıdır.

Bağlama noktaları, yoks ve AS örneği için şöyle görünmelidir:

](media/lama/sap-lama-ascs.png)  ' de bağlama noktalarını (Bu bir örnektir). IP adresleri ve dışa aktarma yolu, daha önce kullanıldıklarından farklıdır)


#### <a name="install-sap-hana"></a>SAP HANA yükleme

SAP HANA, hdblcm komut satırı aracını kullanarak yüklerseniz, sanal konak adı sağlamak için--hostname parametresini kullanın. Veritabanının sanal ana bilgisayar adının IP adresini bir ağ arabirimine eklemeniz gerekir. Önerilen yol, sapacext kullanmaktır. IP adresini sapacext kullanarak bağlarsanız, yeniden başlatmadan sonra IP adresini kaldırdığınızdan emin olun.

HANA kiracısına bağlanmak için uygulama sunucuları tarafından kullanılan ad için başka bir sanal konak adı ve IP adresi ekleyin.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

HANA sanal makinesinde değil, uygulama sunucusu sanal makinesinde SWPM 'nin veritabanı örneği yüklemesini çalıştırın. *SAP sistemi için Iletişim veritabanı*'Ndaki *veritabanı ana bilgisayarı* için *AH1-DB* kullanın.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>SAP HANA için SAP NetWeaver uygulama sunucusunu yükler

SAP yazılım sağlama Yöneticisi 'Ni (SWPM) başlatabilmeniz için, uygulama sunucusunun sanal ana bilgisayar adının IP adresini bağlamanız gerekir. Önerilen yol, sapacext kullanmaktır. IP adresini sapacext kullanarak bağlarsanız, yeniden başlatmadan sonra IP adresini kaldırdığınızdan emin olun.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

HDB userStore 'da anahtarı bulmak için kullanılan kimliği ayarlamak için SAP NetWeaver profil parametresi DBS/HDB/hdb_use_ident kullanılması önerilir. Bu parametreyi SWPM ile veritabanı örneği yüklemesinden sonra el ile ekleyebilir veya ile SWPM 'yi çalıştırın

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

El ile ayarlarsanız, Yeni HDB userStore girdileri de oluşturmanız gerekir.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

İletişim kutusu *birincil uygulama sunucusu örneğindeki* *pas örneği konak adı* için *AH1-dı-0* kullanın.

#### <a name="post-installation-steps-for-sap-hana"></a>SAP HANA için yükleme sonrası adımlar

Kiracı kopyalama yapmayı denemeden önce SYSTEMDB ve tüm kiracı veritabanlarını yedeklediğinizden emin olun, kiracı taşıyın veya sistem çoğaltması oluşturun.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Aşağıdaki örneklerde, System ID AS1 ile SAP NetWeaver sistemini yüklediğinizi varsaydık. Sanal konak adları, SAP NetWeaver sistemi tarafından kullanılan SQL Server örneği için AS1-DB, SAP NetWeaver ASCS ve AS1-dı-0 için ilk SAP NetWeaver uygulama sunucusu için AS1-ascs.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>SQL Server için SAP NetWeaver YOKLARı 'nı yükler

SAP yazılım sağlama Yöneticisi 'Ni (SWPM) başlatabilmeniz için, yoks 'nin sanal ana bilgisayar adının IP adresini bağlamanız gerekir. Önerilen yol, sapacext kullanmaktır. IP adresini sapacext kullanarak bağlarsanız, yeniden başlatmadan sonra IP adresini kaldırdığınızdan emin olun.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

SPM 'yi çalıştırın ve *ascs örnek ana bilgisayar adı*için *AS1-ascs* kullanın.

#### <a name="install-sql-server"></a>SQL Server yüklensin

Veritabanının sanal ana bilgisayar adının IP adresini bir ağ arabirimine eklemeniz gerekir. Önerilen yol, sapacext kullanmaktır. IP adresini sapacext kullanarak bağlarsanız, yeniden başlatmadan sonra IP adresini kaldırdığınızdan emin olun.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

SQL Server sanal makinesinde SWPM veritabanı örneği yüklemesini çalıştırın. SQL Server bağlanmak için kullanılan ana bilgisayar adını geçersiz kılmak için SAPINST_USE_HOSTNAME =*AS1-DB* kullanın. Sanal makineyi Azure Resource Manager şablonu kullanarak dağıttıysanız, veritabanı veri dosyaları için kullanılan dizini *C:\sql\data* ve veritabanı günlük dosyası için *c:\SQL\LOG*olarak ayarladığınızdan emin olun.

*NT AUTHORITY\SYSTEM* kullanıcısının SQL Server erişimi olduğundan ve sunucu rolü *sysadmin*olduğundan emin olun. Daha fazla bilgi için bkz. SAP Note [1877727] ve [2562184].

#### <a name="install-sap-netweaver-application-server"></a>SAP NetWeaver uygulama sunucusunu yükler

SAP yazılım sağlama Yöneticisi 'Ni (SWPM) başlatabilmeniz için, uygulama sunucusunun sanal ana bilgisayar adının IP adresini bağlamanız gerekir. Önerilen yol, sapacext kullanmaktır. IP adresini sapacext kullanarak bağlarsanız, yeniden başlatmadan sonra IP adresini kaldırdığınızdan emin olun.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

İletişim kutusu *birincil uygulama sunucusu örneğindeki* *pas örneği konak adı* için *AS1-dı-0* kullanın.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="errors-and-warnings-during-discover"></a>Bulma sırasında hatalar ve uyarılar

* Seçme izni reddedildi
  * MICROSOFT [ODBC SQL Server sürücüsü] [SQL Server] ' Log_shipping_primary_databases ' nesnesi, ' msdb ' veritabanı, ' dbo ' şeması üzerinde SELECT izni reddedildi. [SOAPFaultException]  
  ' Log_shipping_primary_databases ' nesnesi, ' msdb ' veritabanı, ' dbo ' şeması üzerinde SELECT izni reddedildi.
  * Çözüm  
    *NT AUTHORITY\SYSTEM* 'ın SQL Server erişebildiğinizden emin olun. Bkz. SAP Note [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Örnek doğrulaması için hatalar ve uyarılar

* HDB Kullanıcı deposunun doğrulanması sırasında bir özel durum oluştu  
  * Günlük Görüntüleyicisine bakın  
    com. sap. NW. lm. aci. Monitor. API. Validation. RuntimeValidationException: KIMLIĞI ' RuntimeHDBConnectionValidator ' olan doğrulayıcının özel durumu (doğrulama: ' VALIDATION_HDB_USERSTORE '): hdbuserstore alınamadı  
    HANA userStore doğru konumda değil
  * Çözüm  
    /Usr/SAP/AH1/hdbclient/Install/ınstaltar .Ini öğesinin doğru olduğundan emin olun

### <a name="errors-and-warnings-during-a-system-copy"></a>Sistem kopyası sırasında hatalar ve uyarılar

* Sistem sağlama adımı doğrulanırken bir hata oluştu
  * Neden: com. sap. NW. lm. aci. Engine. Base. API. util. Exception. HAOperationException çağıran '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-0durum = 5 @ no__t-1port = 35013 PF =/usr/SAP/hostctrl/exe/Host_ profil-R-T dev_lvminfo-u SISTEM-p kanca-r ' | /usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-2 durum = 5 @ no__t-3port = 35013 PF =/usr/SAP/hostctrl/exe/host_profile-R-T dev_lvminfo-u SISTEM-p kanca-r
  * Çözüm  
    Kaynak HANA sistemindeki tüm veritabanlarının yedeklemesini al

* Veritabanı örneğinin sistem kopyalama adımı *başlangıcı*
  * ' 000D3A282BC91EE8A1D76CF1F92E2944 ' konak Aracısı Işlemi başarısız oldu (OperationException. FaultCode: ' 127 ', Ileti: ' komutunun yürütülmesi başarısız oldu. : [Microsoft] [ODBC SQL Server sürücüsü] [SQL Server] kullanıcının ' AS2 ' veritabanını değiştirme izni yok, veritabanı yok veya veritabanı erişim denetimlerine izin veren bir durumda değil. ')
  * Çözüm  
    *NT AUTHORITY\SYSTEM* 'ın SQL Server erişebildiğinizden emin olun. Bkz. SAP Note [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Sistem kopyası sırasında hatalar ve uyarılar

* Uygulama sunucusu ya da yoks adımındaki *zorlamalı yazmaç ve başlatma örneği aracısına* örnek Aracısı kaydettirilmeye çalışılırken hata oluştu
  * Örnek Aracısı kaydettirilmeye çalışılırken hata oluştu. (RemoteException: ', ' \\as1-ascs \ sapmnt \ AS1 \ SYS \ profile \ AS1_D00_as1-di-0 ' profilinden örnek veriler yüklenemedi: ' \\AS1-ascs \ sapmnt \ AS1 \ SYS \ profile \ AS1_D00_as1-di-0 ' profiline erişilemiyor: böyle bir dosya veya dizin yok. ')
  * Çözüm  
   ASCS/SCS 'deki sapmnt paylaşımının SAP_AS1_GlobalAdmin için tam erişime sahip olduğundan emin olun

* Adım, *kopyalama Için başlangıç korumasını etkinleştirme* hatası
  * ' @No__t-0as1-ascs \ sapmnt \ AS1 \ SYS \ profile \ AS1_D00_as1-di-0 ' adlı dosya açılamadı nedeni: böyle bir dosya veya dizin yok
  * Çözüm  
    Uygulama sunucusunun bilgisayar hesabının profile yazma erişimi olması gerekir

### <a name="errors-and-warnings-during-create-system-replication"></a>Sistem çoğaltması oluşturma sırasında hatalar ve uyarılar

* Sistem çoğaltması oluştur 'a tıklanmadaki özel durum
  * Neden: com. sap. NW. lm. aci. Engine. Base. API. util. Exception. HAOperationException çağıran '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-0durum = 5 @ no__t-1port = 35013 PF =/usr/SAP/hostctrl/exe/Host_ profil-R-T dev_lvminfo-u SISTEM-p kanca-r ' | /usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 @ no__t-2 durum = 5 @ no__t-3port = 35013 PF =/usr/SAP/hostctrl/exe/host_profile-R-T dev_lvminfo-u SISTEM-p kanca-r
  * Çözüm  
    Sapacext 'ın `<hanasid` > adm olarak yürütülebileceğini test edin

* Depolama adımında tam kopya etkin olmadığında hata
  * Itoesgecopydata. storageVolumeCopyList: 1 ve Field Targetstoragessystemıd için bağlam öznitelik iletisi raporlanırken bir hata oluştu
  * Çözüm  
    Adımdaki uyarıları yoksayın ve yeniden deneyin. Bu sorun, yeni bir destek paketinde/SAP 'nin düzeltme ekinde düzeltilecektir.

### <a name="errors-and-warnings-during-relocate"></a>Yeniden konumlandırma sırasında hatalar ve uyarılar

* NFS yeniden dışarı aktarmaları için '/usr/sap/AH1 ' yoluna izin verilmez.
  * Ayrıntılar için SAP Note [2628497] ' a bakın.
  * Çözüm  
    Ascs HostAgent profiline yoks dışarı aktarmaları ekleyin. Bkz. SAP Note [2628497]

* YOKS yerini alırken işlev uygulanmadı
  * Komut çıkışı: exportfs: Host:/usr/SAP/AX1: Işlev uygulanmadı
  * Çözüm  
    Hedef sanal makinede NFS Sunucusu hizmetinin etkinleştirildiğinden emin olun

### <a name="errors-and-warnings-during-application-server-installation"></a>Uygulama sunucusu yüklemesi sırasında hatalar ve uyarılar

* Sapınst adımını yürütme hatası: getProfileDir
  * Hata: (adım tarafından bildirilen son hata: modül çağrısında ESAPinstException yakalandı: ' | adım doğrulayıcısı NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | INSID | 0 | NW_readProfileDir | ind | ind | ind | ind | readProfile | 0 | Getprofiledır ' bir hata bildirdi: düğüm \\ \ as1-ascs\sapmnt\AS1\SYS\profile yok. Bu sorunu çözmek için Sapınst 'yi etkileşimli modda başlatın
  * Çözüm  
    SWPM 'nin profile erişimi olan bir kullanıcı ile çalıştığından emin olun. Bu Kullanıcı, uygulama sunucusu Yükleme Sihirbazı 'nda yapılandırılabilir

* Sapınst adımını yürütme hatası: askUnicode
  * Hata: (adım tarafından bildirilen son hata: modül çağrısında ESAPinstException yakalandı: ' | adım doğrulayıcısı NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | INSID | 0 | NW_getUnicode | ind | ind | ind | ind | UNICODE | 0 | Askunıcode ' bir hata bildirdi: Bu sorunu çözmek için Sapınst 'yi etkileşimli modda başlatın)
  * Çözüm  
    Son SAP çekirdeği kullanıyorsanız, SWPM, sistemin artık ASCS 'nin ileti sunucusunu kullanarak bir Unicode sistem olup olmadığını belirleyemez. Daha fazla bilgi için bkz. SAP Note [2445033] .  
    Bu sorun, yeni bir destek paketinde/SAP 'nin düzeltme ekinde düzeltilecektir.  
    Bu sorunu geçici olarak çözmek için SAP sisteminizin varsayılan profilinde OS_UNICODE = UC profil parametresini ayarlayın.

* Sapınst adımını yürütme hatası: dCheckGivenServer
  * Sapınst adımını yürütme hatası: dCheckGivenServer "Version =" 1.0 "hatası: (adım tarafından bildirilen son hata: \<p > Yükleme Kullanıcı tarafından iptal edildi. \</p >
  * Çözüm  
    SWPM 'nin profile erişimi olan bir kullanıcı ile çalıştığından emin olun. Bu Kullanıcı, uygulama sunucusu Yükleme Sihirbazı 'nda yapılandırılabilir

* Sapınst adımını yürütme hatası: checkClient
  * Sapınst adımını yürütme hatası: checkClient "Version =" 1.0 "hatası: (adım tarafından bildirilen son hata: \<p > Yükleme Kullanıcı tarafından iptal edildi. \</p >)
  * Çözüm  
    SQL Server için Microsoft ODBC sürücüsünün uygulama sunucusunu yüklemek istediğiniz sanal makinede yüklü olduğundan emin olun

* Sapınst adımını yürütme hatası: copyScripts
  * Adım tarafından bildirilen son hata: sistem çağrısı başarısız oldu. Ayrıntılar: parametre (\\ \ AS1-ascs/sapmnt/AS1/SYS/exe/UC/NTAMD64/strdbs) ile ' fopenU ' sistem çağrısını yürütmede hata 13 (0x0000000d) (Izin reddedildi). cmd, w), (\ bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/Impl/src/) dosyasında satır (494) syslib/FileSystem/syxxcfstrm2. cpp), yığın izleme:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  Csiservıceset. cpp: 63: Csiservıceset:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  Jsextenma. hpp: 1136: CallFunctionBase:: Call ()  
  ıaxxcfile. cpp: 183: ıastring Cıaosfileconnect:: callMemberFunction (ıastring const & Name, args_t const & args)  
  ıaxxcfile. cpp: 1849: ıastring Cıaosfileconnect:: newFileStream (args_t const & _Args)  
  ıaxxbdosya. cpp: 773: Cıaosfile:: newFileStream_impl (4)  
  syxxcfile. cpp: 233: Csyfileımpl:: openStream (ırivfile:: eFileOpenMode)  
  syxxcfstrı. cpp: 29: Csyfilestreamımpl:: Csyfilestreamımpl (CSyFileStream *, ıastring, ırivfile:: eFileOpenMode)  
  syxxcfstrd. cpp: 265: Csyfilestreamımpl:: Open ()  
  syxxcfstrm2. cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\ \ AW1-ascs/sapmnt/AW1/SYS/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2. cpp: 456: CSyFileStream2Impl:: Open ()
  * Çözüm  
    SWPM 'nin profile erişimi olan bir kullanıcı ile çalıştığından emin olun. Bu Kullanıcı, uygulama sunucusu Yükleme Sihirbazı 'nda yapılandırılabilir

* Sapınst adımını yürütme hatası: Askşifreler
  * Adım tarafından bildirilen son hata: sistem çağrısı başarısız oldu. Ayrıntılar: hata 5 (0x00000005) (erişim reddedildi.) ' NetValidatePasswordPolicy ' sistem çağrısının (...), dosyadaki (\ bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/Impl/src/syslib/Account/synxcaccmg. cpp) (\ 359), yığın izleme:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  Csiservıceset. cpp: 63: Csiservıceset:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  Jsextenma. hpp: 1136: CallFunctionBase:: Call ()  
  CSiStepExecute. cpp: 764: CSiStepExecute:: ınvokedialog ()  
  Koyu Modeguiengine. cpp: 56: koyu Modeguiengine:: showDialogCalledByJs ()  
  Koyu Modedialog. cpp: 85: koyu Modedialog:: gönder ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  Jsextenma. hpp: 1136: CallFunctionBase:: Call ()  
  ıaxxcaccount. cpp: 107: ıastring Cıaosaccountconnect:: callMemberFunction (ıastring const & Name, args_t const & args)  
  ıaxxcaccount. cpp: 1186: ıastring Cıaosaccountconnect:: validatePasswordPolicy (args_t const & _Args)  
  ıaxxbaccount. cpp: 430: Cıaosaccount:: validatePasswordPolicy_impl ()  
  synxcaccmg. cpp: 297: ısyaccountynt::P asswordValidationMessage Csyaccountmgtımpl:: validatePasswordPolicy (saponazure, * * * * *) const)
  * Çözüm  
    VM 'den etki alanı denetleyicisine iletişime izin vermek için adım *yalıtımına* bir konak kuralı eklediğinizden emin olun

## <a name="next-steps"></a>Sonraki adımlar
* [Azure işlemlerinde SAP HANA kılavuzu][hana-ops-guide]
* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
