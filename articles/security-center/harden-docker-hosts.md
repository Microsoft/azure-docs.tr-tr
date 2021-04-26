---
title: Azure Güvenlik Merkezi 'ni kullanarak Docker konaklarınızı sağlamlaştırın ve kapsayıcıları koruyabilirsiniz
description: Docker konaklarınızı koruma ve CIS Docker kıyaslaması ile uyumlu olduğunu doğrulama
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916243"
---
# <a name="harden-your-docker-hosts"></a>Docker konaklarınızı sağlamlaştırma

Azure Güvenlik Merkezi, IaaS Linux VM 'lerinde barındırılan yönetilmeyen kapsayıcıları veya Docker Kapsayıcıları çalıştıran diğer Linux makinelerini tanımlar. Güvenlik Merkezi, bu kapsayıcıların yapılandırmalarının sürekli değerlendirir. Daha sonra bunları [Internet güvenliği (CIS) Docker kıyaslaması Için merkezi](https://www.cisecurity.org/benchmark/docker/)ile karşılaştırır.

Güvenlik Merkezi, CIS Docker kıyaslaması 'nın kural kümesinin tamamını içerir ve kapsayıcılarınız denetimlerden herhangi birini karşılamadığı takdirde sizi uyarır. Güvenlik Merkezi, yanlış yapılandırma bulduğunda güvenlik önerileri oluşturur. Önerileri görüntülemek ve sorunları düzeltmek için Güvenlik Merkezi 'nin **öneriler sayfasını** kullanın.

Güvenlik açıkları bulunduğunda, bunlar tek bir öneri içinde gruplandırılır.

>[!NOTE]
> Bu CIS kıyaslama denetimleri, AKS tarafından yönetilen örneklerde veya Databricks tarafından yönetilen VM 'lerde çalışmaz.

## <a name="availability"></a>Kullanılabilirlik

|Görünüş|Ayrıntılar|
|----|:----|
|Yayın durumu:|Genel kullanılabilirlik (GA)|
|Fiyat|[Sunucular Için Azure Defender](defender-for-servers-introduction.md) gerekir|
|Gerekli roller ve izinler:|Konağın bağlandığı çalışma alanındaki **okuyucu**|
|Larının|![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) Ulusal/Sogeign (US Gov, Çin gov, diğer gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Docker yapılandırmanızda güvenlik açıklarını belirleyip düzeltin

1. Güvenlik Merkezi 'nin menüsünde, **öneriler** sayfasını açın.

1. **Kapsayıcı güvenlik yapılandırmalarında** öneri güvenlik açıklarına filtre uygulamak ve öneriyi seçmek gerekir.

    Öneri sayfasında etkilenen kaynaklar (Docker konakları) gösterilir. 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Kapsayıcı güvenlik yapılandırmalarında güvenlik açıklarını düzeltme önerisi ":::

1. Belirli bir konağın başarısız olduğu CIS denetimlerini görüntülemek ve düzeltmek için, araştırmak istediğiniz Konağı seçin. 

    > [!TIP]
    > Varlık envanteri sayfasından başladıysanız ve bu öneriye buradan ulaştıysanız, öneri sayfasında **eylem al** düğmesine basın.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Log Analytics başlatmak için eylem Al düğmesi":::

    Log Analytics, çalıştırmaya hazırlamış özel bir işlemle açılır. Varsayılan özel sorgu, sorunları çözmenize yardımcı olacak yönergeler ile birlikte, değerlendirilen tüm başarısız kuralların bir listesini içerir.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Başarısız olan tüm CIS denetimlerini gösteren sorguyla Log Analytics sayfası":::

1. Gerekirse sorgu parametrelerini ince ayar.

1. Komutun ana bilgisayarınız için uygun ve uygun olduğundan emin olduğunuzda **Çalıştır**' ı seçin.


## <a name="next-steps"></a>Sonraki adımlar

Docker sağlamlaştırma, güvenlik merkezi 'nin kapsayıcı güvenlik özelliklerinin yalnızca bir yönüdür. 

[Güvenlik Merkezi 'nde daha fazla kapsayıcı güvenliği](container-security.md)öğrenin.