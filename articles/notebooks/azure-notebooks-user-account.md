---
title: Azure Notebooks oturum aç
description: Kullanıcı hesabınızı bir Microsoft hesabı veya iş/okul hesabı kullanarak Azure Notebooks için yapılandırın.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: bc93f55a3349498fa82095c2bc2b3178e234c107
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973147"
---
# <a name="your-user-account-for-azure-notebooks"></a>Azure Notebooks için Kullanıcı hesabınız

Kullanıcı hesabıyla oturum açmadan Azure Notebooks kullanabilirsiniz:

- Oturum açmadan, not defterlerini oluşturup çalıştırabilir, ancak projelerin bir parçası olarak not defterlerini veya veri dosyalarını sürdüremezsiniz. Örneğin, bir Azure not defterine bağlantı alan kullanıcılar, oturum açmaya gerek kalmadan Not defterinin keyfini çıkarabilirsiniz.
- Oturum açıldığında, Azure Notebooks tüm projelerinizi hesabınızla saklar. Oturum açan kullanıcıların, projelerini ve not defterlerini başkalarıyla paylaşmasına izin veren bir kullanıcı KIMLIĞI de vardır.
  - Azure Notebooks için kullanılan hesap aynı zamanda bir Azure aboneliğiyle ilişkilendirildiğinde, daha güçlü sunucularda Not defterleri çalıştırma, özel not defterleri oluşturma ve not defterlerine bireysel kullanıcılara izin verme gibi ek avantajlar elde edersiniz.

Azure Notebooks oturum açmak için bir Microsoft hesabı ya da "Iş veya okul" hesabı gerekir. Not defterleri sayfasının sağ üst tarafındaki **oturum aç** komutunu seçerken hesabınız istenir:

![Azure Notebooks için oturum açma komutu](media/accounts/sign-in-command.png)

Azure Notebooks içinde yaptığınız tüm işler, oturum açmak için kullandığınız hesapla ilişkilidir. Her hesabın [Kullanıcı profilinizde](azure-notebooks-user-profile.md)de benzersiz BIR kullanıcı kimliği olmalıdır. Sonuç olarak, ayrı proje kümelerini ve ayrı kimlikleri korumanız gerekiyorsa farklı hesaplarla Azure Notebooks oturum açabilirsiniz. Örneğin, bir veri bilimi ekibinin her üyesi her iki ayrı hesabı da kendi şirketlerinin dışındaki kişilere işlerini sunmak için kullandıkları bir paylaşılan grup hesabı olabilir. Benzer şekilde, Eğitmenler, dış danışmanlık veya açık kaynak çalışmalarınızda kullanılan bir hesaptan farklı olan eğitim rolleri için bir hesap tutabilir.

## <a name="microsoft-accounts"></a>Microsoft hesapları

Microsoft hesapları, Windows, Azure, outlook.com, OneDrive ve XBox Live gibi çeşitli Microsoft ürün ve hizmetlerinde oturum açmak için kullanılır. Bu hizmetlerden herhangi birini kullanıyorsanız, Azure Notebooks ile kullanabileceğiniz bir Microsoft hesabınızın olması olasıdır.

Emin değilseniz, hesap isteminde **bir tane oluştur** komutu seçin. Herhangi bir sağlayıcıdan herhangi bir e-posta adresini kullanarak yeni bir Microsoft hesabı oluşturabilirsiniz.

![Yeni bir Microsoft hesabı oluşturmak için komut](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Zaten onunla ilişkili bir hesabı olan bir e-posta adresine sahip yeni bir hesap oluşturmaya çalışırsanız, "bir iş veya okul e-posta adresi ile burada kayıt yapamazsınız" iletisini görebilirsiniz. Gmail veya Yahoo! gibi kişisel bir e-posta kullanın veya yeni bir Outlook e-postası alın. " Bu durumda, yeni bir hesap oluşturmadan iş e-posta adresiyle oturum açmayı deneyin.

Alt hesaplar için Azure Notebooks erişimi varsayılan olarak engellenir. Bir alt hesapla oturum açmak aşağıda gösterilen hatayı görüntüler:

![Bir alt hesapla oturum açılmaya çalışılırken hata oluştu: bir sorun oluştu, ana bilgisayarınız erişimi engelledi](media/accounts/child-account-error.png)

Erişimi etkinleştirmek için bir üst öğenin aşağıdaki adımları yapması gerekir:

1. @No__t-0 ' i ziyaret edin ve bir üst hesapla oturum açın.
1. Söz konusu çocuk için bölümünde, **Bu çocuğun üçüncü taraf uygulamalarına erişimini Yönet**' i seçin.
1. Sonraki sayfada, **erişimi etkinleştir**' i seçin.
1. Azure Notebooks oturum açmak için alt hesap bir sonraki kullanıldığında, görüntülenen izinler isteminde **Evet** ' i seçin.

> [!Warning]
> Azure Notebooks için üçüncü taraf uygulamalara erişimin etkinleştirilmesi, diğer tüm üçüncü taraf uygulamalar için de erişim sağlar. Ebeveynler, erişimi etkinleştirirken dikkatli bir şekilde kullanılması önerilir ve alt öğelerinin etkinliklerini daha yakından izlemek isteyebilir.

## <a name="work-or-school-accounts"></a>İş veya okul hesapları

Bir iş veya okul hesabı, kuruluşun bir üyesinin Office 365 gibi Microsoft bulut hizmetlerine ve ayrıca etki alanına katılmış bir bilgisayarda Windows 'da oturum açmak için bir hesap olarak erişmesini sağlamak üzere bir kuruluşun yöneticisi tarafından oluşturulur. Bir iş veya okul hesabı genellikle any-user@contoso.com gibi bir kurumsal e-posta adresi kullanır.

Bir iş veya okul hesabıyla Azure Notebooks oturum açmak, Azure Notebooks, hesabın e-posta adresi ve kullanıcının tarayıcı bilgileri gibi bilgileri toplayıp kullanması (ancak açıklamadığı) için yönetici onayı gerektirebilir. (Tarayıcı verileri, popüler kullanıma göre özellikleri iyileştirmek için kullanılır.)

Kullanıcıların ayrı ayrı ele alınmasına izin verirse, bir kuruluş hesabının yöneticisinin Kullanıcı adına onay sağlaması gerekir. Bu durumda, kullanıcılar "Bu uygulamaya erişemezsiniz" iletisini görür:

![Bir iş veya okul hesabı kullanırken "Bu uygulamaya erişemezsiniz" iletisi](media/accounts/consent-permissions-denied.png)

Yönetici olarak onay sağlamak için, işlem boyunca size yol gösteren [Yönetici onay sayfasını](https://notebooks.azure.com/account/adminConsent)kullanın.

## <a name="next-steps"></a>Sonraki adımlar  

> [!div class="nextstepaction"]
> [Profilinizi ve Kullanıcı KIMLIĞINIZI düzenleyin](azure-notebooks-user-profile.md)
