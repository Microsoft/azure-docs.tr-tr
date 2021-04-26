---
title: B2B işbirliği kullanıcılarına toplu davet etme öğreticisi-Azure AD
description: Bu öğreticide, harici Azure AD B2B işbirliği kullanıcılarına toplu davet göndermek için PowerShell ve CSV dosyasının nasıl kullanılacağını öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4e4892c01775b472cd8cdcf0f35b920d7e5e86
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055688"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Öğretici: Azure AD B2B işbirliği kullanıcılarını toplu davet etme

Şirket dışındaki ortaklarla çalışmak için Azure Active Directory (Azure AD) B2B işbirliğini kullanıyorsanız, kuruluşunuza aynı anda birden çok konuk kullanıcı davet edebilirsiniz. Bu öğreticide, dış kullanıcılara toplu davetiye göndermek için Azure portal kullanmayı öğreneceksiniz. Özellikle aşağıdakileri yapın:

> [!div class="checklist"]
> * Kullanıcı bilgileri ve davetiye tercihleri ile bir virgülle ayrılmış değer (. csv) dosyası hazırlamak için **toplu davet kullanıcılarını** kullanın
> * . Csv dosyasını Azure AD 'ye yükleyin
> * Kullanıcıların dizine eklendiğini doğrulama

Azure Active Directory yoksa, başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="understand-the-csv-template"></a>CSV şablonunu anlama

Azure AD Konuk kullanıcılarını toplu olarak başarıyla davet etmenize yardımcı olması için toplu karşıya yükleme CSV şablonunu indirin ve girin. İndirmediğiniz CSV şablonu şu örnekteki gibi görünebilir:

