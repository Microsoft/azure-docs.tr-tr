---
title: Ağları Azure Otomasyonu 'na güvenli bir şekilde bağlamak için Azure özel bağlantısı 'nı kullanın
description: Ağları Azure Otomasyonu 'na güvenli bir şekilde bağlamak için Azure özel bağlantısı 'nı kullanın
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28f4c314b65a27c71c7620ff5941463b1ea68b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831465"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Ağları Azure Otomasyonu 'na güvenli bir şekilde bağlamak için Azure özel bağlantısı 'nı kullanın

Azure Özel Uç Noktası sizi Azure Özel Bağlantı ile desteklenen bir hizmete özel olarak ve güvenle bağlayan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak Otomasyon hizmetini sanal ağınıza etkin bir şekilde getiriyor. VNet ve Otomasyon hesabı arasındaki ağ trafiği, VNet üzerinden ve Microsoft omurga ağındaki özel bir bağlantı üzerinden, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır.

Örneğin, giden internet erişimini devre dışı bıraktığınız bir sanal ağınız var. Ancak, Otomasyon hesabınıza özel olarak erişmek ve karma runbook çalışanları üzerinde Web kancaları, durum yapılandırması ve Runbook işleri gibi Otomasyon özelliklerini kullanmak istersiniz. Üstelik, kullanıcıların yalnızca VNET aracılığıyla Otomasyon hesabına erişimi olmasını istiyorsunuz.  Özel uç nokta dağıtımı bu hedeflere erişir.

Bu makalede, ' nin ne zaman kullanılacağı ve otomasyon hesabınızla özel bir uç noktanın nasıl ayarlanacağı ele alınmaktadır.

![Azure Otomasyonu için özel bağlantıya kavramsal genel bakış](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Azure Otomasyonu ile özel bağlantı desteği yalnızca Azure ticari ve Azure ABD kamu bulutlarında kullanılabilir.

## <a name="advantages"></a>Avantajlar

Özel bağlantıyla birlikte şunları yapabilirsiniz:

- Herkese açık bir ağ erişimi açmadan özel olarak Azure Otomasyonu 'na bağlanın.
- Herhangi bir genel ağ erişimi açmadan Azure Izleyici Log Analytics çalışma alanına özel olarak bağlanın.

    >[!NOTE]
    >Automation hesabınız iş verilerini iletmek için bir Log Analytics çalışma alanına bağlanmışsa ve Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter, durum yapılandırması veya VM'leri çalışma saatleri dışında başlat/durdur gibi özellikleri etkinleştirdiğinizde Log Analytics çalışma alanınız için ayrı bir özel uç nokta gereklidir. Azure Izleyici için özel bağlantı hakkında daha fazla bilgi için bkz. Azure [özel bağlantı kullanarak ağları güvenli bir şekilde Azure izleyici 'ye bağlama](../../azure-monitor/logs/private-link-security.md).

- Otomasyon verilerinize yalnızca yetkili özel ağlar aracılığıyla erişildiğinden emin olun.
- Özel uç noktanızla bağlanan Azure Otomasyonu kaynağınızı tanımlayarak özel ağlarınızdan veri alımını önleyin.
- Özel şirket içi ağınızı ExpressRoute ve özel bağlantı kullanarak Azure Otomasyonu 'na güvenle bağlayın.
- Tüm trafiği Microsoft Azure omurga ağı içinde saklayın.

