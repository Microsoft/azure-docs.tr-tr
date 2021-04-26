---
title: SQL Server sanal makinesine bağlanma (Kaynak Yöneticisi) | Microsoft Docs
description: Azure 'da SQL Server sanal makinenize nasıl bağlanacağınızı öğrenin. Bu konu klasik dağıtım modelini kullanır. Senaryolar ağ yapılandırmasına ve istemcinin konumuna göre farklılık gösterir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37cb8f5a2ff0916f53ae50f5750664204ab1ba75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737498"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Azure 'da SQL Server sanal makinesine bağlanma
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Genel Bakış

Bu konu, Azure sanal makinesine (VM) SQL 'e nasıl bağlanabileceğinizi açıklamaktadır. Bazı [genel bağlantı senaryolarını](#connection-scenarios) ele alır ve ardından [portalda bağlantı ayarlarını değiştirmek için adımlar](#change)sağlar. Portalın dışında sorun gidermeniz veya bağlantı yapılandırmanız gerekiyorsa, bu konunun sonundaki [el ile yapılandırma](#manual) konusuna bakın. 

Hem sağlama hem de bağlantı konusunda tam bir anlatım olmasını istiyorsanız bkz. [Azure 'da SQL Server sanal makinesi sağlama](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Bağlantı senaryoları

İstemcinin bir SQL Server VM bağlanma şekli, istemcinin konumuna ve ağ yapılandırmasına bağlı olarak farklılık gösterir.

Azure portal SQL Server VM sağlarsanız, **SQL bağlantısı** türünü belirtme seçeneğiniz vardır.

![Sağlama sırasında genel SQL bağlantısı seçeneği](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

Bağlantı seçenekleriniz şunlardır:

| Seçenek | Açıklama |
|---|---|
| **Genel** | İnternet üzerinden SQL Server 'e bağlanın. |
| **Özel** | Aynı sanal ağda SQL Server bağlanın. |
| **Yerel** | Aynı sanal makinede yerel olarak SQL Server bağlantı. | 

Aşağıdaki bölümlerde **ortak** ve **özel** seçenekler daha ayrıntılı açıklanmıştır.

## <a name="connect-to-sql-server-over-the-internet"></a>Internet üzerinden SQL Server bağlanma

SQL Server veritabanı altyapısına internet 'ten bağlanmak istiyorsanız, sağlama sırasında portalda **SQL bağlantı** türü için **genel** ' i seçin. Portal aşağıdaki adımları otomatik olarak yapar:

* SQL Server için TCP/IP protokolünü etkinleştirilir.
* SQL Server TCP bağlantı noktasını açmak için bir güvenlik duvarı kuralı yapılandırır (varsayılan 1433).
* SQL Server kimlik doğrulamasını, genel erişim için gereklidir.
* VM 'deki ağ güvenlik grubunu SQL Server bağlantı noktasındaki tüm TCP trafiğine yapılandırır.

> [!IMPORTANT]
> SQL Server Developer ve Express sürümleri için sanal makine görüntüleri TCP/IP protokolünü otomatik olarak etkinleştirmez. Geliştirici ve Express sürümleri için, VM 'yi oluşturduktan sonra [TCP/IP protokolünü el ile etkinleştirmek](#manualtcp) üzere SQL Server Yapılandırma Yöneticisi kullanmanız gerekir.

İnternet erişimi olan herhangi bir istemci, sanal makinenin genel IP adresini veya söz konusu IP adresine atanmış herhangi bir DNS etiketini belirterek SQL Server örneğine bağlanabilir. SQL Server bağlantı noktası 1433 ise bağlantı dizesinde belirtmeniz gerekmez. Aşağıdaki bağlantı dizesi, SQL kimlik doğrulaması kullanan bir DNS etiketiyle SQL VM 'ye bağlanır `sqlvmlabel.eastus.cloudapp.azure.com` (genel IP adresini de kullanabilirsiniz).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Bu dize Internet üzerinden istemciler için bağlantıya izin verse de, bu, herkesin SQL Server örneğine bağlanabildiği anlamına gelmez. Dış istemciler, doğru Kullanıcı adını ve parolayı kullanmalıdır. Bununla birlikte, ek güvenlik için, bilinen 1433 numaralı bağlantı noktasından kaçınabilirsiniz. Örneğin, bağlantı noktası 1500 ' i dinlemek için SQL Server yapılandırmak ve uygun güvenlik duvarı ve ağ güvenlik grubu kuralları oluşturmak için, bağlantı noktası numarasını sunucu adına ekleyerek bağlanabilirsiniz. Aşağıdaki örnek, sunucu adına **1500**, özel bir bağlantı noktası numarası ekleyerek öncekini değiştirir:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> SQL Server sanal makine üzerinde Internet üzerinden sorguladığınızda, Azure veri merkezindeki tüm giden veriler [giden veri aktarımları için normal fiyatlandırmaya](https://azure.microsoft.com/pricing/details/data-transfers/)tabidir.

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Bir sanal ağ içinde SQL Server bağlanma

Portalda **SQL bağlantı** türü için **özel** ' i seçtiğinizde, Azure ayarların çoğunu **ortak** ile aynı şekilde yapılandırır. Bunun farkı, SQL Server bağlantı noktasında dış trafiğe izin veren bir ağ güvenlik grubu kuralı olmaması (varsayılan 1433).

> [!IMPORTANT]
> SQL Server Developer ve Express sürümleri için sanal makine görüntüleri TCP/IP protokolünü otomatik olarak etkinleştirmez. Geliştirici ve Express sürümleri için, VM 'yi oluşturduktan sonra [TCP/IP protokolünü el ile etkinleştirmek](#manualtcp) üzere SQL Server Yapılandırma Yöneticisi kullanmanız gerekir.

Özel bağlantı, genellikle çeşitli senaryolara izin veren bir [sanal ağ](../../../virtual-network/virtual-networks-overview.md)ile birlikte kullanılır. Aynı sanal ağdaki VM 'leri, bu VM 'Ler farklı kaynak gruplarında bulunsa bile bağlayabilirsiniz. [Siteden sıteye VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md)ile, şirket içi ağlar ve makinelerle VM 'leri bağlayan bir karma mimari de oluşturabilirsiniz.

Sanal ağlar Ayrıca, Azure VM 'lerinizi bir etki alanına katlamada sağlar. Bu, SQL Server için Windows kimlik doğrulaması kullanmanın tek yoludur. Diğer bağlantı senaryoları, Kullanıcı adları ve parolalarla SQL kimlik doğrulaması gerektirir.

Sanal ağınızda DNS yapılandırdığınız varsayıldığında, bağlantı dizesinde SQL Server VM bilgisayar adını belirterek SQL Server örneğinize bağlanabilirsiniz. Aşağıdaki örnek ayrıca Windows kimlik doğrulamasının yapılandırıldığını ve kullanıcıya SQL Server örneğine erişim verildiğini varsayar.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> SQL bağlantı ayarlarını değiştir

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Azure portal SQL Server sanal makinenizin bağlantı ayarlarını değiştirebilirsiniz.

1. Azure portal, **SQL sanal makineler**' i seçin.

2. SQL Server VM seçin.

3. **Ayarlar** altında **güvenlik**' i seçin.

4. **SQL bağlantı düzeyini** gerekli ayarınızla değiştirin. İsteğe bağlı olarak, SQL Server bağlantı noktasını veya SQL kimlik doğrulaması ayarlarını değiştirmek için bu alanı kullanabilirsiniz.

   ![SQL bağlantısını değiştir](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Güncelleştirmenin tamamlanabilmesi için birkaç dakika bekleyin.

   ![SQL VM güncelleştirme bildirimi](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Geliştirici ve Express sürümleri için TCP/IP 'yi etkinleştirme

SQL Server bağlantı ayarlarını değiştirirken Azure, SQL Server Developer ve Express sürümleri için TCP/IP protokolünü otomatik olarak etkinleştirmez. Aşağıdaki adımlarda, uzaktan IP adresiyle bağlanabilmeniz için TCP/IP’yi el ile nasıl etkinleştirebileceğiniz açıklanmıştır.

İlk olarak, uzak masaüstü ile SQL Server sanal makinesine bağlanın.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Sonra TCP/IP protokolünü **SQL Server Yapılandırma Yöneticisi** etkinleştirin.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>SSMS ile bağlanma

Aşağıdaki adımlarda, Azure VM 'niz için isteğe bağlı bir DNS etiketi oluşturma ve SQL Server Management Studio (SSMS) ile bağlanma işlemlerinin nasıl yapılacağı gösterilmektedir.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> El ile yapılandırma ve sorun giderme

Portal otomatik olarak bağlantı yapılandırma seçenekleri sağlasa da, bağlantının el ile nasıl yapılandırılacağını öğrenmek yararlı olur. Gereksinimleri anlamak sorun gidermeye da yardımcı olabilir.

Aşağıdaki tabloda, Azure VM 'de SQL Server bağlanma gereksinimleri listelenmektedir.

| Gereksinim | Açıklama |
|---|---|
| [SQL Server kimlik doğrulama modunu etkinleştir](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Sanal ağ üzerinde Active Directory yapılandırmadığınız takdirde VM 'ye uzaktan bağlanmak için SQL Server kimlik doğrulaması gerekir. |
| [SQL oturum açma oluştur](/sql/relational-databases/security/authentication-access/create-a-login) | SQL kimlik doğrulaması kullanıyorsanız, hedef veritabanınıza yönelik izinlere de sahip olan bir Kullanıcı adı ve parolayla SQL oturum açma gerekir. |
| [TCP/IP protokolünü etkinleştir](#manualtcp) | SQL Server TCP üzerinden bağlantılara izin vermelidir. |
| [SQL Server bağlantı noktası için güvenlik duvarı kuralını etkinleştir](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | VM 'deki güvenlik duvarı SQL Server bağlantı noktasında gelen trafiğe izin vermelidir (varsayılan 1433). |
| [TCP 1433 için bir ağ güvenlik grubu kuralı oluşturma](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Internet üzerinden bağlanmak istiyorsanız, VM 'nin SQL Server bağlantı noktasında trafik almasına izin vermelisiniz (varsayılan 1433). Yalnızca yerel ve sanal ağ bağlantılarında bu için gerekli değildir. Azure portal için gereken tek adım budur. |

> [!TIP]
> Yukarıdaki tablodaki adımlar, portalda bağlantı yapılandırdığınızda sizin için yapılır. Bu adımları yalnızca yapılandırmanızı doğrulamak veya SQL Server için el ile bağlantı ayarlamak için kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu bağlantı adımlarıyla birlikte sağlama talimatlarını görmek için bkz. [Azure 'da SQL Server sanal makinesi sağlama](create-sql-vm-portal.md).

Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer konular için bkz. [Azure sanal makinelerinde SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md).