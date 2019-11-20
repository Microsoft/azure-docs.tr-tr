---
title: Azure portal ile sanal makine sağla
description: Bu nasıl yapılır kılavuzunda, Azure portal Windows SQL Server 2017 sanal makineleri oluşturma seçeneklerinizi açıklanmaktadır.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 68fda45038da48660da0c29787b3a86e00d9b129
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033580"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Azure portal bir Windows SQL Server sanal makinesi sağlama

Bu kılavuzda, Azure portal bir Windows SQL Server sanal makinesi oluşturduğunuzda kullanılabilir farklı seçenekler hakkında ayrıntılı bilgi sağlanır. Bu makalede, olası bir sağlama göreviyle daha fazla geçen [SQL Server VM hızlı başlangıç](quickstart-sql-vm-create-portal.md)ile daha fazla yapılandırma seçeneği ele alınmaktadır. 

Kendi SQL Server VM oluşturmak için bu kılavuzu kullanın. Ya da bunu, Azure portal kullanılabilen seçeneklere yönelik bir başvuru olarak kullanın.

> [!TIP]
> SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](virtual-machines-windows-sql-server-iaas-faq.md) bölümüne bakın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a id="select"></a>SQL Server sanal makine galeri görüntüleri

SQL Server bir sanal makine oluşturduğunuzda, sanal makine galerisindeki önceden yapılandırılmış birkaç görüntüden birini seçebilirsiniz. Aşağıdaki adımlarda SQL Server 2017 görüntülerinden birini seçme gösterilmektedir.

