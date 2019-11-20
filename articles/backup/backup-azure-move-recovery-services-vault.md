---
title: Azure Backup kurtarma hizmetleri kasalarını taşıma
description: Kurtarma Hizmetleri kasasını Azure abonelikleri ve kaynak grupları arasında taşıma yönergeleri.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 7d0a6c47fa08774161d3a2b7507cdfecaf8c7197
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172883"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Kurtarma Hizmetleri kasasını Azure abonelikleri ve kaynak grupları arasında taşıma

Bu makalede, Azure abonelikleri arasında Azure Backup için yapılandırılmış bir kurtarma hizmetleri kasasının veya aynı abonelikteki başka bir kaynak grubunda nasıl taşınacağı açıklanır. Kurtarma Hizmetleri kasasını taşımak için Azure portal veya PowerShell kullanabilirsiniz.

## <a name="supported-regions"></a>Desteklenen bölgeler

Kurtarma Hizmetleri Kasası için kaynak taşıma Avustralya Doğu, Avustralya Güney Doğu, Kanada Orta, Kanada Doğu, Güney Doğu Asya, Doğu Asya, Orta ABD, Orta Kuzey ABD, Doğu ABD, Doğu ABD2, Güney Orta ABD, Orta Batı ABD, Batı orta ABD2, Batı ABD, Orta Hindistan, Güney Hindistan, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Kuzey Avrupa, Batı Avrupa, Güney Afrika Kuzey, Güney Afrika Batı, UK Güney ve UK Batı.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Kurtarma Hizmetleri kasasını taşımaya yönelik önkoşullar

- Kaynak grupları arasında kasa taşıma sırasında, hem kaynak hem de hedef kaynak grupları, yazma ve silme işlemlerini önleyecek şekilde kilitlidir. Daha fazla bilgi için [bu makaleye](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) bakın.
- Yalnızca yönetici aboneliğinin bir kasayı taşıma izinleri vardır.
- Kasayı abonelikler arasında taşımak için hedef aboneliğin, kaynak abonelikle aynı kiracıda bulunması ve durumunun etkinleştirilmesi gerekir.
- Hedef kaynak grubunda yazma işlemleri gerçekleştirmek için izninizin olması gerekir.
- Kasanın taşınması yalnızca kaynak grubunu değiştirir. Kurtarma Hizmetleri Kasası aynı konumda yer alacak ve değiştirilemez.
- Tek seferde her bölge için yalnızca bir kurtarma hizmetleri Kasası taşıyabilirsiniz.
- Bir VM, aboneliklerdeki kurtarma hizmetleri kasasıyla veya yeni bir kaynak grubuna taşınmazsa, geçerli VM kurtarma noktaları, süreleri dolana kadar kasada değişmeden kalır.
- VM 'nin kasayla taşınıp taşınmadığı veya değil, VM 'yi her zaman kasadaki Korunan yedekleme geçmişinden geri yükleyebilirsiniz.
- Azure disk şifrelemesi, anahtar kasasının ve VM 'Lerin aynı Azure bölgesinde ve abonelikte bulunmasını gerektirir.
- Bir sanal makineyi yönetilen disklere taşımak için, bu [makaleye](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)bakın.
- Klasik model aracılığıyla dağıtılan kaynakları taşımaya yönelik seçenekler, kaynakları bir abonelik içinde veya yeni bir aboneliğe taşıdığınıza bağlı olarak farklılık gösterir. Daha fazla bilgi için [bu makaleye](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) bakın.
- Kasa için tanımlanan yedekleme ilkeleri, kasalardan abonelikler arasında veya yeni bir kaynak grubuna taşındıktan sonra tutulur.
- Azure dosyaları, Azure Dosya Eşitleme ya da SQL ve kaynak grupları arasında IaaS VM 'lerinde kasanın taşınması desteklenmez.
- VM yedekleme verilerini içeren bir kasayı abonelikler arasında taşırsanız, sanal makinelerinizi aynı aboneliğe taşımanız ve yedeklemeleri sürdürmek için aynı hedef VM kaynak grubu adını (eski abonelikte olduğu gibi) kullanmanız gerekir.

> [!NOTE]
>
> **Azure Site Recovery** ile kullanılmak üzere yapılandırılan kurtarma hizmetleri kasaları henüz taşınamaz. **Azure Site Recovery**kullanarak olağanüstü durum kurtarma için herhangi bir VM (Azure IaaS, Hyper-V, VMware) veya fiziksel makine yapılandırdıysanız taşıma işlemi engellenir. Site Recovery hizmeti için kaynak taşıma özelliği henüz kullanılamıyor.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Kurtarma Hizmetleri kasasını farklı kaynak grubuna taşımak için Azure portal kullanın

