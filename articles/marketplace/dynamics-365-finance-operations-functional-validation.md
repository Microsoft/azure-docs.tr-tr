---
title: Azure Marketi 'nde bir AppSource Dynamics 365 finans ve operasyon teklifinin işlevsel doğrulaması.
description: Azure Market 'te bir Dynamics 365 finans ve operasyon teklifini işlevsel olarak doğrulama.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 9be90cdac742a581c6346f923f44e769c8a70f76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613638"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 finans ve Operations işlev doğrulaması

[Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)'nde ilk yayımlamayı tamamlayabilmeniz Için, Dynamics 365 finans ve işlemleri için teklifler iki işlevsel doğrulama gerektirir:

- Temel işlevleri gösteren Dynamics 365 ortamının tanıtım videosunu karşıya yükleyin.
- Çözümün [yaşam döngüsü Hizmetleri](https://lcs.dynamics.com/) (LCS) ortamını gösteren ekran görüntüleri sunun.

> [!NOTE]
> Sonraki yeniden sertifika yayımlarım tanıtım gerektirmez. Daha fazla bilgi için bkz. [Appsource ilke belgesi](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Doğrulama

İşlevsel doğrulama için iki seçenek vardır:

- Pasifik Standart Saati (PST) sırasında bizimle birlikte 30 dakikalık bir konferans çağrısını basılı tutarak, [LCS](https://lcs.dynamics.com/) ortamını ve çözümünü göstermek ve kaydetmek için veya
- İş Ortağı Merkezi 'nde, [ticari Market](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)'e  >  **genel bakış** ' a gidin ve teklifin ek içerik sekmesine bir demo video URL 'si ve LCS ekran görüntüleri yükleyin.

Microsoft sertifika ekibi, video ve dosyaları inceler, sonra çözümü onaylar veya sonraki adımlarla ilgili e-postalar.

### <a name="option-1-30-minute-conference-call"></a>Seçenek 1:30-dakikalık konferans çağrısı

Nihai bir gözden geçirme çağrısını zamanlamak için, [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) teklifinizin adı ve 8 ile 5 Pasifik saati arasında bazı olası zaman yuvaları ile iletişim kurun.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Seçenek 2: tanıtım videosu ve LCS ekran görüntülerini karşıya yükleme

1. Bir video kaydedin ve seçtiğiniz barındırma sitesine adresini yükleyin. Aşağıdaki yönergeleri izleyin:

    - Microsoft sertifika ekibi tarafından görüntülenebilir.
    - 20 dakikadan kısa sürer.
    - , Dynamics 365 ortamında çözümünüzün en fazla üç çekirdekli işlev vurgularını içerir.

    > [!NOTE]
    > Yönergeleri karşılıyorsa mevcut bir pazarlama videosunu kullanmak kabul edilebilir.

2. , Yayınlamak istediğiniz teklif veya çözümle eşleşen [LCS](https://lcs.dynamics.com/) ortamının aşağıdaki ekran görüntülerini alın. Sertifika ekibinin metni okuyabilmesi için yeterince net olması gerekir. Ekran görüntülerini JPG dosyası olarak kaydedin. Bu [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) kurulumu, ekran görüntüleri sağlama yerine doğrulayabilmemiz IÇIN LCS ortamınız için izin sağlayabilirsiniz.

    1. **LCS**  >  **iş süreci Modelleyicisi**  >  **proje kitaplığı**' na gidin. Tüm Işlem adımlarının ekran görüntülerini alın. **Diyagramları** ve **Gözden geçirilen** sütunları aşağıda gösterildiği gibi ekleyin:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Proje kitaplığı penceresini gösterir.":::

      2. **LCS**  >  **Çözüm Yönetimi**  >  **test çözüm paketine** gidin. Bu örneklerde gösterilen pakete genel bakış ve içerikleri içeren ekran görüntülerini alın:

    | Alan | Görüntü |
    | --- | --- |
    | Pakete genel bakış | [!["Pakete genel bakış" penceresini gösteren ekran görüntüsü.](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Çözüm onaylayanları</li></ul> | [![Pakete genel bakış ekranı](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Paket içeriği<ul><li>Modelleme</li><li>Yazılım dağıtılabilir paket</li></ul> | [![Paket içerikleri ekranı bir](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>Yapılandırmayı GER</li><li>Veritabanı yedeklemesi</li></ul><br>Yapıtlar **yapılandırma** bölümünde yapıt gerekli değildir. | [![Paket içeriği ekranı iki](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI rapor modeli</li><li>BPM yapıtı</li></ul><br>**Power BI** bölümünde yapıtlar gerekli değildir. | [![Paket içeriği ekranı üç](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>İşlem veri paketi</li><li>Çözüm Lisans Sözleşmesi ve Gizlilik ilkesi</li></ul><br>**Grup yapılandırma** ve **Power BI rapor modeli** bölümleri, finans ve işlemler tekliflerini içerecek şekilde isteğe bağlıdır. | [![Paket içeriği ekranı dört](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    LCS portalının her bölümü hakkında daha fazla bilgi edinmek için, [LCS Kullanıcı Kılavuzu](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide)' na bakın.

3. Iş Ortağı Merkezi 'ne yükleyin.

    1. Demo video adresini ve ekran görüntülerini içeren bir metin belgesi oluşturun veya ekran görüntülerini ayrı bir JPG dosyası olarak kaydedin.
    2. Metin ve tüm JPG dosyalarını, finans ve Işlemler teklifinizin **ek içerik** sekmesinde Iş Ortağı Merkezi ' nde [ticari Market](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) 'teki bir. zip dosyasına ekleyin.

    [![Proje kitaplığı penceresini gösterir](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

Teklif oluşturma hakkında bilgi edinmek için bkz. [bir Dynamics 365 for Operations teklifini oluşturma](./partner-center-portal/create-new-operations-offer.md).