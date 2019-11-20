---
title: Azure Güvenlik Duvarı uygulama kurallarını SQL FQDN 'leri ile yapılandırma
description: Bu makalede, Azure Güvenlik Duvarı uygulama kurallarında SQL FQDN 'leri yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 239998f29ac9a578174c5dba547bb24ba0755505
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318182"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Azure Güvenlik Duvarı uygulama kurallarını SQL FQDN 'leri ile yapılandırma

> [!IMPORTANT]
> SQL FQDN 'leri olan Azure Güvenlik Duvarı uygulama kuralları şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Artık, Azure Güvenlik Duvarı uygulama kurallarını SQL FQDN 'leri ile yapılandırabilirsiniz. Bu, sanal ağlarınızdaki erişimi yalnızca belirtilen SQL Server örneklerine sınırlamanıza izin verir.

SQL FQDN 'leri ile trafiği filtreleyebilirsiniz:

- VNET 'ınızdan bir Azure SQL veritabanı veya Azure SQL veri ambarı 'na. Örneğin: Yalnızca *SQL-Server1.Database.Windows.net*erişimine izin verir.
- Şirket içinden Azure SQL yönetilen örneklerine veya sanal ağlarınızdaki SQL IaaS 'ye çalışır.
- Sanal ağlarınız üzerinde çalışan Azure SQL yönetilen örneklerine veya SQL IaaS 'ye bağlı noktadan

Genel Önizleme sırasında SQL FQDN filtrelemesi yalnızca [proxy modunda](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) desteklenir (bağlantı noktası 1433). Varsayılan yeniden yönlendirme modunda SQL kullanıyorsanız, [ağ kurallarının](overview.md#network-traffic-filtering-rules)bir PARÇASı olarak SQL hizmeti etiketini kullanarak erişimi filtreleyebilirsiniz.
SQL IaaS trafiği için varsayılan olmayan bağlantı noktaları kullanırsanız, bu bağlantı noktalarını güvenlik duvarı uygulama kurallarında yapılandırabilirsiniz.

SQL FQDN 'leri olan uygulama kuralları şu anda tüm bölgelerde Azure portal, Azure CLı, REST ve şablonlar aracılığıyla kullanılabilir.

## <a name="configure-using-azure-cli"></a>Azure CLı kullanarak yapılandırma

1. [Azure CLI kullanarak bir Azure Güvenlik Duvarı](deploy-cli.md)dağıtın.
2. Trafiği Azure SQL veritabanı, SQL veri ambarı veya SQL yönetilen örneği 'ne filtrelemeniz durumunda SQL bağlantı modunun **Ara sunucu**olarak ayarlandığından emin olun. SQL bağlantı modunu değiştirme hakkında bilgi edinmek için bkz. [Azure SQL bağlantı mimarisi](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* modu *yeniden yönlendirmeye*kıyasla daha fazla gecikme süresine neden olabilir. Azure 'da bağlanan istemciler için varsayılan olan yeniden yönlendirme modunu kullanmaya devam etmek istiyorsanız, erişimi güvenlik duvarı [ağ KURALLARıNDA](tutorial-firewall-deploy-portal.md#configure-a-network-rule)SQL [hizmeti etiketi](service-tags.md) kullanarak filtreleyebilirsiniz.

3. SQL Server 'a erişim izni vermek için SQL FQDN ile bir uygulama kuralı yapılandırın:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Azure portal kullanarak yapılandırma
1. [Azure CLI kullanarak bir Azure Güvenlik Duvarı](deploy-cli.md)dağıtın.
2. Trafiği Azure SQL veritabanı, SQL veri ambarı veya SQL yönetilen örneği 'ne filtrelemeniz durumunda SQL bağlantı modunun **Ara sunucu**olarak ayarlandığından emin olun. SQL bağlantı modunu değiştirme hakkında bilgi edinmek için bkz. [Azure SQL bağlantı mimarisi](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* modu *yeniden yönlendirmeye*kıyasla daha fazla gecikme süresine neden olabilir. Azure 'da bağlanan istemciler için varsayılan olan yeniden yönlendirme modunu kullanmaya devam etmek istiyorsanız, erişimi güvenlik duvarı [ağ KURALLARıNDA](tutorial-firewall-deploy-portal.md#configure-a-network-rule)SQL [hizmeti etiketi](service-tags.md) kullanarak filtreleyebilirsiniz.
3. Uygulama kuralını uygun protokol, bağlantı noktası ve SQL FQDN 'SI ile ekleyin ve ardından **Kaydet**' i seçin.
   ![SQL FQDN ile uygulama kuralı](media/sql-fqdn-filtering/application-rule-sql.png)
4. Ağ üzerinden trafiği filtreleyen bir sanal makineden SQL 'e erişin. 
5. [Azure Güvenlik Duvarı günlüklerinin](log-analytics-samples.md) trafiğe izin verildiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

SQL proxy ve yeniden yönlendirme modları hakkında bilgi edinmek için bkz. [Azure SQL veritabanı bağlantı mimarisi](../sql-database/sql-database-connectivity-architecture.md).