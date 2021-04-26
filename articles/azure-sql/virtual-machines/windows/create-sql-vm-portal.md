---
title: Azure portal bir Windows sanal makinesi sağlayın
description: Bu kılavuz, bir Windows sanal makinesinde SQL Server sağlamak için Azure portal kullanımına yönelik seçenekleri içerir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f0b7e04becd42180df3393d7036294c1629cdc21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499501"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>SQL Server ile bir Windows sanal makinesi sağlamak için Azure portal kullanma

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu kılavuzda, bir Windows sanal makinesinde (VM) SQL Server sağlamak üzere Azure portal kullanımı için kullanılabilen seçenekler ele alınmaktadır. Bu makalede, tek bir yapılandırmaya daha fazla odaklanmakta olan [SQL Server VM hızlı](sql-vm-create-portal-quickstart.md)başlangıçtan daha fazla yapılandırma seçeneği ele alınmaktadır. 

Kendi SQL Server VM oluşturmak için bu kılavuzu kullanın. Ya da bunu, Azure portal kullanılabilen seçeneklere yönelik bir başvuru olarak kullanın.

> [!TIP]
> SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> SQL Server sanal makine galeri görüntüleri

SQL Server bir sanal makine oluşturduğunuzda, sanal makine galerisindeki önceden yapılandırılmış birkaç görüntüden birini seçebilirsiniz. Aşağıdaki adımlarda SQL Server 2017 görüntülerinden birini seçme gösterilmektedir.

1. Azure portal sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna *Azure SQL* yazın. 

   Ayrıca, **Azure SQL** ' in yanındaki yıldızı, sık kullanılan olarak kaydetmek ve sol gezinti bölmesinde bir öğe olarak eklemek için de seçebilirsiniz. 

1. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **Ayrıntıları göster**' i seçerek ek bilgileri görüntüleyebilirsiniz. 
1. **SQL sanal makineler** kutucuğunda SQL Server resim arama kutusuna *2017* yazın ve ardından açılır listeden **Ücretsiz SQL Server Lisans: Windows Server 2016 üzerinde SQL Server 2017 Developer** ' ı seçin. 

   ![SQL VM görüntüsünü seçin](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > Geliştirme testi için SQL Server tam özellikli, ücretsiz bir sürümü olduğundan, bu makalede geliştirici sürümü kullanılır. Yalnızca çalışan VM'ler için ücret ödersiniz. Ancak, bu kılavuzda kullanılacak görüntülerden herhangi birini seçebilirsiniz. Kullanılabilir görüntülerin açıklaması için [SQL Server Windows sanal makineleri genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)bölümüne bakın.

   > [!TIP]
   > SQL Server lisans maliyetleri, oluşturduğunuz sanal makinenin ikinci başına fiyatlandırmasına dahil edilir ve sürüm ve çekirdekler için farklılık gösterir. Ancak, SQL Server Developer sürümü üretim için değil, geliştirme ve test için ücretsizdir. Ayrıca, SQL Express hafif iş yükleri (1 GB 'tan az bellek, 10 GB 'tan az depolama alanı) için ücretsizdir. Ayrıca, kendi lisansını de (KLG) getirebilir ve yalnızca VM için ödeme yapabilirsiniz. Bu görüntü adlarının başına {BYOL} ön eki getirilir. 
   >
   > Bu seçeneklerle ilgili daha fazla bilgi için bkz. [SQL Server Azure VM’leri için fiyatlandırma kılavuzu](pricing-guidance.md).


1. **Oluştur**’u seçin.


## <a name="1-configure-basic-settings"></a>1. temel ayarları Yapılandır

**Temel bilgiler** sekmesinde, aşağıdaki bilgileri sağlayın:

* **Proje ayrıntıları**' nın altında, doğru aboneliğin seçildiğinden emin olun. 
* **Kaynak grubu** bölümünde, listeden var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' u seçin. Bir kaynak grubu, Azure’daki ilgili kaynakların bir koleksiyonudur (sanal makineler, depolama hesapları, sanal ağlar, vb.). 

  ![Abonelik](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Yalnızca Azure’daki SQL Server dağıtımlarını test ediyor veya öğreniyorsanız, yeni bir kaynak grubu kullanmak faydalıdır. Test işleminizi tamamladıktan sonra, sanal makineyi ve bu kaynak grubu ile ilişkili tüm kaynakları otomatik olarak silmek için kaynak grubunu silin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../../active-directory-b2c/overview.md).


* **Örnek ayrıntıları**:

    1. Benzersiz bir **sanal makine adı** girin.  
    1. **Bölgeniz** için bir konum seçin. 
    1. Bu kılavuzun amacı doğrultusunda, **kullanılabilirlik seçeneklerini** _altyapı yedekliliği olmadan_ ayarlayın. Kullanılabilirlik seçenekleri hakkında daha fazla bilgi edinmek için bkz. [kullanılabilirlik](../../../virtual-machines/availability.md). 
    1. **Görüntü** listesinde _Ücretsiz SQL Server Lisansı: Windows Server 2016 üzerinde SQL Server 2017 Developer_' ı seçin.  
    1. Sanal makine **boyutunun** **boyutunu değiştirmeyi** seçin ve **a2 temel** teklifini seçin. Beklenmedik ücretleri engellemek için, bu kaynaklarla işiniz bittiğinde kaynaklarınızı temizlediğinizden emin olun. Üretim iş yükleri için [Azure Virtual Machines'de SQL Server için en iyi performans uygulamaları](performance-guidelines-best-practices.md)’nda önerilen makine boyutlarına ve yapılandırmalara bakın.

    ![Örnek ayrıntıları](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> **Boyut seçin** penceresinde gösterilen tahmini aylık maliyet, SQL Server lisans maliyetlerini içermez. Bu tahmin VM 'nin tek başına maliyetidir. SQL Server Express ve Developer sürümleri için bu tahmin toplam tahmini maliyettir. Diğer sürümler için [Windows Sanal Makineler fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) bakın ve hedef SQL Server sürümünüzü seçin. Ayrıca bkz. [Azure VM 'leri Için fiyatlandırma Kılavuzu](pricing-guidance.md) ve [sanal makineler için Boyutlar](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)SQL Server.

* **Yönetici hesabı** altında, bir Kullanıcı adı ve parola sağlayın. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.

   ![Yönetici hesabı](./media/create-sql-vm-portal/basics-administrator-account.png)

* **Gelen bağlantı noktası kuralları** altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılır listeden **RDP (3389)** öğesini seçin. 

   ![Gelen bağlantı noktası kuralları](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. isteğe bağlı özellikleri yapılandırma

### <a name="disks"></a>Diskler

**Diskler** sekmesinde disk seçeneklerinizi yapılandırın. 

* **Işletim sistemi disk türü** altında, işletim sistemi için istediğiniz disk türünü seçin. Premium, üretim sistemleri için önerilir, ancak temel bir VM için kullanılamaz. Premium SSD kullanmak için sanal makine boyutunu değiştirin. 
* **Gelişmiş**' in altında, **yönetilen diskleri** kullan altında **Evet** ' i seçin.

   > [!NOTE]
   > Microsoft, SQL Server için Yönetilen Diskleri önerir. Yönetilen Diskler, depolama alanını arka planda yönetir. Ayrıca, Yönetilen Disklere sahip sanal makineler aynı kullanılabilirlik kümesinde olduğunda Azure uygun artıklık düzeyini sağlamak için depolama kaynaklarını dağıtır. Daha fazla bilgi için bkz. [Azure yönetilen disklere genel bakış](../../../virtual-machines/managed-disks-overview.md). Bir kullanılabilirlik kümesindeki yönetilen diskler hakkında daha fazla ayrıntı için bkz. [Kullanılabilirlik kümesindeki VM’ler için yönetilen diskleri kullanma](../../../virtual-machines/availability.md).

![SQL VM disk ayarları](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Ağ

**Ağ** sekmesinde, ağ seçeneklerinizi yapılandırın. 

* Yeni bir **sanal ağ** oluşturun veya SQL Server VM var olan bir sanal ağı kullanın. Bir **alt ağ** da belirleyin. 

* **NIC ağ güvenlik grubu** altında, temel bir güvenlik grubu ya da Gelişmiş güvenlik grubu ' nu seçin. Temel seçeneği belirlendiğinde, **temel** sekmede yapılandırılmış değerler olan SQL Server VM için gelen bağlantı noktalarını seçmenize izin verilir. Gelişmiş seçeneğinin belirlenmesi, var olan bir ağ güvenlik grubunu seçmenizi sağlar veya yeni bir tane oluşturabilirsiniz. 

* Ağ ayarlarında başka değişiklikler yapabilir veya varsayılan değerleri tutabilirsiniz.

![SQL VM ağ ayarları](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>İzleme

**İzleme** sekmesinde, izlemeyi yapılandırın ve otomatik olarak kapatır. 

* Azure, varsayılan olarak, sanal makine için ayrılmış depolama hesabıyla **önyükleme tanılamayı** mümkün bir şekilde sunar. Bu sekmede, bu ayarları değiştirebilir ve **Işletim sistemi Konuk tanılamayı** etkinleştirebilirsiniz. 
* Ayrıca, bu sekmede **sistem tarafından atanan yönetilen kimliği** ve **otomatik olarak kapatmalar** 'ı etkinleştirebilirsiniz. 

![SQL VM yönetimi ayarları](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. SQL Server ayarlarını yapılandırın

**SQL Server ayarları** sekmesinde, SQL Server için belirli ayarları ve iyileştirmeleri yapılandırın. SQL Server için aşağıdaki ayarları yapılandırabilirsiniz:

- [Bağlantı](#connectivity)
- [Kimlik Doğrulaması](#authentication)
- [Azure Key Vault tümleştirme](#azure-key-vault-integration)
- [Depolama yapılandırması](#storage-configuration)
- [Otomatik düzeltme eki uygulama](#automated-patching)
- [Otomatik yedekleme](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Bağlantı

**SQL bağlantısı** altında, bu VM’de SQL Server örneğini istediğiniz erişim türünü belirtin. Bu izlenecek yolun amaçları doğrultusunda, internet 'teki makinelerden veya hizmetlerden SQL Server bağlantılara izin vermek için **Genel (Internet)** seçeneğini belirleyin. Bu seçenek belirlendiğinde Azure, güvenlik duvarını ve ağ güvenlik grubunu seçilen bağlantı noktasındaki trafiğe izin verecek şekilde otomatik olarak yapılandırır.

> [!TIP]
> Varsayılan olarak, SQL Server **1433** gibi iyi bilinen bir bağlantı noktasını dinler. Daha yüksek güvenlik için önceki iletişim kutusunda dinleme bağlantı noktasını 1401 gibi varsayılan olmayan bir bağlantı noktasıyla değiştirin. Bağlantı noktasını değiştirirseniz, SQL Server Management Studio (SSMS) gibi herhangi bir istemci araçlarından Bu bağlantı noktasını kullanarak bağlanmanız gerekir.

![SQL VM güvenliği](./media/create-sql-vm-portal/azure-sqlvm-security.png)

İnternet üzerinden SQL Server'a bağlanmak için, sonraki bölümde açıklanan, SQL Server Kimlik Doğrulamasını etkinleştirmeniz gerekir.

İnternet üzerinden Veritabanı Altyapısı’na bağlantıları etkinleştirmek istemiyorsanız, aşağıdaki seçeneklerden birini seçin:

* SQL Server’a Yalnızca VM içinden gelen bağlantılara izin vermek için **Yerel (yalnızca VM dahilinde)**
* SQL Server’a aynı sanal ağdaki makineler ve hizmetlerden gelen bağlantılara izin vermek için **Özel (Sanal Ağ dahilinde)**

Genel olarak, senaryonuzun izin verdiği en kısıtlayıcı bağlantıyı seçerek güvenliği geliştirin. Ancak tüm seçenekler ağ güvenlik grubu (NSG) kuralları ve SQL/Windows kimlik doğrulaması aracılığıyla güvenli kılınabilir. VM oluşturulduktan sonra NSG 'yi düzenleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Sanal Makineler'de SQL Server için Güvenlikle İlgili Dikkat Edilmesi Gerekenler](security-considerations-best-practices.md).

### <a name="authentication"></a>Kimlik Doğrulaması

SQL Server kimlik doğrulaması gerekiyorsa, **SQL Server ayarları** sekmesinden **SQL kimlik doğrulaması** altında **Etkinleştir** ' i seçin.

![SQL Server Kimlik Doğrulaması](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Internet üzerinden SQL Server erişmeyi planlıyorsanız (genel bağlantı seçeneği), burada SQL kimlik doğrulamasını etkinleştirmeniz gerekir. SQL Server genel erişimi, SQL kimlik doğrulaması gerektirir.

SQL Server Kimlik Doğrulamasını etkinleştirirseniz, bir **Oturum açma adı** ve **parola** belirtin. Bu oturum açma adı SQL Server kimlik doğrulaması oturumu ve **sysadmin** sabit sunucu rolünün bir üyesi olarak yapılandırılır. Kimlik Doğrulama Modları hakkında daha fazla bilgi için bkz. [Kimlik Doğrulama Modu Seçme](/sql/relational-databases/security/choose-an-authentication-mode).

SQL Server kimlik doğrulamasını etkinleştirmemeyi tercih ediyorsanız, SQL Server örneğine bağlanmak için VM 'deki yerel yönetici hesabını kullanabilirsiniz.

### <a name="azure-key-vault-integration"></a>Azure Anahtar Kasası tümleştirme

Şifreleme için Azure 'da güvenlik gizli dizileri depolamak için **SQL Server ayarları**' nı seçin ve  **Azure Anahtar Kasası tümleştirmesi**' ne gidin. **Etkinleştir** ' i seçin ve istenen bilgileri girin. 

![Azure Anahtar Kasası tümleştirme](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

Aşağıdaki tabloda Azure Key Vault (AKV) tümleştirmesini yapılandırmak için gereken parametreler listelenmektedir.

| PARAMETRE | AÇIKLAMA | ÖRNEK |
| --- | --- | --- |
| **Anahtar Kasası URL'si** |Anahtar kasası konumu. |`https://contosokeyvault.vault.azure.net/` |
| **Asıl ad** |Azure Active Directory hizmet asıl adı. Bu ad İstemci Kimliği olarak da bilinir. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Asıl gizli anahtar** |Azure Active Directory hizmet asıl gizli anahtarı. Bu gizli anahtar İstemci Gizli Anahtarı olarak da bilinir. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Kimlik bilgisi adı** |**Kimlik bilgisi adı**: Akv tümleştirmesi SQL Server içinde bir kimlik bilgisi oluşturur ve VM 'nin anahtar kasasına erişmesine izin verir. Bu kimlik bilgisi için bir ad seçin. |`mycred1` |

Daha fazla bilgi için bkz. [Azure VM’lerde SQL Server için Azure Anahtar Kasası Tümleştirmeyi Yapılandırma](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Depolama yapılandırması

**SQL Server ayarları** sekmesinde, **depolama yapılandırması** altında, **yapılandırma Değiştir** ' i seçerek performans için iyileştirilmiş depolama yapılandırması sayfasını açın ve depolama gereksinimlerini belirtin.

![Depolama yapılandırmasını değiştirebileceğiniz vurguların bulunduğu ekran görüntüsü.](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

**Depolama iyileştirme seçimi** altında aşağıdaki seçeneklerden birini seçin:

* **Genel**, varsayılan ayardır ve çoğu iş yükünü destekler.
* **İşlemsel işleme** , geleneksel veritabanı OLTP iş yükleri için depolamayı iyileştirir.
* **Veri depolama**, depolamayı çözümleme ve raporlama iş yükleri için iyileştirir.

![SQL VM depolama yapılandırması](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Değerleri varsayılan olarak bırakmayı tercih edebilir veya depolama topolojisini ıOPS gereksinimlerinize uyacak şekilde el ile değiştirebilirsiniz. Daha fazla bilgi için bkz. [depolama yapılandırması](storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server Lisansı

Yazılım Güvencesi müşterisi iseniz, kendi SQL Server lisansınızı getirmek ve kaynaklara kaydetmek için [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) kullanabilirsiniz. 

![SQL VM lisansı](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Otomatik düzeltme eki uygulama

**Otomatik düzeltme eki uygulama** varsayılan olarak etkindir. Otomatik düzeltme eki uygulama Azure’un SQL Server’a ve işletim sistemine otomatik olarak düzeltme eki uygulamasını sağlar. Bakım penceresi için haftanın gününü, saati ve süreyi belirtin. Azure düzeltme eki uygulamayı bu bakım penceresinde gerçekleştirir. Bakım penceresi zamanlaması VM yerel ayarını kullanır. Azure 'un SQL Server ve işletim sistemi tarafından otomatik olarak düzeltme ekini almak istemiyorsanız, **devre dışı bırak**' ı seçin.  

![SQL VM otomatik düzeltme eki uygulama](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Daha fazla bilgi için bkz. [Azure Virtual Machines’de SQL Server için Otomatik Düzeltme Eki Uygulama](automated-patching.md).

### <a name="automated-backup"></a>Otomatik yedekleme

**Otomatik yedekleme** altında, tüm veritabanları için otomatik veritabanı yedeklemeyi etkinleştirin. Otomatik yedekleme varsayılan olarak devre dışıdır.

SQL otomatik yedeklemeyi etkinleştirdiğinizde aşağıdaki ayarları yapılandırabilirsiniz:

* Yedeklemeler için elde tutma süresi (gün)
* Yedeklemeler için kullanılacak depolama hesabı
* Yedeklemeler için şifreleme seçeneği ve parola
* Backup sistem veritabanları
* Yedekleme zamanlamasını yapılandırma

Yedeklemeyi şifrelemek için **Etkinleştir**' i seçin. Ardından **Parola**’yı belirtin. Azure yedeklemeleri şifrelemek için bir sertifika oluşturur ve bu sertifikayı korumak için belirtilen parolayı kullanır. Zamanlama varsayılan olarak otomatik olarak ayarlanır, ancak **el ile**' yi seçerek el ile zamanlama oluşturabilirsiniz. 

![SQL VM otomatik yedeklemeleri](./media/create-sql-vm-portal/automated-backup.png)

Daha fazla bilgi için bkz. [Azure Virtual Machines’de SQL Server için Otomatik Yedekleme](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

[Machine Learning Services](/sql/advanced-analytics/)etkinleştirme seçeneğiniz vardır. Bu seçenek SQL Server 2017 ' de Python ve R ile makine öğrenimini kullanmanıza olanak sağlar. **SQL Server ayarları** penceresinde **Etkinleştir** ' i seçin.


## <a name="4-review--create"></a>4. gözden geçir + oluştur

**Gözden geçir + oluştur** sekmesinde:
1. Özeti gözden geçirin.
1. Bu VM için belirtilen SQL Server, kaynak grubu ve kaynakları oluşturmak için **Oluştur** ' u seçin.

Azure portalından dağıtımı izleyebilirsiniz. Ekranın üst kısmındaki **Bildirimler** düğmesi dağıtımın temel durumunu gösterir.

> [!NOTE]
> Azure 'un bir SQL Server VM dağıtması için bir zaman örneği: varsayılan ayarlarla Doğu ABD bölgesine sağlanan bir test SQL Server VM yaklaşık 12 dakika sürer. Bölgenize ve seçtiğiniz ayarlara bağlı olarak daha hızlı veya daha yavaş dağıtım süreleriyle karşılaşabilirsiniz.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> VM 'yi uzak masaüstü ile açma

Uzak Masaüstü Protokolü (RDP) ile SQL Server sanal makinesine bağlanmak için aşağıdaki adımları kullanın:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

SQL Server sanal makineye bağlandıktan sonra, SQL Server Management Studio'yu başlatabilir ve yerel yönetici kimlik bilgilerinizi kullanarak Windows Kimlik Doğrulamasına bağlanabilirsiniz. SQL Server Kimlik Doğrulamasını etkinleştirdiyseniz, sağlama işlemi sırasında yapılandırdığınız SQL oturum açma adı ve parolasını kullanarak da SQL Kimlik Doğrulamasına bağlanabilirsiniz.

Makineye erişim, gereksinimlerinize göre makineyi ve SQL Server ayarlarını doğrudan değiştirmenize olanak tanır. Örneğin, güvenlik duvarı ayarlarını yapılandırabilir veya SQL Server yapılandırma ayarlarını değiştirebilirsiniz.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> SQL Server uzaktan bağlanma

Bu kılavuzda, sanal makine ve **SQL Server kimlik doğrulaması** için **genel** erişim ' i seçmiş olursunuz. Bu ayarlar, İnternet üzerinden tüm istemcilerden gelen (doğru SQL oturum açma bilgilerine sahip oldukları varsayılarak) SQL Server bağlantılarına izin verecek şekilde sanal makineyi yapılandırdı.

> [!NOTE]
> Sağlama sırasında Genel’i seçmediyseniz, sağlamadan sonra SQL bağlantı ayarlarınızı portal üzerinden değiştirebilirsiniz. Daha fazla bilgi edinmek için bkz. [SQL bağlantı ayarlarınızı değiştirme](ways-to-connect-to-sql.md#change).

Aşağıdaki bölümlerde, SQL Server VM örneğinize internet üzerinden nasıl bağlanalecek gösterilmektedir.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Bu örnek, 1433 ortak bağlantı noktasını kullanır. Ancak, SQL Server VM dağıtımı sırasında farklı bir bağlantı noktası (1401 gibi) belirtilmişse bu değerin değiştirilmesi gerekir. 


## <a name="next-steps"></a>Sonraki adımlar

Azure’da SQL Server'ı kullanma hakkında diğer bilgiler için bkz. [Azure Virtual Machines’de SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) ve [Sık Sorulan Sorular](frequently-asked-questions-faq.md).