Kurtarma Hizmetleri kasasını ve ilişkili kaynaklarını farklı kaynak grubuna taşımak için

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Kurtarma Hizmetleri kasalarının** listesini açın ve taşımak istediğiniz kasayı seçin. Kasa panosu açıldığında, aşağıdaki görüntüde gösterildiği gibi görünür.

   ![Kurtarma hizmeti kasasını aç](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Kasanızın **Essentials** bilgilerini görmüyorsanız açılan simgeye tıklayın. Artık kasanızın temel bilgileri bilgisini görmeniz gerekir.

   ![Essentials bilgi sekmesi](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Kasa genel bakış menüsünde, **kaynak grubu**' nun yanındaki **Değiştir** ' e tıklayarak **kaynakları taşı** dikey penceresini açın.

   ![Kaynak grubunu değiştir](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. **Kaynakları taşı** dikey penceresinde, seçili kasa için, aşağıdaki görüntüde gösterildiği gibi onay kutusunu seçerek isteğe bağlı ilişkili kaynakları taşımanız önerilir.

   ![Aboneliği taşı](./media/backup-azure-move-recovery-services/move-resource.png)

5. Hedef kaynak grubunu eklemek için, **kaynak grubu** açılır listesinde var olan bir kaynak grubunu seçin veya **Yeni grup oluştur** seçeneğine tıklayın.

   ![Kaynak oluştur](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Kaynak grubu eklendikten sonra, **taşınan kaynaklarla ilişkili araçların ve betiklerin yeni kaynak kimlikleri seçeneği kullanılarak güncelleştirene** ve ardından kasayı taşımayı tamamlaması için **Tamam** ' a tıkladığımda emin olun.

   ![Onay Iletisi](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Kurtarma Hizmetleri kasasını farklı bir aboneliğe taşımak için Azure portal kullanma

Kurtarma Hizmetleri kasasını ve ilişkili kaynaklarını farklı bir aboneliğe taşıyabilirsiniz

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Kurtarma Hizmetleri kasalarının listesini açın ve taşımak istediğiniz kasayı seçin. Kasa panosu açıldığında, aşağıdaki görüntüde gösterildiği gibi görünür.

    ![Kurtarma hizmeti kasasını aç](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Kasanızın **Essentials** bilgilerini görmüyorsanız açılan simgeye tıklayın. Artık kasanızın temel bilgileri bilgisini görmeniz gerekir.

    ![Essentials bilgi sekmesi](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Kasa genel bakış menüsünde, **abonelik**' ın yanındaki **Değiştir** ' e tıklayarak **kaynakları taşı** dikey penceresini açın.

   ![Aboneliği Değiştir](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Taşınacak kaynakları seçin, burada, listelenen tüm isteğe bağlı kaynakları seçmek için **Tümünü Seç** seçeneğini kullanmanızı öneririz.

   ![kaynağı taşı](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Kasanın taşınmasını istediğiniz **abonelik** açılır listesinden hedef aboneliği seçin.
6. Hedef kaynak grubunu eklemek için, **kaynak grubu** açılır listesinde var olan bir kaynak grubunu seçin veya **Yeni grup oluştur** seçeneğine tıklayın.

   ![Abonelik Ekle](./media/backup-azure-move-recovery-services/add-subscription.png)

7. **Taşınan kaynaklarla ilişkili araçların ve betiklerin, onaylamak üzere yeni kaynak kimlikleri seçeneğini kullanacak şekilde güncelleştirene kadar çalışmayacaktır** ve ardından **Tamam**' a tıklayın.

> [!NOTE]
> Çapraz abonelik yedeklemesi (RS kasası ve korunan VM 'Ler farklı aboneliklerde) desteklenen bir senaryo değildir. Ayrıca, yerel yedekli depolama (LRS) ile global olarak yedekli depolama (GRS) arasında depolama artıklığı seçeneği ve tam tersi de kasa taşıma işlemi sırasında değiştirilemez.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>PowerShell kullanarak kurtarma hizmetleri kasasını taşıma

Kurtarma Hizmetleri kasasını başka bir kaynak grubuna taşımak için `Move-AzureRMResource` cmdlet 'ini kullanın. `Move-AzureRMResource` kaynak adı ve kaynak türü gerektirir. `Get-AzureRmRecoveryServicesVault` cmdlet 'ini her ikisini de alabilirsiniz.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Kaynakları farklı aboneliğe taşımak için `-DestinationSubscriptionId` parametresini ekleyin.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Yukarıdaki cmdlet 'leri yürüttükten sonra, belirtilen kaynakları taşımak istediğinizi onaylamanız istenir. Onaylamak için **Y** yazın. Başarılı bir doğrulamadan sonra kaynak hareketlenir.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLı kullanarak kurtarma hizmetleri kasasını taşıma

Kurtarma Hizmetleri kasasını başka bir kaynak grubuna taşımak için aşağıdaki cmdlet 'i kullanın:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Yeni bir aboneliğe taşımak için sağlamak `--destination-subscription-id` parametresi.

## <a name="post-migration"></a>Geçiş sonrası

1. Kaynak grupları için erişim denetimlerini ayarlayın/doğrulayın.  
2. Taşıma işlemi tamamlandığında, yedekleme raporlama ve izleme özelliğinin yeniden yapılandırılması gerekir. Taşıma işlemi sırasında önceki yapılandırma kaybedilir.

## <a name="next-steps"></a>Sonraki adımlar

Kaynak grupları ve abonelikler arasında birçok farklı kaynak türünü taşıyabilirsiniz.

Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
