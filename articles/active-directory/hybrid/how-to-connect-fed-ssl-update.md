---
title: Azure AD Connect - güncelleştirme SSL sertifikası için bir AD FS grubu | Microsoft Docs
description: Bu belge Azure AD Connect kullanarak bir AD FS grubunun SSL sertifikasını güncelleştirmek için adımları açıklanmaktadır.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fcdf75586de9621bd0e42cba6fa9c80e5d30817
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595205"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Active Directory Federasyon Hizmetleri (AD FS) grubu için SSL sertifikasını güncelleştirme

## <a name="overview"></a>Genel Bakış
Bu makalede, bir Active Directory Federasyon Hizmetleri (AD FS) grubu için SSL sertifikasını güncelleştirmek için Azure AD Connect'i nasıl kullanabileceğiniz açıklanır. Seçilen kullanıcı oturum açma yöntemi, AD FS olmasa bile SSL sertifikası için bir AD FS grubunu kolayca güncelleştirmek için Azure AD Connect aracını kullanabilirsiniz.

Tüm Federasyon ve üç basit adımda Web uygulaması Ara sunucusu (WAP) sunucuları için AD FS grubunu SSL sertifikasını güncelleştirme bütün işlem gerçekleştirebilirsiniz:

![Üç adım](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>AD FS tarafından kullanılan sertifikalar hakkında daha fazla bilgi için bkz: [AD FS tarafından kullanılan sertifikaları anlama](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Önkoşullar

* **AD FS grubu**: AD FS grubunuzun Windows Server 2012 R2 tabanlı veya üstü olduğundan emin olun.
* **Azure AD Connect**: Azure AD Connect sürümü 1.1.553.0 olduğundan emin olun veya yüksek. Görev kullanacağınız **güncelleştirme AD FS SSL sertifikası**.

![SSL görevi güncelleştir](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>1\. adım: AD FS grubu bilgileri sağlayın

Azure AD Connect tarafından otomatik olarak AD FS grubu hakkında bilgi edinmek çalışır:
1. (Windows Server 2016 veya sonraki sürümleri) AD FS grubu bilgileri Sorgulanıyor.
2. Önceki çalıştırmalardan bilgileri başvuran, hangi Azure AD Connect ile yerel olarak depolanır.

Ekleyerek veya kaldırarak AD FS grubunun geçerli yapılandırmayı yansıtacak şekilde sunucuları görüntülenen sunucularının listesini değiştirebilirsiniz. Sunucu bilgilerini sağlanan hemen sonra Azure AD Connect bağlantı ve geçerli SSL sertifikası durumunu görüntüler.

![AD FS sunucu bilgisi](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Listede artık AD FS grubunun parçası olan bir sunucunuz varsa, tıklayın **Kaldır** sunucusu AD FS grubunuzdaki sunucuların listesini silmek için.

![Çevrimdışı sunucu listesinde](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Bir sunucu için bir AD FS grubunu Azure AD CONNECT'te sunucuların listesinden kaldırmak, yerel bir işlemdir ve Azure AD Connect yerel olarak tutar AD FS grubu bilgileri güncelleştirir. Azure AD Connect değişimi yansıtmak için AD FS yapılandırmasını değiştirmez.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>2\. adım: Yeni bir SSL sertifikası sağlayın

Azure AD Connect, AD FS grubunun sunucuları hakkında bilgi onayladıktan sonra yeni SSL sertifikası için sorar. Yüklemeye devam etmek için bir parola korumalı PFX sertifikası sağlayın.

![SSL sertifikası](./media/how-to-connect-fed-ssl-update/certificate.png)

Sertifika verdikten sonra Azure AD Connect önkoşulları bir dizi gider. Sertifika için bir AD FS grubunu doğru olduğundan emin olmak için sertifika doğrulayın:

-   Sertifikanın konu adı/alternatif konu adı ya da Federasyon Hizmeti adıyla aynı olduğundan veya joker sertifikadır.
-   Sertifika 30 günden fazla bir süre için geçerlidir.
-   Sertifikanın güven zincirinde geçerli değil.
-   Parola korumalı sertifikadır.

## <a name="step-3-select-servers-for-the-update"></a>3\. adım: Güncelleştirme sunucularını seçin

Sonraki adımda, güncelleştirilmiş SSL sertifikasına sahip olmasını gerektiren sunucuları seçin. Çevrimdışı olan sunucuları güncelleştirmesi seçilemez.

![Güncelleştirme için sunucuları seçin](./media/how-to-connect-fed-ssl-update/selectservers.png)

Yapılandırmayı tamamladıktan sonra Azure AD Connect güncelleştirme durumunu gösterir ve AD FS oturum açma kullanarak doğrulamak için bir seçenek sunan bir ileti görüntüler.

![Yapılandırma tamamlandı](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>SSS

* **Hangi sertifikanın konu adı için yeni AD FS SSL sertifikası olmalı?**

    Azure AD Connect, sertifika konu adı/alternatif konu adı, Federasyon Hizmeti adı içerip içermediğini denetler. Örneğin, Federasyon Hizmeti adınız fs.contoso.com ise, konu adı/alternatif konu adı fs.contoso.com olması gerekir.  Joker karakterli sertifikalar de kabul edilir.

* **Neden kimlik bilgileri için WAP sunucusu sayfasında yeniden soruluyor?**

    AD FS sunucularına bağlanmak için verdiğiniz kimlik bilgileri de WAP sunucularını yönetme ayrıcalığına sahip değilseniz, Azure AD Connect WAP sunucularında yönetici ayrıcalıklarına sahip kimlik bilgilerini ister.

* **Sunucu çevrimdışı olarak gösterilir. Ne yapmalıyım?**

    Azure AD Connect, sunucu çevrimdışı ise, herhangi bir işlem gerçekleştirilemiyor. Sunucu AD FS grubunun parçasıysa, ardından sunucu bağlantısını denetleyin. Sorunu çözdükten sonra sihirbazda durumunu güncelleştirmek için Yenile simgesine basın. Sunucu parçası ise Grup daha önce ancak şimdi artık yok.'a tıklayın **Kaldır** , Azure AD Connect sunucuları listeden silmek için korur. Listeden Azure AD Connect sunucusu kaldırma, AD FS yapılandırmasını değiştirmez. AD FS'yi Windows Server 2016 veya sonraki sürümlerde, yapılandırma ayarlarında sunucu kalır kullanıyorsanız ve yeniden başlatıldığında gösterilecek görev çalıştırılır.

* **Yeni SSL sertifikası ile bir alt kümesini grubu Sunucularım güncelleştirebilir miyim?**

    Evet. Her zaman görevin çalışacağı **SSL sertifika güncelleştirmesi** geri kalan sunucular yeniden güncelleştirilecek. Üzerinde **SSL için sunucu seçin sertifika güncelleştirme** sayfası üzerinde sunucularının listesini sıralayabilirsiniz **SSL sona erme tarihi** henüz güncelleştirilmemiş sunucuları kolayca erişmek için.

* **Sunucunun önceki çalıştırmada kaldırdım, ancak bu yine de çevrimdışı ve listelenmiş olarak AD FS sunucuları sayfasında gösteriliyor. Neden kaldırdım daha sonra çevrimdışı sunucunun hala var mı?**

    Listeden Azure AD Connect sunucusunun kaldırılması AD FS yapılandırmasında kaldırmaz. Azure AD Connect, AD FS (Windows Server 2016 veya üzeri) grupla ilgili tüm bilgilere yönelik başvuruyor. Sunucu AD FS yapılandırmasında hala mevcutsa listede listelenir.  

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Connect ve federasyon](how-to-connect-fed-whatis.md)
- [Active Directory Federasyon Hizmetleri Yönetimi ve Azure AD Connect ile özelleştirme](how-to-connect-fed-management.md)

