---
title: Azure Resource Manager VM 'Ler için yüksek kullanılabilirliği ayarlama | Microsoft Docs
description: Bu öğreticide, Azure Resource Manager modunda Azure sanal makineleri ile her zaman açık kullanılabilirlik grubu oluşturma gösterilmektedir.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a06ea59af0776fe3decb0b56a3ef886f08b2dfda
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100724"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Azure sanal makinelerinde Always on kullanılabilirlik gruplarını otomatik olarak yapılandırın: Resource Manager

Bu öğreticide, Azure Resource Manager sanal makineler kullanan SQL Server kullanılabilirlik grubu oluşturma gösterilmektedir. Öğretici, bir şablonu yapılandırmak için Azure dikey pencerelerini kullanır. Varsayılan ayarları gözden geçirebilir, gerekli ayarları yazabilir ve bu öğreticiyi ilerlediyseniz portalda dikey pencereleri güncelleştirebilirsiniz.

Tüm öğreticide, Azure sanal makinelerinde aşağıdaki öğeleri içeren bir SQL Server kullanılabilirlik grubu oluşturulur:

* Ön uç ve arka uç alt ağı dahil olmak üzere birden çok alt ağa sahip bir sanal ağ
* Active Directory etki alanına sahip iki etki alanı denetleyicisi
* SQL Server çalıştıran ve arka uç alt ağına dağıtılan ve Active Directory etki alanına katılmış iki sanal makine
* Düğüm çoğunluğu çekirdek modeliyle üç düğümlü bir yük devretme kümesi
* Bir kullanılabilirlik veritabanının iki zaman uyumlu-COMMIT çoğaltması olan bir kullanılabilirlik grubu

Aşağıdaki çizim, tüm çözümü temsil eder.