1. Azure portal sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **Ayrıntıları göster**' i seçerek ek bilgileri görüntüleyebilirsiniz. 
1. **SQL sanal makineler** kutucuğunda SQL Server resim arama kutusuna `2017` yazın ve ardından açılan **SQL Server lisans: SQL Server 2017 Developer for Windows Server 2016** ' i seçin. 


   ![SQL VM görüntüsünü seçin](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > Geliştirici sürümü, geliştirme testi için SQL Server tam özellikli, ücretsiz bir sürümü olduğundan Bu izlenecek yolda kullanılır. Yalnızca çalışan VM'ler için ücret ödersiniz. Ancak, bu kılavuzda kullanılacak görüntülerden herhangi birini seçebilirsiniz. Kullanılabilir görüntülerin açıklaması için [SQL Server Windows sanal makineleri genel bakış](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)bölümüne bakın.

   > [!TIP]
   > SQL Server lisans maliyetleri, oluşturduğunuz sanal makinenin ikinci başına fiyatlandırmasına dahil edilir ve sürüm ve çekirdekler için farklılık gösterir. Ancak, SQL Server Developer Edition geliştirme/test için (üretim değil) ücretsizdir ve SQL Express hafif iş yükleri (1 GB 'tan az depolama alanı 10 GB 'tan az) için ücretsizdir. Ayrıca, kendi lisansını de (KLG) getirebilir ve yalnızca VM için ödeme yapabilirsiniz. Bu görüntü adlarının başına {BYOL} ön eki getirilir. 
   >
   > Bu seçeneklerle ilgili daha fazla bilgi için bkz. [SQL Server Azure VM’leri için fiyatlandırma kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md).


1. **Oluştur**'u seçin.


## <a name="1-configure-basic-settings"></a>1. temel ayarları Yapılandır


**Temel bilgiler** sekmesinde, aşağıdaki bilgileri sağlayın:

* **Proje ayrıntıları**' nın altında, doğru aboneliğin seçildiğinden emin olun. 
*  **Kaynak grubu** bölümünde, listeden var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' u seçin. Bir kaynak grubu, Azure’daki ilgili kaynakların bir koleksiyonudur (sanal makineler, depolama hesapları, sanal ağlar, vb.). 

    ![Abonelik](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Yalnızca Azure’daki SQL Server dağıtımlarını test ediyor veya öğreniyorsanız, yeni bir kaynak grubu kullanmak faydalıdır. Test işleminizi tamamladıktan sonra, sanal makineyi ve bu kaynak grubu ile ilişkili tüm kaynakları otomatik olarak silmek için kaynak grubunu silin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager’a Genel Bakış](../../../azure-resource-manager/resource-group-overview.md).


* **Örnek ayrıntıları**:
    1. Benzersiz bir **sanal makine adı**girin.  
    1. **Bölgeniz**için bir konum seçin. 
    1. Bu kılavuzun amacı doğrultusunda, **kullanılabilirlik seçeneklerini** _altyapı yedekliliği olmadan_ayarlayın. Kullanılabilirlik seçenekleri hakkında daha fazla bilgi edinmek için bkz. [kullanılabilirlik](../../windows/availability.md). 
    1. **Görüntü** listesinde _Ücretsiz SQL Server Lisansı: Windows Server 2016 üzerinde SQL Server 2017 Developer_' ı seçin.  
    1. Sanal makine **boyutunun** **boyutunu değiştirmeyi** seçin ve **a2 temel** teklifini seçin. Beklenmedik ücretleri engellemek için, bu kaynaklarla işiniz bittiğinde kaynaklarınızı temizlediğinizden emin olun. Üretim iş yükleri için [Azure Virtual Machines'de SQL Server için en iyi performans uygulamaları](virtual-machines-windows-sql-performance.md)’nda önerilen makine boyutlarına ve yapılandırmalara bakın.

    ![Örnek ayrıntıları](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> **Boyut seçin** penceresinde gösterilen tahmini aylık maliyet, SQL Server lisans maliyetlerini içermez. Bu tahmin VM 'nin tek başına maliyetidir. SQL Server Express ve Developer sürümleri için bu tahmin toplam tahmini maliyettir. Diğer sürümler için [Windows Sanal Makineler fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) bakın ve hedef SQL Server sürümünüzü seçin. Ayrıca bkz. [Azure VM 'leri Için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md) ve [sanal makineler için Boyutlar](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)SQL Server.

* **Yönetici hesabı**altında, bir Kullanıcı adı ve parola sağlayın. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.

   ![Yönetici hesabı](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* **Gelen bağlantı noktası kuralları**altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılır listeden **RDP (3389)** öğesini seçin. 

   ![Gelen bağlantı noktası kuralları](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. isteğe bağlı özellikleri yapılandırma

### <a name="disks"></a>Diskler

**Diskler** sekmesinde disk seçeneklerinizi yapılandırın. 

* **Işletim sistemi disk türü**altında, işletim sistemi için istediğiniz disk türünü seçin. Premium, üretim sistemleri için önerilir, ancak temel bir VM için kullanılamaz. Premium SSD kullanmak için sanal makine boyutunu değiştirin. 
* **Gelişmiş**' in altında, **yönetilen diskleri**kullan altında **Evet** ' i seçin.

   > [!NOTE]
   > Microsoft, SQL Server için Yönetilen Diskleri önerir. Yönetilen Diskler, depolama alanını arka planda yönetir. Ayrıca, Yönetilen Disklere sahip sanal makineler aynı kullanılabilirlik kümesinde olduğunda Azure uygun artıklık düzeyini sağlamak için depolama kaynaklarını dağıtır. Daha fazla bilgi için bkz. [Azure yönetilen disklere genel bakış](../managed-disks-overview.md). Bir kullanılabilirlik kümesindeki yönetilen diskler hakkında daha fazla ayrıntı için bkz. [Kullanılabilirlik kümesindeki VM’ler için yönetilen diskleri kullanma](../manage-availability.md).

![SQL VM disk ayarları](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Ağ

**Ağ** sekmesinde, ağ seçeneklerinizi yapılandırın. 

* Yeni bir **sanal ağ**oluşturun veya SQL Server VM mevcut bir vNet 'i kullanın. Bir **alt ağ** da belirleyin. 

* **NIC ağ güvenlik grubu**altında, temel bir güvenlik grubu ya da Gelişmiş güvenlik grubu ' nu seçin. Temel seçeneği belirlemek SQL Server VM için gelen bağlantı noktalarını seçmenizi sağlar ( **temel** sekmede yapılandırılmış olan değerler). Gelişmiş seçeneğinin belirlenmesi, var olan bir ağ güvenlik grubunu seçmenizi sağlar veya yeni bir tane oluşturabilirsiniz. 

* Ağ ayarlarında başka değişiklikler yapabilir veya varsayılan değerleri tutabilirsiniz.

![SQL VM ağ ayarları](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>İzleme

**İzleme** sekmesinde, izleme ve oto kapatma yapılandırın. 

* Azure, varsayılan olarak, sanal makine için ayrılmış depolama hesabıyla **önyükleme tanılamayı** mümkün bir şekilde sunar. Bu ayarları burada değiştirebilir ve **Işletim sistemi Konuk tanılamayı**etkinleştirebilirsiniz. 
* Bu sekmede, **sistem tarafından atanan yönetilen kimliği** ve **oto de kapatma** özelliğini etkinleştirebilirsiniz. 

![SQL VM yönetimi ayarları](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. SQL Server ayarlarını yapılandırın

**SQL Server ayarları** sekmesinde, SQL Server için belirli ayarları ve iyileştirmeleri yapılandırın. SQL Server için yapılandırabilmeniz gereken ayarlar şunlardır:

| Ayar |
| --- |
| [Bağlantı](#connectivity) |
| [Kimlik doğrulaması](#authentication) |
| [Azure Anahtar Kasası Tümleştirmesi](#azure-key-vault-integration) |
| [Depolama yapılandırması](#storage-configuration) |
| [Otomatik Düzeltme Eki Uygulama](#automated-patching) |
| [Otomatik Yedekleme](#automated-backup) |
| [R Services (gelişmiş analiz)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Bağlantı

**SQL bağlantısı** altında, bu VM’de SQL Server örneğini istediğiniz erişim türünü belirtin. Bu izlenecek yolun amaçları doğrultusunda, internet 'teki makinelerden veya hizmetlerden SQL Server bağlantılara izin vermek için **Genel (Internet)** seçeneğini belirleyin. Bu seçenek belirlendiğinde Azure, güvenlik duvarını ve ağ güvenlik grubunu seçilen bağlantı noktasındaki trafiğe izin verecek şekilde otomatik olarak yapılandırır.

> [!TIP]
> Varsayılan olarak, SQL Server **1433** gibi iyi bilinen bir bağlantı noktasını dinler. Daha yüksek güvenlik için önceki iletişim kutusunda dinleme bağlantı noktasını 1401 gibi varsayılan olmayan bir bağlantı noktasıyla değiştirin. Bağlantı noktasını değiştirirseniz SSMS gibi herhangi bir istemci araçlarından Bu bağlantı noktasını kullanarak bağlanmanız gerekir.

![SQL VM güvenliği](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

İnternet üzerinden SQL Server'a bağlanmak için, sonraki bölümde açıklanan, SQL Server Kimlik Doğrulamasını etkinleştirmeniz gerekir.

İnternet üzerinden Veritabanı Altyapısı’na bağlantıları etkinleştirmek istemiyorsanız, aşağıdaki seçeneklerden birini seçin:

* SQL Server’a Yalnızca VM içinden gelen bağlantılara izin vermek için **Yerel (yalnızca VM dahilinde)**
* SQL Server’a aynı sanal ağdaki makineler ve hizmetlerden gelen bağlantılara izin vermek için **Özel (Sanal Ağ dahilinde)**

Genel olarak, senaryonuzun izin verdiği en kısıtlayıcı bağlantıyı seçerek güvenliği geliştirin. Ancak tüm seçenekler Ağ Güvenlik Grubu kuralları ve SQL/Windows Kimlik Doğrulaması üzerinden korumaya alınabilir. VM oluşturulduktan sonra Ağ Güvenlik Grubu’nu düzenleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Sanal Makineler'de SQL Server için Güvenlikle İlgili Dikkat Edilmesi Gerekenler](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Kimlik Doğrulaması

SQL Server kimlik doğrulaması gerekliyse, **SQL Server ayarları** sekmesinde **SQL kimlik doğrulaması** altında **Etkinleştir** ' e tıklayın.

![SQL Server Kimlik Doğrulaması](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Internet üzerinden SQL Server erişmeyi planlıyorsanız (genel bağlantı seçeneği), burada SQL kimlik doğrulamasını etkinleştirmeniz gerekir. SQL Server'a genel erişim SQL Kimlik Doğrulaması kullanılması gerektirir.

SQL Server Kimlik Doğrulamasını etkinleştirirseniz, bir **Oturum açma adı** ve **parola** belirtin. Bu oturum açma adı, SQL Server kimlik doğrulaması oturumu ve **sysadmin** sabit sunucu rolünün üyesi olarak yapılandırılır. Kimlik Doğrulama Modları hakkında daha fazla bilgi için bkz. [Kimlik Doğrulama Modu Seçme](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

SQL Server Kimlik Doğrulamasını etkinleştirmezseniz, SQL Server örneğine bağlanmak için VM’deki yerel Yönetici hesabını kullanabilirsiniz.


### <a name="azure-key-vault-integration"></a>Azure Anahtar Kasası tümleştirme

Şifreleme için Azure 'da güvenlik gizli dizileri depolamak için **SQL Server ayarları**' nı seçin ve **Azure Anahtar Kasası tümleştirmesi**' ne gidin. **Etkinleştir** ' i seçin ve istenen bilgileri girin. 

![Azure Anahtar Kasası tümleştirme](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

Aşağıdaki tabloda Azure Anahtar Kasası Tümleştirmeyi yapılandırmak için gereken parametreler listelenmektedir.

| PARAMETRE | AÇIKLAMA | ÖRNEK |
| --- | --- | --- |
| **Anahtar Kasası URL'si** |Anahtar kasası konumu. |https:\//contosokeyvault.vault.azure.net/ |
| **Asıl ad** |Azure Active Directory hizmet asıl adı. Bu ad İstemci Kimliği olarak da bilinir. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Asıl parola** |Azure Active Directory hizmet asıl gizli anahtarı. Bu gizli anahtar İstemci Gizli Anahtarı olarak da bilinir. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Kimlik bilgisi adı** |**Kimlik bilgisi adı**: AKV Tümleştirme, VM’nin anahtar kasasına erişim sağlamasına izin vererek, SQL Server’da bir kimlik bilgisi oluşturur Bu kimlik bilgisi için bir ad seçin. |mycred1 |

Daha fazla bilgi için bkz. [Azure VM’lerde SQL Server için Azure Anahtar Kasası Tümleştirmeyi Yapılandırma](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Depolama yapılandırması

**SQL Server ayarları** sekmesinde, **depolama yapılandırması**altında, **yapılandırma Değiştir** ' i seçerek performans için iyileştirilmiş depolama yapılandırması sayfasını açın ve depolama gereksinimlerini belirtin.

![SQL VM depolama yapılandırması](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

**Depolama iyileştirme seçimi** altında aşağıdaki seçeneklerden birini seçin:

* **Genel**, varsayılan ayardır ve çoğu iş yükünü destekler.
* **İşlemsel işleme** , geleneksel veritabanı OLTP iş yükleri için depolamayı iyileştirir.
* **Veri depolama**, depolamayı çözümleme ve raporlama iş yükleri için iyileştirir.

![SQL VM depolama yapılandırması](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Değerleri varsayılan olarak bırakmayı tercih edebilir veya depolama topolojisini ıOPS gereksinimlerinize uyacak şekilde el ile değiştirebilirsiniz. Daha fazla bilgi için bkz. [depolama yapılandırması](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server Lisansı
Yazılım Güvencesi müşterisi iseniz, kendi SQL Server lisansınızı getirmek ve kaynaklara kaydetmek için [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) kullanabilirsiniz. 

![SQL VM lisansı](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Otomatik düzeltme eki uygulama

**Otomatik düzeltme eki uygulama** varsayılan olarak etkindir. Otomatik düzeltme eki uygulama Azure’un SQL Server’a ve işletim sistemine otomatik olarak düzeltme eki uygulamasını sağlar. Bakım penceresi için haftanın gününü, saati ve süreyi belirtin. Azure düzeltme eki uygulamayı bu bakım penceresinde gerçekleştirir. Bakım penceresi zamanlaması saat için VM yerel saatini kullanır. Azure’un SQL Server’a ve işletim sistemine otomatik olarak düzeltme eki uygulamasını istemiyorsanız tıklayın **Devre dışı**’na tıklayın.  

![SQL VM otomatik düzeltme eki uygulama](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Daha fazla bilgi için bkz. [Azure Virtual Machines’de SQL Server için Otomatik Düzeltme Eki Uygulama](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Otomatik yedekleme

**Otomatik yedekleme** altında, tüm veritabanları için otomatik veritabanı yedeklemeyi etkinleştirin. Otomatik yedekleme varsayılan olarak devre dışıdır.

SQL otomatik yedeklemeyi etkinleştirdiğinizde aşağıdaki ayarları yapılandırabilirsiniz:

* Yedeklemeler için elde tutma süresi (gün)
* Yedeklemeler için kullanılacak depolama hesabı
* Yedeklemeler için şifreleme seçeneği ve parola
* Backup sistem veritabanları
* Yedekleme zamanlamasını yapılandırma

Yedeklemeyi şifrelemek için **Etkinleştir**’e tıklayın. Ardından **Parola**’yı belirtin. Azure yedeklemeleri şifrelemek için bir sertifika oluşturur ve bu sertifikayı korumak için belirtilen parolayı kullanır. Zamanlama varsayılan olarak otomatik olarak ayarlanır, ancak **el ile**' yi seçerek el ile zamanlama oluşturabilirsiniz. 

![SQL VM otomatik yedeklemeleri](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Daha fazla bilgi için bkz. [Azure Virtual Machines’de SQL Server için Otomatik Yedekleme](virtual-machines-windows-sql-automated-backup.md).


### <a name="r-services-advanced-analytics"></a>R Services (gelişmiş analiz)

[SQL Server R Services (gelişmiş analiz)](/sql/advanced-analytics/r/sql-server-r-services/)seçeneğini etkinleştirme seçeneğiniz vardır. Bu seçenek SQL Server 2017 ile gelişmiş analiz kullanmanıza olanak sağlar. **SQL Server ayarları** penceresinde **Etkinleştir** ' i seçin.


## <a name="4-review--create"></a>4. gözden geçir + oluştur

**Gözden geçir + oluştur** sekmesinde, Özeti gözden geçirin ve **Oluştur** ' u seçerek bu VM için belirtilen SQL Server, kaynak grubu ve kaynakları oluşturun.

Azure portalından dağıtımı izleyebilirsiniz. Ekranın üst kısmındaki **Bildirimler** düğmesi dağıtımın temel durumunu gösterir.

> [!NOTE]
> Size dağıtım zamanları hakkında bir fikir vermek için,Doğu ABD bölgesinde, varsayılan ayarlarla bir SQL VM dağıttım. Bu test dağıtımının tamamlanması yaklaşık 12 dakika sürdü. Ancak bölgeniz ve seçili ayarlarınıza göre, daha hızlı veya daha yavaş dağıtım süresiyle karşılaşabilirsiniz.

## <a id="remotedesktop"></a> VM’yi Uzak Masaüstü ile açma

Uzak Masaüstü kullanarak SQL Server sanal makinesine bağlanmak için aşağıdaki adımları kullanın:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

SQL Server sanal makineye bağlandıktan sonra, SQL Server Management Studio'yu başlatabilir ve yerel yönetici kimlik bilgilerinizi kullanarak Windows Kimlik Doğrulamasına bağlanabilirsiniz. SQL Server Kimlik Doğrulamasını etkinleştirdiyseniz, sağlama işlemi sırasında yapılandırdığınız SQL oturum açma adı ve parolasını kullanarak da SQL Kimlik Doğrulamasına bağlanabilirsiniz.

Makineye erişim, gereksinimlerinize göre makineyi ve SQL Server ayarlarını doğrudan değiştirmenize olanak tanır. Örneğin, güvenlik duvarı ayarlarını yapılandırabilir veya SQL Server yapılandırma ayarlarını değiştirebilirsiniz.

## <a id="connect"></a> SQL Server'a uzaktan bağlanma

Bu kılavuzda, sanal makine ve **SQL Server kimlik doğrulaması**için **genel** erişim ' i seçmiş olursunuz. Bu ayarlar, İnternet üzerinden tüm istemcilerden gelen (doğru SQL oturum açma bilgilerine sahip oldukları varsayılarak) SQL Server bağlantılarına izin verecek şekilde sanal makineyi yapılandırdı.

> [!NOTE]
> Sağlama sırasında Genel’i seçmediyseniz, sağlamadan sonra SQL bağlantı ayarlarınızı portal üzerinden değiştirebilirsiniz. Daha fazla bilgi edinmek için bkz. [SQL bağlantı ayarlarınızı değiştirme](virtual-machines-windows-sql-connect.md#change).

Aşağıdaki bölümlerde, SQL Server VM örneğinize internet üzerinden nasıl bağlanalecek gösterilmektedir.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Bu örnek, 1433 ortak bağlantı noktasını kullanır. Ancak, SQL Server VM dağıtımı sırasında farklı bir bağlantı noktası (1401 gibi) belirtilmişse bu değerin değiştirilmesi gerekir. 


## <a name="next-steps"></a>Sonraki adımlar

Azure’da SQL Server'ı kullanma hakkında diğer bilgiler için bkz. [Azure Virtual Machines’de SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) ve [Sık Sorulan Sorular](virtual-machines-windows-sql-server-iaas-faq.md).
