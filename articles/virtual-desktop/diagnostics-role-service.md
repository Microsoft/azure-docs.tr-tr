---
title: Windows sanal masaüstü Tanılama sorunları-Azure
description: Sorunları tanılamak için Windows sanal masaüstü Tanılama özelliğini kullanma.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cf654f07cbbd729fac8feb9e2f8f7ceff471ae6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447004"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Windows sanal masaüstü sorunlarını tanımla ve Tanıla

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)bakın.

Windows sanal masaüstü, yöneticinin sorunları tek bir arabirim üzerinden belirlemesine izin veren bir tanılama özelliği sunar. Windows sanal masaüstü 'nün tanılama özellikleri hakkında daha fazla bilgi edinmek için bkz. [Tanılama özelliği için Log Analytics kullanma](diagnostics-log-analytics.md).

Tanılama rolü hizmeti Windows sanal masaüstü 'nün bir parçası olduğundan, Windows sanal masaüstüne ulaşmayacak bağlantılar tanılama sonuçlarında gösterilmez. Son Kullanıcı ağ bağlantısı sorunlarıyla karşılaşıyorsa Windows sanal masaüstü bağlantısı sorunları ortaya çıkabilir.

## <a name="common-error-scenarios"></a>Yaygın hata senaryoları

WVDErrors tablosu tüm etkinlik türlerindeki hataları izler. "ServiceError" adlı sütun "true" veya "false" olarak işaretlenmiş ek bir bayrak sağlar. Bu bayrak, hatanın hizmetle ilgili olup olmadığını bildirir.

* Değer "true" ise, hizmet ekibi bu sorunu zaten araştırmış olabilir. Bu, Kullanıcı deneyimini etkiler ve çok sayıda görünüyorsa, Windows sanal masaüstü için bir destek bileti göndermeniz önerilir.
* Değer "false" ise, bu, kendinizi çözebilmeniz için bir yanlış yapılandırma olabilir. Hata mesajı, nereden başlayabileceğiniz konusunda bir Clue verebilir.

Aşağıdaki tabloda, yöneticilerinizin çalıştırabilecek yaygın hatalar listelenmektedir.

>[!NOTE]
>Bu liste, en yaygın hataları içerir ve sıradan bir temposunda güncellenir. En güncel bilgilere sahip olduğunuzdan emin olmak için, bu makaleye ayda en az bir kez tekrar kontrol ettiğinizden emin olun.

## <a name="management-errors"></a>Yönetim hataları

|Hata iletisi|Önerilen çözüm|
|---|---|
|Kayıt anahtarı oluşturulamadı |Kayıt belirteci oluşturulamadı. Daha kısa bir süre sonu ile yeniden oluşturmayı deneyin (1 saat ile 1 ay arasında). |
|Kayıt anahtarı silinemedi|Kayıt belirteci silinemedi. Yeniden silmeyi deneyin. Hala işe yaramazsa, belirtecin hala orada olup olmadığını denetlemek için PowerShell 'i kullanın. Varsa, PowerShell ile silin.|
|Oturum Ana bilgisayar boşaltma modu değiştirilemedi |VM 'de boşaltma modu değiştirilemedi. VM durumunu denetleyin. VM 'nin kullanılamadığı, boşaltma modu değiştirilemez.|
|Kullanıcı oturumlarının bağlantısı kesilemedi |Kullanıcının VM bağlantısı kesilemedi. VM durumunu denetleyin. VM kullanılamıyor ise, Kullanıcı oturumunun bağlantısı kesilemiyor. VM kullanılabiliyorsa, bağlantısının kesildiğini görmek için Kullanıcı oturumunun durumunu kontrol edin. |
|Oturum Ana bilgisayarı içindeki tüm kullanıcılar (ler) oturumu kapatılamadı |Kullanıcılar VM 'den oturum açamadı. VM durumunu denetleyin. Kullanılamaz durumdaysa, kullanıcılar oturum açamaz. Zaten oturum açmış olup olmadığını görmek için Kullanıcı oturumu durumunu denetleyin. PowerShell ile oturumu kapatmaya zorlayabilirsiniz. |
|Kullanıcının uygulama grubundan ataması kaldırılamadı|Bir kullanıcı için uygulama grubu yayımdan kaldırılamadı. Kullanıcının Azure AD 'de kullanılabilir olup olmadığını denetleyin. Kullanıcının uygulama grubunun yayımlandığı bir Kullanıcı grubunun parçası olup olmadığını denetleyin. |
|Kullanılabilir konumlar alınırken bir hata oluştu |Konak havuzu oluşturma Sihirbazı 'nda kullanılan VM 'nin konumunu denetleyin. Görüntü bu konumda yoksa, bu konuma görüntü ekleyin veya farklı bir VM konumu seçin. |

