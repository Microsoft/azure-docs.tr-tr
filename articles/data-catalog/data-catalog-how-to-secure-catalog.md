---
title: Azure Veri Kataloğu 'na erişimi güvenli hale getirme
description: Bu makalede, Azure Veri Kataloğu 'nda bir veri kataloğunun ve veri varlıklarının güvenliğini sağlama açıklanmaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976757"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Veri kataloğuna ve veri varlıklarına erişimi güvenli hale getirme

> [!IMPORTANT]
> Bu özellik yalnızca Azure Veri Kataloğu 'nun standart sürümünde kullanılabilir.

Azure Veri Kataloğu, veri kataloğuna kimlerin erişebileceğini ve katalogdaki meta verilerde gerçekleştirebilecekleri işlemleri (kaydolma, not ekleme, Sahiplik alma) belirtmenize olanak tanır. 

## <a name="catalog-users-and-permissions"></a>Katalog kullanıcıları ve izinleri

Bir kullanıcıya veya gruba bir veri kataloğu erişimi sağlamak ve izinleri ayarlamak için:

1. [Veri kataloğunuzun giriş sayfasında](https://www.azuredatacatalog.com), araç çubuğunda **Ayarlar** ' a tıklayın.

   ![Azure Veri Kataloğu giriş sayfası ayarları düğmesi](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Ayarlar sayfasında, **Katalog kullanıcıları** bölümünü genişletin.

   ![Azure Veri Kataloğu kullanıcıları ekleme düğmesi](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3.           **Ekle**'yi tıklatın.

4. Katalogla ilişkili Azure Active Directory (AAD) içindeki **güvenlik grubunun** tam **Kullanıcı adını** veya adını girin. Birden fazla Kullanıcı veya grup ekliyorsanız ayırıcı olarak virgül (', ') kullanın.

   ![Azure Veri Kataloğu kullanıcıları-kullanıcılar veya gruplar](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Metin kutusunun dışında **ENTER** veya **Tab** tuşuna basın. 

6. Tüm izinlerin (**ek açıklama**, **kayıt**ve **alma**) bu kullanıcılara veya gruplara varsayılan olarak atandığını onaylayın. Diğer bir deyişle, Kullanıcı veya grup veri varlıklarını [kaydedebilir]( data-catalog-how-to-register.md), [veri varlıklarına açıklama]( data-catalog-how-to-annotate.md)ekleyebilir ve [veri varlıklarının sahipliğini alabilir]( data-catalog-how-to-manage.md). 

   ![Azure Veri Kataloğu kullanıcıları-varsayılan izinler](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Kullanıcıya veya gruba yalnızca okuma erişimini sağlamak için bu kullanıcı veya grup için **ek açıklama** seçeneğini temizleyin. Bunu yaptığınızda Kullanıcı veya Grup, katalogda veri varlıklarına açıklama ekleyebilir, ancak bunları görüntüleyebilirler. 

8. Bir kullanıcının veya grubun veri varlıklarını kaydetmesini engellemek için, bu kullanıcı veya grup için **Kaydet** seçeneğini temizleyin.

9. Bir kullanıcının bir veri varlığının sahipliğini ele geçirmesine engel olmak için, bu kullanıcı veya grup için **Sahiplik Al** seçeneğini temizleyin. 

10. Katalog kullanıcılarından bir Kullanıcı/Grup silmek için, listenin altındaki Kullanıcı/Grup için **x** öğesine tıklayın. 

   ![Azure Veri Kataloğu kataloğu kullanıcıları-Kullanıcı X simgesini Sil](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Kullanıcıları doğrudan eklemek ve izinleri atamak yerine, Katalog kullanıcılarına güvenlik grupları eklemenizi öneririz. Daha sonra, rolleri ve kataloglarına gereken erişimi olan güvenlik gruplarına kullanıcı ekleyin.

## <a name="special-considerations"></a>Özel durumlar

- Güvenlik gruplarına atanan izinler eklenebilir. Bir kullanıcının iki grupta olduğunu varsayalım. Bir grupta not ekleme izinleri ve diğer grubun ek açıklama izinleri yok. Daha sonra, Kullanıcı not ekleme izinlerine sahiptir. 
- Açıkça bir kullanıcıya atanan izinler, kullanıcının ait olduğu gruplara atanan izinleri geçersiz kılar. Önceki örnekte, kullanıcıyı doğrudan Katalog kullanıcılarına eklemiş ve not ekleme izinleri atamadığınızı varsayalım. Kullanıcı, ek açıklama izinleri olan bir grubun üyesi olsa da, Kullanıcı veri varlıklarına açıklama eklemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Veri Kataloğu ile çalışmaya başlama](data-catalog-get-started.md)
