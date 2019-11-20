---
title: Bir şemanın yaşam döngüsünü anlama
description: Bir şema 'in gittiği yaşam döngüsü ve her aşama hakkındaki ayrıntılar hakkında bilgi edinin.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: d071a2af985d294bc87a5363d9e7566762e0fd8a
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960487"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Azure Blueprint yaşam döngüsünü anlama

Azure 'daki birçok kaynak gibi Azure 'da bir şema, tipik ve doğal yaşam döngüsüne sahiptir. Bunlar artık gerekli olmadığında veya ilgili olduğunda oluşturulur, dağıtılır ve son olarak silinir.
Planlar standart yaşam döngüsü işlemlerini destekler. Daha sonra, altyapısını kod olarak yöneten kuruluşlar için ortak sürekli tümleştirmeyi ve sürekli dağıtım işlem hatlarını destekleyen ek durum düzeyleri sağlar.

Bir şema ve aşamaları tamamen anlamak için standart yaşam döngüsünü ele alacağız:

> [!div class="checklist"]
> - Şema oluşturma ve düzenleniyor
> - Şema 'i yayımlama
> - Şema 'in yeni bir sürümünü oluşturma ve düzenlemeyle
> - Şema 'in yeni bir sürümünü yayımlama
> - Şema 'in belirli bir sürümünü silme
> - Şema silme

## <a name="creating-and-editing-a-blueprint"></a>Şema oluşturma ve düzenleniyor

Bir Blueprint oluştururken, bu yapıya yapıt ekleyin, bir yönetim grubuna veya aboneliğine kaydedin ve benzersiz bir ad ve benzersiz bir sürüm sağlamış olmanız gerekir. Şema artık **taslak** modunda ve henüz atanamaz. **Taslak** modundayken, güncelleştirilme ve değiştirme devam edebilir.

**Taslak** modunda hiçbir şekilde yayımlanmamış bir şema, şema **tanımları** sayfasında **yayımlandıklarından**farklı bir simge görüntüler. **En son sürüm** , bu yayımlanmamış hiçbir şema için **taslak** olarak görüntülenir.

[Azure Portal](../create-blueprint-portal.md#create-a-blueprint) veya [REST API](../create-blueprint-rest-api.md#create-a-blueprint)bir şema oluşturun ve düzenleyin.

## <a name="publishing-a-blueprint"></a>Şema yayımlama

Tüm planlı değişiklikler **taslak** modunda bir şema üzerinde yapıldıktan sonra **yayımlanabilir** ve atama için kullanılabilir hale getirilebilir. Şema 'in **yayımlanan** sürümü değiştirilemez.
**Yayımladıktan**sonra, şema **taslak** taslaktan farklı bir simgeyle görüntülenir ve belirtilen sürüm numarasını **en son sürüm** sütununda görüntüler.

[Azure Portal](../create-blueprint-portal.md#publish-a-blueprint) veya [REST API](../create-blueprint-rest-api.md#publish-a-blueprint)ile bir şema yayımlayın.

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Şema 'in yeni bir sürümünü oluşturma ve düzenlemeyle

Bir şema 'in **yayımlanmış** bir sürümü değiştirilemez. Ancak, yeni bir şema sürümü var olan şema öğesine eklenebilir ve gerektiğinde değiştirilebilir. Varolan bir şemayı düzenleyerek değişiklik yapın. Yeni değişiklikler kaydedildiğinde, şema şimdi **yayımdan kaldırılmış değişiklikleri**içerir. Bu değişiklikler, Blueprint 'in yeni bir **taslak** sürümüdür.

[Azure Portal](../create-blueprint-portal.md#edit-a-blueprint)ile bir şema 'i düzenleyin.

## <a name="publishing-a-new-version-of-the-blueprint"></a>Şema 'in yeni bir sürümünü yayımlama

Bir şema 'in düzenlenmiş her sürümü, atanmadan önce **yayımlanmalıdır** . Bir **şema Için yayımdan kaldırılan değişiklikler** yapıldı Ancak **Yayımlanmadığında**, şemayı **Yayınla** sayfasında şeması Düzenle düğmesi bulunur. Düğme görünür değilse, şema zaten **yayımlandı** ve **yayımlanmamış değişikliklere**sahip değildir.

> [!NOTE]
> Tek bir şema, her biri aboneliğe atanabileceği birden çok **yayımlanmış** sürüme sahip olabilir.

**Yayımlanmamış değişikliklerle**bir şema yayımlamak için, yeni bir şema yayımlamak için aynı adımları kullanın.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Şema 'in belirli bir sürümünü silme

Bir şema 'in her sürümü benzersiz bir nesnedir ve tek tek **yayımlanabilir**. Bu nedenle, bir şema 'in her sürümü de silinebilir. Bir şema sürümünün silinmesi, bu şema 'in diğer sürümlerini etkilemez.

> [!NOTE]
> Etkin atamaları olan bir şema silmek mümkün değildir. Önce atamaları silin ve ardından kaldırmak istediğiniz sürümü silin.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki sayfadan **şema tanımlarını** seçin ve bir sürümünü silmek istediğiniz şema öğesini bulmak için filtre seçeneklerini kullanın. Düzenle sayfasını açmak için tıklayın.

1. **Yayınlanan sürümler** sekmesine tıklayın ve silmek istediğiniz sürümü bulun.

1. Silmek için sürüme sağ tıklayın ve **Bu sürümü Sil**' i seçin.

## <a name="deleting-the-blueprint"></a>Şema silme

Çekirdek Şeması da silinebilir. Çekirdek şema silme, hem **taslak** hem de **yayımlanan** şemalar dahil olmak üzere, bu şema 'in tüm şema sürümlerini de siler. Şema 'in bir sürümünü silmenin yanı da, çekirdek şeması silme, şema sürümlerinin herhangi birinin mevcut atamalarını kaldırmaz.

> [!NOTE]
> Etkin atamaları olan bir şema silmek mümkün değildir. Önce atamaları silin ve ardından kaldırmak istediğiniz sürümü silin.

[Azure Portal](../create-blueprint-portal.md#delete-a-blueprint) veya [REST API](../create-blueprint-rest-api.md#delete-a-blueprint)bir şema öğesini silin.

## <a name="assignments"></a>Atamalar

Bir şema için bir şema atanabileceği yaşam döngüsü sırasında birkaç noktaya yer vardır. Bir şema sürümünün bir sürümü **yayımlandığında**, bu sürüm bir aboneliğe atanabilir. Bu yaşam döngüsü, daha yeni bir sürüm geliştirildiğinde bir şema sürümlerinin kullanılmasını ve etkin olarak atanmasını sağlar.

Planların sürümleri atandığında, atanma yerleri ve hangi parametrelere atandıklarından emin olmak önemlidir. Parametreler statik veya dinamik olabilir. Daha fazla bilgi için bkz. [statik ve dinamik parametreler](parameters.md).

### <a name="updating-assignments"></a>Atamalar güncelleştiriliyor

Bir şema atandığında, atama güncelleştirilebilen olabilir. Mevcut bir atamayı güncelleştirmek için aşağıdakiler de dahil olmak üzere çeşitli nedenler vardır:

- [Kaynak kilitlemeyi](resource-locking.md) ekleme veya kaldırma
- [Dinamik parametrelerin](parameters.md#dynamic-parameters) değerini değiştirme
- Atamayı, şema 'in daha yeni **yayımlanmış** bir sürümüne yükseltin

Nasıl yapılacağını öğrenmek için bkz. [var olan atamaları güncelleştirme](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Atama kaldırma

Şema artık gerekmiyorsa, yönetim grubu veya abonelikten atanmamış olabilir. Şeması kaldırma sırasında aşağıdakiler gerçekleşir:

- [Şema kaynak kilitlemeyi](resource-locking.md) kaldırma
- Şema atama nesnesini silme
- Oluştur **Sistem tarafından atanan yönetilen bir kimlik** kullanılmışsa de silinir

> [!NOTE]
> Şema atama tarafından dağıtılan tüm kaynaklar yerinde kalır, ancak artık Azure şemaları tarafından korunmaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Statik ve dinamik parametrelerin](parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.