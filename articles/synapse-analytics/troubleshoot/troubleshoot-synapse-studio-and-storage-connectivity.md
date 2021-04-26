---
title: Synapse Studio ve depolama alanı arasındaki bağlantı sorunlarını giderme
description: Synapse Studio ve depolama alanı arasındaki bağlantı sorunlarını giderme
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d5f79131608315f7e1c05cbfc0117300eea6c511
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566282"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Azure Synapse Analytics Synapse Studio ile depolama alanı arasındaki bağlantı sorunlarını giderme

SYNAPSE Studio 'da, bağlantılı depolarınızda bulunan veri kaynaklarını keşfedebilirsiniz. Bu kılavuz, veri kaynaklarınıza erişmeye çalışırken bağlantı sorunlarını çözmenize yardımcı olur. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Büyük/küçük harf #1: depolama hesabı uygun izinlere sahip değil

Depolama hesabınız uygun izinlere sahip değilse, SYNAPSE Studio 'da "Data"--> "Linked" ile depolama yapısını genişletemeyebilirsiniz. Aşağıdaki sorun belirti ekran görüntüsüne bakın. 

Ayrıntılı hata iletisi değişebilir, ancak hata iletisinin genel anlamı şu şekilde olabilir: "Bu istek bu işlemi gerçekleştirmek için yetkilendirilmemiş.".

Bağlantılı depolama düğümünde:  
![Depolama bağlantısı sorunu 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

Depolama kapsayıcısı düğümünde:  
![Depolama bağlantı sorunu 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**Çözüm**: hesabınızı uygun role atamak için bkz [. blob ve kuyruk verilerine erişim için Azure rolü atamak üzere Azure Portal kullanma](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Büyük/küçük harf #2: istek depolama sunucusuna gönderilemedi

"Data" (SYNAPSE Studio 'daki "Linked" > depolama yapısını genişletmek için oku seçerken, "REQUEST_SEND_ERROR" sorununu sol bölmede görebilirsiniz. Aşağıdaki sorun belirti ekran görüntüsüne bakın:

Bağlantılı depolama düğümünde:  
![Depolama bağlantısı sorunu 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

Depolama kapsayıcısı düğümünde:  
![Depolama bağlantı sorunu 2A](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Bu sorunun arkasında birkaç olası neden olabilir:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>Depolama kaynağı bir sanal ağın arkasında ve bir depolama özel uç noktasının yapılandırılması gerekiyor

**Çözüm**: Bu durumda, depolama hesabınız için depolama özel uç noktasını yapılandırmanız gerekir. VNet için depolama özel uç noktasının nasıl yapılandırılacağı hakkında bilgi için bkz. [Azure Portal kullanarak blob ve sıra verilerine erişim için bir Azure rolü atama](../security/how-to-connect-to-workspace-from-restricted-network.md).

\<storage-account-name\>Depolama özel uç noktasının yapılandırıldıktan sonra bağlantıyı denetlemek için "Nslookup. DFS.Core.Windows.net" komutunu kullanabilirsiniz. Şuna benzer bir dize döndürmelidir: " \<storage-account-name\> . Privatelink.DFS.Core.Windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>Depolama kaynağı vNet 'in arkasında değil, ancak yapılandırılmış güvenlik duvarı nedeniyle blob hizmeti (Azure AD) uç noktasına erişilemiyor

**Çözüm**: Bu durumda, Azure Portal depolama hesabınızı açmanız gerekir. Sol gezinti bölmesinde, **destek + sorun giderme** ' ye gidin ve **BLOB HIZMETI (Azure AD)** bağlantı durumunu denetlemek için **bağlantı denetimi** ' ni seçin. Erişilebilir değilse, depolama hesabınız sayfasında **güvenlik duvarları ve sanal ağlar** yapılandırmasını denetlemek için yükseltilen kılavuzu izleyin. Depolama güvenlik duvarları hakkında daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Denetlenecek diğer sorunlar 

* Eriştiğiniz depolama kaynağı Azure Data Lake Storage 2. ve aynı anda bir güvenlik duvarı ve vNet 'in arkasında (depolama özel uç noktası yapılandırılmış).
* Eriştiğiniz kapsayıcı kaynağı silinmiş veya yok.
* Kesişen kiracı: kullanıcının oturum açmak için kullandığı çalışma alanı kiracısı, depolama hesabının kiracısıyla aynı değil. 


## <a name="next-steps"></a>Sonraki adımlar
Yukarıdaki adımlar sorununuzu çözmeye yardımcı değilse, [bir destek bileti oluşturun](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).