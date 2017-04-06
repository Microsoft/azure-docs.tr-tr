---
title: "Azure Active Directory raporlama - önizleme | Microsoft Belgeleri"
description: "Azure Active Directory önizlemesinde kullanılabilen çeşitli raporları listeler"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: be986fd7bb1745dcf43a1066dfabc1e1c699ab4c
ms.openlocfilehash: b9cd11954a52600c1cd50155cb7ce9b7d2355cd3
ms.lasthandoff: 01/20/2017


---
# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory raporlama - önizleme


*Bu belge, [Azure Active Directory Raporlama Kılavuzu](active-directory-reporting-guide.md)’nun bir parçasıdır.*

Azure Active Directory önizlemesindeki raporlama ile ortamınızın nasıl çalıştığını belirlemek için gereken tüm bilgileri edinirsiniz. [Önizlemede neler var?](active-directory-preview-explainer.md)

İki temel raporlama alanı vardır:

* **Oturum açma etkinlikleri**: Yönetilen uygulamaların kullanımı ve kullanıcıların oturum açma etkinlikleri hakkında bilgiler
* **Denetim günlükleri** - Kullanıcılar ve grup yönetimi, yönetilen uygulamalarınız ve dizin etkinlikleriniz hakkında sistem etkinliği bilgileri

