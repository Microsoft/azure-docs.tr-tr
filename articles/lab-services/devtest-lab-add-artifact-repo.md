---
title: Azure DevTest Labs 'de laboratuvara git deposu ekleme | Microsoft Docs
description: Azure DevTest Labs içindeki özel yapıt kaynağınız için GitHub veya Azure DevOps Services git deposu eklemeyi öğrenin.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1555eb4e48a0cf43a38aa811e20ffbbed8ee87a9
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755811"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Özel yapıtları ve Kaynak Yöneticisi şablonlarını depolamak için bir git deposu ekleme

Laboratuvarınızda VM 'Ler için [özel yapılar oluşturabilir](devtest-lab-artifact-author.md) veya [özel bir test ortamı oluşturmak için Azure Resource Manager şablonları kullanabilirsiniz](devtest-lab-create-environment-from-arm.md). Takımınızın oluşturduğu yapıtlar veya Kaynak Yöneticisi şablonlar için özel bir git deposu eklemeniz gerekir. Depo [GitHub](https://github.com) üzerinde veya [Azure DevOps Services](https://visualstudio.com)üzerinde barındırılabilir.

Olduğu gibi dağıtabileceğiniz [yapıtların GitHub deposunu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) sunuyoruz veya bunları laboratuvarlarınız için özelleştirebilirsiniz. Bir yapıyı özelleştirirken veya oluşturduğunuzda yapıtı ortak depoda depokaydedemezsiniz. Özel yapıtlar ve oluşturduğunuz yapıtlar için kendi özel deponuzu oluşturmanız gerekir. 

Bir VM oluşturduğunuzda, Kaynak Yöneticisi şablonunu kaydedebilir, isterseniz özelleştirebilirsiniz ve daha sonra daha sonra daha fazla VM oluşturmak için kullanabilirsiniz. Özel Kaynak Yöneticisi şablonlarınızı depolamak için kendi özel deponuzu oluşturmanız gerekir.  

