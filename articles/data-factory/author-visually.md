---
title: Görsel yazma
description: Azure Data Factory 'de görsel yazma kullanmayı öğrenin
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 09/08/2020
ms.openlocfilehash: 704360ac37e016de9efe2248181f7db358f5a7cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371490"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory 'de görsel yazma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory Kullanıcı arabirimi deneyimi (UX), herhangi bir kod yazmak zorunda kalmadan veri fabrikanızın kaynaklarını görsel olarak yazmanızı ve dağıtmanızı sağlar. Etkinlikleri işlem hattı tuvaline sürükleyebilir, test çalıştırmaları yapabilir, yinelemeli olarak hataları ayıklayabilir, işlem hattı çalıştırmalarınızı dağıtabilir ve izleyebilirsiniz.

Şu anda Azure Data Factory UX yalnızca Microsoft Edge ve Google Chrome 'da desteklenir.

## <a name="authoring-canvas"></a>Yazı tuvali

**Yazma tuvalini** açmak için kalem simgesine tıklayın. 

![Yazı tuvali](media/author-visually/authoring-canvas.png)

Burada, fabrikanızı oluşturan işlem hatları, Etkinlikler, veri kümeleri, bağlı hizmetler, veri akışları, Tetikleyiciler ve tümleştirme çalışma zamanları hakkında yazar. Yazma tuvali 'ni kullanarak bir işlem hattı oluşturmaya başlamak için bkz. [kopyalama etkinliğini kullanarak verileri kopyalama](tutorial-copy-data-portal.md). 

Varsayılan görsel yazma deneyimi, Data Factory hizmetiyle doğrudan çalışır. Veri Fabrikası işlem hatlarınız üzerinde çalışma için kaynak denetimi ve işbirliği yapmak üzere Azure Repos git veya GitHub tümleştirmesi de desteklenir. Bu yazma deneyimleri arasındaki farklar hakkında daha fazla bilgi edinmek için [Azure Data Factory Içindeki kaynak denetimi](source-control.md)konusuna bakın.

### <a name="properties-pane"></a>Özellikler bölmesi

İşlem hatları, veri kümeleri ve veri akışları gibi üst düzey kaynaklar için üst düzey özellikler, tuvalin sağ tarafındaki Özellikler bölmesinde düzenlenebilir. Özellikler bölmesi ad, açıklama, ek açıklamalar ve diğer üst düzey özellikler gibi özellikleri içerir. İşlem hattı etkinlikleri ve veri akışı dönüştürmeleri gibi alt kaynaklar, tuvalin alt kısmındaki panel kullanılarak düzenlenir. 

![Özellikler bölmesi](media/author-visually/properties-pane.png)

Özellikler bölmesi yalnızca kaynak oluşturma sırasında varsayılan olarak açılır. Düzenlemek için, tuvalin sağ üst köşesinde bulunan özellikler bölmesi simgesine tıklayın.

### <a name="related-resources"></a>İlgili kaynaklar

Özellikler bölmesinde, **ilgili** sekmeyi seçerek seçilen kaynağa hangi kaynakların bağımlı olduğunu görebilirsiniz. Geçerli kaynağa başvuruda bulunan herhangi bir kaynak burada listelenir.

![İlgili kaynaklar](media/author-visually/related-resources.png)

Örneğin, yukarıdaki görüntüde bir işlem hattı ve iki veri akışı şu anda seçili olan veri kümesini kullanır.

## <a name="management-hub"></a>Yönetim merkezi

Azure Data Factory UX içindeki *Yönet* sekmesi tarafından erişilen Yönetim Merkezi, veri fabrikanızın genel yönetim eylemlerini barındıran bir portalıdır. Burada, veri depoları ve dış hesaplar, kaynak denetimi yapılandırması ve tetikleyici ayarları bağlantılarını yönetebilirsiniz. Daha fazla bilgi için [Yönetim hub 'ının](author-management-hub.md)özellikleri hakkında bilgi edinin.

![Bağlı hizmetleri yönetme](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>İfadeler ve işlevler

Azure Data Factory ' de birçok özellik belirtmek için, statik değerler yerine ifadeler ve işlevler kullanılabilir.

Bir özellik değeri için bir ifade belirtmek üzere, **dinamik Içerik Ekle** ' yi seçin veya alana odaklanırken **alt + P** ' ye tıklayın.

![Dinamik Içerik Ekle](media/author-visually/dynamic-content-1.png)

Bu, desteklenen sistem değişkenlerinden, etkinlik çıktısından, işlevlerden ve Kullanıcı tarafından belirtilen değişkenlerle ya da parametrelerden ifade oluşturabileceğiniz **Data Factory Ifade oluşturucusunu** açar. 

![İfade oluşturucusu](media/author-visually/dynamic-content-2.png)

İfade dili hakkında daha fazla bilgi için, [Azure Data Factory Içindeki ifadeler ve işlevler](control-flow-expression-language-functions.md)bölümüne bakın.

## <a name="provide-feedback"></a>Geribildirim gönderme

Özellikler hakkında yorum yapmak veya araçla ilgili sorunları bildirmek için **geri bildirim** ' ı seçin:

![Geri Bildirim](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Sonraki adımlar

İşlem hatlarını izleme ve yönetme hakkında daha fazla bilgi edinmek için bkz. işlem [hatlarını programlama yoluyla izleme ve yönetme](monitor-programmatically.md).