Aradığınız verilerin kapsamını bağlı olarak, bu raporlara erişmek için [Azure portal](https://portal.azure.com) hizmetler listesinde **Kullanıcılar ve gruplar** ya da **Kurumsal uygulamalar** seçeneklerine tıklayabilirsiniz.

## <a name="sign-in-activities"></a>Oturum açma etkinlikleri
### <a name="user-sign-in-activities"></a>Kullanıcı oturum açma etkinlikleri
Kullanıcı oturum açma raporu tarafından sağlanan bilgiler sayesinde aşağıdakiler gibi soruların yanıtlarını bulabilirsiniz:

* Belirli bir kullanıcının oturum açma düzeni nedir?
* Bir hafta içerisinde kaç adet kullanıcı oturum açtı?
* Bu açılan oturumların durumu nedir?

Bu verilere giriş noktanız, **Kullanıcılar ve gruplar** altındaki **Genel Bakış** bölümünde bulunan kullanıcı oturum açma grafiğidir.

 ![Raporlama](./media/active-directory-reporting-azure-portal/05.png "Reporting")

Kullanıcı oturum açma grafiği, belirli bir zaman dönemi içerisinde tüm kullanıcılara ait oturum açma işlemlerinin haftalık olarak toplanmış halini gösterir. Zaman dönemi için varsayılan süre 30 gündür.

![Raporlama](./media/active-directory-reporting-azure-portal/02.png "Reporting")

Oturum açma grafiğinde bir güne tıkladığınızda, oturum açma etkinliklerinin ayrıntılı bir listesini alırsınız.

![Raporlama](./media/active-directory-reporting-azure-portal/03.png "Reporting")

Oturum açma etkinlikleri listesindeki her satır, seçili oturum açma hakkında aşağıdakiler gibi ayrıntılı bilgiler sağlar:

* Kim oturum açtı?
* İlgili UPN neydi?
* Oturum açmanın hedefi hangi uygulamaydı?
* Oturum açmanın IP adresi nedir?
* Oturum açmanın durumu neydi?

### <a name="usage-of-managed-applications"></a>Yönetilen uygulamaların kullanımı
Oturum açma bilgilerinizin uygulama odaklı bir görünümüyle aşağıdakiler gibi sorular yanıtlanabilir:

* Uygulamalarımı kimler kullanıyor?
* Kuruluşunuzdaki en çok kullanılan ilk 3 uygulama nelerdir?
* Kısa bir süre önce bir uygulamayı kullanıma sundum. Uygulamanın durumu nedir?

Bu verilere giriş noktanız, **Kurumsal uygulamalar** altındaki **Genel Bakış** bölümünde bulunan kuruluşunuzda son 30 gün içinde en çok kullanılan ilk 3 uygulama raporudur.

 ![Raporlama](./media/active-directory-reporting-azure-portal/06.png "Reporting")

Belirli bir zaman döneminde en çok kullanılan ilk 3 uygulamanızda oturum açma işlemlerine ilişkin haftalık toplanan uygulama kullanımı grafiği. Zaman dönemi için varsayılan süre 30 gündür.

![Raporlama](./media/active-directory-reporting-azure-portal/78.png "Reporting")

İsterseniz belirli bir uygulamaya odaklanabilirsiniz.

![Raporlama](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Reporting")

Uygulama kullanımı grafiğinde bir güne tıkladığınızda, oturum açma etkinliklerinin ayrıntılı bir listesini alırsınız.

![Raporlama](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Reporting")

**Oturum açma işlemleri** seçeneği, size tüm uygulamalarınıza ait oturum açma olaylarına genel bir bakış sunar.

![Raporlama](./media/active-directory-reporting-azure-portal/85.png "Reporting")

Sütun seçiciyi kullanarak, görüntülenmesini istediğiniz veri alanlarını seçebilirsiniz.

![Raporlama](./media/active-directory-reporting-azure-portal/column_chooser.png "Reporting")

### <a name="filtering-sign-ins"></a>Oturum açma işlemlerini filtreleme
Görüntülenen veri miktarını sınırlamak için, oturum açma işlemlerini aşağıdaki alanları kullanarak filtreleyebilirsiniz:

* Tarih ve saat 
* Kullanıcı asıl adı
* Uygulama adı
* İstemci adı
* Oturum açma durumu

![Raporlama](./media/active-directory-reporting-azure-portal/293.png "Reporting")

Oturum açma etkinliklerine ait girişleri filtrelemenin başka bir yöntemi de belirli girdiler için arama gerçekleştirmektir.
Arama yöntemi, oturum açma işlemlerinin kapsamı olarak belirli **kullanıcıları**, **grupları** veya **uygulamaları** seçmenize olanak tanır.

![Raporlama](./media/active-directory-reporting-azure-portal/84.png "Reporting")

## <a name="audit-logs"></a>Denetim günlükleri
Azure Active Directory'deki denetim günlükleri uyumluluk amacıyla sistem etkinliklerinin kayıtlarını sağlar.

Azure portalda ilgili etkinlikleri denetlemeye yönelik üç ana kategori vardır:

* Kullanıcılar ve gruplar   
* Uygulamalar
* Dizin   

Denetim rapor etkinliklerinin tam listesi için [denetim raporu olaylarının listesi](active-directory-reporting-audit-events.md#list-of-audit-report-events) bölümüne bakın.

Tüm denetim verilerine giriş noktanız, **Azure Active Directory**’nin **Etkinlik** bölümünde bulunan **Denetim günlükleri** kısmıdır.

![Denetim](./media/active-directory-reporting-azure-portal/61.png "Auditing")

Denetim günlüğünde aktörleri (kim), etkinlikleri (ne) ve hedefleri gösteren bir liste görünümü vardır.

![Denetim](./media/active-directory-reporting-azure-portal/345.png "Auditing")

Liste görünümünde bir öğeye tıklayarak bu öğe hakkında daha fazla bilgi edinebilirsiniz.

![Denetim](./media/active-directory-reporting-azure-portal/873.png "Auditing")

### <a name="users-and-groups-audit-logs"></a>Kullanıcı ve gruplara yönelik denetim günlükleri
Kullanıcı ve grup tabanlı denetim raporları ile aşağıdakiler gibi soruların yanıtlarını alabilirsiniz:

* Kullanıcılara hangi tür güncelleştirmeler uygulanmış?
* Kaç adet kullanıcı değiştirildi?
* Kaç adet parola değiştirildi?
* Bir yönetici bir dizinde neler yaptı?
* Eklenmiş olan gruplar hangileridir?
* Üyelik değişiklikleri olan gruplar var mı?
* Grubun sahipleri değişti mi?
* Bir grup veya kullanıcıya hangi lisanslar atanmış?

Yalnızca kullanıcı ve gruplarla ilgili denetim verilerini gözden geçirmek istiyorsanız, **Kullanıcılar ve Gruplar**’ın **Etkinlik** bölümündeki **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz.

![Denetim](./media/active-directory-reporting-azure-portal/93.png "Auditing")

### <a name="application-audit-logs"></a>Uygulama denetim günlükleri
Uygulama tabanlı denetim raporları ile aşağıdakiler gibi soruların yanıtlarını alabilirsiniz:

* Eklenmiş veya güncelleştirilmiş olan uygulamalar hangileridir?
* Kaldırılmış olan uygulamalar hangileridir?
* Belirli bir uygulamaya ait bir hizmet ilkesi değiştirildi mi?
* Uygulamaların adları değiştirildi mi?
* Belirli bir uygulama için kim onay verdi?

Yalnızca uygulamalarla ilgili denetim verilerini gözden geçirmek istiyorsanız, **Kurumsal uygulamalar**’ın **Etkinlik** bölümündeki **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz.

![Denetim](./media/active-directory-reporting-azure-portal/134.png "Auditing")

### <a name="filtering-audit-logs"></a>Denetim günlüklerini filtreleme
Görüntülenen veri miktarını sınırlamak için, oturum açma işlemlerini aşağıdaki alanları kullanarak filtreleyebilirsiniz:

* Tarih ve saat
* Aktörün kullanıcı asıl adı
* Etkinlik türü
* Etkinlik

![Denetim](./media/active-directory-reporting-azure-portal/356.png "Auditing")

**Etkinlik Türü** listesinin içeriği bu dikey pencerenin giriş noktasına bağlıdır.  
Giriş noktanız Azure Active Directory ise, bu liste tüm olası etkinlik türlerini içerir:

* Uygulama 
* Grup 
* Kullanıcı
* Cihaz
* Dizin
* İlke
* Diğer

![Denetim](./media/active-directory-reporting-azure-portal/825.png "Auditing")

Listedeki etkinliklerin kapsamı etkinlik türüne göre belirlenir.
Örneğin, **Etkinlik Türü** olarak **Grup** seçeneğini belirlediyseniz **Etkinlik** listesi yalnızca grupla ilgili etkinlikleri içerir.   

![Denetim](./media/active-directory-reporting-azure-portal/654.png "Auditing")

Denetim raporlarına ait girişleri filtrelemenin başka bir yöntemi de belirli girdiler için arama gerçekleştirmektir.

![Denetim](./media/active-directory-reporting-azure-portal/237.png "Auditing")

## <a name="next-steps"></a>Sonraki adımlar
Bkz. [Azure Active Directory Raporlama Kılavuzu](active-directory-reporting-guide.md).


