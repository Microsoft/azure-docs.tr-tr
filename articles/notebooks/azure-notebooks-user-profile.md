---
title: Azure Notebooks ile kullanım için Kullanıcı profili ve KIMLIĞI
description: Azure Notebooks ile Kullanıcı profilinizi ve Kullanıcı KIMLIĞINIZI oluşturma ve yönetme, paylaşılan not defterlerinin URL 'sinin bir parçası haline geliyor.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 3985e3d5fbe6e24014694665557f13bcc9a35a25
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598202"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Azure Notebooks için profiliniz ve Kullanıcı KIMLIĞINIZ

Azure Notebooks güçlü, işbirliğine dayalı alanı içinde Kullanıcı profiliniz, genel görüntünüzü başkalarına sunar:

[![An Azure Notebooks profili sayfası](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Kullanıcı KIMLIĞINIZ, projeleri ve not defterlerini paylaşmak için kullandığınız URL 'lerin bir parçasıdır. Aşağıdaki listede farklı URL desenleri açıklanmaktadır:

- `https://notebooks.azure.com/<user_id>`: profil sayfanız.
- `https://notebooks.azure.com/<user_id>/projects`: projeleriniz. Tüm projeleri görürsünüz; diğer kullanıcılar yalnızca genel projelerinizi görür.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: proje dosyaları.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Belirli projelerin klonları.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: belirli bir not defteri veya dosyanın HTML önizlemesi.

## <a name="your-user-id"></a>Kullanıcı KIMLIĞINIZ

Azure Notebooks ilk kez oturum açarken, hesabınıza otomatik olarak geçici bir kullanıcı KIMLIĞI atanır (örneğin, "Anon-idr3ca"). "Anon-" ile başlayan bir Kullanıcı KIMLIĞINIZ olduğu sürece, Azure Notebooks her oturum açışınızda bunu değiştirmenizi ister:

![Azure Notebooks oturum açarken kullanıcı KIMLIĞI oluşturmak için istemde bulun](media/accounts/create-user-id.png)

**Kullanıcı kimliğini Yapılandır** komutu ayrıca geçici kullanıcı adının yanında görünür:

![Geçici bir KIMLIK kullanırken görüntülenen kullanıcı KIMLIĞI komutunu yapılandırma](media/accounts/configure-user-id-command.png)

Ayrıca, Kullanıcı KIMLIĞINIZI profil sayfanızda dilediğiniz zaman değiştirebilirsiniz.

Bir kullanıcı KIMLIĞI, dört ve altı harfli, sayı ve kısa çizgi arasında oluşmalıdır. Başka hiçbir karaktere izin verilmez ve Kullanıcı KIMLIĞI kısa çizgi ile başlayamaz veya bitemez ya da bir satırda birden çok tire kullanamaz. Kullanıcı kimlikleri tüm Azure Notebooks hesaplarında benzersiz olduğundan, "Kullanıcı KIMLIĞI zaten kullanımda" iletisini görebilirsiniz. (Bir Microsoft ticari markayı Kullanıcı KIMLIĞI olarak kullanmaya çalıştığınızda ileti de görüntülenir.) Bu durumlarda, farklı bir kullanıcı KIMLIĞI seçin.

> [!Important]
> KIMLIĞINIZI değiştirmek, önceki KIMLIĞINIZI kullanarak paylaştığınız tüm URL 'Leri geçersiz kılar. Bağlantıları yeniden doğrulamak için KIMLIĞINIZI önceki KIMLIĞINIZE geri dönüştürebilirsiniz. Ancak, başka bir Kullanıcı bu sırada kullanılmamış olan KIMLIĞI talep etmek için mümkündür.

## <a name="your-profile"></a>Profiliniz

Profiliniz, `https://notebooks.azure.com/<user_id>` URL 'sindeki herkese açık olarak görüntülenebilir bilgilerden oluşur. Profil sayfanız Ayrıca son kullanılan projelerinizi ve tüm yıldızlı projelerini gösterir.

Profilinizi düzenlemek için profil sayfanızdaki **profil bilgilerini düzenle** komutunu kullanın. Profilinizin bölümleri aşağıdaki gibidir:

| Section | İçindekiler |
| --- | --- |
| Profil fotoğrafı | Profil sayfanızda gösterilen bir görüntü. |
| Hesap bilgileri | Görünen ad, Kullanıcı KIMLIĞI ve genel e-posta hesabı. Buradaki e-posta hesabı, diğer kullanıcılara sizinle iletişim kurmak için bir anlamı sağlar ve Azure Notebooks oturum açmak için kullandığınız [hesaptan](azure-notebooks-user-account.md) farklı olabilir. |
| Profil bilgileri | Konumunuz, şirketiniz, iş unvanı, Web siteniz ve kendinize ait kısa bir açıklama. |
| Sosyal profiller | Bunları paylaşmak istiyorsanız GItHub, Twitter ve Facebook kimlikleriniz. |
| Gizlilik ayarları | İki komut sağlar:<ul><li>**Profilimi dışarı aktar**: fotoğraflarınızın, profil bilgilerinizin ve güvenlik günlüklerinin dahil Azure Notebooks kaydettiği tüm bilgileri içeren bir *. zip* dosyası oluşturur ve indirir.</li><li>**Hesabımı Sil**: Azure Notebooks depolanan tüm kişisel bilgilerinizi kalıcı olarak siler.</li></ul> |
| Site özelliklerini etkinleştir | Azure Notebooks davranışının yönlerini denetlemenizi sağlar:<ul><li>**Not defterleri Için Birleşik ön uç**: daha hızlı ve daha iyi kalıcılık sağlar.</li><li>**Varsayılan olarak JupyterLab 'Da Çalıştır**: varsayılan olarak, Azure Notebooks çoğu kullanıcı için uygun olan basit bir kullanıcı arabirimi sağlar. Jupyıterlab, deneyimli kullanıcılar için daha zengin ancak daha karmaşık bir arabirim sağlar.</li><li>**VNext Web sitesi**: Bu belgede gösterilen, modernlanmış Web yerleşimini etkin hale sunar.</li></ul> |

## <a name="next-steps"></a>Sonraki adımlar  

> [!div class="nextstepaction"]
> [Öğretici: doğrusal regresyon yapmak için bir Jupyter Not defteri çalıştırma](tutorial-create-run-jupyter-notebook.md)
