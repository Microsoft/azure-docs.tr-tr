---
title: Azure AD Connect içinde eşitlenmeyen bir özniteliğin sorunlarını giderme | Microsoft Docs '
description: Bu konuda, sorun giderme görevini kullanarak öznitelik eşitlemeyle ilgili sorunları gidermeye yönelik adımlar sağlanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317496"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Azure AD Connect eşitlemede bir özniteliğin sorunlarını giderme

## <a name="recommended-steps"></a>**Önerilen Adımlar**

Öznitelik eşitleme sorunlarını araştırmadan önce **Azure AD Connect** eşitleme işlemini anlaalım:

  ![Eşitleme Işlemini Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terimler**

* **CS:** Bağlayıcı alanı, veritabanındaki bir tablodur.
* **MV:** Meta veri deposu, veritabanındaki bir tablo.
* **Ad:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Eşitleme adımları**

* AD 'den içeri aktar: Active Directory nesneleri AD CS 'ye getirilir.

* AAD 'den içeri aktar: Azure Active Directory nesneleri AAD CS 'ye alınır.

* Eşitleme: **gelen eşitleme kuralları** ve **giden eşitleme kuralları** , daha düşük bir öncelik sayısı sırasına göre çalışır. Eşitleme kurallarını görüntülemek için, Masaüstü uygulamalarından **eşitleme kuralları Düzenleyicisi** ' ne gidebilirsiniz. **Gelen eşitleme KURALLARı** CS 'den MV 'ye veri getirir. **Giden eşitleme kuralları** , verileri MV 'dan CS 'ye gider.

* AD 'ye Aktar: eşitleme çalıştırıldıktan sonra, nesneler AD CS 'den **Active Directory**'ye dışarı aktarılabilir.

* AAD 'ye Aktar: eşitleme çalıştırıldıktan sonra, nesneler AAD CS 'den **Azure Active Directory** dışarı aktarılmalıdır.

### <a name="step-by-step-investigation"></a>**Adım adım araştırma**

* **Meta** veri deposundaki aramamıza başlayacağız ve kaynaktan hedefe öznitelik eşlemesine bakacağız.

* Masaüstü uygulamalarından aşağıda gösterildiği gibi **Synchronization Service Manager** başlatın:

  ![Synchronization Service Manager Başlat](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* **Synchronization Service Manager** meta veri deposu **aramasını** seçin, **nesne türüne göre kapsam**' ı seçin, bir öznitelik kullanarak nesneyi seçin ve **Ara** düğmesine tıklayın.

  ![Meta veri deposu arama](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Tüm özniteliklerini görüntülemek için **Metadize** aramasında bulunan nesneye çift tıklayın. Tüm **bağlayıcı alanlarında** karşılık gelen nesneye bakmak için **Bağlayıcılar** sekmesine tıklayabilirsiniz.

  ![Meta veri deposu nesne bağlayıcıları](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* **Bağlayıcı alanı** özniteliklerini görüntülemek Için **Active Directory bağlayıcıya** çift tıklayın. **Önizleme** düğmesine tıklayın, aşağıdaki Iletişim kutusunda **Önizleme Oluştur** düğmesine tıklayın.

  ![Önizleme düğmesi vurgulanmış şekilde bağlayıcı alanı nesne özellikleri ekranını gösteren ekran görüntüsü.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Şimdi **Içeri aktarma öznitelik akışı**' na tıklayın, bu, **Active Directory bağlayıcı alanından** **metadize**'ye özniteliklerin akışını gösterir. **Eşitleme kuralı** sütunu, bu özniteliğe katkıda bulunulan **eşitleme kuralını** gösterir. **Veri kaynağı** sütunu, **bağlayıcı** alanındaki öznitelikleri gösterir. **Metadize özniteliği** sütunu, **meta** veri deposundaki öznitelikleri gösterir. Burada eşitleme değil özniteliği arayabilirsiniz. Burada özniteliği bulamazsanız, bu eşlenmez ve özniteliği eşlemek için yeni özel **eşitleme kuralı** oluşturmanız gerekir.

  ![Bağlayıcı alanı öznitelikleri](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* **Meta** veri deposundaki öznitelik akışını **giden eşitleme kurallarını** kullanarak **Active Directory bağlayıcı alanına** geri doğru görüntülemek Için sol bölmedeki **öznitelik akışını dışarı aktar** ' a tıklayın.

  ![Meta veri deposundaki öznitelik akışını giden eşitleme kuralları kullanılarak Active Directory bağlayıcı alanına geri gösteren ekran görüntüsü.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Benzer şekilde, **Azure Active Directory bağlayıcı alanı** nesnesini görüntüleyebilir ve **meta** veri deposundaki öznitelik akışını **bağlayıcı alanına** görüntülemek için **Önizleme** oluşturabilir ve bunun tersini yapabilir. bu şekilde, bir özniteliğin neden eşitlenmediğini inceleyebilirsiniz.

## <a name="recommended-documents"></a>**Önerilen Belgeler**
* [Azure AD Connect eşitleme: teknik kavramlar](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect eşitleme: mimariyi anlama](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect eşitleme: bildirime dayalı sağlamayı anlama](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect eşitleme: bildirim temelli sağlama Ifadelerini anlama](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect eşitleme: Varsayılan yapılandırmayı anlama](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect eşitleme: kullanıcıları, grupları ve kişileri anlama](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect eşitleme: gölge öznitelikleri](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md).
- [Karma kimlik nedir?](whatis-hybrid-identity.md).