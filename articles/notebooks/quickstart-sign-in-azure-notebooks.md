---
title: Azure Notebooks oturum açın
description: Azure not defterlerinde hızlı bir şekilde oturum açın ve bir kullanıcı KIMLIĞI ayarlayın. Bu, kayıtlı projelere erişme ve not defterlerini başkalarıyla paylaşma olanağı sağlar.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 0c12c8673ff61dcca62c72addcbf0d9b33c2c252
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973053"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Hızlı başlangıç: oturum açma ve Kullanıcı KIMLIĞI ayarlama

Oturum açmadan Azure Notebooks her zaman görüntüleyebilseniz de, not defterlerini çalıştırmak, kaydedilmiş projelere ve not defterlerine erişmek ve not defterlerinizi başkalarıyla paylaşmak için oturum açmanız gerekir.

## <a name="sign-in"></a>Oturum aç

1. [Notebooks.Azure.com](https://notebooks.azure.com/)' un sağ üst kısmında **oturum aç** ' ı seçin.

    ![Azure Notebooks oturum açma komutunun konumu](media/accounts/sign-in-command.png)

1. İstendiğinde, bir Microsoft hesabının e-posta adresini veya bir iş veya okul hesabını girip **İleri ' yi**seçin. Hesap türleri [Kullanıcı hesabınızda Azure Notebooks](azure-notebooks-user-account.md)açıklanmaktadır. Bir Microsoft hesabınız yoksa veya Azure Notebooks özel olarak kullanmak istiyorsanız, **birini oluştur**' u seçin:

    ![Oturum açma isteminde yeni Microsoft hesabı komutu oluştur](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Zaten onunla ilişkili bir hesabı olan bir e-posta adresine sahip yeni bir hesap oluşturmaya çalışırsanız, "bir iş veya okul e-posta adresi ile burada kayıt yapamazsınız" iletisini görebilirsiniz. Gmail veya Yahoo! gibi kişisel bir e-posta kullanın veya yeni bir Outlook e-postası alın. " Bu durumda, yeni bir hesap oluşturmadan iş e-posta adresiyle oturum açmayı deneyin.

1. İstendiğinde parolanızı girin.

1. İlk kez oturum açıyorsanız, Azure Notebooks hesabınıza erişmek için izin ister. Devam etmek için **Evet** ' i seçin:

    ![Hesap izinleri istemi](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Kullanıcı KIMLIĞI ayarlama

1. İlk oturum açma sırasında, "Anon-idrca3" gibi geçici bir kullanıcı KIMLIĞI atanır. "Anon-" ile başlayan bir Kullanıcı KIMLIĞINIZ olduğunda, Azure Notebooks kendinizinkini bir KIMLIK oluşturmanız istenir. Kullanıcı KIMLIĞINIZ, projelerinizi ve not defterlerinizi paylaşmak için elde ettiğiniz herhangi bir URL 'de kullanılır, bu nedenle benzersiz ve anlamlı bir şey seçin.

    ![Azure Notebooks için Kullanıcı KIMLIĞI girmek isteyip sorma](media/accounts/create-user-id.png)

    **Hayır**' ı seçerseniz, Azure Notebooks her oturum AÇıŞıNıZDA Kullanıcı kimliğini sormaya devam eder. Kullanıcı KIMLIĞINIZ, [Kullanıcı profilinizde](azure-notebooks-user-profile.md)herhangi bir zamanda da ayarlanabilir.

1. Başarıyla oturum açtıktan sonra Azure Notebooks, genel profil sayfanıza gider ve burada, bilgilerinizin geri kalanını dolduracak şekilde **profil bilgilerini düzenle** ' yi seçebilirsiniz (daha fazla bilgi için, [PROFILINIZE ve kullanıcı kimliğinize](azure-notebooks-user-profile.md)bakın):

    ![Azure Notebooks profili sayfasının ilk görünümü](media/accounts/profile-page-new.png)

> [!NOTE]
> "Kullanıcı KIMLIĞI zaten kullanımda" iletisini görürseniz, farklı bir KIMLIK deneyin. Kullanıcı kimlikleri tüm Azure Notebooks hesaplarında benzersizdir ve Azure Notebooks Microsoft marka adları gibi bazı Kullanıcı kimliklerini de ayırır.

## <a name="sign-out"></a>Oturumu Kapat

Oturumu kapatmak için sayfanın sağ üst kısmındaki Kullanıcı adınızı seçin ve ardından **Oturumu Kapat**' ı seçin:

![Azure Notebooks 'de oturum kapatma komutunun konumu](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Not defteri oluşturma ve paylaşma](quickstart-create-share-jupyter-notebook.md)
