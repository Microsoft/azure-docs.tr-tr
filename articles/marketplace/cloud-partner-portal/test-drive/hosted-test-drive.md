---
title: Barındırılan test sürücüsü | Azure Marketi
description: Market 'Te barındırılan test sürücüsü kurma
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2ce43402fb283cce09687bc7226770731f1bae0d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827527"
---
# <a name="hosted-test-drive"></a>Barındırılan Test Sürüşü

Barındırılan bir test sürücüsü Microsoft barındırma tarafından kurulum 'un karmaşıklığını ortadan kaldırır ve Kullanıcı hazırlama ve sağlamayı kaldırma sınama sürücüsünü gerçekleştiren hizmeti korur. Bu makale, AppSource 'ta teklifi olan veya yeni bir tane oluşturan ve müşteri katılımı için Dynamics 365, finans ve Işlemler için Dynamics 365 veya Dynamics 365 Business Central ile bağlanan bir barındırılan test sürücüsü sunmak isteyen yayımcılar içindir. Instance.

## <a name="how-to-publish-a-test-drive"></a>Test sürücüsü yayımlama

Mevcut teklifine gidin veya yeni bir teklif oluşturun.

Yan menüden sürücüyü sına seçeneğini belirleyin.

Bir sınama sürücüsü\' seçeneğini etkinleştirmek \'için \'Evet\' seçin.

\'Ayrıntılar\' bölümünde aşağıdaki alanları sağlayın.

- **Açıklama**: test sürücünüze genel bir bakış sağlar. Sınama sürücüsü sağlanırken bu metin kullanıcıya gösterilir. Bu alan, biçimlendirilen içerik sağlamak istiyorsanız HTML 'yi destekler.
- **Kullanıcı el ile**: ayrıntılı bir kullanıcı el ile yükleyin (Type. PDF dosyası) ve bu sayede, kullanıcıların uygulamanızı nasıl kullanacağınızı anlayabilmesine yardımcı olur.
- **Test sürücüsü tanıtım videosu**: isteğe bağlı olarak, uygulamanızı gösteren bir videoyu karşıya yükleyin.

[Burada](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)yer alan yönergeleri kullanarak Kiracınızdaki test sürücüsü kullanıcılarını sağlama ve sağlamayı kaldırma Için appsource iznini verin.

Bu adımda, aşağıda bahsedilen \'Azure AD Uygulaması Kimliği\' ve \'Azure AD Uygulaması anahtar\' değerlerini oluşturacaksınız.

\'teknik yapılandırma\' bölümünde aşağıdaki alanları sağlayın:

- **Test sürücüsünün türü**: Microsoft tarafından barındırılan \'(müşteri katılımı için Dynamics 365) ' seçeneğini belirleyin. Bu, Microsoft 'un Kullanıcı hazırlama ve sağlamayı kaldırma sınama sürücüsünü gerçekleştiren hizmeti barındırdığını ve bakımını gerçekleştireceğini belirtir.
- **Maksimum eşzamanlı test sürücüsü**: bu alanı, belirli bir zaman noktasında etkin bir test sürücüsüne sahip olabilecek eşzamanlı kullanıcı sayısına ayarlayın. Her Kullanıcı, test sürücüleri etkinken bir Dynamics lisansını kullanır, bu nedenle, sınama sürücüsü kullanıcıları için en az bu sayıda Dynamics lisansın kullanılabilir olduğundan emin olmanız gerekir. Önerilen 3-5 değeri.
- **Sınama sürücüsü süresi (saat)** : bu alanı, kullanıcıların test sürücüsünün etkin olacağı saat sayısına ayarlayın. Bu kadar saat sonra, Kullanıcı kiracınızdan kaldırılacak. Uygulamanızın karmaşıklığına bağlı olarak önerilen 2-24 saat değeri. Kullanıcı, zaman aşımına uğrar ve test sürücüsüne yeniden erişmek istediğinizde, her zaman başka bir test sürücüsü isteyebilir.
- **Örnek URL 'si**: test sürücüsünü başlatırken kullanıcının başlangıçta GEZINDIĞI URL 'yi belirtin. Bu genellikle, uygulamanız ve örnek verileriniz üzerine yüklenmiş olan Dynamics 365 örneğinizin URL 'sidir. Örnek değer: https:\//testdrive.crm.dynamics.com
- **Azure AD KIRACı kimliği**: Dynamics 365 örneğiniz Için Azure KIRACıNıN kimliğini belirtin. Bu değeri almak için Azure portal oturum açın ve \'Azure Active Directory\' -\> menü dikey penceresinden Özellikler ' i seçin\> Dizin KIMLIĞINI kopyalayın. Örnek değer: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD UYGULAMASı ID**: 7. adımda oluşturduğunuz Azure AD uygulaması kimliği. \ örnek değer: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD uygulaması anahtar**: 7. adımda oluşturulan Azure AD uygulaması için gizli dizi. \ örnek değer: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Azure AD kiracı adı**: Dynamics 365 örneğiniz Için Azure kiracının adını belirtin. \<TenantName biçimini kullanın.\>onmicrosoft.com. Örnek değer: testdrive.onmicrosoft.com
- **Örnek Web API URL 'si**: Dynamics 365 örneğiniz IÇIN Web API URL 'sini sağlayın. Bu değeri, Microsoft Dynamics 365 Örneğinizde oturum açarak alabilir ve ayar\> özelleştirme-\> geliştirici kaynakları-\> örnek Web API 'sine (Bu URL 'YI kopyalayın) giderek getirebilirsiniz. Örnek değer: https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Rol adı**: test sürücüsü için oluşturduğunuz özel Dynamics 365 güvenlik rolünün adını belirtin. Bu, kullanıcılara test sürücüleri sırasında atanacak olan roldür. Örnek değer: testdriverole

## <a name="next-steps"></a>Sonraki adımlar

Teklifinizi **yayımladığınızda** , uygulamanız sertifikayı geçtikten sonra teklifinizin **önizlemesini** görürsünüz. Kullanıcı arabiriminde bir test sürücüsü başlatın ve test sürücülerinizin doğru çalıştığını doğrulayın. Önizleme teklifinizi rahat hissettiğinizde, şimdi **canlı çalışmaya devam edersiniz!**