* GitHub deposu oluşturmayı öğrenmek için bkz. [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Git deposuna sahip Azure DevOps Services bir proje oluşturmayı öğrenmek için bkz. [Azure DevOps Services bağlanma](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Aşağıdaki şekilde, yapıtlar içeren bir deponun GitHub 'da nasıl görünebileceğini gösteren bir örnek verilmiştir:  

![Örnek GitHub yapay deposu](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Depo bilgilerini ve kimlik bilgilerini alın
Laboratuvarınıza bir depo eklemek için önce deponuzdan anahtar bilgileri alın. Aşağıdaki bölümlerde, GitHub veya Azure DevOps Services 'te barındırılan depolarda gerekli bilgilerin nasıl alınacağı açıklanır.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub deposu kopya URL 'sini ve kişisel erişim belirtecini al

1. Yapıt veya Kaynak Yöneticisi Şablon tanımlarını içeren GitHub deposunun ana sayfasına gidin.
2. **Clone or download**'u (Kopyala veya indir) seçin.
3. URL 'YI panoya kopyalamak için **https kopya URL 'si** düğmesini seçin. URL 'YI daha sonra kullanmak üzere kaydedin.
4. GitHub ' ın sağ üst köşesinde profil görüntüsünü seçin ve ardından **Ayarlar**' ı seçin.
5. Sol taraftaki **kişisel ayarlar** menüsünde **kişisel erişim belirteçleri**' ni seçin.
6. **Yeni belirteç oluştur**' u seçin.
7. **Yeni kişisel erişim belirteci** sayfasında, **belirteç açıklaması**altında bir açıklama girin. **Kapsamları Seç**altında varsayılan öğeleri kabul edin ve ardından **belirteç oluştur**' u seçin.
8. Oluşturulan belirteci kaydedin. Belirteci daha sonra kullanırsınız.
9. GitHub 'ı kapatın.   
10. [Laboratuvarınızı depoya bağlama](#connect-your-lab-to-the-repository) bölümüne devam edin.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Azure Repos kopya URL 'sini ve kişisel erişim belirtecini alın

1. Ekip koleksiyonunuzun ana sayfasına gidin (örneğin, https://contoso-web-team.visualstudio.com) ve ardından projenizi seçin.
2. Proje giriş sayfasında **kod**' u seçin.
3. Kopya URL 'sini görüntülemek için, proje **kodu** sayfasında, **Kopyala**' yı seçin.
4. URL 'YI kaydedin. URL 'YI daha sonra kullanırsınız.
5. Kişisel erişim belirteci oluşturmak için, Kullanıcı hesabı açılan menüsünde **profilimi**seçin.
6. Profil bilgileri sayfasında **güvenlik**' i seçin.
7. **Güvenlik** sekmesinde, **Ekle**' yi seçin.
8. **Kişisel erişim belirteci oluştur** sayfasında:
   1. Belirteç için bir **Açıklama** girin.
   2. **Süre sonu** listesinde **180 gün**' yı seçin.
   3. **Hesaplar** listesinde, **tüm erişilebilir hesaplar**' ı seçin.
   4. **Salt oku** seçeneğini belirleyin.
   5. **Belirteç oluştur**' u seçin.
9. Yeni belirteç, **kişisel erişim belirteçleri** listesinde görünür. **Belirteci Kopyala**' yı seçin ve ardından daha sonra kullanmak üzere belirteç değerini kaydedin.
10. [Laboratuvarınızı depoya bağlama](#connect-your-lab-to-the-repository) bölümüne devam edin.

## <a name="connect-your-lab-to-the-repository"></a>Laboratuvarınızı depoya bağlama
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)’ında oturum açın.
2. **Diğer hizmetler**' i seçin ve ardından hizmetler listesinden **DevTest Labs** ' i seçin.
3. Laboratuvarlar listesinden laboratuvarınızı seçin. 
4. **Yapılandırma ve ilkeler**  > **depolar**  >  **+ Ekle**' yi seçin.

    ![Depo Ekle düğmesi](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. İkinci **depolar** sayfasında, aşağıdaki bilgileri belirtin:
   1. **Ad**. Depo için bir ad girin.
   2. **Url git clone**. Daha önce GitHub veya Azure DevOps Services kopyaladığınız git HTTPS kopya URL 'sini girin.
   3. **Dalı**. Tanımlarınızı almak için dalı girin.
   4. **Kişisel erişim belirteci**. Daha önce GitHub veya Azure DevOps Services aldığınız kişisel erişim belirtecini girin.
   5. **Klasör yolları**. Yapıtı veya Kaynak Yöneticisi şablonu tanımlarınızı içeren kopya URL 'sine göre en az bir klasör yolu girin. Bir alt dizin belirttiğinizde, klasör yolunda eğik çizgi eklediğinizden emin olun.

      ![Depolar alanı](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. **Kaydet**’i seçin.

### <a name="related-blog-posts"></a>İlgili blog gönderileri
* [DevTest Labs 'de başarısız yapıtların sorunlarını giderme](devtest-lab-troubleshoot-artifact-failure.md)
* [DevTest Labs 'de bir Kaynak Yöneticisi şablonu kullanarak bir VM 'yi mevcut bir Active Directory etki alanına katma](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Sonraki adımlar
Özel Git deponuzu oluşturduktan sonra, gereksinimlerinize bağlı olarak aşağıdakilerden birini veya her ikisini de yapabilirsiniz:
* [Özel yapıtlarınızı](devtest-lab-artifact-author.md)depolayın. Bunları daha sonra yeni VM 'Ler oluşturmak için kullanabilirsiniz.
* [Kaynak Yöneticisi şablonlarını kullanarak çoklu VM ortamları ve PaaS kaynakları oluşturun](devtest-lab-create-environment-from-arm.md). Daha sonra, şablonları özel deponuzda saklayabilirsiniz.

Bir VM oluşturduğunuzda, yapıtların veya şablonlarının git deponuza eklendiğini doğrulayabilirsiniz. Bunlar, yapıtlar veya şablonlar listesinde hemen kullanılabilir. Özel deponuzu adı, kaynağı belirten sütununda gösterilir. 
