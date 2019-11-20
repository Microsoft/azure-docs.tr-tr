---
title: Linux bilgisayarlarını Azure Güvenlik Merkezi 'ne ekleme | Microsoft Docs
description: Bu hızlı başlangıç, Linux bilgisayarlarınızı Güvenlik Merkezi’ne nasıl ekleyebileceğinizi gösterir.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: memildin
ms.openlocfilehash: 9f49b858a23d0a1f58505d9f9971a31e8c9167e9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664490"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Hızlı başlangıç: Linux bilgisayarlarını Azure Güvenlik Merkezi’ne ekleme
Azure aboneliklerinizi ekledikten sonra, Azure dışında çalışan Linux kaynakları için güvenlik merkezini, örneğin şirket içi veya diğer bulutlarda, bir aracı sağlayarak etkinleştirebilirsiniz. Aracıya Microsoft Monitoring Agent (MMA) denir, ancak OMS Aracısı olarak da bilinir.

Bu hızlı başlangıçta aracının bir Linux bilgisayara nasıl yükleneceği gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar
Güvenlik Merkezi ile çalışmaya başlamak için Microsoft Azure aboneliğinizin olması gerekir. Bir aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.

Bu hızlı başlangıçtaki adımlara geçmeden önce Güvenlik Merkezi’nin Standart fiyatlandırma katmanında olmanız gerekir. Yükseltme yönergeleri için bkz. [Azure aboneliğinizi Güvenlik Merkezi Standart katmanına ekleme](security-center-get-started.md). Güvenlik Merkezi 'nin standardını ücretsiz olarak deneyebilirsiniz. Daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-linux-computer"></a>Yeni Linux bilgisayarı ekleme

1. [Azure portalında](https://azure.microsoft.com/features/azure-portal/) oturum açın.
2. **Microsoft Azure** menüsünde **Güvenlik Merkezi**’ni seçin. **Güvenlik Merkezi - Genel Bakış** açılır.

   ![Güvenlik Merkezine genel bakış][2]

3. Güvenlik Merkezi ana menüsü altında, **Başlarken**’i seçin.
4. **Kullanmaya başlayın sekmesini seçin** . ![kullanmaya başlayın][3]

5. **Yeni Azure olmayan bilgisayarlar ekleme** atında **Yapılandır**’a tıklayın, Log Analytics çalışma alanlarınızın bir listesi gösterilir. Listede, varsa, otomatik sağlama etkinleştirildiğinde Güvenlik Merkezi tarafından sizin için oluşturulan varsayılan çalışma alanı bulunur. Bu çalışma alanını veya kullanmak istediğiniz başka bir çalışma alanını seçin.

    ![Azure dışı bilgisayar ekleme](./media/quick-onboard-linux-computer/non-azure.png)

6. **Direct Agent** (Doğrudan Aracı) sayfasının **DOWNLOAD AND ONBOARD AGENT FOR LINUX** (Linux için aracıyı indir ve ekle) bölümünden **copy** (Kopyala) düğmesini seçerek *wget* komutunu kopyalayın.

7. Not Defteri'ni açın ve bu komutu yapıştırın. Bu dosyayı Linux bilgisayarınızdan erişilebilen bir konuma kaydedin.

## <a name="install-the-agent"></a>Aracıyı yükleme

1. Linux bilgisayarınızda daha önce kaydedilen dosyayı açın. İçeriğin tamamını seçin, kopyalayın, bir terminal konsolu açın ve komutu yapıştırın.
2. Yükleme tamamlandığında *pgrep* komutunu çalıştırarak *omsagent*’ın yüklü olduğunu doğrulayın. Komut tarafından aşağıda gösterildiği gibi *omsagent* PID’si (İşlem Kimliği) döndürülür:

   ![Aracıyı yükleme][5]

Aracının günlükleri şurada bulunabilir: */var/SeçenekKodu/Microsoft/omsagent/\<çalışma alanı kimliği >/log/*

  ![Aracı günlükleri][6]

30 dakikayı bulabilecek bir süre geçtikten sonra Linux bilgisayarı Güvenlik Merkezi’nde görünür.

Artık Azure VM’lerinizi ve Azure dışı bilgisayarlarınızı tek bir yerde izleyebilirsiniz. **Bilgi İşlem** dikey penceresinde, önerilerle birlikte tüm VM’lere ve bilgisayarlara ilişkin bir genel bakış görürsünüz. Her sütunda bir dizi öneri sunulur. Renk VM'nin veya bilgisayarın söz konusu öneri için geçerli güvenlik durumunu belirtir. Güvenlik Merkezi ayrıca bu bilgisayarlara yönelik tüm algılamaları Güvenlik uyarıları bölümünde gösterir.

  ![Compute (İşlem) dikey penceresi][7] **Compute** (İşlem) dikey penceresinde temsil edilen iki tür simge vardır:

  ![simge1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Azure dışı bilgisayar

  ![simge2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli olmadığında, aracıyı Linux bilgisayardan kaldırabilirsiniz.

Aracıyı kaldırmak için:

1. Linux aracısı [evrensel betiğini](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) bilgisayara indirin.
2. Bilgisayarda paket .sh dosyasını aracıyı ve yapılandırmasını tamamen kaldıran *--purge* bağımsız değişkeni ile çalıştırın.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta aracıyı bir Linux bilgisayarda sağladınız. Güvenlik Merkezi'ni kullanma hakkında daha fazla bilgi için bir güvenlik ilkesi yapılandırma ve kaynaklarınızın güvenliğini değerlendirme ile ilgili öğreticiye geçin.

> [!div class="nextstepaction"]
> [Öğretici: Güvenlik ilkelerini tanımlama ve değerlendirme](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
