---
title: Agari kimlik avlama savunması ve marka koruma Çözümlerinizi Azure Sentinel 'e bağlama | Microsoft Docs
description: Agari kimlik avı savunması ve marka koruma bağlayıcısını kullanarak günlüklerini Azure Sentinel 'e çekmeye nasıl kullanacağınızı öğrenin. Çalışma kitaplarında Agari verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a60a0291d6669b2a9115dffa8e0d4d63fae4a440
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724447"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Agari kimlik avlama savunması ve marka koruma Çözümlerinizi Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Agari kimlik avı savunması ve marka koruma Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Agari kimlik avı savunma ve marka koruma Bağlayıcısı, çalışma kitaplarında verileri görüntüleyebilmeniz, özel uyarılar oluşturmak için sorgulayıp ve araştırmayı iyileştirecek şekilde birleştirebilmeniz için, marka koruma ve kimlik avı savunma çözümlerinin günlüklerini Azure Sentinel 'e kolayca bağlamanıza olanak tanır. Agari çözümleri Azure Işlevleri ve REST API kullanarak Azure Sentinel ile tümleşir.

Ayrıca, marka koruması ve kimlik avı yanıt müşterileri, güvenlik Graph API aracılığıyla tehdit zekasından yararlanabilir.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

Agari 'nin kimlik avı savunması ve marka koruma çözümlerini Azure Sentinel 'e bağlamak için aşağıdakiler gereklidir:

- Azure Sentinel çalışma alanında okuma ve yazma izinleri.

- Çalışma alanı için paylaşılan anahtarların okuma izinleri. [Çalışma alanı anahtarları hakkında daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- İşlev Uygulaması oluşturmak için Azure Işlevleri üzerinde okuma ve yazma izinleri. [Azure işlevleri hakkında daha fazla bilgi edinin](../azure-functions/index.yml).

- Agari **ISTEMCI kimliğiniz** ve **gizli anahtarlarınızın** (tüm Agari çözümlerinde aynı) olduğundan emin olun. Yönergeler için bkz. [Agari geliştiricileri sitesi](https://developers.agari.com/agari-platform/docs/quick-start) .

## <a name="configure-and-connect-agari-solutions"></a>Agari çözümlerini yapılandırma ve bağlama 

Agari çözümleri, Azure İşlev Uygulaması kullanarak günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

> [!NOTE]
> Bu bağlayıcı, günlük dosyalarını Azure Sentinel 'e çekmek üzere Agari 'nin çözümlerine bağlanmak için Azure Işlevleri 'ni kullanır. Bu, ek veri alımı maliyetlerine neden olabilir. Ayrıntılar için [Azure işlevleri fiyatlandırma](https://azure.microsoft.com/pricing/details/functions/) sayfasına bakın.

1. **Agari API kimlik bilgilerinizi toplayın:** 

    Agari **ISTEMCI kimliğiniz** ve **gizli anahtarlarınız** olduğundan emin olun. Yönergeler [Agari geliştiricileri sitesinde](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials)bulunabilir.

1. **Seçim Güvenlik Graph API etkinleştirin:** 

    Agari İşlev Uygulaması, güvenlik Graph API aracılığıyla tehdit zekasını Azure Sentinel ile paylaşmanıza olanak sağlar. Bu özelliği kullanmak için, [Sentinel Threat Intelligence platformları bağlayıcısını](connect-threat-intelligence.md) etkinleştirmeniz ve ayrıca Azure Active Directory [bir uygulamayı kaydetmeniz](/graph/auth-register-app-v2) gerekir.

    Bu işlem, aşağıdaki İşlev Uygulaması dağıtımı sırasında kullanabileceğiniz üç bilgi verir: **Graf KIRACı kimliği**, **Graf Istemci kimliği** ve **Graf istemci parolası**.

1. **Bağlayıcıyı ve ilişkili Azure İşlev Uygulaması dağıtma:** 

    1. Azure Sentinel portalında **veri bağlayıcıları**' nı seçin. **Agari kimlik avı savunması 'nı ve marka korumasını (Önizleme)** seçin ve ardından **bağlayıcı sayfasını açın**.

    1. **Yapılandırma** altında, Azure Sentinel **çalışma alanı kimliği** ve **birincil anahtarı** kopyalayın ve bunları kaydederek yapıştırın.

    1. **Azure 'A dağıt**' ı seçin. (Düğmeyi bulmak için aşağı kaydırmanız gerekebilir.)

    1. **Özel dağıtım** ekranı görüntülenir.

        - Agari **ISTEMCI kimliğini** ve **istemci gizli** anahtarını (gizli anahtarlar) girin

        - Kopyaladığınız ve yerleştirdiğiniz Azure Sentinel **çalışma alanı kimliğinizi** ve **çalışma alanı anahtarınızı** (birincil anahtar) girin.

        - İçin etkin abonelikleriniz olan Agari çözümleri için **true** veya **false değerini** seçin.

        - Güvenlik Graph API kullanarak ıocs 'yi Azure Sentinel ile paylaşmak için bir Azure uygulaması oluşturduysanız, **güvenlik grafiği paylaşımını etkinleştirmek** için **true** , Graph **KIRACı kimliğini**, **Graf istemci kimliğini** ve **Graf istemci parolasını** girin.

    1. **Gözden geçir ve oluştur**’u seçin. Doğrulama tamamlandığında **Oluştur**' a tıklayın.

1. **İşlev Uygulaması gerekli izinleri atayın:**

    Agari Bağlayıcısı, günlük erişimi zaman damgalarını depolamak için bir ortam değişkeni kullanır. Uygulamanın bu değişkene yazması için, sistem tarafından atanan kimliğe izinler atanmalıdır.

    1. Azure portal **işlev uygulaması** gidin.

    1. **İşlev uygulaması** dikey penceresinde, listeden işlev uygulaması seçin ve ardından işlev uygulaması gezinti menüsünde **Ayarlar** altında **kimlik** ' i seçin.

    1. **Sistem atandı** sekmesinde **durumu** **Açık** olarak ayarlayın. 

    1. **Kaydet**' i seçin ve bir **Azure rol atamaları** düğmesi görüntülenir. Tıklayın.

    1. **Azure rolü atamaları** ekranında **rol ataması Ekle**' yi seçin. **Kapsamı** **abonelik** olarak ayarlayın, **abonelik** açılır listesinden aboneliğinizi seçin ve **rolü** **uygulama yapılandırma veri sahibi** olarak ayarlayın. 

    1. **Kaydet**’i seçin.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler aşağıdaki tablolardaki *customlogs* altındaki **günlüklerde** görünür: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Agari çözüm verilerini sorgulamak için, sorgu penceresinde yukarıdaki tablo adlarından birini girin.

Bazı yararlı örnek sorgular için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Agari sızdırma savunması ve marka koruma çözümlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .