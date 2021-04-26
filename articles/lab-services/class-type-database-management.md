---
title: İlişkisel veritabanları için veritabanı yönetimine öğretmek üzere laboratuvar ayarlama | Microsoft Docs
description: İlişkisel veritabanlarının yönetimini öğretmek için laboratuvar ayarlamayı öğrenin.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462424"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>İlişkisel veritabanları için veritabanı yönetimine öğretmek üzere laboratuvar ayarlama

Bu makalede, Azure Lab Services ' de temel veritabanları yönetim sınıfı için bir laboratuvarın nasıl ayarlanacağı açıklanır. Veritabanları kavramlarından biri, üniversitenin çoğu bilgisayar bilimi departmanlarından biridir. Yapılandırılmış Sorgu Dili (SQL) uluslararası bir standarttır. SQL, bir veritabanında içerik ekleme, erişme ve yönetme dahil olmak üzere, ilişki veritabanı yönetimine yönelik standart dildir.  Bu en çok, hızlı işleme, kanıtlanmış güvenilirlik, kolaylıklar ve kullanım esnekliği için belirtilmiştir.

Bu makalede, hem MySQL veritabanı sunucusu hem de SQL Server 2019 sunucusuyla bir laboratuvarda sanal makine şablonu ayarlamayı göstereceğiz.  [MySQL](https://www.mysql.com/) , ücretsiz olarak kullanılabilen bir açık kaynaklı Ilişkisel veritabanı yönetim SISTEMIDIR (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) , MICROSOFT 'un RDBMS 'nin en son sürümüdür.

## <a name="lab-configuration"></a>Laboratuvar yapılandırması

Bu Laboratuvarı ayarlamak için, başlamak üzere bir Azure aboneliğine ve laboratuvar hesabına sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Bir Azure aboneliği aldıktan sonra, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilirsiniz. Yeni laboratuvar hesabı oluşturma hakkında daha fazla bilgi için bkz. [Laboratuvar hesabı kurmak Için öğretici](tutorial-setup-lab-account.md).  Ayrıca var olan bir laboratuvar hesabı da kullanabilirsiniz.

### <a name="lab-account-settings"></a>Laboratuvar hesabı ayarları

Laboratuvar hesabı için aşağıdaki tabloda açıklanan ayarları etkinleştirin. Market görüntülerinin nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtme](./specify-marketplace-images.md).

| Laboratuvar hesabı ayarı | Yönergeler |
| ------------------- | ------------ |
|Market görüntüsü| Laboratuvar hesabınızda kullanmak için ' SQL Server 2019 standardını Windows Server 2019 üzerinde etkinleştirin '.|

### <a name="lab-settings"></a>Laboratuvar ayarları

Bir sınıf Laboratuvarı ayarlarken aşağıdaki tablodaki ayarları kullanın.  Sınıf Laboratuvarı oluşturma hakkında daha fazla bilgi için bkz. [bir derslik Laboratuvarı ayarlama öğreticisi](tutorial-setup-classroom-lab.md).

| Laboratuvar ayarları | Değer/yönergeler |
| ------------ | ------------------ |
|Sanal makine boyutu| Orta. Bu boyut, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir.|
|Sanal makine görüntüsü| Windows Server 2019 üzerinde SQL Server 2019 standart|

## <a name="template-machine-configuration"></a>Şablon makine yapılandırması

Windows Server 2019 ' ye MySQL yüklemek için, [bir sanal makinede MySQL Community Server 'ı yüklemek ve çalıştırmak](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)bölümünde bahsedilen adımları izleyebilirsiniz.

SQL Server 2019, yeni Laboratuvarı oluştururken seçtiğimiz sanal makine görüntüsüne önceden yüklenmiştir.

## <a name="cost-estimate"></a>Maliyet tahmini

Bu sınıf için olası bir maliyet tahminini ele alalım.  25 öğrencilerden oluşan bir sınıf kullanacağız.  20 saatlik zamanlanan sınıf zamanı vardır.  Ayrıca, her öğrenci, zamanlanan sınıf zamanı dışında ev ödevleri veya atamalar için 10 saatlik kota alır.  Seçtiğiniz sanal makine boyutu, 42 laboratuvar birimi olan orta idi.

Bu sınıf için olası bir maliyet tahmini örneği aşağıda verilmiştir:

25 öğrenci \* (20 zamanlanan saat + 10 kota saati) \* 0,42 ABD doları/315,00 ABD Doları

Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç

Bu makale, hem MySQL hem de SQL Server kullanarak temel veritabanı yönetim kavramları için laboratuvar oluşturmak için gereken adımlarda size kılavuzluk sağlar. Diğer veritabanı sınıfları için benzer bir kurulum kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar, herhangi bir laboratuvarı ayarlamak için ortaktır.

- [Şablon oluşturma ve yönetme](how-to-create-manage-template.md)
- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users)