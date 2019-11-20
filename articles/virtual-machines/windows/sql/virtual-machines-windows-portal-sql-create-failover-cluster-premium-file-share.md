---
title: Premium dosya paylaşımıyla SQL Server FCı-Azure sanal makineleri
description: Bu makalede, Azure sanal makinelerinde Premium dosya paylaşma kullanılarak SQL Server yük devretme kümesi örneği oluşturma işlemi açıklanmaktadır.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 10a3c2bf421c7182dca00dfcbf7c3f559141a745
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084074"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Azure sanal makinelerinde Premium dosya paylaşımıyla SQL Server yük devretme kümesi örneği yapılandırma

Bu makalede, [Premium dosya paylaşma](../../../storage/files/storage-how-to-create-premium-fileshare.md)kullanılarak Azure sanal makinelerinde bir SQL Server yük devretme kümesi örneği (FCI) oluşturma işlemi açıklanmaktadır.

Premium dosya paylaşımları, Windows Server 2012 veya üzeri sürümlerde SQL Server 2012 veya sonraki bir sürümü için yük devretme kümesi örnekleriyle birlikte kullanılmak üzere tam olarak desteklenen SSD destekli, tutarlı olmayan düşük gecikmeli dosya paylaşımlardır. Premium dosya paylaşımları size daha fazla esneklik sağlar, böylece herhangi bir kesinti olmadan dosya paylaşımını yeniden boyutlandırabilir ve ölçeklendirebilirsiniz.


## <a name="before-you-begin"></a>Başlamadan önce

Başlamadan önce bilmeniz gereken birkaç nokta vardır.

Bu teknolojilerin işlemsel olarak anlaşılmasına sahip olmanız gerekir:

