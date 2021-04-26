---
title: Yayınlanan Azure Sertifikalı cihazınızı düzenleme
description: Azure Sertifikalı cihaz programı aracılığıyla cihazınızı sertifikalandırdıktan ve yayımladıktan sonra cihaz bilgilerini düzenleme kılavuzu.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 14a29d438103f07dd35b3a3380b7cc094f215824
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304452"
---
# <a name="edit-your-published-device"></a>Yayınlanan cihazınızı düzenleme

Cihazınız sertifikalandırılması ve Azure Sertifikalı cihaz kataloğuna yayımlandıktan sonra, cihazınızın ayrıntılarını güncelleştirmeniz gerekebilir. Bunun nedeni, dağıtıcı listenizin bir güncelleştirmesi, satın alma sayfası URL 'Lerinde değişiklikler veya donanım belirtimlerinde (işletim sistemi sürümü veya yeni bir bileşen ekleme gibi) yapılan güncelleştirmeler olabilir. Azure Sertifikalı cihaz portalını kullanarak, ürününüzü kataloğumuza kaldırmadan cihaz bilgilerinizin güncelleştirilmesini kolaylaştırdık.

## <a name="prerequisites"></a>Önkoşullar

- [Azure Sertifikalı cihaz portalında](https://certify.azure.com)oturum açmış ve cihazınız için **onaylanmış** bir projenin olması gerekir. Sertifikalı bir cihazınız yoksa, kullanmaya başlamak için bu [öğreticiyi](tutorial-01-creating-your-project.md) görüntüleyebilirsiniz.

## <a name="editing-your-published-project"></a>Yayımlanmış projenizi düzenleniyor

Proje özetinde, zaten gözden geçirilmiş ve kabul edildiği için projenizin salt okunurdur modunda olduğunu fark etmelisiniz. Değişiklik yapmak için, projenize bir düzenleme istemeniz ve güncelleştirmenin Azure Sertifika ekibi tarafından yeniden onaylanmasını sağlayabilirsiniz.

1. `Request Metadata Edit`Sayfanın üst kısmındaki düğmeye tıklayın  

    ![Meta veri güncelleştirmesi iste](./media/images/request-metadata-edit.png)

1. Düzenledikten sonra ürününüzü gözden geçirilmek üzere göndermeniz gereken sayfada bildirimi kabul edin.
    > [!NOTE]
    > Bu düzenlemeyi onaylayarak, zaten yayımlanmışsa cihazınızı Azure Sertifikalı cihaz kataloğu 'ndan **kaldırmadıysanız** . Cihazınızı yeniden yayınlayana kadar ürünün önceki sürümü katalogda kalacaktır.

1. Bu uyarıyı gönderdikten sonra cihaz ayrıntılarınızı düzenleyebilirsiniz. Değiştirilen öğesinin bölümünde bir notun ayrıldığınızdan emin olun `Comments for Reviewer` `Device Details` .

    ![Meta veri düzenlemeyi göz önünde](./media/images/edit-notes.png)

1. Proje Özeti sayfasında, `Submit for review` değişikliklerinizin Azure Sertifika ekibi tarafından yeniden onaylanmasını sağlamak için öğesine tıklayın.
1. Değişiklikleriniz incelendikten ve onaylandıktan sonra, portalda Katalog üzerinden yaptığınız değişiklikleri yeniden yayımlayabilirsiniz ( [öğreticimize](./tutorial-04-publishing-your-device.md)bakın).

## <a name="next-steps"></a>Sonraki adımlar

Artık cihazınızı Azure Sertifikalı cihaz kataloğunda başarıyla düzenlediniz. Katalogda yaptığınız değişiklikleri kullanıma alabilir veya başka bir cihazı onaylayın!
- [Azure Sertifikalı cihaz kataloğu](https://devicecatalog.azure.com/)
- [Bir cihaz sertifika ile çalışmaya başlama](./tutorial-01-creating-your-project.md)
