---
title: include dosyası
description: include dosyası
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 88e11adadcc06875964146ad2046828267258038
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389377"
---
## <a name="lifecycle"></a>Yaşam Döngüsü

**Yaşam döngüsü** sekmesinde, kullanıcının erişim paketine atamanın ne zaman sona ereceğini belirtirsiniz. Ayrıca, kullanıcıların atamalarını genişletip genişletemeyeceğini de belirtebilirsiniz.

1. **Süre sonu** bölümünde, **erişim paketi atamalarının süresi** **bitiş tarihi**, **gün sayısı**veya **hiçbir zaman**olarak ayarlanır.

    **Tarih**açısından, gelecekte bir sona erme tarihi seçin.

    **Gün sayısı**için 0 ila 3660 gün arasında bir sayı belirtin.

    Seçiminize bağlı olarak, bir kullanıcının erişim paketine atanması belirli bir tarihte, belirli bir gün sonra onaylandıktan sonra veya hiçbir zaman sona erer.

1. Ek ayarları göstermek için **Gelişmiş süre sonu ayarlarını göster** ' e tıklayın.

    ![Erişim paketi yaşam döngüsü süre sonu ayarları](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Kullanıcının atamalarını genişletmesine izin vermek için **kullanıcıların erişimi genişlemesine Izin ver** seçeneğini belirleyin **.**

    İlkede uzantılara izin veriliyorsa, Kullanıcı bir e-posta 14 gün ve ayrıca erişim paketi atamasının, atamayı genişletmesini istemek üzere süre sonu olarak ayarlanmayacak şekilde 1 gün alır.

    Bir kullanıcının erişimi genişletilmişse, belirtilen uzatma tarihine (ilkeyi oluşturan kullanıcının saat diliminde ayarlanan Tarih) sonra erişim paketini isteyemeyecektir.

1. Uzantı vermek için onay gerektirmek için, uzantıyı **Evet**'e **vermek üzere onay gerektir** seçeneğini belirleyin.

    **İstekler** sekmesinde belirtilen onay ayarları da kullanılacaktır.

1. **İleri** veya **Güncelleştir**' e tıklayın.