![Her satır ve sütunun amacını ve değerlerini açıklayan karşıya yükleme ve çağrı aşımları için elektronik tablo](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>CSV şablonu yapısı

İndirilen bir CSV şablonundaki satırlar aşağıdaki gibidir:

- **Sürüm numarası**: sürüm numarasını içeren ilk satır, KARŞıYA yükleme CSV 'ye eklenmelidir.
- **Sütun başlıkları**: sütun başlıklarının biçimi &lt; *öğe adı* &gt; [PropertyName] &lt; *gerekli veya boş* &gt; . Örneğin, `Email address to invite [inviteeEmail] Required`. Şablonun bazı eski sürümlerinde hafif Çeşitlemeler bulunabilir.
- **Örnekler satırı**: şablona her sütun için değer örneklerinin bir satırını ekledik. Örnekler satırını kaldırmalı ve kendi girişlerinizin yerine değiştirmelisiniz.

### <a name="additional-guidance"></a>Ek yönergeler

- Karşıya yükleme şablonunun ilk iki satırı kaldırılmamalıdır veya değiştirilmemelidir veya karşıya yükleme işlenemiyor.
- Önce gerekli sütunlar listelenir.
- Şablona yeni sütun eklenmesini önermiyoruz. Eklediğiniz tüm ek sütunlar yoksayılır ve işlenmez.
- CSV şablonunun en son sürümünü mümkün olduğunca sık indirmeniz önerilir.

## <a name="prerequisites"></a>Önkoşullar

Davetleri gönderebileceğiniz iki veya daha fazla test e-posta hesabı olması gerekir. Hesaplar, kuruluşunuzun dışından olmalıdır. Gmail.com veya outlook.com adresleri gibi sosyal hesapları içeren herhangi bir hesap türü kullanabilirsiniz.

## <a name="invite-guest-users-in-bulk"></a>Konuk kullanıcıları toplu olarak davet etme

1. Kuruluşta genel yönetici olan bir hesapla Azure portal oturum açın.
2. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
3. **Yönet**' ın altında **tüm kullanıcılar**' ı seçin.
4. **Toplu işlemleri**  >  **toplu davet et**' i seçin.

    ![Toplu davet düğmesi](media/tutorial-bulk-invite/bulk-invite-button.png)

4. **Kullanıcıları toplu davet et** sayfasında, davet özelliklerine sahip geçerli bir. csv şablonu almak için **İndir** ' i seçin.

     ![CSV dosyasını indirme](media/tutorial-bulk-invite/download-button.png)

1. . Csv şablonunu açın ve her Konuk Kullanıcı için bir satır ekleyin. Gerekli değerler şunlardır:

   * **Davet e-posta adresi** -davet alacak Kullanıcı

   * **Yeniden yönlendirme URL 'si** -daveti kabul ettikten sonra davet edilen kullanıcının iletildiği URL. Kullanıcıyı uygulamalarım sayfasına iletmek istiyorsanız, bu değeri veya olarak değiştirmeniz gerekir https://myapps.microsoft.com https://myapplications.microsoft.com .

    ![Konuk kullanıcılar için girilen bir CSV dosyası örneği](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > İletinin başarıyla ayrıştırılmasını engelleyecek şekilde **özelleştirilmiş davet iletisinde** virgül kullanmayın.

6. Dosyayı kaydedin.
7. **Toplu davet kullanıcıları** sayfasında, **CSV dosyanızı karşıya yükleyin** bölümünde dosyaya gidin. Dosyayı seçtiğinizde,. csv dosyasının doğrulanması başlar. 
8. Dosya içeriği doğrulandığında, **dosyanın başarıyla karşıya yüklendiğini** görürsünüz. Hatalar varsa, işi gönderebilmeniz için önce bunları çözmeniz gerekir.
9. Dosyanız doğrulamayı geçtiğinde, davetleri ekleyen Azure toplu işlemini başlatmak için **Gönder** ' i seçin. 
10. İş durumunu görüntülemek için, **her bir işlemin durumunu görüntülemek üzere buraya tıklayın ' ı** seçin. Ya da **etkinlik** bölümünde **toplu işlem sonuçları** ' nı seçebilirsiniz. Toplu işlemdeki her bir satır öğesi hakkında ayrıntılar için **# Success**, **# Failure** veya **Total Requests** sütunlarının altındaki değerleri seçin. Hatalar oluştuysa, başarısızlık nedenleri listelenecektir.

    ![Toplu işlem sonuçları örneği](media/tutorial-bulk-invite/bulk-operation-results.png)

11. İş tamamlandığında toplu işlemin başarılı olduğunu belirten bir bildirim görürsünüz.

## <a name="verify-guest-users-in-the-directory"></a>Dizindeki Konuk kullanıcıları doğrulama

Eklediğiniz konuk kullanıcıların Azure portal dizinde mı yoksa PowerShell kullanarak mı bulunduğunu denetleyin.

### <a name="view-guest-users-in-the-azure-portal"></a>Azure portal Konuk kullanıcıları görüntüleme

1. Kuruluşta Kullanıcı Yöneticisi olan bir hesapla Azure portal oturum açın.
2. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
3. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
4. **Göster** altında **Yalnızca Konuk kullanıcılar** ' ı seçin ve eklediğiniz kullanıcıların listelendiğini doğrulayın.

### <a name="view-guest-users-with-powershell"></a>PowerShell ile Konuk kullanıcıları görüntüleme

Şu komutu çalıştırın:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Bir Kullanıcı asıl adı (UPN) ile, davet ettiğiniz kullanıcıları *emadresi*#EXT # etki biçiminde görmeniz gerekir \@ . Örneğin, *lstokes_fabrikam. com # ext # \@ contoso.onmicrosoft.com*, burada contoso.onmicrosoft.com, davetleri gönderdiğiniz kuruluştur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, Konuk kullanıcının yanındaki onay kutusunu seçip **Sil**' i seçerek kullanıcılar sayfasındaki Azure Portal dizindeki test Kullanıcı hesaplarını silebilirsiniz. 

Ya da bir kullanıcı hesabını silmek için aşağıdaki PowerShell komutunu çalıştırabilirsiniz:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Örnek: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kuruluşunuz dışındaki konuk kullanıcılara toplu davetler göndereceksiniz. Daha sonra, davet kullanımı işleminin nasıl çalıştığını öğreneceksiniz.

> [!div class="nextstepaction"]
> [Azure AD B2B işbirliği daveti kullanım işlemi hakkında bilgi edinin](redemption-experience.md)
