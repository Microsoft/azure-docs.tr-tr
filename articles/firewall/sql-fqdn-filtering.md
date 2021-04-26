---
title: SQL FQDN’lerle Azure Güvenlik Duvarı uygulama kurallarını yapılandırma
description: Bu makalede, Azure Güvenlik Duvarı uygulama kurallarında SQL FQDN 'leri yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: c65f32cc3ce56ddf3fd235de8c002528e7a3cebd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791451"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL FQDN’lerle Azure Güvenlik Duvarı uygulama kurallarını yapılandırma

Artık, Azure Güvenlik Duvarı uygulama kurallarını SQL FQDN 'leri ile yapılandırabilirsiniz. Bu, sanal ağlarınızdaki erişimi yalnızca belirtilen SQL Server örneklerine sınırlamanıza izin verir.

SQL FQDN'leri ile şu trafiği filtreleyebilirsiniz:

- Sanal ağlarınızdaki Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e. Örneğin: yalnızca *SQL-Server1.Database.Windows.net* erişimine izin verin.
- Şirket içinden Azure SQL yönetilen örneklerine veya sanal ağlarınızdaki SQL IaaS 'ye çalışır.
- Sanal ağlarınız üzerinde çalışan Azure SQL yönetilen örneklerine veya SQL IaaS 'ye bağlı noktadan

SQL FQDN filtrelemesi yalnızca [proxy modunda](../azure-sql/database/connectivity-architecture.md#connection-policy) desteklenir (bağlantı noktası 1433). Varsayılan yeniden yönlendirme modunda SQL kullanıyorsanız, [ağ kurallarının](features.md#network-traffic-filtering-rules)bir PARÇASı olarak SQL hizmeti etiketini kullanarak erişimi filtreleyebilirsiniz.
SQL IaaS trafiği için varsayılanın dışında bağlantı noktaları kullanıyorsanız bu bağlantı noktalarını güvenlik duvarı uygulama kurallarından yapılandırabilirsiniz.

## <a name="configure-using-azure-cli"></a>Azure CLı kullanarak yapılandırma

1. [Azure CLI kullanarak bir Azure Güvenlik Duvarı](deploy-cli.md)dağıtın.
2. Trafiği Azure SQL veritabanı, Azure SYNAPSE Analytics veya SQL yönetilen örneği 'ne filtrelemeniz durumunda SQL bağlantı modunun **Ara sunucu** olarak ayarlandığından emin olun. SQL bağlantı modunu değiştirme hakkında bilgi edinmek için bkz. [Azure SQL bağlantı ayarları](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > SQL *proxy* modu *yeniden yönlendirmeye* kıyasla daha fazla gecikme süresine neden olabilir. Azure 'da bağlanan istemciler için varsayılan olan yeniden yönlendirme modunu kullanmaya devam etmek istiyorsanız, erişimi güvenlik duvarı [ağ KURALLARıNDA](tutorial-firewall-deploy-portal.md#configure-a-network-rule)SQL [hizmeti etiketi](service-tags.md) kullanarak filtreleyebilirsiniz.

3. SQL Server 'a erişim sağlamak için SQL FQDN kullanarak bir uygulama kuralıyla yeni bir kural koleksiyonu oluşturun:

   ```azurecli
    az extension add -n azure-firewall
    
    az network firewall application-rule create \ 
    -g FWRG \
    --f azfirewall \ 
    --c sqlRuleCollection \
    --priority 1000 \
    --action Allow \
    --name sqlRule \
    --protocols mssql=1433 \
    --source-addresses 10.0.0.0/24 \
    --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-azure-powershell"></a>Azure PowerShell kullanarak yapılandırma

1. [Azure PowerShell kullanarak bir Azure Güvenlik Duvarı](deploy-ps.md)dağıtın.
2. Trafiği Azure SQL veritabanı, Azure SYNAPSE Analytics veya SQL yönetilen örneği 'ne filtrelemeniz durumunda SQL bağlantı modunun **Ara sunucu** olarak ayarlandığından emin olun. SQL bağlantı modunu değiştirme hakkında bilgi edinmek için bkz. [Azure SQL bağlantı ayarları](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > SQL *proxy* modu *yeniden yönlendirmeye* kıyasla daha fazla gecikme süresine neden olabilir. Azure 'da bağlanan istemciler için varsayılan olan yeniden yönlendirme modunu kullanmaya devam etmek istiyorsanız, erişimi güvenlik duvarı [ağ KURALLARıNDA](tutorial-firewall-deploy-portal.md#configure-a-network-rule)SQL [hizmeti etiketi](service-tags.md) kullanarak filtreleyebilirsiniz.

3. SQL Server 'a erişim sağlamak için SQL FQDN kullanarak bir uygulama kuralıyla yeni bir kural koleksiyonu oluşturun:

   ```azurepowershell
   $AzFw = Get-AzFirewall -Name "azfirewall" -ResourceGroupName "FWRG"
    
   $sqlRule = @{
      Name          = "sqlRule"
      Protocol      = "mssql:1433" 
      TargetFqdn    = "sql-serv1.database.windows.net"
      SourceAddress = "10.0.0.0/24"
   }
    
   $rule = New-AzFirewallApplicationRule @sqlRule
    
   $sqlRuleCollection = @{
      Name       = "sqlRuleCollection" 
      Priority   = 1000 
      Rule       = $rule
      ActionType = "Allow"
   }
    
   $ruleCollection = New-AzFirewallApplicationRuleCollection @sqlRuleCollection
    
   $Azfw.ApplicationRuleCollections.Add($ruleCollection)    
   Set-AzFirewall -AzureFirewall $AzFw    
   ```

## <a name="configure-using-the-azure-portal"></a>Azure portalını kullanarak yapılandırma
1. [Azure CLI kullanarak bir Azure Güvenlik Duvarı](deploy-cli.md)dağıtın.
2. Trafiği Azure SQL veritabanı, Azure SYNAPSE Analytics veya SQL yönetilen örneği 'ne filtrelemeniz durumunda SQL bağlantı modunun **Ara sunucu** olarak ayarlandığından emin olun. SQL bağlantı modunu değiştirme hakkında bilgi edinmek için bkz. [Azure SQL bağlantı ayarları](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).  

   > [!NOTE]
   > SQL *proxy* modu *yeniden yönlendirmeye* kıyasla daha fazla gecikme süresine neden olabilir. Azure 'da bağlanan istemciler için varsayılan olan yeniden yönlendirme modunu kullanmaya devam etmek istiyorsanız, erişimi güvenlik duvarı [ağ KURALLARıNDA](tutorial-firewall-deploy-portal.md#configure-a-network-rule)SQL [hizmeti etiketi](service-tags.md) kullanarak filtreleyebilirsiniz.
3. Uygulama kuralını uygun protokol, bağlantı noktası ve SQL FQDN 'SI ile ekleyin ve ardından **Kaydet**' i seçin.
   ![SQL FQDN ile uygulama kuralı](media/sql-fqdn-filtering/application-rule-sql.png)
4. Ağ üzerinden trafiği filtreleyen bir sanal makineden SQL 'e erişin. 
5. [Azure Güvenlik Duvarı günlüklerinin](./firewall-workbook.md) trafiğe izin verildiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

SQL proxy ve yeniden yönlendirme modları hakkında bilgi edinmek için bkz. [Azure SQL veritabanı bağlantı mimarisi](../azure-sql/database/connectivity-architecture.md).