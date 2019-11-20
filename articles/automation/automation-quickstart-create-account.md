---
title: Azure hızlı başlangıç-Azure Otomasyonu hesabı oluşturma | Microsoft Docs
description: Azure Otomasyonu hesabı oluşturmayı ve Runbook çalıştırmayı öğrenin
services: automation
author: csand-msft
ms.author: csand
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: automation
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 80a175aad3c692a5dfd04c7399211c58134c5164
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937158"
---
# <a name="create-an-azure-automation-account"></a>Azure Otomasyonu hesabı oluşturma

Azure Otomasyonu hesapları Azure aracılığıyla oluşturulabilir. Bu yöntem, Otomasyon hesaplarını ve ilgili kaynakları oluşturmak ve yapılandırmak için tarayıcı tabanlı bir kullanıcı arabirimi sağlar. Bu hızlı başlangıçta bir Otomasyon hesabı oluşturma ve hesapta bir runbook çalıştırma adımları sağlanır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure 'da oturum açın

@No__t-0 ' da Azure 'da oturum açın

## <a name="create-automation-account"></a>Otomasyon hesabı oluştur

1. Azure 'un sol üst köşesinde bulunan **kaynak oluştur** düğmesine tıklayın.

1. **Yönetim araçları &** seçin ve ardından **Otomasyon**' u seçin.

1. Hesap bilgilerini girin. **Azure farklı çalıştır hesabı oluştur**için **Evet** ' i seçerek Azure 'da kimlik doğrulamasını basitleştirecek yapıtlar otomatik olarak etkinleştirilir. Bir Otomasyon hesabı oluştururken ad, seçildikten sonra değiştirilemez olarak dikkat edilmesi önemlidir. *Otomasyon hesabı adları, bölge ve kaynak grubu başına benzersizdir. Silinen Otomasyon hesaplarının adları hemen kullanılamayabilir.* Tek bir Otomasyon hesabı, belirli bir kiracının tüm bölgeleri ve abonelikleri genelinde kaynakları yönetebilir. Tamamlandığında, Otomasyon hesabı dağıtımını başlatmak için **Oluştur**' a tıklayın.

    ![Otomasyon hesabınızla ilgili bilgileri sayfaya girin](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Bir Otomasyon hesabı dağıtabilmeniz için bir Otomasyon hesabı dağıtabileceğiniz konumların güncelleştirilmiş bir listesi için, [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

1. Dağıtım tamamlandığında, **tüm hizmetler**' e tıklayın, **Otomasyon hesapları** ' nı seçin ve oluşturduğunuz Otomasyon hesabını seçin.

    ![Otomasyon hesabına genel bakış](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Runbook 'u çalıştırma

Öğretici runbook 'lardan birini çalıştırın.

1. **Işlem Otomasyonu**altında **runbook 'lar** ' a tıklayın. Runbook 'ların listesi görüntülenir. Varsayılan olarak, hesapta çeşitli öğretici runbook 'lar etkindir.

    ![Otomasyon hesabı runbook 'ları listesi](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. **AzureAutomationTutorialScript** runbook 'unu seçin. Bu eylem runbook genel bakış sayfasını açar.

    ![Runbook 'a genel bakış](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. **Başlat**' a tıklayın ve **runbook 'U Başlat** sayfasında **Tamam** ' a tıklayarak runbook 'u başlatın.

    ![Runbook iş sayfası](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. **İş durumu** **çalışmaya**başladıktan sonra, runbook Iş çıkışını görüntülemek için **Çıkış** ' a veya **Tüm Günlükler** ' e tıklayın. Bu öğretici runbook 'u için çıkış, Azure kaynaklarınızın bir listesidir.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Otomasyon hesabı dağıtıldı, bir runbook işi başlattı ve iş sonuçları görüntülendi. Azure Otomasyonu hakkında daha fazla bilgi edinmek için ilk Runbook 'unuzu oluşturmaya yönelik hızlı başlangıç ile devam edin.

> [!div class="nextstepaction"]
> [Otomasyon hızlı başlangıcı-runbook oluşturma](./automation-quickstart-create-runbook.md)

