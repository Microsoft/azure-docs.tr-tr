---
title: Azure Sertifikalı cihaz programı-öğretici-projenizi oluşturma
description: Azure Sertifikalı cihaz portalında proje oluşturma kılavuzu
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 11d72847209a2e706d4aa32d38af1b2c8af3dfa0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314006"
---
# <a name="tutorial-create-your-project"></a>Öğretici: projenizi oluşturma

Azure Sertifikalı cihaz programı aracılığıyla cihazınızı onaylamak için seçme konusunda tebrikler! Cihazınız için uygun sertifika programını seçtiniz ve portala başlamak için hazır olursunuz.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * [Azure Sertifikalı cihaz portalında](https://certify.azure.com/) oturum açın
> * Cihazınız için yeni bir sertifika projesi oluşturma
> * Projenizin temel cihaz ayrıntılarını belirtin

## <a name="prerequisites"></a>Önkoşullar

- Geçerli bir iş/okul [Azure Active Directory hesabınızın](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)olması gerekir.
- Doğrulanmış bir Microsoft İş Ortağı Ağı (MPN) hesabınız olması gerekir. MPN hesabınız yoksa, başlamadan önce [iş ortağı ağına katın](https://partner.microsoft.com/) .

## <a name="signing-into-the-azure-certified-device-portal"></a>Azure Sertifikalı cihaz portalında oturum açma

Başlamak için, portalda oturum açmanız gerekir. burada, cihaz bilgilerinizi, sertifika testini ve cihaz yayınlarınızı Azure Sertifikalı cihaz kataloğuna yönetme işlemini gerçekleştirirsiniz.

1. [Azure Sertifikalı cihaz portalına](https://certify.azure.com)gidin.
1. `Company profile`Sol taraftaki ' ı seçin ve üreticinizin bilgilerini güncelleştirin.
   ![Şirket profili bölümü](./media/images/company-profile.png)
1. Projenize başlamak için program anlaşmasını kabul edin.

## <a name="creating-your-project-on-the-portal"></a>Portalda projenizi oluşturma

Portalda her şey hazır olduğunuza göre, sertifika sürecini başlatabilirsiniz. İlk olarak, cihazınız için bir proje oluşturmanız gerekir.

1. Giriş ekranında, öğesini seçin `Create new project` . Bu, sonraki bölümde temel cihaz bilgilerini eklemek için bir pencere açar.

 ![Yeni proje oluştur düğmesinin görüntüsü](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Temel cihaz bilgilerini tanımlama

Ardından, temel cihaz bilgilerini sağlamanız gerekir. Bu bilgileri daha sonra düzenleyebilirsiniz.

1. Bölümünde istenen alanları doldurun `Basics` . **Gerekli** alanlarla ilgili açıklama için aşağıdaki tabloya bakın:

    | Alanlar                  | Açıklama                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Proje adı           | Azure Sertifikalı cihaz kataloğunda görünür olmayacak iç ad                                                        |
    | Cihaz adı            | Cihazınızın ortak adı                                                                                                |
    | Cihaz Türü            | Tamamlanmış ürün veya Solution-Ready geliştirici seti belirtimi.     Terminoloji hakkında daha fazla bilgi için bkz. [sertifika sözlüğü](./resources-glossary.md).                                                                     |
    | Cihaz sınıfı           | Ağ Geçidi, algılayıcı veya diğer.  Terminoloji hakkında daha fazla bilgi için bkz. [sertifika sözlüğü](./resources-glossary.md).                                                                    |
    | Cihaz kaynak kodu URL 'SI | Solution-Ready Dev Kit 'i sertifikakıdıysanız, isteğe bağlı olarak, aksi takdirde gerekir. URL, cihaz kodunuz için bir GitHub konumu olmalıdır. |
1. `Next`Sekmeye devam etmek için düğmeyi seçin `Certifications` .

    ![Yeni proje oluştur formunun görüntüsü, Sertifikalar sekmesi](./media/images/create-new-project-certificationswindow.png)

1. Cihazınız için hangi sertifikalarınızın elde etmek istediğinizi belirtin.
1. Seçin `Create` ve yeni proje portalın ana sayfasında kaydedilecek ve görünür hale alınacaktır.

    ![Proje tablosunun görüntüsü](./media/images/project-table.png)

1. Tablodaki proje adı ' nı seçin. Bu işlem, cihazınız hakkında başka ayrıntılar ekleyebileceğiniz ve görüntüleyebileceğiniz proje özet sayfasını başlatır.

    ![Proje Ayrıntıları sayfasının görüntüsü](./media/images/device-details-section.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık cihaz ayrıntılarını eklemeye ve sertifika hizmetimizi kullanarak cihazınızı test etmeye hazırsınız. Cihazınızın ayrıntılarını nasıl düzenleyeceğinizi öğrenmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Öğretici: cihaz ayrıntıları ekleme](tutorial-02-adding-device-details.md)
