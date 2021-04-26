---
title: Azure Sentinel 'de müşteri tarafından yönetilen anahtarları ayarlama | Microsoft Docs
description: Azure Sentinel 'de müşteri tarafından yönetilen anahtarları (CMK) ayarlamayı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: f7c4905f3122bf5abd2dc955170113cac3039a5d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219051"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Azure Sentinel müşteri tarafından yönetilen anahtarı ayarlama

Bu makalede, Azure Sentinel için müşteri tarafından yönetilen bir anahtar (CMK) yapılandırma ile ilgili arka plan bilgileri ve adımlar sağlanmaktadır. CMK, Azure Sentinel 'e kaydedilmiş veya gönderilen tüm verilerin, sizin tarafınızdan oluşturulmuş veya size ait bir Azure Key Vault anahtarıyla ilgili tüm depolama kaynaklarında şifrelenmesini sağlar.

> [!NOTE]
> - Azure Sentinel CMK özelliği yalnızca **yeni müşterilere** sağlanır.
>
> - Bu özelliğe erişim, Azure Özellik kaydı tarafından denetlenir. İletişim kurarak erişim isteyebilirsiniz azuresentinelCMK@microsoft.com . Bekleyen istekler kullanılabilir kapasiteye göre onaylanır.
>
> - CMK özelliği yalnızca, günde 1 GB veya daha fazla TB gönderen müşteriler tarafından kullanılabilir. Azure aboneliğinizde CMK sağlamak üzere Microsoft 'a uyguladığınızda, ek fiyatlandırma hakkında bilgi alacaksınız. [Log Analytics fiyatlandırması](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters)hakkında daha fazla bilgi edinin.

## <a name="how-cmk-works"></a>CMK nasıl kullanılır? 

Azure Sentinel çözümü, Log Analytics ve diğerleri dahil olmak üzere günlük toplama ve özellikler için çeşitli depolama kaynakları kullanır. Azure Sentinel CMK yapılandırmasının bir parçası olarak, ilgili depolama kaynaklarında CMK ayarlarını da yapılandırmanız gerekir. Log Analytics dışındaki depolama kaynaklarında kaydedilen veriler de şifrelenir.

[CMK](../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview)hakkında daha fazla bilgi edinin.

> [!NOTE]
> Azure Sentinel 'de CMK 'yi etkinleştirirseniz CMK 'yı desteklemeyen tüm genel Önizleme özellikleri etkinleştirilmeyecektir.

## <a name="enable-cmk"></a>CMK 'yi etkinleştir 

CMK sağlamak için şu adımları izleyin: 

1.  Azure Key Vault oluşturun ve anahtarı depolarsınız.

2.  Log Analytics çalışma alanınızda CMK 'yi etkinleştirin.

3.  Cosmos DB için kaydolun.

4.  Azure Key Vault örneğinize bir erişim ilkesi ekleyin.

5.  Azure Sentinel 'de CMK 'yi etkinleştirin.

6.  Azure Sentinel'i etkinleştirme.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>1. Adım: Azure Key Vault oluşturma ve anahtar depolama

1.  [Azure Key Vault kaynak oluşturun](/azure-stack/user/azure-stack-key-vault-manage-portal), ardından veri şifrelemesi için kullanılacak bir anahtar oluşturun veya içeri aktarın.
    > [!NOTE]
    >  Azure Key Vault anahtarınızı ve erişimini korumak için kurtarılabilir olarak yapılandırılmalıdır.

