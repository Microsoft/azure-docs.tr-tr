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
ms.openlocfilehash: db13884e3deed8e990d21d82e215a1e837371275
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98052755"
---
## <a name="lifecycle"></a>Yaşam döngüsü

**Yaşam döngüsü** sekmesinde, kullanıcının erişim paketine atamanın ne zaman sona ereceğini belirtirsiniz. Ayrıca, kullanıcıların atamalarını genişletip genişletemeyeceğini de belirtebilirsiniz.

1. **Süre sonu** bölümünde, **erişim paketi atamalarının süresi** **bitiş tarihi**, **gün sayısı** veya **hiçbir zaman** olarak ayarlanır.

    **Tarih** açısından, gelecekte bir sona erme tarihi seçin.

    **Gün sayısı** için 0 ila 3660 gün arasında bir sayı belirtin.

    Seçiminize bağlı olarak, bir kullanıcının erişim paketine atanması belirli bir tarihte, belirli bir gün sonra onaylandıktan sonra veya hiçbir zaman sona erer.

1. Ek ayarları göstermek için **Gelişmiş süre sonu ayarlarını göster** ' e tıklayın.

    ![Erişim paketi yaşam döngüsü süre sonu ayarları](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Kullanıcının atamalarını genişletmesine izin vermek için **kullanıcıların erişimi genişlemesine Izin ver** seçeneğini belirleyin **.**

    İlkede uzantılara izin veriliyorsa, Kullanıcı bir e-posta 14 gün ve erişim paketi atamasının süre sonu olarak ayarlanmasının ardından bir gün sonra da atamayı genişletmesini ister. Kullanıcı, bir uzantı talep ettikleri sırada hala ilke kapsamında olmalıdır. Ayrıca, ilke için açık bir bitiş tarihi varsa ve Kullanıcı erişimi uzatmak için bir istek gönderdiğinde, erişim paketine Kullanıcı erişimi vermek için kullanılan ilkede tanımlandığı şekilde, istekteki uzantı tarihinin, atamaların süresi dolduğunda veya daha önce olması gerekir. Örneğin, ilke, atamaların 30 Haziran tarihinde süre sonu olarak ayarlandığını gösteriyorsa, bir kullanıcının istemesi için izin verilen en fazla uzantı 30 Haziran 'da olur.

    Bir kullanıcının erişimi genişletilmişse, belirtilen uzatma tarihine (ilkeyi oluşturan kullanıcının saat diliminde ayarlanan Tarih) sonra erişim paketini isteyemeyecektir.

1. Uzantı vermek için onay gerektirmek için, uzantıyı **Evet**'e **vermek üzere onay gerektir** seçeneğini belirleyin.

    **İstekler** sekmesinde belirtilen onay ayarları da kullanılacaktır.

1. **İleri** veya **Güncelleştir**' e tıklayın.