### <a name="connection-error-codes"></a>Bağlantı hata kodları

|Sayısal kod|Hata kodu|Önerilen çözüm|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Oturum Ana bilgisayarı Active Directory doğru şekilde katılmadı.|
|-2146233088|Connectionfaileduserhasvalidsessionbutrdshıssağlıksız|Oturum Konağı kullanılamadığından bağlantı başarısız oldu. Oturum konağının sistem durumunu kontrol edin.|
|-2146233088|ConnectionFailedClientDisconnect|Bu hatayla sık karşılaşırsanız, kullanıcının bilgisayarının ağa bağlı olduğundan emin olun.|
|-2146233088|Connectionfailednohealthyırdshavailable|Ana bilgisayar kullanıcısının bağlanmaya çalıştığı oturum sağlıklı değil. Sanal makinede hata ayıklayın.|
|-2146233088|ConnectionFailedUserNotAuthorized|Kullanıcının yayımlanan uygulamaya veya masaüstüne erişim izni yok. Hata, yönetici yayımlanmış kaynakları kaldırdıktan sonra görünebilir. Kullanıcıdan uzak masaüstü uygulamasında akışı yenilemesini isteyin.|
|2|FileNotFound|Kullanıcının erişmeye çalıştığı uygulama yanlış yüklenmiş veya hatalı bir yola ayarlı.<br><br>Kullanıcı etkin bir oturuma sahip olsa da yeni uygulamalar yayımlarken, Kullanıcı bu uygulamaya erişemez. Kullanıcının uygulamaya erişebilmesi için oturumun kapatılması ve yeniden başlatılması gerekir. |
|3|Invalidcredentials|Kullanıcının girdiği Kullanıcı adı veya parola, mevcut kullanıcı adları veya parolalarla eşleşmiyor. Yazım hataları bilgilerini gözden geçirin ve yeniden deneyin.|
|8|Connectionkopuk|Istemci ve ağ geçidi veya sunucu bağlantısı kesilen bağlantı. Beklenmedik bir şekilde gerçekleşmediği takdirde eyleme gerek yoktur.|
|14|UnexpectedNetworkDisconnect|Ağ bağlantısı bırakıldı. Kullanıcıdan tekrar bağlanmasını isteyin.|
|24|Smarconnectfailed|Konak sanal makinesinde RD Ağ Geçidi doğrudan bir görüş satırı yoktur. Ağ geçidi IP adresinin çözümlenebildiğinden emin olun.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Hata: bir uygulama grubuna kullanıcı atamaları eklenemiyor

Bir Kullanıcı bir uygulama grubuna atandıktan sonra, Azure portal "oturum sonlandırma" veya "kimlik doğrulama sorunları hakkında-uzantı Microsoft_Azure_WVD" bildiren bir uyarı görüntüler. Daha sonra atama sayfası yüklenmez ve sonra sayfaların Azure portal (örneğin, Azure Izleyici, Log Analytics, hizmet durumu vb.) yüklenmesini durdurur.

**Neden:** Koşullu erişim ilkesiyle ilgili bir sorun var. Azure portal, SharePoint Online 'a bağımlı olan Microsoft Graph için bir belirteç almaya çalışıyor. Müşterinin, veri depolamaya erişmek için kullanım koşullarını kabul etmesini gerektiren "Microsoft Office 365 veri depolama alanı kullanım koşulları" adlı bir koşullu erişim ilkesi vardır. Ancak, henüz oturum açmamış, Azure portal belirteci alamıyor.

**Çözüm:** Azure portal oturum açmadan önce, önce yöneticinin SharePoint 'te oturum açması ve kullanım koşullarını kabul etmesi gerekir. Bundan sonra, normal gibi Azure portal oturum açabilmelidir.

## <a name="next-steps"></a>Sonraki adımlar

Windows sanal masaüstü içindeki roller hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü ortamı](environment-setup.md).

Windows sanal masaüstü için kullanılabilir PowerShell cmdlet 'lerinin listesini görmek için [PowerShell başvurusuna](/powershell/windows-virtual-desktop/overview)bakın.
