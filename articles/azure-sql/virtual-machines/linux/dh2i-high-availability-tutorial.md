---
title: Linux tabanlı Azure sanal makinelerinde çalışan DH2i DxEnterprise ile Always on kullanılabilirlik grubu kurulumu
description: Linux üzerinde SQL Server Azure sanal makinelerinde bir kullanılabilirlik grubuyla yüksek kullanılabilirlik elde etmek için DH2i DxEnterprise 'ı Küme Yöneticisi olarak kullanın
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 56002aaa977b94b0fabee4f17343f483706eb77d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449435"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Öğretici-Linux tabanlı Azure sanal makinelerinde çalışan DH2i DxEnterprise ile üç düğümlü her zaman açık kullanılabilirlik grubu kurma

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu öğreticide, Linux tabanlı Azure sanal makinelerinde (VM 'Ler) çalışan DH2i DxEnterprise ile SQL Server Always on kullanılabilirlik grubunun nasıl yapılandırılacağı açıklanmaktadır. 

DxEnterprise hakkında daha fazla bilgi için bkz. [DH2i dxenterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Microsoft veri hareketini, kullanılabilirlik grubunu ve SQL Server bileşenlerini destekler. Küme ve çekirdek yönetimi için DH2i DxEnterprise kümesinin belgeleriyle ilgili destek için DH2i ile iletişim kurun.
 

Bu öğretici aşağıdaki adımlardan oluşur:

> [!div class="checklist"]
> * Kullanılabilirlik grubunun parçası olacak tüm Azure sanal makinelerine (VM 'Ler) SQL Server yükler.
> * DxEnterprise 'ı tüm VM 'Lere yükleyip DxEnterprise kümesini yapılandırın.
> * Yük devretme desteği ve yüksek kullanılabilirlik sağlamak için sanal konakları oluşturun, kullanılabilirlik grubuna bir kullanılabilirlik grubu ve veritabanı ekleyin.
> * Kullanılabilirlik grubu dinleyicisi için Iç Azure Yük dengeleyiciyi oluşturun (isteğe bağlı).
> * El ile veya otomatik yük devretme gerçekleştirin.

Bu öğreticide, [Dxadmin istemci kullanıcı arabirimini](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/)kullanarak bir dxenterprise kümesi ayarlayacağız. İsteğe bağlı olarak, [Dxclı](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) komut satırı arabirimini kullanarak da kümeyi ayarlayabilirsiniz. Bu örnek için dört VM kullandık. Bu sanal makinelerin üçü Ubuntu 18,04 çalıştırıyorsa ve üç düğümlü kümenin bir parçasıdır. Dördüncü VM, kümeyi yönetmek ve yapılandırmak için DxAdmin aracıyla Windows 10 çalıştırıyorsa.

## <a name="prerequisites"></a>Önkoşullar

- Azure 'da dört VM oluşturun. Linux tabanlı sanal makineler oluşturmak için [hızlı başlangıç: Azure Portal Linux sanal makinesi oluşturma](../../../virtual-machines/linux/quick-create-portal.md) makalesini izleyin. Benzer şekilde, Windows tabanlı sanal makine oluşturmak için, Azure portal makalesinde [hızlı başlangıç: Windows sanal makinesi oluşturma](../../../virtual-machines/windows/quick-create-portal.md) makalesini izleyin.
- Kümenin parçası olacak tüm Linux tabanlı sanal makinelere .NET 3,1 ' i yükler. Seçtiğiniz Linux işletim sistemine bağlı olarak [burada](/dotnet/core/install/linux) belgelenen yönergeleri izleyin.
- Kullanılabilirlik grubu yönetim özellikleri etkin olan geçerli bir DxEnterprise lisansı gerekecektir. Daha fazla bilgi için bkz. ücretsiz bir deneme elde etme hakkında [Dxenterprise ücretsiz deneme sürümü](https://dh2i.com/trial/) .

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Kullanılabilirlik grubunun parçası olacak tüm Azure VM 'lerine SQL Server 'yi yükler

Bu öğreticide, kullanılabilirlik grubunu çalıştıran üç düğümlü Linux tabanlı kümeyi ayarlarız. Linux 'ta Linux platformunuzun seçimine bağlı olarak [SQL Server yükleme](/sql/linux/sql-server-linux-overview#install) belgelerini izleyin. Ayrıca, bu öğretici için [SQL Server araçlarını](/sql/linux/sql-server-linux-setup-tools) yüklemenizi öneririz.
 
> [!NOTE]
> Seçtiğiniz Linux işletim sisteminin hem DH2i DxEnterprise tarafından desteklenen ortak bir dağıtım olduğundan emin olun [(bkz. en düşük sistem gereksinimleri bölümü)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) ve [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> Bu örnekte, hem DH2i DxEnterprise hem de Microsoft SQL Server tarafından desteklenen Ubuntu 18,04 kullanılır.

Bu öğretici için, bu düğüm kümenin bir parçası olmadığı ve yalnızca DxAdmin kullanılarak kümeyi yönetmek için kullanılan SQL Server Windows sanal makinesine yüklenemedik.

Bu adımı tamamladıktan sonra, kullanılabilirlik grubuna katılacak üç Linux tabanlı VM 'ye SQL Server ve [SQL Server araçlarına](/sql/linux/sql-server-linux-setup-tools) (isteğe bağlı olarak) sahip olmanız gerekir.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>DxEnterprise 'ı tüm VM 'Lere yükleyip kümeyi yapılandırın

Bu adımda, Linux için DH2i DxEnterprise 'ı üç Linux VM 'ye yükleyeceğiz. Aşağıdaki tabloda, her bir sunucunun kümede oynadığı rol açıklanmaktadır:

| VM sayısı | DH2i DxEnterprise rolü | Microsoft SQL Server kullanılabilirlik grubu çoğaltma rolü |
|--|--|--|
| 1 | Küme düğümü-Linux tabanlı | Birincil |
| 1 | Küme düğümü-Linux tabanlı | İkincil-zaman uyumlu tamamlama |
| 1 | Küme düğümü-Linux tabanlı | İkincil-zaman uyumlu tamamlama |
| 1 | DxAdmin Istemcisi | NA |


Üç Linux tabanlı düğüme DxEnterprise yüklemek için, seçtiğiniz Linux işletim sistemini temel alan DH2i DxEnterprise belgelerini izleyin. Aşağıda listelenen yöntemlerden birini kullanarak DxEnterprise 'ı yüklersiniz.

- **Ubuntu**
    - [Depo yükleme Hızlı Başlangıç Kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Uzantı Hızlı Başlangıç Kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Market görüntü Hızlı Başlangıç Kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Depo yükleme Hızlı Başlangıç Kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Uzantı Hızlı Başlangıç Kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Market görüntü Hızlı Başlangıç Kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Windows VM 'ye yalnızca DxAdmin istemci aracını yüklemek için, [Dxadmin istemci kullanıcı arabirimi hızlı başlangıç kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/)izleyin.

Bu adımdan sonra, Linux VM 'lerinde DxEnterprise kümesinin ve Windows Istemci makinesinde DxAdmin istemcisinin yüklü olması gerekir. 

> [!NOTE]
> Otomatik yük devretmeyi etkinleştirmek için [burada](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) açıklandığı gibi, düğümden birinin *yalnızca yapılandırma modu* olarak eklendiği üç düğüm kümesi de oluşturabilirsiniz. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Yük devretme desteği ve yüksek kullanılabilirlik sağlamak için sanal konakları oluşturun

Bu adımda, bir sanal ana bilgisayar, kullanılabilirlik grubu oluşturacağız ve sonra DxAdmin Kullanıcı arabirimini kullanarak bir veritabanı ekleyeceğiz.   

> [!NOTE]
> Bu adım sırasında her zaman açık ' i etkinleştirmek için SQL Server örnekleri yeniden başlatılır. 

Yukarıdaki adımda oluşturulan kümeye bağlanmak için DxAdmin çalıştıran Windows istemci makinesine bağlanın. Her zaman açık ' i etkinleştirmek ve sanal konak ve kullanılabilirlik grubunu oluşturmak için, [MSSQL kullanılabilirlik gruplarında belgelenen adımları DxAdmin ile](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) izleyin. 

> [!TIP]
> Veritabanlarını eklemeden önce veritabanının SQL Server birincil örneğinde oluşturulduğundan ve yedeklendiğinden emin olun.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Dinleyici için Iç Azure Yük dengeleyiciyi oluşturma (isteğe bağlı)

İsteğe bağlı bu adımda, kullanılabilirlik grubu dinleyicilerinin IP adreslerini tutan Azure Yük dengeleyiciyi oluşturup yapılandırabilirsiniz. Azure Load Balancer hakkında daha fazla bilgi için [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)başvurun. Azure yük dengeleyici ve kullanılabilirlik grubu dinleyicisini DxAdmin kullanarak yapılandırmak için DxEnterprise [Azure Load Balancer hızlı başlangıç kılavuzu](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)izleyin.

Bu adımdan sonra, oluşturulup Iç Azure yük dengeleyiciye eşlenmiş bir kullanılabilirlik grubu dinleyicisine sahip olmanız gerekir.

## <a name="test-manual-or-automatic-failover"></a>El ile veya otomatik yük devretme testi

Otomatik yük devretme testi için devam edebilir ve birincil çoğaltmayı (Azure portal sanal makineyi devre dışı bırakabilirsiniz) getirebilirsiniz. Bu, birincil düğümün ani kullanım dışı kalması için çoğaltma yapar. Beklenen davranış:
- Küme Yöneticisi, kullanılabilirlik grubundaki ikincil çoğaltmalardan birini birincil olarak yükseltir.
- Başarısız birincil çoğaltma, yedeklendikten sonra otomatik olarak kümeye katılır. Küme Yöneticisi onu ikincil çoğaltmaya yükseltir.

 
Ayrıca, aşağıdaki açıklanan adımları izleyerek el ile yük devretme işlemi gerçekleştirebilirsiniz:

1. DxAdmin aracılığıyla kümeye bağlanma   
1. Kullanılabilirlik grubu için sanal konağı Genişlet
1. Hedef düğüme/ikincil çoğaltmaya sağ tıklayın ve yük devretmeyi başlatmak için **üyede barındırmayı Başlat** ' ı seçin. 

DxEnterprise içindeki daha fazla işlem hakkında daha fazla bilgi için, [Dxenterprise Yönetici Kılavuzu](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) ve [Dxenterprise dxclı Kılavuzu 'na](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) erişin

## <a name="next-steps"></a>Sonraki Adımlar

- [Linux 'Ta kullanılabilirlik grupları](/sql/linux/sql-server-linux-availability-group-overview) hakkında daha fazla bilgi edinin
- [Hızlı başlangıç: Azure portal Linux sanal makinesi oluşturma](../../../virtual-machines/linux/quick-create-portal.md)
- [Hızlı başlangıç: Azure portalda Windows sanal makinesi oluşturma](../../../virtual-machines/windows/quick-create-portal.md)
- [Linux üzerinde SQL Server 2019 için desteklenen platformlar](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)