Daha fazla bilgi için bkz.  [özel bağlantının önemli avantajları](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Sınırlamalar

- Özel bağlantının geçerli uygulamasında Otomasyon hesabı bulutu işleri, Özel uç nokta kullanılarak güvenliği sağlanmış Azure kaynaklarına erişemez. Örneğin, Azure Key Vault, Azure SQL, Azure depolama hesabı vb. Bunun geçici çözümü için bunun yerine [karma Runbook Worker](../automation-hybrid-runbook-worker.md) kullanın.
- Windows veya Linux için [Log Analytics aracısının](../../azure-monitor/agents/log-analytics-agent.md) en son sürümünü kullanmanız gerekir.
- [Log Analytics ağ geçidi](../../azure-monitor/agents/gateway.md) özel bağlantıyı desteklemez.

## <a name="how-it-works"></a>Nasıl çalışır?

Azure Otomasyonu özel bağlantısı, bir veya daha fazla özel uç noktayı (ve bu nedenle içerdikleri sanal ağları) Otomasyon hesabı kaynağına bağlar. Bu uç noktalar, bir runbook 'u başlatmak için Web kancaları kullanan makinelerdir, karma Runbook Worker rolünü barındıran makineler ve Istenen durum yapılandırması (DSC) düğümleri.

Otomasyon için özel uç noktalar oluşturduktan sonra, genel kullanıma açık Otomasyon URL 'lerinin her biri sanal ağınızdaki bir özel uç noktaya eşlenir. Siz veya bir makine doğrudan Otomasyon URL 'Lerine bağlantı sağlayabilir.

### <a name="webhook-scenario"></a>Web kancası senaryosu

Web kancası URL 'sinde bir GÖNDERI yaparak runbook 'ları başlatabilirsiniz. Örneğin, URL şöyle görünür: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Karma Runbook Worker senaryosu

Azure Otomasyonu 'nun Kullanıcı karma Runbook Worker özelliği, runbook 'ları doğrudan Azure 'da veya Azure 'da çalışan, Azure Arc etkinleştirilmiş sunucularla kayıtlı sunucular dahil olmak üzere Azure 'da çalıştırmanızı sağlar. Rolü barındıran makineden veya sunucudan runbook 'ları doğrudan üzerinde ve bu yerel kaynakları yönetmek için ortamdaki kaynaklara karşı çalıştırabilirsiniz.

Bir JRDS uç noktası, karma çalışan tarafından Runbook 'ları başlatmak/durdurmak, runbook 'ları çalışana indirmek ve iş günlüğü akışını Automation hizmetine geri göndermek için kullanılır.JRDS uç noktasını etkinleştirdikten sonra URL şöyle görünür: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Bu, Azure sanal ağına bağlı karma çalışan üzerinde runbook yürütmenin, Internet 'e giden bir bağlantıyı açmaya gerek kalmadan işleri yürütebilmesini sağlar.  

> [!NOTE]
>Azure Otomasyonu için özel bağlantıların geçerli uygulamasıyla, yalnızca bir Azure sanal ağına bağlı olan karma Runbook Worker üzerinde çalışan işleri destekler ve bulut işlerini desteklemez.

## <a name="hybrid-worker-scenario-for-update-management"></a>Güncelleştirme Yönetimi için karma çalışanı senaryosu  

Sistem karma Runbook Worker, Windows ve Linux makinelerine Kullanıcı tarafından belirtilen güncelleştirmeleri yüklemek için tasarlanan Güncelleştirme Yönetimi özelliği tarafından kullanılan bir gizli runbook kümesini destekler. Azure Otomasyonu Güncelleştirme Yönetimi etkinleştirildiğinde, Log Analytics çalışma alanınıza bağlı tüm makineler otomatik olarak bir sistem karma runbook çalışanı olarak yapılandırılır.

& [güncelleştirme yönetimi hakkında](../update-management/overview.md)güncelleştirme yönetimi gözden geçirmeyi anlamak için. Güncelleştirme Yönetimi özelliğinin bir Log Analytics çalışma alanına bağımlılığı vardır ve bu nedenle çalışma alanını bir Otomasyon hesabıyla bağlamayı gerektirir. Log Analytics çalışma alanı, çözüm tarafından toplanan verileri depolar ve günlük aramalarını ve görünümlerini barındırır.

Güncelleştirme yönetimi için yapılandırılmış makinelerinizin özel bağlantı kanalı üzerinden güvenli bir şekilde Otomasyon & Log Analytics çalışma alanına bağlanmasını istiyorsanız, özel bağlantıyla yapılandırılmış Otomasyon hesabına bağlı Log Analytics çalışma alanı için özel bağlantıyı etkinleştirmeniz gerekir.

[Yapılandırma Log Analytics](../../azure-monitor/logs/private-link-security.md#configure-log-analytics)' de açıklanan adımları izleyerek, bir Log Analytics çalışma alanının özel bağlantı kapsamları dışından nasıl erişilebilir olduğunu denetleyebilirsiniz. Alma **için genel ağ erişimine Izin ver** ' i **Hayır** olarak ayarlarsanız, bağlı kapsamların dışındaki makineler bu çalışma alanına veri yükleyebilir. **Sorgular için ortak ağ erişimine Izin ver** ' i **Hayır** olarak ayarlarsanız, kapsamların dışındaki makineler bu çalışma alanındaki verilere erişemez.

Kullanıcı & sistem karma çalışanları için özel bağlantıyı etkinleştirmek üzere **Dscandhyıbridworker** Target alt kaynağını kullanın.

> [!NOTE]
> Güncelleştirme Yönetimi tarafından yönetilen ve Azure dışında barındırılan makineler, Özel uç noktaları kullanan ExpressRoute özel eşlemesi, VPN tünelleri ve eşlenmiş sanal ağlar üzerinden Azure VNet 'e bağlanır.

### <a name="state-configuration-agentsvc-scenario"></a>Durum Yapılandırması (Agentsvc) senaryosu

Durum Yapılandırması size herhangi bir bulutta veya şirket içi veri merkezinde bulunan düğümler için PowerShell Istenen durum yapılandırması (DSC) yapılandırmalarını yazmanıza, yönetmenize ve derlemenize olanak tanıyan Azure yapılandırma yönetimi hizmeti sağlar.

Makinedeki aracı DSC hizmetine kaydolur ve sonra DSC yapılandırması çekmek için hizmet uç noktasını kullanır. Aracı hizmeti uç noktası şöyle görünür: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

Ortak & özel uç nokta URL 'SI aynı olur, ancak özel bağlantı etkinleştirildiğinde özel bir IP adresine eşlenir.

## <a name="planning-based-on-your-network"></a>Ağınızı temel alan planlama

Otomasyon hesabı kaynağını ayarlamadan önce ağ yalıtımı gereksinimlerinizi göz önünde bulundurun. Sanal ağlarınızın genel İnternet 'e erişimini ve otomasyon hesabınıza yönelik erişim kısıtlamalarını (Otomasyon hesabınızla tümleşikse Azure Izleyici günlüklerine özel bir bağlantı grubu kapsamı ayarlama dahil) değerlendirin. Ayrıca, desteklenen özelliklerin sorun olmadan çalışmasını sağlamak için planınızın bir parçası olarak Automation hizmeti [DNS kayıtlarını](./automation-region-dns-records.md) gözden geçirin.

### <a name="connect-to-a-private-endpoint"></a>Özel bir uç noktaya Bağlan

Ağımızı bağlamak için özel bir uç nokta oluşturun. Bunu, [Azure Portal özel bağlantı merkezinde](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)oluşturabilirsiniz. PublicNetworkAccess ve Private bağlantısına yaptığınız değişiklikler uygulandıktan sonra, bu değişikliklerin etkili olması 35 dakika sürebilir.

Bu bölümde, Otomasyon hesabınız için özel bir uç nokta oluşturacaksınız.

1. Ekranın sol üst kısmında, **özel bağlantı merkezi > ağ > kaynak oluştur**' u seçin.

2. **Özel bağlantı merkezi 'Ne genel bakış**' da, **bir hizmete özel bağlantı oluşturma** seçeneğinde, **Başlat**' ı seçin.

3. **Sanal makine oluşturma-temel bilgiler** bölümünde aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu, önceki bölümde oluşturdunuz.  |
    | **ÖRNEK AYRıNTıLARı** |  |
    | Name | *Privateendpoint* girin. |
    | Region | **Yourregion**' ı seçin. |
    |||

4. **Sonraki: kaynak**' ı seçin.

5. **Özel uç nokta oluştur-kaynak** bölümünde aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |Bağlantı yöntemi  | Dizinimde bir Azure kaynağına bağlan ' ı seçin.|
    | Abonelik| Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. Automation/automationAccounts**' ı seçin. |
    | Kaynak |*Myautomationaccount* seçin|
    |Hedef alt kaynak |Senaryonuza bağlı olarak *Web kancası* veya *Dscandhyıbridworker* ' ı seçin.|
    |||

6. Ileri 'yi seçin **: yapılandırma**.

7. **Özel uç nokta oluşturma-yapılandırma**' da, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |**AĞ**| |
    | Sanal ağ| *MyVirtualNetwork* öğesini seçin. |
    | Alt ağ | *Mysubnet* öğesini seçin. |
    |**ÖZEL DNS TÜMLEŞTİRMESİ**||
    |Özel DNS bölgesi ile tümleştirme |**Evet**’i seçin. |
    |Özel DNS Bölgesi |Seç *(yeni) Privatelink. Azure-Automation.net* |
    |||

8. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz.

9. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

Özel **bağlantı merkezinde** özel **uç noktalar** ' ı seçerek özel bağlantı kaynağınızı görüntüleyin.

![Otomasyon kaynağı özel bağlantısı](./media/private-link-security/private-link-automation-resource.png)

Tüm ayrıntıları görmek için kaynağı seçin. Bu, Otomasyon hesabınız için yeni bir özel uç nokta oluşturur ve sanal ağınızdan özel bir IP atar. **Bağlantı durumu** **Onaylandı** olarak gösterilir.

Benzer şekilde, durum yapılandırması (Agentsvc) ve karma Runbook Worker iş çalışma zamanı (jrds) için benzersiz bir tam etki alanı adı (FQDN) oluşturulur. Her birine sanal ağınızdan ayrı bir IP atanır ve **bağlantı durumu** **Onaylandı** olarak gösterilir.

Hizmet tüketicisinin Otomasyon kaynağında Azure RBAC izinleri varsa, otomatik onay yöntemini seçebilirler. Bu durumda, istek Otomasyon sağlayıcı kaynağına ulaştığında, hizmet sağlayıcısından herhangi bir işlem yapmanız gerekmez ve bağlantı otomatik olarak onaylanır.

## <a name="set-public-network-access-flags"></a>Genel ağ erişim bayraklarını ayarla

Bir Otomasyon hesabını tüm genel konfigürasyonları reddedecek şekilde yapılandırabilir ve yalnızca özel uç noktalar aracılığıyla ağ güvenliğini artırmak için bağlantılara izin verebilirsiniz. Otomasyon hesabına erişimi yalnızca VNet içinden kısıtlamak ve genel İnternet 'ten erişime izin vermek istiyorsanız `publicNetworkAccess` özelliği olarak ayarlayabilirsiniz `$false` .

**Genel ağ erişimi** ayarı olarak ayarlandığında `$false` , yalnızca özel uç noktalar aracılığıyla bağlantılara izin verilir ve genel uç noktalar aracılığıyla tüm bağlantılar yetkisiz bir hata ILETISIYLE ve 401 http durumuyla reddedilir.

Aşağıdaki PowerShell betiği, `Get` `Set` Otomasyon hesabı düzeyinde nasıl ve **genel ağ erişim** özelliğinin nasıl yapılacağını gösterir:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Ayrıca, Azure portal ortak ağ erişimi özelliğini denetleyebilirsiniz. Otomasyon hesabınızdan, **Hesap ayarları** bölümünün altındaki sol bölmeden **ağ yalıtımı** ' nı seçin. Genel ağ erişimi ayarı **Hayır** olarak ayarlandığında, yalnızca özel uç noktalar üzerinden bağlantılara izin verilir ve genel uç noktalar üzerinden tüm bağlantılar reddedilir.

![Genel ağ erişim ayarı](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS yapılandırması

Bağlantı dizesinin bir parçası olarak tam etki alanı adı (FQDN) kullanarak bir özel bağlantı kaynağına bağlanırken, DNS ayarlarınızı ayrılmış özel IP adresine çözümlemek üzere doğru şekilde yapılandırmak önemlidir. Mevcut Azure hizmetlerinde ortak bir uç nokta üzerinden bağlanılırken kullanılacak bir DNS yapılandırması zaten olabilir. DNS yapılandırmanızın özel uç noktanız kullanılarak bağlanacak şekilde incelenmesi ve güncellenmesi gerekir.

Özel uç nokta ile ilişkili ağ arabirimi, DNS 'nizi yapılandırmak için gerekli olan ve belirli bir özel bağlantı kaynağı için ayrılan FQDN ve özel IP adresleri dahil olmak üzere tam bilgi kümesini içerir.

Özel uç noktalar için DNS ayarlarınızı yapılandırmak üzere aşağıdaki seçenekleri kullanabilirsiniz:

* Ana bilgisayar dosyasını kullanın (yalnızca test için önerilir). Önce ad çözümlemesi için DNS kullanarak geçersiz kılmak üzere bir sanal makinede ana bilgisayar dosyasını kullanabilirsiniz. DNS girişiniz aşağıdaki örnekteki gibi görünmelidir: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Özel bir [DNS bölgesi](../../dns/private-dns-privatednszone.md)kullanın. Belirli bir özel uç nokta için DNS çözümlemesini geçersiz kılmak üzere özel DNS bölgelerini kullanabilirsiniz. Özel bir DNS bölgesi, belirli etki alanlarını çözümlemek için sanal ağınıza bağlanabilir. Sanal makinenizde aracının özel uç nokta üzerinden iletişim kurmasını sağlamak için, olarak Özel DNS bir kayıt oluşturun `privatelink.azure-automation.net` . Özel uç noktanın IP 'si için yeni *BIR DNS a* kaydı eşlemesi ekleyin.

* DNS ileticinizi kullanın (isteğe bağlı). Belirli bir özel bağlantı kaynağı için DNS çözümlemesini geçersiz kılmak üzere DNS ileticinizi kullanabilirsiniz. [DNS sunucunuz](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) bir sanal ağ üzerinde barındırılıyorsa, tüm özel bağlantı kaynakları için yapılandırmayı basitleştirmek üzere özel bir DNS bölgesi kullanmak üzere bir DNS iletme kuralı oluşturabilirsiniz.

Daha fazla bilgi için bkz. [Azure Özel Uç Nokta DNS yapılandırması](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Sonraki adımlar

Özel uç nokta hakkında daha fazla bilgi edinmek için bkz. [Azure özel uç noktası nedir?](../../private-link/private-endpoint-overview.md).
