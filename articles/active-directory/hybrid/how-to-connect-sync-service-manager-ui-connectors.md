---
title: Azure AD Synchronization Service Manager Kullanıcı arabirimindeki bağlayıcılar | Microsoft Docs '
description: Azure AD Connect için Synchronization Service Manager bağlayıcılar sekmesini anlayın.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91319886"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Azure AD Connect eşitleme Service Manager bağlayıcı kullanma

![Eşitleme Service Manager gösteren ekran görüntüsü.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Bağlayıcılar sekmesi, eşitleme altyapısının bağlı olduğu tüm sistemleri yönetmek için kullanılır.

## <a name="connector-actions"></a>Bağlayıcı eylemleri
| Eylem | Yorum |
| --- | --- |
| Oluştur |Kullanmayın. Ek AD ormanlarına bağlanmak için Yükleme Sihirbazı ' nı kullanın. |
| Özellikler |Etki alanı ve OU filtrelemesi için kullanılır. |
| [Silme](#delete) |Bağlayıcı alanındaki verileri silmek ya da bir ormana bağlantıyı silmek için kullanılır. |
| [Çalıştırma profillerini yapılandırma](#configure-run-profiles) |Etki alanı filtreleme dışında, burada yapılandırılacak bir şey yoktur. Bu eylemi, zaten yapılandırılmış çalıştırma profillerini görmek için kullanabilirsiniz. |
| Çalıştır |Bir profilin tek bir çalıştırmasını başlatmak için kullanılır. |
| Durdur |Şu anda bir profili çalıştıran bir bağlayıcıyı sonlandırır. |
| Bağlayıcıyı dışarı aktar |Kullanmayın. |
| Bağlayıcıyı içeri aktar |Kullanmayın. |
| Bağlayıcıyı Güncelleştir |Kullanmayın. |
| Şemayı Yenile |Önbelleğe alınmış şemayı yeniler. Bunun yerine yükleme sihirbazındaki seçeneğinin kullanılması tercih edilir çünkü bu, eşitleme kurallarını da güncelleştirir. |
| [Bağlayıcı alanını ara](#search-connector-space) |Nesneleri bulmak ve sistem aracılığıyla bir nesneyi ve verilerini Izlemek için kullanılır. |

### <a name="delete"></a>Sil
Silme eylemi iki farklı şey için kullanılır.  
!["Bağlayıcıyı Sil" penceresini "yalnızca bağlayıcı alanı Sil" seçiliyken gösteren ekran görüntüsü.](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

**Bağlayıcı alanını Sil seçeneği yalnızca** tüm verileri kaldırır, ancak yapılandırmayı tutar.

**Bağlayıcı ve bağlayıcı alanı silme** seçeneği, verileri ve yapılandırmayı kaldırır. Bu seçenek, artık bir ormana bağlanmak istemediğiniz durumlarda kullanılır.

Her iki seçenek de tüm nesneleri eşitler ve metadize nesnelerini güncelleştirir. Bu eylem, uzun süren bir işlemdir.

### <a name="configure-run-profiles"></a>Çalıştırma profillerini yapılandırma
Bu seçenek, bir bağlayıcı için yapılandırılmış çalıştırma profillerini görmenizi sağlar.

!["Delta Içeri aktarma" seçiliyken "çalıştırma profillerini Yapılandır" penceresini gösteren ekran görüntüsü.](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Bağlayıcı alanını ara
Bağlayıcı alanını ara eylemi, nesneleri bulmak ve veri sorunlarını gidermek için kullanışlıdır.

!["Bağlayıcı alanını ara" penceresini gösteren ekran görüntüsü.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Bir **kapsam** seçerek başlayın. Verilere (RDN, DN, tutturucu, alt ağaç) veya nesnenin durumuna (diğer tüm seçenekler) göre arama yapabilirsiniz.  
!["Kapsam" açılan menüsünü gösteren ekran görüntüsü.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Örneğin Sub-Tree arama yaparsanız, tek bir OU 'daki tüm nesneleri alırsınız.  
!["Alt ağaç" aramasına bir örnek gösteren ekran görüntüsü.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Bu kılavuzdan bir nesne seçebilir, **Özellikler**' i seçebilir ve kaynak bağlayıcı alanından, meta veri deposu ve hedef bağlayıcı alanına göre [takip](tshoot-connect-object-not-syncing.md) edebilirsiniz.

### <a name="changing-the-ad-ds-account-password"></a>AD DS hesap parolasını değiştirme
Hesap parolasını değiştirirseniz, eşitleme hizmeti artık şirket içi AD 'deki değişiklikleri içeri/dışarı aktaramayacaktır.   Şu durumlarla karşılaşabilirsiniz:

- AD Bağlayıcısı için içeri/dışarı aktarma adımı "Hayır-Başlat-kimlik bilgileri" hatası ile başarısız oluyor.
- Windows Olay Görüntüleyicisi altında, uygulama olay günlüğü olay KIMLIĞI 6000 ve "Yönetim Aracısı" contoso.com ", kimlik bilgileri geçersiz olduğundan çalıştırılamadı." iletisini içeren bir hata içerir. "

Bu sorunu çözmek için, AD DS kullanıcı hesabını aşağıdakileri kullanarak güncelleştirin:


1. Synchronization Service Manager başlatın (→ eşitleme hizmetini başlatın).
</br>![Eşitleme Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. **Bağlayıcılar** sekmesine gidin.
3. AD DS hesabını kullanmak üzere yapılandırılan AD bağlayıcısını seçin.
4. Eylemler altında **Özellikler**' i seçin.
5. Açılır iletişim kutusunda Active Directory ormana Bağlan ' ı seçin:
6. Orman adı, şirket içi AD 'ye karşılık gelen adını gösterir.
7. Kullanıcı adı, eşitleme için kullanılan AD DS hesabını belirtir.
8. Parola metin kutusuna AD DS hesabının yeni parolasını girin ![ Azure AD Connect şifreleme anahtarını Eşitle yardımcı programı](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Yeni parolayı kaydetmek için Tamam ' ı tıklatın ve eski parolayı bellek önbelleğinden kaldırmak için eşitleme hizmetini yeniden başlatın.



## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
