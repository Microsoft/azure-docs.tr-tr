---
title: Azure Otomasyonu hesap sorunlarını giderme
description: Bu makalede, Azure hesabıyla ilgili sorunları gidermeye ve gidermeye nasıl çözüm yapılacağı açıklanır.
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896588"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Azure Otomasyonu hesap sorunlarını giderme

Bu makalede, bir Azure Otomasyonu hesabı kullanırken karşılaşabileceğiniz sorunlara yönelik çözümler ele alınmaktadır. Otomasyon hesapları hakkında genel bilgi için bkz. [Azure Otomasyonu hesabı kimlik doğrulamasına genel bakış](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Senaryo: abonelikler için Otomasyon kaynak sağlayıcısı kaydedilemiyor

### <a name="issue"></a>Sorun

Güncelleştirme Yönetimi, Otomasyon hesabınızda yönetim özellikleriyle çalışırken aşağıdaki hatayla karşılaşırsınız:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Nedeni

Otomasyon kaynak sağlayıcısı abonelikte kayıtlı değil.

### <a name="resolution"></a>Çözüm

Otomasyon kaynak sağlayıcısını kaydetmek için Azure portal aşağıdaki adımları izleyin:

1. Tarayıcınızdan [Azure Portal](https://portal.azure.com)gidin.

2. **Abonelikler**' e gidin ve aboneliğinizi seçin.   

3. **Ayarlar** altında **kaynak sağlayıcıları**' nı seçin.

4. Kaynak sağlayıcıları listesinden, **Microsoft. Automation** kaynak sağlayıcısının kayıtlı olduğunu doğrulayın.

5. Sağlayıcı listede yoksa, [kaynak sağlayıcısı kaydı hatalarını giderme](../../azure-resource-manager/templates/error-register-resource-provider.md)bölümünde açıklandığı şekilde kaydedin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale sorununuzu gidermezse, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* İle bağlanın [@AzureSupport](https://twitter.com/azuresupport) . Bu, Azure Community 'yi doğru kaynaklara bağlamaya yönelik resmi Microsoft Azure hesabıdır: yanıtlar, destek ve uzmanlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