- [Windows küme teknolojileri](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server yük devretme kümesi örnekleri](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Bilmeniz gereken tek şey, bir Azure IaaS VM yük devretme kümesinde, sunucu başına (küme düğümü) ve tek bir alt ağ için tek bir NIC önerilir. Azure ağ iletişimi, Azure IaaS VM Konuk kümesinde ek NIC 'Lerin ve alt ağların gereksiz olmasını sağlayan fiziksel yedekliliğe sahiptir. Küme doğrulama raporu, düğümlerin yalnızca tek bir ağda ulaşılabilir olduğunu size bildirir. Bu uyarıyı, Azure IaaS VM yük devretme kümelerinde yoksayabilirsiniz.

Ayrıca, bu teknolojilerin genel olarak anlaşılmasına de sahip olmanız gerekir:

- [Azure Premium dosya paylaşma](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure kaynak grupları](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Şu anda Azure sanal makinelerinde SQL Server yük devretme kümesi örnekleri yalnızca [SQL Server IaaS aracı uzantısının](virtual-machines-windows-sql-server-agent-extension.md) [basit](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) yönetim moduyla desteklenir. Tam uzantı modundan hafif moda geçmek için, karşılık gelen VM 'Ler için **SQL sanal makine** kaynağını silin ve ardından bunları [basıt](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) modda SQL VM kaynak sağlayıcısı 'na kaydedin. Azure portal kullanarak **SQL sanal makine** kaynağını sildiğinizde, doğru sanal makinenin yanındaki onay kutusunu temizleyin.
>
> Tam uzantı otomatik yedekleme, düzeltme eki uygulama ve gelişmiş Portal yönetimi gibi özellikleri destekler. Bu özellikler, aracı [hafif](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) yönetim modunda yeniden yüklendikten sonra SQL Server VM 'ler için çalışmaz.

Premium dosya paylaşımları, ıOPS 'yi ve birçok iş yükünün ihtiyaçlarını karşılayacak tüm kapasiteleri sağlar. GÇ yoğun iş yükleri için, yönetilen Premium disklere veya ultra disklere göre [SQL Server yük devretme kümesi örneklerini depolama alanları doğrudan ile](virtual-machines-windows-portal-sql-create-failover-cluster.md)düşünün.  

Ortamınızın ıOPS etkinliğini denetleyin ve bir dağıtıma veya geçişe başlamadan önce Premium dosya paylaşımlarının ihtiyacınız olan ıOPS 'yi sağlayabildiğini doğrulayın. SQL Server verileri, günlüğü ve Geçici VERITABANı dosyaları için gereken toplam ıOPS (disk aktarımı/saniye) ve üretilen iş (disk Baytları/saniye) izlemek için Windows performans Izleyicisi disk sayaçlarını kullanın.

Çok sayıda iş yükü ıO GÇ kullanır, bu nedenle ağır kullanım dönemleri sırasında kontrol etmek ve maksimum ıOPS ve ortalama ıOPS 'yi uyarmak iyi bir fikirdir. Premium dosya paylaşımları, paylaşımın boyutuna bağlı olarak ıOPS sağlar. Premium dosya paylaşımları ayrıca, GÇ 'nizi bir saate kadar üç temel miktarı üç katına almanıza olanak sağlayan, yük patlaması sağlar.

Premium dosya paylaşma performansı hakkında daha fazla bilgi için bkz. [dosya paylaşımının performans katmanları](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers).

### <a name="licensing-and-pricing"></a>Lisanslama ve fiyatlandırma

Azure sanal makineler 'de, Kullandıkça Öde (PAYG) veya kendi lisansını getir (KLG) VM görüntülerini kullanarak SQL Server lisanslayabilirim. Seçtiğiniz görüntünün türü ücretlendirileceğini etkiler.

Kullandıkça Öde lisanslaması sayesinde, Azure sanal makinelerinde SQL Server bir yük devretme kümesi örneği (FCı), Pasif düğümler de dahil olmak üzere FCı 'nın tüm düğümleri için ücretlendirir. Daha fazla bilgi için bkz. [sanal makine fiyatlandırması SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Yazılım güvencesi içeren Kurumsal Anlaşma sahipseniz, her etkin düğüm için bir ücretsiz pasif FCı düğümü kullanabilirsiniz. Azure 'da Bu avantajdan faydalanmak için, KLG VM görüntülerini kullanın ve FCı 'nin hem etkin hem de pasif düğümlerinde aynı lisansı kullanın. Daha fazla bilgi için bkz. [Kurumsal Anlaşma](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Azure sanal makinelerinde SQL Server için Kullandıkça öde ve KLG lisanslamayı karşılaştırmak için bkz. [SQL VM 'leri kullanmaya başlama](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Lisanslama SQL Server hakkında tüm bilgiler için bkz. [fiyatlandırma](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Dosya akışı

Premium dosya paylaşımıyla yük devretme kümesi için FILESTREAM desteklenmez. FILESTREAM kullanmak için [depolama alanları doğrudan](virtual-machines-windows-portal-sql-create-failover-cluster.md)kullanarak kümenizi dağıtın.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlamadan önce, zaten şunları yapmalısınız:

- Microsoft Azure aboneliği.
- Azure sanal makinelerinde bir Windows etki alanı.
- Hem Azure sanal makinelerinde hem de Active Directory nesne oluşturma izinlerine sahip olan bir hesap.
- Bu bileşenler için yeterli IP adresi alanına sahip bir Azure sanal ağı ve alt ağı:
   - İki sanal makine.
   - Yük devretme kümesi IP adresi.
   - Her FCı için bir IP adresi.
- Etki alanı denetleyicilerine işaret eden Azure ağı üzerinde yapılandırılmış DNS.
- Veri dosyalarınız için veritabanınızın depolama kotasına dayalı bir [Premium dosya paylaşma](../../../storage/files/storage-how-to-create-premium-fileshare.md) .
- Veri dosyalarınız için kullanılan Premium dosya paylaşımından farklı yedeklemeler için bir dosya paylaşma. Bu dosya paylaşımının standart ya da Premium olması olabilir.

Bu önkoşulları yerine, yük devretme kümenizi oluşturmaya başlayabilirsiniz. İlk adım, sanal makineleri oluşturmaktır.

## <a name="step-1-create-the-virtual-machines"></a>1\. Adım: sanal makineleri oluşturma

1. [Azure Portal](https://portal.azure.com) aboneliğinizde oturum açın.

1. [Azure kullanılabilirlik kümesi oluşturun](../tutorial-availability-sets.md).

   Kullanılabilirlik kümesi, sanal makineleri hata etki alanları ve güncelleştirme etki alanları genelinde gruplandırır. Uygulamanızın, ağ anahtarı veya bir sunucu rafı güç birimi gibi tek hata noktalarından etkilenmemesini sağlar.

   Sanal makineleriniz için kaynak grubu oluşturmadıysanız, bir Azure kullanılabilirlik kümesi oluşturduğunuzda bunu yapın. Kullanılabilirlik kümesini oluşturmak için Azure portal kullanıyorsanız şu adımları uygulayın:

   1. Azure portal Azure Marketi 'ni açmak için **kaynak oluştur** ' u seçin. **Kullanılabilirlik kümesi**araması yapın.
   1. **Kullanılabilirlik kümesi**seçin.
   1. **Oluştur**'u seçin.
   1. **Kullanılabilirlik kümesi oluştur**altında şu değerleri girin:
      - **Ad**: kullanılabilirlik kümesi için bir ad.
      - **Abonelik**: Azure aboneliğiniz.
      - **Kaynak grubu**: var olan bir grubu kullanmak istiyorsanız, **Varolanı Seç** ' e tıklayın ve ardından listeden grubu seçin. Aksi takdirde, **Yeni oluştur** ' u seçin ve grup için bir ad girin.
      - **Konum**: sanal makinelerinizi oluşturmayı planladığınız konumu ayarlayın.
      - **Hata etki alanları**: varsayılanı kullanın (**3**).
      - **Etki alanlarını güncelleştir**: varsayılan (**5**) kullanın.
   1. Kullanılabilirlik kümesini oluşturmak için **Oluştur** ' u seçin.

1. Kullanılabilirlik kümesinde sanal makineleri oluşturun.

   Azure kullanılabilirlik kümesinde iki SQL Server sanal makine sağlayın. Yönergeler için bkz. [Azure portal SQL Server sanal makinesi sağlama](virtual-machines-windows-portal-sql-server-provision.md).

   Her iki sanal makineyi de Yerleştir:

   - Kullanılabilirlik kümesi ile aynı Azure Kaynak grubunda.
   - Etki alanı denetleyiciyle aynı ağda.
   - Hem sanal makineler için yeterli IP adresi alanına sahip bir alt ağda hem de tüm FC, kümede kullanmaya devam edebilirsiniz.
   - Azure kullanılabilirlik kümesi 'nde.

      >[!IMPORTANT]
      >Bir sanal makineyi oluşturduktan sonra kullanılabilirlik kümesini ayarlayamazsınız veya değiştiremezsiniz.

   Azure Marketi 'nden bir görüntü seçin. Windows Server ve SQL Server içeren bir Azure Market görüntüsü kullanabilir veya yalnızca Windows Server içeren bir tane kullanabilirsiniz. Ayrıntılar için bkz. [Azure sanal makinelerinde SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md).

   Azure galerisindeki resmi SQL Server görüntüleri yüklü bir SQL Server örneği, SQL Server yükleme yazılımını ve gerekli anahtarı içerir.

   >[!IMPORTANT]
   > Sanal makineyi oluşturduktan sonra, önceden yüklenmiş tek başına SQL Server örneğini kaldırın. Yük devretme kümesini ve Premium dosya paylaşımından depolama alanı olarak ayarladıktan sonra SQL Server FCı oluşturmak için önceden yüklenmiş SQL Server medyasını kullanacaksınız.

   Alternatif olarak, yalnızca işletim sistemini içeren Azure Marketi görüntülerini kullanabilirsiniz. Bir **Windows Server 2016 Datacenter** görüntüsü seçin ve yük devretme kümesini ve Premium dosya paylaşımından depolama olarak ayarladıktan sonra SQL Server FCI 'yi kurun. Bu görüntüde SQL Server yükleme medyası yok. SQL Server yükleme medyasını her sunucu için çalıştırabileceğiniz bir konuma yerleştirin.

1. Azure sanal makinelerinizi oluşturduktan sonra, RDP kullanarak her birine bağlanın.

   RDP kullanarak bir sanal makineye ilk kez bağlandığınızda, bir istem, BILGISAYARıN ağda bulunabilir olmasını isteyip istemediğinizi sorar. **Evet**’i seçin.

1. SQL Server tabanlı sanal makine görüntülerinden birini kullanıyorsanız, SQL Server örneğini kaldırın.

   1. **Programlar ve Özellikler**' de **Microsoft SQL Server 201_ (64-bit)** öğesine sağ tıklayın ve **Kaldır/Değiştir**' i seçin.
   1. **Kaldır**’ı seçin.
   1. Varsayılan örneği seçin.
   1. **Veritabanı motoru Hizmetleri**altındaki tüm özellikleri kaldırın. **Paylaşılan özellikleri**kaldırmayın. Aşağıdaki ekran görüntüsüne benzer bir şey göreceksiniz:

        ![Özellik seç](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. **İleri**' yi ve ardından **Kaldır**' ı seçin.

1. <a name="ports"></a>Güvenlik Duvarı bağlantı noktalarını açın.

   Her bir sanal makinede, Windows güvenlik duvarında şu bağlantı noktalarını açın:

   | Amaç | TCP bağlantı noktası | Notlar
   | ------ | ------ | ------
   | SQL Server | 1433 | Varsayılan SQL Server örnekleri için normal bağlantı noktası. Galeriden bir görüntü kullandıysanız, bu bağlantı noktası otomatik olarak açılır.
   | Durum yoklaması | 59999 | Açık herhangi bir TCP bağlantı noktası. Sonraki bir adımda, yük dengeleyici [durum araştırmasını](#probe) ve kümeyi Bu bağlantı noktasını kullanacak şekilde yapılandırın.
   | Dosya paylaşımı | 445 | Dosya paylaşma hizmeti tarafından kullanılan bağlantı noktası.

1. [Sanal makineleri önceden var olan etki alanına ekleyin](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Sanal makineleri oluşturup yapılandırdıktan sonra, Premium dosya paylaşımından yapılandırma yapabilirsiniz.

## <a name="step-2-mount-the-premium-file-share"></a>2\. Adım: Premium dosya paylaşımından bağlama

1. [Azure Portal](https://portal.azure.com) oturum açın ve depolama hesabınıza gidin.
1. **Dosya hizmeti** altında **dosya paylaşımları** ' na gidin ve SQL depolama alanınızı kullanmak istediğiniz Premium dosya paylaşımını seçin.
1. Dosya paylaşımınızın bağlantı dizesini görüntülemek için **Bağlan** ' ı seçin.
1. Açılan listeden kullanmak istediğiniz sürücü harfini seçin ve ardından her iki kod bloğunu Not defteri 'ne kopyalayın.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Her iki PowerShell komutunu da dosya paylaşımının Connect portalından Kopyala":::

1. SQL Server FCı 'nizin hizmet hesabı için kullanacağı hesapla SQL Server VM bağlanmak için RDP 'yi kullanın.
1. Bir yönetim PowerShell komut konsolunu açın.
1. Daha önce portalda çalışırken kaydettiğiniz komutları çalıştırın.
1. Dosya Gezgini 'ni veya **Çalıştır** iletişim kutusunu (Windows logosu tuşu + r) kullanarak paylaşıma gidin. `\\storageaccountname.file.core.windows.net\filesharename`ağ yolunu kullanın. Örneğin, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. SQL veri dosyalarınızı içine yerleştirmek için yeni bağlı dosya paylaşımında en az bir klasör oluşturun.
1. Kümeye katılacak her bir SQL Server VM bu adımları yineleyin.

  > [!IMPORTANT]
  > Bu paylaşımın ıOPS ve alan kapasitesini veri ve günlük dosyaları için kaydetmek üzere yedekleme dosyaları için ayrı bir dosya paylaşma kullanmayı düşünün. Yedekleme dosyaları için Premium veya standart dosya paylaşımından birini kullanabilirsiniz.

## <a name="step-3-configure-the-failover-cluster-with-the-file-share"></a>3\. Adım: yük devretme kümesini dosya paylaşımıyla yapılandırma

Bir sonraki adım, yük devretme kümesini yapılandırmaktır. Bu adımda, aşağıdaki alt adımları tamamlayacaksınız:

1. Windows Server Yük Devretme Kümelemesi özelliğini ekleyin.
1. Kümeyi doğrulayın.
1. Yük devretme kümesini oluşturun.
1. Bulut tanığını oluşturun.


### <a name="add-windows-server-failover-clustering"></a>Windows Server Yük Devretme Kümelemesi Ekle

1. Yerel yöneticilerin üyesi olan ve Active Directory nesne oluşturma izni olan bir etki alanı hesabı kullanarak RDP ile ilk sanal makineye bağlanın. Yapılandırmanın geri kalanı için bu hesabı kullanın.

1. [Yük devretme kümelemesini her bir sanal makineye ekleyin](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Yük Devretme Kümelemesi 'ni kullanıcı arabiriminden yüklemek için şu adımları her iki sanal makinede de gerçekleştirin:
   1. **Sunucu Yöneticisi**, **Yönet**' i seçin ve ardından **rol ve Özellik Ekle**' yi seçin.
   1. **Rol ve Özellik Ekleme Sihirbazı**' nda, **özellikleri seçerken** **İleri** ' yi seçin.
   1. **Özellikleri Seç**bölümünde **Yük Devretme Kümelemesi**' ni seçin. Tüm gerekli özellikleri ve yönetim araçlarını dahil edin. **Özellik Ekle**' yi seçin.
   1. **İleri**' yi seçin ve sonra özellikleri yüklemek için **son** ' u seçin.

   PowerShell 'i kullanarak Yük Devretme Kümelemesi 'ni yüklemek için sanal makinelerden birindeki yönetici PowerShell oturumundan aşağıdaki betiği çalıştırın:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Kümeyi doğrulama

Kümeyi Kullanıcı arabiriminde veya PowerShell kullanarak doğrulayın.

Kullanıcı arabirimini kullanarak kümeyi doğrulamak için sanal makinelerden birinde aşağıdaki adımları uygulayın:

1. **Sunucu Yöneticisi**altında **Araçlar**' ı seçin ve **Yük devretme kümesi Yöneticisi**' i seçin.
1. **Yük devretme kümesi Yöneticisi**altında **eylem**' i seçin ve ardından **Yapılandırmayı Doğrula**' yı seçin.
1. **İleri**’yi seçin.
1. **Sunucu veya küme Seç**altında, her iki sanal makinenin adını da girin.
1. **Test seçenekleri**altında **yalnızca Seçdiğim Testleri Çalıştır**' ı seçin. **İleri**’yi seçin.
1. **Test seçimi**altında, **depolama** ve **depolama alanları doğrudan**dışındaki tüm testleri aşağıda gösterildiği gibi seçin:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Küme doğrulama testlerini seçin":::

1. **İleri**’yi seçin.
1. **Onay**altında **İleri**' yi seçin.

**Yapılandırma Doğrulama Sihirbazı** doğrulama testlerini çalıştırır.

PowerShell 'i kullanarak kümeyi doğrulamak için sanal makinelerden birindeki yönetici PowerShell oturumundan aşağıdaki betiği çalıştırın:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Kümeyi doğruladıktan sonra, yük devretme kümesini oluşturun.

### <a name="create-the-failover-cluster"></a>Yük devretme kümesi oluşturma

Yük devretme kümesini oluşturmak için şunlar gerekir:
- Küme düğümleri olacak sanal makinelerin adları.
- Yük devretme kümesi için bir ad
- Yük devretme kümesi için bir IP adresi. Küme düğümleri ile aynı Azure sanal ağı ve alt ağı üzerinde kullanılmayan bir IP adresi kullanabilirsiniz.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2016 ile Windows Server 2012

Aşağıdaki PowerShell betiği Windows Server 2016 aracılığıyla Windows Server 2012 için bir yük devretme kümesi oluşturur. Betiği, düğümlerin adlarıyla (sanal makine adları) ve kullanılabilir bir IP adresini Azure sanal ağından güncelleştirin.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Aşağıdaki PowerShell betiği Windows Server 2019 için bir yük devretme kümesi oluşturur. Daha fazla bilgi için bkz. [Yük devretme kümesi: küme ağ nesnesi](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Betiği, düğümlerin adlarıyla (sanal makine adları) ve kullanılabilir bir IP adresini Azure sanal ağından güncelleştirin.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Bulut tanığı oluşturma

Bulut tanığı, bir Azure Depolama Blobu içinde depolanan yeni bir küme çekirdeği tanığı türüdür. Bu, bir tanık paylaşma barındıran ayrı bir VM gereksinimini ortadan kaldırır.

1. [Yük devretme kümesi için bir bulut tanığı oluşturun](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Blob kapsayıcısı oluşturun.

1. Erişim anahtarlarını ve kapsayıcı URL 'sini kaydedin.

1. Yük devretme kümesi çekirdek tanığını yapılandırın. Bkz. [Kullanıcı arabiriminde çekirdek tanığını yapılandırma](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).


## <a name="step-4-test-cluster-failover"></a>4\. Adım: test kümesi yük devretmesi

Kümenizin yük devretmesini test edin. **Yük devretme kümesi Yöneticisi**, kümenize sağ tıklayıp **daha fazla eylem** ' i seçin > **çekirdek küme kaynağını taşı** > **düğümü seçin**ve ardından kümenin diğer düğümünü seçin. Çekirdek küme kaynağını kümenin her düğümüne taşıyın ve ardından birincil düğüme geri taşıyın. Kümeyi her düğüme başarıyla taşıyabiliyorsanız SQL Server yüklemeye hazırsınız demektir.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Çekirdek kaynağı diğer düğümlere taşıyarak küme yük devretmesini test etme":::

## <a name="step-5-create-the-sql-server-fci"></a>5\. Adım: SQL Server FCı oluşturma

Yük devretme kümesini yapılandırdıktan sonra, SQL Server FCı 'yi oluşturabilirsiniz.

1. RDP kullanarak ilk sanal makineye bağlanın.

1. **Yük devretme kümesi Yöneticisi**, tüm çekirdek küme kaynaklarının ilk sanal makinede olduğundan emin olun. Gerekirse, tüm kaynakları bu sanal makineye taşıyın.

1. Yükleme medyasını bulun. Sanal makine Azure Market görüntülerinden birini kullanıyorsa, medya `C:\SQLServer_<version number>_Full`bulunur. **Kurulum 'u**seçin.

1. **SQL Server Yükleme Merkezi**'nde **yükleme**' yi seçin.

1. **Yeni SQL Server yük devretme kümesi yüklemesi ' ni**seçin. SQL Server FCı 'yi yüklemek için sihirbazdaki yönergeleri izleyin.

   FCı veri dizinlerinin Premium dosya paylaşımında olması gerekir. Paylaşımın tam yolunu şu biçimde girin: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Veri dizini olarak bir dosya sunucusu belirtmiş olduğunu söyleyen bir uyarı görüntülenir. Bu uyarı beklenmektedir. Dosya paylaşımının devam eden hesabının, SQL Server hizmetin olası hatalardan kaçınmak için kullandığı hesapla aynı olduğundan emin olun.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Dosya paylaşımının SQL veri dizinleri olarak kullanılması":::

1. Sihirbazdaki adımları tamamladıktan sonra, kurulum ilk düğümde bir SQL Server FCı yükler.

1. Kurulum, ilk düğümde FCı 'yı yükledikten sonra, RDP kullanarak ikinci düğüme bağlanın.

1. **SQL Server yükleme merkezini**açın. **Yükleme**' yi seçin.

1. **SQL Server yük devretme kümesine düğüm Ekle**' yi seçin. SQL Server yüklemek ve sunucuyu FCı 'ye eklemek için sihirbazdaki yönergeleri izleyin.

   >[!NOTE]
   >SQL Server ile bir Azure Marketi Galeri görüntüsü kullandıysanız, görüntüye SQL Server Araçlar eklenmiştir. Bu görüntülerden birini kullanmıyorsanız, SQL Server araçlarını ayrı olarak yükleyebilirsiniz. Bkz. [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>6\. Adım: Azure Yük dengeleyiciyi oluşturma

Azure sanal makinelerinde kümeler, aynı anda tek bir küme düğümünde olması gereken bir IP adresini tutmak için bir yük dengeleyici kullanır. Bu çözümde, yük dengeleyici SQL Server FCı için IP adresini tutar.

Daha fazla bilgi için bkz. [Azure yük dengeleyici oluşturma ve yapılandırma](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure portal yük dengeleyiciyi oluşturun

Yük dengeleyiciyi oluşturmak için:

1. Azure portal, sanal makineleri içeren kaynak grubuna gidin.

1. **Add (Ekle)** seçeneğini belirleyin. **Load Balancer**Için Azure Marketi 'nde arama yapın. **Load Balancer**seçin.

1. **Oluştur**'u seçin.

1. Aşağıdaki değerleri kullanarak yük dengeleyiciyi ayarlayın:

   - **Abonelik**: Azure aboneliğiniz.
   - **Kaynak grubu**: sanal makinelerinizi içeren kaynak grubu.
   - **Ad**: yük dengeleyiciyi tanımlayan bir ad.
   - **Bölge**: sanal makinelerinizi içeren Azure konumu.
   - **Tür**: public veya Private. Sanal ağ içinden bir özel yük dengeleyiciye erişilebilir. Azure uygulamalarının çoğu özel yük dengeleyici kullanabilir. Uygulamanızın doğrudan Internet üzerinden SQL Server erişmesi gerekiyorsa, ortak yük dengeleyici kullanın.
   - **SKU**: standart.
   - **Sanal ağ**: sanal makinelerle aynı ağ.
   - **IP adresi ataması**: statik. 
   - **Özel IP adresi**: SQL Server FCI küme ağı KAYNAĞıNA atadığınız IP adresi.

   Aşağıdaki görüntüde **yük dengeleyici oluşturma** Kullanıcı arabirimi gösterilmektedir:

   ![Yük dengeleyiciyi ayarlama](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Yük dengeleyici arka uç havuzunu yapılandırma

1. Sanal makineleri içeren Azure Kaynak grubuna dönün ve yeni yük dengeleyiciyi bulun. Kaynak grubundaki görünümü yenilemeniz gerekebilir. Yük dengeleyiciyi seçin.

1. **Arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

1. Arka uç havuzunu VM 'Leri içeren kullanılabilirlik kümesiyle ilişkilendirin.

1. **Hedef ağ IP yapılandırması**altında, **sanal makine** ' yi seçin ve küme düğümleri olarak katılacak sanal makineleri seçin. FCı 'yı barındıracak tüm sanal makineleri eklediğinizden emin olun.

1. Arka uç havuzunu oluşturmak için **Tamam ' ı** seçin.

### <a name="configure-a-load-balancer-health-probe"></a>Yük dengeleyici durum araştırması yapılandırma

1. Yük dengeleyici dikey penceresinde **sistem durumu araştırmaları**' nı seçin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. **Sistem durumu araştırması Ekle** dikey penceresinde aşağıdaki <a name="probe"> </a>sistem durumu araştırma parametrelerini ayarlayın.

   - **Ad**: sistem durumu araştırması için bir ad.
   - **Protokol**: TCP.
   - **Bağlantı noktası**: [Bu adımdaki](#ports)sistem durumu araştırması için güvenlik duvarında oluşturduğunuz bağlantı noktası. Bu makalede, örnek `59999`TCP bağlantı noktası kullanır.
   - **Aralık**: 5 saniye.
   - **Sağlıksız eşik**: 2 ardışık başarısızlık.

1. **Tamam**’ı seçin.

### <a name="set-load-balancing-rules"></a>Yük Dengeleme kurallarını ayarlama

1. Yük dengeleyici dikey penceresinde **Yük Dengeleme kuralları**' nı seçin.

1. **Add (Ekle)** seçeneğini belirleyin.

1. Yük Dengeleme kuralı parametrelerini ayarlayın:

   - **Ad**: Yük Dengeleme kuralları için bir ad.
   - **Ön uç IP adresi**: SQL Server FCI küme ağı kaynağı için IP adresi.
   - **Bağlantı noktası**: SQL Server FCı TCP bağlantı noktası. Varsayılan örnek bağlantı noktası 1433 ' dir.
   - **Arka uç bağlantı noktası**: **kayan IP 'yi (doğrudan sunucu dönüşü)** etkinleştirdiğinizde **bağlantı** noktası değeriyle aynı bağlantı noktasını kullanır.
   - **Arka uç havuzu**: daha önce yapılandırdığınız arka uç havuzu adı.
   - **Sistem durumu araştırması**: daha önce yapılandırdığınız sistem durumu araştırması.
   - **Oturum kalıcılığı**: yok.
   - **Boşta kalma zaman aşımı (dakika)** : 4.
   - **Kayan IP (doğrudan sunucu dönüşü)** : etkin.

1. **Tamam**’ı seçin.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>7\. Adım: araştırma için kümeyi yapılandırma

PowerShell 'de küme araştırması bağlantı noktası parametresini ayarlayın.

Küme araştırması bağlantı noktası parametresini ayarlamak için aşağıdaki betikteki değişkenleri ortamınızdaki değerlerle güncelleştirin. Betikten açılı ayraçları (`<` ve `>`) kaldırın.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Aşağıdaki listede, güncelleştirmeniz gereken değerler açıklanmaktadır:

   - `<Cluster Network Name>`: ağ için Windows Server yük devretme kümesi adı. **Yük Devretme Kümesi Yöneticisi** > **ağlarda**ağa sağ tıklayıp **Özellikler**' i seçin. Doğru değer **genel** sekmesinde **ad** ' ın altında bulunur.

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCı IP adresi kaynak adı. **Yük Devretme Kümesi Yöneticisi** > **rolünde**, SQL Server FCI rolü altında, **sunucu adı**altında, IP adresi kaynağına sağ tıklayın ve **Özellikler**' i seçin. Doğru değer **genel** sekmesinde **ad** ' ın altında bulunur.

   - `<ILBIP>`: ıLB IP adresi. Bu adres, Azure portal ıLB ön uç adresi olarak yapılandırılır. Bu ayrıca SQL Server FCı IP adresidir. **Yük devretme kümesi Yöneticisi** , `<SQL Server FCI IP Address Resource Name>`bulduğunuz aynı Özellikler sayfasında bulabilirsiniz.  

   - `<nnnnn>`: Yük Dengeleyici Sistem durumu araştırmasına yapılandırdığınız araştırma bağlantı noktası. Kullanılmayan tüm TCP bağlantı noktaları geçerlidir.

>[!IMPORTANT]
>Küme parametresinin alt ağ maskesi TCP IP yayını adresi olmalıdır: `255.255.255.255`.

Küme araştırmasını ayarladıktan sonra, PowerShell 'de tüm küme parametrelerini görebilirsiniz. Şu betiği çalıştırın:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>8\. Adım: FCı yük devretmesini test etme

Küme işlevselliğini doğrulamak için FCı yük devretmesini test edin. Aşağıdaki adımları uygulayın:

1. RDP kullanarak SQL Server FCı kümesi düğümlerinden birine bağlanın.

1. **Yük devretme kümesi Yöneticisi**açın. **Rolleri**seçin. Hangi düğümün SQL Server FCı rolüne sahip olduğuna dikkat edin.

1. SQL Server FCı rolüne sağ tıklayın.

1. **Taşı**' yı seçin ve ardından **mümkün olan en iyi düğümü**seçin.

**Yük devretme kümesi Yöneticisi** rolü gösterir ve kaynakları çevrimdışı duruma geçer. Kaynaklar daha sonra taşınır ve diğer düğümde yeniden çevrimiçi duruma gelir.

### <a name="test-connectivity"></a>Bağlantıyı test etme

Bağlantıyı sınamak için aynı sanal ağdaki başka bir sanal makinede oturum açın. **SQL Server Management Studio** açın ve SQL Server FCI adına bağlanın.

>[!NOTE]
>Gerekirse, [SQL Server Management Studio indirebilirsiniz](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Sınırlamalar

Azure sanal makineleri, kümelenmiş paylaşılan birimlerde (CSV) ve [Standart yük dengeleyicide](../../../load-balancer/load-balancer-standard-overview.md)depolama Ile Windows Server 2019 ' de Microsoft Dağıtılmış işlem DÜZENLEYICISI (MSDTC) ' i destekler.

Azure sanal makinelerde, Windows Server 2016 veya önceki sürümlerde MSDTC desteklenmez çünkü:

- Kümelenmiş MSDTC kaynağı, paylaşılan depolama alanı kullanacak şekilde yapılandırılamaz. Windows Server 2016 ' de, bir MSDTC kaynağı oluşturursanız, depolama alanı kullanılabilir olsa bile, kullanılabilir bir paylaşılan depolama alanı göstermez. Bu sorun Windows Server 2019 ' de düzeltildi.
- Temel yük dengeleyici RPC bağlantı noktalarını işlemez.

## <a name="see-also"></a>Ayrıca bkz.

- [Windows küme teknolojileri](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server yük devretme kümesi örnekleri](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