1.  [Kurtarma seçeneklerini aç:](../key-vault/general/key-vault-recovery.md)

    -   [Geçici silmenin](../key-vault/general/soft-delete-overview.md) açık olduğundan emin olun.

    -   Geçici silme işleminden sonra bile gizli dizi/kasaların zorla silinmesine karşı koruma sağlamak için [Temizleme korumasını](../key-vault/general/soft-delete-overview.md#purge-protection) açın.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>2. Adım: Log Analytics çalışma alanınızda CMK 'yi etkinleştirme

Aşağıdaki adımlarda Azure Sentinel çalışma alanı olarak kullanılacak bir CMK çalışma alanı oluşturmak için [Azure 'da müşteri tarafından yönetilen anahtar yapılandırma](../azure-monitor/logs/customer-managed-keys.md) yönergelerini izleyin.

### <a name="step-3-register-for-cosmos-db"></a>3. Adım: Cosmos DB için kaydolun

Azure Sentinel, Cosmos DB ek bir depolama kaynağı olarak kullanılır. Cosmos DB kaydolduğunuzdan emin olun.

Azure aboneliğinizin Azure Cosmos DB kaynak sağlayıcısını [kaydetmek](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) için Cosmos DB yönergesini izleyin.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>4. Adım: Azure Key Vault örneğine bir erişim ilkesi ekleme

Cosmos DB Azure Key Vault örneğinize erişim eklediğinizden emin olun. Azure Cosmos DB sorumlusu ile [Azure Key Vault örneğinize bir erişim ilkesi eklemek](../cosmos-db/how-to-setup-cmk.md#add-access-policy) için Cosmos DB yönergesini izleyin.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>5. Adım: Azure Sentinel 'de CMK 'yi etkinleştirme

Azure Sentinel CMK özelliği, yalnızca doğrudan Azure ürün grubundan erişim alındıktan sonra yeni müşterilere sağlanır. Çözümünüzde CMK 'yi etkinleştirmek için Azure Sentinel ekibinin onayını almak üzere Microsoft 'taki kişilerinizi kullanın.

Onay aldıktan sonra CMK özelliğini etkinleştirmek için aşağıdaki bilgileri girmeniz istenir.

-  CMK 'yi etkinleştirmek istediğiniz çalışma alanı KIMLIĞI

-  Key Vault URL 'SI: anahtarın "anahtar tanımlayıcısını" en son eğik çizgiye kopyalayın:  
    

    ![anahtar tanımlayıcı](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel ekibi, sizin belirttiğiniz çalışma alanınız için Azure Sentinel CMK özelliğini etkinleştirir.

-  Bu özelliği kullanmayı onaylanan Azure Sentinel ürün ekibinin doğrulanması. Devam etmeden önce bu yapmanız gerekir.

### <a name="step-6-enable-azure-sentinel"></a>6. Adım: Azure Sentinel 'i etkinleştirme


Azure portal gidin ve CMK 'yi ayarladığınız çalışma alanında Azure Sentinel 'i etkinleştirin. Daha fazla bilgi için bkz. [Azure Sentinel ekleme](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Anahtar şifreleme anahtarı iptali veya silme


Bir kullanıcının, anahtar şifreleme anahtarını silerek veya Azure Sentinel 'e erişimi bir saat içinde kaldırarak, Azure Sentinel değişikliği kabul eder ve veriler artık kullanılamıyor gibi davranır. Bu noktada, gerçekleştirilen ve veri alımı, kalıcı yapılandırma değişiklikleri ve olay oluşturma gibi kalıcı depolama kaynakları kullanan tüm işlemler engellenir. Daha önce depolanan veriler silinmez, ancak erişilemez kalacak. Erişilemeyen veriler veri bekletme ilkesine tabidir ve bu ilkeye uygun olarak temizlenir.

Şifreleme anahtarı iptal edildikten veya silindikten sonra mümkün olan tek işlem, hesap silme işlemidir.

Erişim İptalden sonra geri yüklenirse, Azure Sentinel bir saat içinde verilere erişimi geri yükler.

Bunun Azure Izleyici 'de nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure izleyici CMK iptali](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## <a name="key-encryption-key-rotation"></a>Anahtar şifreleme anahtarı döndürme


Azure Sentinel ve Log Analytics anahtar döndürmeyi destekler. Bir Kullanıcı Key Vault içinde anahtar döndürme gerçekleştirdiğinde, Azure Sentinel bir saat içinde yeni anahtarı destekler.

Key Vault, anahtarın yeni bir sürümünü oluşturarak anahtar döndürme gerçekleştirebilirsiniz:

![anahtar döndürme](./media/customer-managed-keys/key-rotation.png)

Anahtarın önceki sürümünü 24 saat sonra devre dışı bırakabilir veya Azure Key Vault denetim günlükleri artık önceki sürümü kullanan herhangi bir etkinliği göstermez.

Aynı anahtarı Azure Sentinel 'de ve Log Analytics ' de kullanırsanız, anahtar döndürme gerçekleştirmek için Log Analytics küme kaynağını yeni Azure Key Vault anahtarı sürümüyle açıkça güncelleştirmeniz gerekir. Daha fazla bilgi için bkz. [Azure izleyici CMK dönüşü](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Sentinel 'de müşteri tarafından yönetilen bir anahtarın nasıl ayarlanacağını öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .