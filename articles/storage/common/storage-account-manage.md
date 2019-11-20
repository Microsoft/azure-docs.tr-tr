---
title: Azure portal-Azure Storage 'da depolama hesabı ayarlarını yönetme | Microsoft Docs
description: Erişim denetimi ayarlarını yapılandırma, hesap erişim anahtarlarını yeniden oluşturma, erişim katmanını değiştirme veya hesap tarafından kullanılan çoğaltma türünü değiştirme gibi Azure portal depolama hesabı ayarlarını yönetmeyi öğrenin. Ayrıca, portalda bir depolama hesabını silmeyi de öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 3c01cc870b20c8256b215eb700548e6cd69ad0d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748996"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Azure portal depolama hesabı ayarlarını yönetme

Depolama hesabınızın çeşitli ayarları [Azure Portal](https://portal.azure.com)kullanılabilir. Bu makalede, bu ayarlardan bazıları ve bunların nasıl kullanılacağı açıklanmaktadır.

## <a name="access-control"></a>Erişim denetimi

Azure depolama, rol tabanlı erişim denetimi (RBAC) aracılığıyla BLOB depolama ve kuyruk depolama için Azure Active Directory yetkilendirmeyi destekler. Azure AD ile yetkilendirme hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](storage-auth-aad.md).

Azure portal **erişim denetimi** ayarları, kullanıcılara, gruplara, hizmet sorumlularına ve YÖNETILEN kimliklere RBAC rolleri atamak için basit bir yol sunar. RBAC rolleri atama hakkında daha fazla bilgi için bkz. [RBAC ile blob ve kuyruk verileri için erişim haklarını yönetme](storage-auth-aad-rbac.md).

## <a name="tags"></a>Etiketler

Azure depolama, Azure kaynaklarınızı özelleştirilmiş bir taksonomi ile düzenlemek için Azure Resource Manager etiketlerini destekler. Depolama hesaplarınıza Etiketler uygulayarak bunları aboneliğinizde mantıksal bir biçimde gruplayabilirsiniz.

Depolama hesapları için, bir etiket adı 128 karakterle sınırlıdır ve bir etiket değeri 256 karakterle sınırlıdır.

Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Erişim tuşları

Bir depolama hesabı oluşturduğunuzda, Azure 2 512 bit depolama hesabı erişim anahtarları oluşturur. Bu anahtarlar, paylaşılan anahtar aracılığıyla depolama hesabınıza erişim yetkisi vermek için kullanılabilir. Uygulamalarınız için kesintiye uğramadan anahtarları döndürebilir ve yeniden oluşturabilirsiniz. Microsoft, bunu düzenli olarak yapmanızı önerir.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Hesap anahtarlarını ve bağlantı dizesini görüntüle

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Erişim anahtarlarını yeniden oluştur

Microsoft, depolama hesabınızı güvende tutmaya yardımcı olmak için erişim anahtarlarınızı düzenli aralıklarla yeniden oluşturmanız önerilir. Anahtarlarınızı döndürebilmeniz için iki erişim tuşu atanır. Anahtarlarınızı döndürdüğünüzde, uygulamanızın işlem boyunca Azure depolama 'ya erişimini koruduğundan emin olursunuz. 

> [!WARNING]
> Erişim anahtarlarınızın yeniden oluşturulması, depolama hesabı anahtarına bağlı olan tüm uygulamaları veya Azure hizmetlerini etkileyebilir. Depolama hesabına erişmek için hesap anahtarını kullanan tüm istemciler, Media Services, bulut, masaüstü ve mobil uygulamalar gibi yeni anahtarı kullanacak şekilde ve Azure depolama için grafik kullanıcı arabirimi uygulamaları (örneğin, Azure Depolama Gezgini) kullanmak üzere güncelleştirilmeleri gerekir [ ](https://azure.microsoft.com/features/storage-explorer/).

Depolama hesabı anahtarlarınızı döndürmek için bu işlemi izleyin:

1. İkincil anahtarı kullanmak için uygulama kodunuzda bağlantı dizelerini güncelleştirin.
2. Depolama hesabınız için birincil erişim tuşunu yeniden oluşturun. Azure portal **erişim tuşları** dikey penceresinde, yeniden **Oluştur KEY1**' a tıklayın ve sonra yeni bir anahtar oluşturmak istediğinizi onaylamak için **Evet** ' e tıklayın.
3. Yeni birincil erişim tuşunu referans olarak kullanmak için bağlantı dizelerini güncelleştirin.
4. İkincil erişim tuşunu da aynı şekilde yeniden oluşturun.

## <a name="account-configuration"></a>Hesap yapılandırması

Bir depolama hesabı oluşturduktan sonra, yapılandırmasını değiştirebilirsiniz. Örneğin, verilerinizin nasıl çoğaltılabileceğini değiştirebilir veya hesabın erişim katmanını sık erişimli iken seyrek erişimli olarak değiştirebilirsiniz. [Azure Portal](https://portal.azure.com), depolama hesabınıza giderek hesap yapılandırmasını görüntülemek ve/veya değiştirmek için **Ayarlar** ' ın altındaki **yapılandırma** ' yı bulup tıklayın.

Depolama hesabı yapılandırmasını değiştirmek, maliyetlerine neden olabilir. Daha ayrıntılı bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/) sayfası.

## <a name="delete-a-storage-account"></a>Bir depolama hesabını silme

Artık kullanmadığınız bir depolama hesabını kaldırmak için [Azure portal](https://portal.azure.com)’da depolama hesabına gidin ve **Sil**’e tıklayın. Depolama hesabı silindiğinde, hesaptaki tüm veriler dahil olmak üzere tüm hesap silinir.

> [!WARNING]
> Silinen depolama hesabını geri yüklemek veya silme işlemi öncesinde içinde yer alan içerikleri almak mümkün değildir. Hesabı silmeden önce kaydetmek istediğiniz şeyleri yedeklediğinizden emin olun. Bu ayrıca hesaptaki tüm kaynaklar için geçerlidir; bir blob, tablo, kuyruk veya dosya sildiğinizde bu işlem kalıcı olarak gerçekleştirilir.
> 

Bir Azure sanal makinesiyle ilişkili bir depolama hesabını silmeye çalışırsanız, depolama hesabının kullanımda olduğu hakkında bir hata alabilirsiniz. Bu hatayı giderme hakkında yardım için lütfen bkz. [Depolama hesaplarını sildiğinizde sorun giderme](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure depolama hesabına genel bakış](storage-account-overview.md)
- [Depolama hesabı oluşturma](storage-quickstart-create-account.md)
