---
title: Ticari Market Analytics verilerine erişmek için örnek uygulama
description: Kendi ticari Market Analytics uygulamanızı derlemek için örnek uygulamayı kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584224"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Ticari Market Analytics verilerine erişmek için örnek uygulama

Örnek uygulamalar C# ve JAVA dillerinde oluşturulur ve [GitHub](https://github.com/partneranalytics)'da kullanılabilir.

- [C# örnek uygulaması](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [JAVA örnek uygulaması](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Örnek uygulamadan alma ve kendi uygulamanızı dilediğiniz dilde oluşturma seçeneğini belirleyebilirsiniz.

Örnek uygulama aşağıdaki hedeflere ulaşır:

- Azure Active Directory (Azure AD) belirteci oluşturur.
- Kullanılabilir veri kümelerini alır.
- Kullanıcı tanımlı sorgular oluşturur.
- Kullanıcı tanımlı ve sistem sorgularını alır.
- Bir raporu zamanlar.

Örnek uygulama, diğer işlevlere yönelik API 'Leri çağırma yöntemini kapsamaz. Ancak, diğer API 'Leri çağırma işlemi yukarıda özetlenen ile aynı kalır.

## <a name="how-to-run-the-application"></a>Uygulamayı çalıştırma

1. Şu komutu kullanarak depoyu yerel sisteme kopyalayın:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Daha fazla yönerge için `ProgrammaticExportSampleAppISV/README.md` GitHub [deposundaki](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)dosyasına bakın.

1. Uygulamayı hızlı bir şekilde çalıştırmak için, **üzerindeappsettings.Development.js** istemci kimliğini ve istemci gizli anahtarını güncelleştirin

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Dosyadaki appsettings.Development.jsbir kod parçacığını gösterir.":::

Uygulamanın çalıştırılması, yerel bir Web sunucusu başlatır ve bir sayfa ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ) açılır.

[![Rapor zamanlama sayfasını gösterir.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Bu sayfa, yerel makinede çalışan Web sunucusuna API çağrıları yapar, bu da gerçek programlı erişim API 'SI çağrılarını yapar.

## <a name="code-snippets"></a>Kod parçacıkları

Programlı erişim API 'SI çağrıları yapmak için C# kodunun temel yapısı aşağıdaki gibidir:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="API çağrılarının ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market Analytics verilerine erişim için API 'Ler](analytics-available-apis.md)
