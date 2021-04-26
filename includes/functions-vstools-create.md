---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84731052"
---
Visual Studio 'daki Azure Işlevleri proje şablonu, Azure 'da bir işlev uygulamasına yayımlayabilecek bir proje oluşturur. İşlevleri, kaynakların daha kolay yönetilmesi, dağıtılması, ölçeklendirilmesi ve paylaşılması için mantıksal birim olarak gruplamak üzere bir işlev uygulaması kullanabilirsiniz.

1. Visual Studio menüsünden **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.

1. **Yeni proje oluştur**' da, arama *kutusuna Işlevler* girin, **Azure Işlevleri** şablonunu seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın** bölümünde projeniz Için bir **Proje adı** girin ve ardından **Oluştur**' u seçin. İşlev uygulamasının adı, bir C# ad alanı olarak geçerli olmalıdır; bu nedenle alt çizgi, kısa çizgi veya alfasayısal olmayan herhangi bir karakter kullanmayın.

1. **Yeni Azure işlevleri uygulama ayarları oluşturma** için aşağıdaki tablodaki değerleri kullanın:

    | Ayar      | Değer  | Açıklama                      |
    | ------------ |  ------- |----------------------------------------- |
    | **İşlevler çalışma zamanı** | **Azure Işlevleri v3 <br /> (.NET Core)** | Bu değer, .NET Core 3. x ' i destekleyen Azure Işlevlerinin sürüm 3. x çalışma zamanını kullanan bir işlev projesi oluşturur. Azure İşlevleri 1.x, .NET Framework’ü destekler. Daha fazla bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerine genel bakış](../articles/azure-functions/functions-versions.md).   |
    | **İşlev şablonu** | **HTTP tetikleyicisi** | Bu değer, bir HTTP isteği tarafından tetiklenen bir işlev oluşturur. |
    | **Depolama hesabı (AzureWebJobsStorage)**  | **Depolama öykünücüsü** | Azure Işlevi için bir depolama hesabı gerektiğinden, projenizi Azure 'da yayımladığınızda bir tane atanır veya oluşturulur. HTTP tetikleyicisi, Azure depolama hesabı bağlantı dizesi kullanmaz; diğer tüm tetikleyici türleri için geçerli bir Azure depolama hesabı bağlantı dizesi gerekir.  |
    | **Yetkilendirme düzeyi** | **Deðeri** | Oluşturulan işlev, anahtar gerektirmeden herhangi bir istemci tarafından tetiklenebilir. Bu yetkilendirme ayarı yeni işlevinizi test etmenizi kolaylaştırır. Anahtarlar ve yetkilendirme hakkında daha fazla bilgi için bkz. [Yetkilendirme anahtarları](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) ve [http ve Web kancası bağlamaları](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Azure Işlevleri proje ayarları](./media/functions-vs-tools-create/functions-project-settings.png)

    **Yetkilendirme düzeyini** **anonim** olarak ayarladığınızdan emin olun. Varsayılan **işlev** düzeyini seçerseniz, işlev uç noktanıza erişmek için isteklerde [işlev anahtarı](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) sunmak gerekir.

1. İşlev projesi ve HTTP tetikleme işlevini oluşturmak için **Oluştur** ' u seçin.