![Azure 'da kullanılabilirlik grupları için test laboratuvarı mimarisi](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Bu Çözümdeki tüm kaynaklar tek bir kaynak grubuna aittir.

Bu öğreticiye başlamadan önce aşağıdakileri onaylayın:

* Zaten bir Azure hesabınız var. [Hesabınız yoksa bir deneme hesabı için kaydolun](https://azure.microsoft.com/pricing/free-trial/).
* Sanal makine galerisinden bir SQL Server sanal makine sağlamak için GUI 'yi nasıl kullanacağınızı zaten biliyoruz. Daha fazla bilgi için bkz. [Azure 'da SQL Server sanal makinesi sağlama](virtual-machines-windows-portal-sql-server-provision.md).
* Kullanılabilirlik grupları zaten iyi bir şekilde anlaşılmış olursunuz. Daha fazla bilgi için bkz. [Always on kullanılabilirlik grupları (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> SharePoint ile kullanılabilirlik grupları kullanmayı düşünüyorsanız, Ayrıca bkz. [sharepoint 2013 için SQL Server 2012 Always on kullanılabilirlik grupları yapılandırma](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

Bu öğreticide, Azure portal için şunu kullanın:

* Portaldan Always on şablonunu seçin.
* Şablon ayarlarını gözden geçirin ve ortamınız için birkaç yapılandırma ayarını güncelleştirin.
* Tüm ortamı oluşturduğundan Azure 'ı izleyin.
* Bir etki alanı denetleyicisine ve ardından SQL Server çalıştıran bir sunucuya bağlanın.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Galeriden kümeyi sağlama
Azure, tüm çözüm için bir galeri görüntüsü sağlar. Şablonu bulmak için:

1. Hesabınızı kullanarak Azure portal oturum açın.
2. Azure portal **Yeni** bölmeyi açmak Için **kaynak oluştur ' a** tıklayın.
3. **Yeni** bölmede **AlwaysOn**' ı arayın.
   ![AlwaysOn şablonu bul](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Arama sonuçlarında **SQL Server AlwaysOn kümesi**' ni bulun.
   ![AlwaysOn şablonu](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. **Bir dağıtım modeli seçin**sayfasında **Kaynak Yöneticisi**' yi seçin.

### <a name="basics"></a>Temel
**Temel bilgiler** ' e tıklayın ve aşağıdaki ayarları yapılandırın:

* **Yönetici Kullanıcı adı** , etki alanı Yöneticisi izinlerine sahip bir kullanıcı hesabıdır ve SQL Server her iki örneğine SQL Server sysadmin sabit sunucu rolünün bir üyesidir. Bu öğretici için **DomainAdmin**kullanın.
* **Parola** , etki alanı yönetici hesabının parolasıdır. Karmaşık bir parola kullanın. Parolayı onaylayın.
* **Abonelik** , Azure 'un, kullanılabilirlik grubu için dağıtılan tüm kaynakları çalıştırabildiği bir aboneliğiniz. Hesabınızda birden çok abonelik varsa, farklı bir abonelik belirtebilirsiniz.
* **Kaynak grubu** , bu şablon tarafından oluşturulan tüm Azure kaynaklarının ait olduğu grubun adıdır. Bu öğretici için, **SQL-ha-RG**kullanın. Daha fazla bilgi için bkz. [Azure Resource Manager’a genel bakış](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Konum** , öğreticinin kaynakları oluşturduğu Azure bölgesidir. Bir Azure bölgesi seçin.

Aşağıdaki ekran görüntüsünde, tamamlanmış bir **temel bilgiler** dikey penceresi verilmiştir:

![Temel](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

**Tamam**'ı tıklatın.

### <a name="domain-and-network-settings"></a>Etki alanı ve ağ ayarları
Bu Azure Galeri şablonu, bir etki alanı ve etki alanı denetleyicileri oluşturur. Ayrıca bir ağ ve iki alt ağ oluşturur. Şablon, mevcut bir etki alanında veya sanal ağda sunucu oluşturamaz. Sonraki adım etki alanı ve ağ ayarlarını yapılandırır.

**Etki alanı ve ağ ayarları** dikey penceresinde, etki alanı ve ağ ayarları için önceden ayarlanmış değerleri gözden geçirin:

* **Orman kök etki alanı adı** , kümeyi barındıran Active Directory etki alanının etki alanı adıdır. Öğretici için **contoso.com**kullanın.
* **Sanal ağ adı** , Azure sanal ağının ağ adıdır. Öğretici için **autohaVNET**kullanın.
* **Etki alanı denetleyicisi alt ağ adı** , sanal ağın etki alanı denetleyicisini barındıran bölümünün adıdır. **Alt ağ-1**kullanın. Bu alt ağ, **10.0.0.0/24**adres önekini kullanır.
* **SQL Server alt ağ adı** , sanal ağın, SQL Server çalıştıran sunucuları ve dosya paylaşma tanığını barındıran bir kısmının adıdır. **Alt ağ-2**kullanın. Bu alt ağ **10.0.1.0/26**adres önekini kullanır.

Azure 'daki sanal ağlar hakkında daha fazla bilgi edinmek için bkz. [sanal ağa genel bakış](../../../virtual-network/virtual-networks-overview.md).  

**Etki alanı ve ağ ayarları** aşağıdaki ekran görüntüsüne benzer şekilde görünmelidir:

![Etki alanı ve ağ ayarları](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Gerekirse, bu değerleri değiştirebilirsiniz. Bu öğreticide, önceden ayarlanmış değerleri kullanın.

Ayarları gözden geçirin ve ardından **Tamam**' a tıklayın.

### <a name="availability-group-settings"></a>Kullanılabilirlik grubu ayarları
**Kullanılabilirlik grubu ayarları**' na, kullanılabilirlik grubu ve dinleyici için önceden ayarlanmış değerleri gözden geçirin.

* **Kullanılabilirlik grubu adı** , kullanılabilirlik grubu için kümelenmiş kaynak adıdır. Bu öğretici için **contoso-AG**kullanın.
* **Kullanılabilirlik grubu dinleyicisi adı** , küme ve iç yük dengeleyici tarafından kullanılır. SQL Server bağlanan istemciler bu adı, veritabanının uygun çoğaltmasına bağlanmak için kullanabilir. Bu öğretici için **contoso-Listener**kullanın.
* **Kullanılabilirlik grubu dinleyicisi bağlantı noktası** SQL Server DINLEYICISININ TCP bağlantı noktasını belirtir. Bu öğreticide, **1433**varsayılan bağlantı noktasını kullanın.

Gerekirse, bu değerleri değiştirebilirsiniz. Bu öğreticide, önceden ayarlanmış değerleri kullanın.  

![kullanılabilirlik grubu ayarları](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

**Tamam**'ı tıklatın.

### <a name="virtual-machine-size-storage-settings"></a>Sanal makine boyutu, depolama ayarları
**VM boyutu, depolama ayarları**üzerinde SQL Server bir sanal makine boyutu seçin ve diğer ayarları gözden geçirin.

* **SQL Server sanal makine boyutu** , SQL Server çalıştıran her iki sanal makinenin boyutudur. İş yükünüz için uygun bir sanal makine boyutu seçin. Bu ortamı öğretici için oluşturuyorsanız, **DS2**kullanın. Üretim iş yükleri için, iş yükünü destekleyebilen bir sanal makine boyutu seçin. Birçok üretim iş yükü **DS4** veya daha büyük gerektirir. Şablon bu boyutta iki sanal makine oluşturur ve her birine SQL Server yüklenir. Daha fazla bilgi için [sanal makine boyutları](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure, SQL Server Enterprise sürümünü yüklüyor. Maliyet, sürüme ve sanal makine boyutuna bağlıdır. Geçerli maliyetler hakkında ayrıntılı bilgi için bkz. [sanal makine fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Etki alanı denetleyicisi sanal makine boyutu** , etki alanı denetleyicilerinin sanal makine boyutudur. Bu öğretici için **D2**kullanın.
* **Dosya paylaşma tanığı sanal makine boyutu** , dosya paylaşma tanığı için sanal makine boyutudur. Bu öğretici için **a1**kullanın.
* **SQL depolama hesabı** , SQL Server verilerini ve işletim sistemi disklerini tutan depolama hesabının adıdır. Bu öğretici için **alwaysonsql01**kullanın.
* **DC depolama hesabı** , etki alanı denetleyicileri için depolama hesabının adıdır. Bu öğretici için **alwaysondc01**kullanın.
* TB 'de **veri disk boyutu SQL Server** , tb 'deki SQL Server veri diskinin boyutudur. 1 ile 4 arasında bir sayı belirtin. Bu öğretici için **1**kullanın.
* **Depolama iyileştirmesi** , SQL Server sanal makineler için belirli depolama yapılandırma ayarlarını iş yükü türüne göre ayarlar. Bu senaryodaki tüm SQL Server sanal makineler, Azure disk konak önbelleği salt okunurdur olarak ayarlanan Premium depolama kullanır. Ayrıca, bu üç ayarlardan birini seçerek iş yükünün SQL Server ayarlarını iyileştirebilirsiniz:

  * **Genel iş yükü** belirli yapılandırma ayarlarını yapar.
  * **İşlem işleme** ayarları, izleme bayrağı 1117 ve 1118 ' dir.
  * **Veri** ambarı, 1117 ve 610 izleme bayrağını ayarlar.

Bu öğretici için **genel iş yükünü**kullanın.

![VM boyut depolama ayarları](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Ayarları gözden geçirin ve ardından **Tamam**' a tıklayın.

#### <a name="a-note-about-storage"></a>Depolama hakkında bir notta
Ek iyileştirmeler SQL Server veri disklerinin boyutuna bağlıdır. Azure, her terabayt veri diski için ek 1 TB Premium depolama alanı ekler. Bir sunucu 2 TB veya daha fazla gerektirdiğinde, şablon her bir SQL Server sanal makinede bir depolama havuzu oluşturur. Depolama havuzu, daha yüksek kapasite, dayanıklılık ve performans sağlamak üzere birden çok disk yapılandırıldığı bir depolama sanallaştırma biçimidir.  Şablon daha sonra depolama havuzunda bir depolama alanı oluşturur ve işletim sistemine tek bir veri diski sunar. Şablon, bu diski SQL Server veri diski olarak belirler. Şablon, aşağıdaki ayarları kullanarak SQL Server için depolama havuzunu tunlar:

* Şeritli boyut, sanal disk için ayırma ayarıdır. İşlemsel iş yükleri 64 KB kullanır. Veri ambarı iş yükleri 256 KB kullanır.
* Dayanıklılık basittir (dayanıklılık yoktur).

> [!NOTE]
> Azure Premium Depolama yerel olarak yedekli olur ve verilerin üç kopyasını tek bir bölgede tutar. bu nedenle, depolama havuzunda ek dayanıklılık gerekli değildir.
>
>

* Sütun sayısı, depolama havuzundaki disk sayısına eşit.

Depolama alanı ve depolama havuzları hakkında daha fazla bilgi için bkz.:

* [Depolama alanlarına genel bakış](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server Yedekleme ve depolama havuzları](https://technet.microsoft.com/library/dn390929.aspx)

SQL Server Configuration en iyi uygulamaları hakkında daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server için En Iyi performans uygulamaları](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>SQL Server ayarları
**SQL Server ayarlar**' da SQL Server sanal makine adı ön ekini, SQL Server sürümünü, SQL Server hizmet hesabını ve parolayı ve SQL otomatik düzeltme eki uygulama bakım zamanlaması ' nı gözden geçirin ve değiştirin.

* **SQL Server adı ön eki** , her SQL Server sanal makine için bir ad oluşturmak üzere kullanılır. Bu öğretici için **SqlServer**kullanın. Şablon SQL Server sanal makineler *SqlServer-0* ve *SqlServer-1*' i adlandırır.
* **SQL Server sürüm** , SQL Server sürümüdür. Bu öğretici için **SQL Server 2014**kullanın. **SQL Server 2012** veya **SQL Server 2016**' yi de seçebilirsiniz.
* **SQL Server hizmet hesabı Kullanıcı adı** , SQL Server hizmeti için etki alanı hesap adıdır. Bu öğretici için **SqlService**kullanın.
* **Parola** SQL Server hizmet hesabının parolasıdır.  Karmaşık bir parola kullanın. Parolayı onaylayın.
* **SQL otomatik düzeltme eki uygulama bakım zamanlaması** , Azure 'un SQL Server 'a otomatik olarak yadığına yönelik haftanın gününü belirler. Bu öğretici için **Pazar**yazın.
* Otomatik düzeltme eki uygulama başladığında Azure bölgesi için **SQL otomatik düzeltme eki başlangıç saati** günün saati.

> [!NOTE]
> Her sanal makine için düzeltme eki uygulama, bir saat kadar aşamalı olarak belirlenir. Hizmetlerin kesintiye uğramasını önlemek için tek seferde yalnızca bir sanal makine düzeltme eki uygulanabilir.
>
>

![SQL Server ayarları](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Ayarları gözden geçirin ve ardından **Tamam**' a tıklayın.

### <a name="summary"></a>Özet
Özet sayfasında, Azure ayarları doğrular. Şablonu da indirebilirsiniz. Özeti gözden geçirin. **Tamam**'ı tıklatın.

### <a name="buy"></a>Satın Al
Bu son dikey pencere, **kullanım koşullarını**ve **Gizlilik ilkesini**içerir. Bu bilgileri gözden geçirin. Azure 'un sanal makineleri ve kullanılabilirlik grubu için tüm diğer gerekli kaynakları oluşturmaya başlaması için hazır olduğunuzda **Oluştur**' a tıklayın.

Azure portal, kaynak grubunu ve tüm kaynakları oluşturur.

## <a name="monitor-deployment"></a>Dağıtımı izle
Azure portal dağıtım ilerlemesini izleyin. Dağıtımı temsil eden bir simge Azure portal panosuna otomatik olarak sabitlenmiştir.

![Azure panosu](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>SQL Server'a bağlanma
SQL Server yeni örnekleri, internet 'e bağlı IP adreslerine sahip sanal makinelerde çalışır. Her bir SQL Server sanal makinesine doğrudan uzak masaüstü (RDP) ekleyebilirsiniz.

RDP 'ye bir SQL Server için aşağıdaki adımları izleyin:

1. Azure portal panosunda, dağıtımın başarılı olduğunu doğrulayın.
2. **Kaynaklar**' a tıklayın.
3. **Kaynaklar** dikey penceresinde, SQL Server çalıştıran sanal makinelerden birinin bilgisayar adı olan **SqlServer-0**' a tıklayın.
4. **SqlServer-0**dikey penceresinde **Bağlan**' a tıklayın. Tarayıcınız uzak bağlantı nesnesini açmak mı yoksa kaydetmek mi istediğinizi sorar. Tıklayın **açık**.
5. **Uzak Masaüstü bağlantısı** , bu uzak bağlantı yayımcısının belirlenemediğini uyarabilir. **Bağlan**'a tıklayın.
6. Windows güvenliği, birincil etki alanı denetleyicisinin IP adresine bağlanmak için kimlik bilgilerinizi girmenizi ister. **Başka bir hesap kullan**' a tıklayın. **Kullanıcı adı**için **contoso\DomainAdmin**yazın. Şablonda yönetici kullanıcı adını ayarladığınızda bu hesabı yapılandırdınız. Şablonu yapılandırdığınızda seçtiğiniz karmaşık parolayı kullanın.
7. **Uzak Masaüstü** , güvenlik sertifikasıyla ilgili sorunlar nedeniyle uzak bilgisayarın kimliğinin doğrulanmadığını uyarabilir. Size güvenlik sertifikası adını gösterir. Öğreticiyi izlediyseniz, ad **SqlServer-0.contoso.com**olur. Yordamı tamamlamak için **Evet**yüklemesini desteklemesi gerekir.

Artık SQL Server sanal makinesine RDP ile bağlanırsınız. SQL Server Management Studio açabilir, SQL Server varsayılan örneğine bağlanabilir ve kullanılabilirlik grubunun yapılandırıldığını doğrulayabilirsiniz.
