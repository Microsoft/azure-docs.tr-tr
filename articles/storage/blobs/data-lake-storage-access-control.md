---
title: Azure Data Lake Storage 2. erişim denetim listeleri | Microsoft Docs
description: POSIX benzeri ACL erişim denetim listelerinin Azure Data Lake Storage 2. nasıl çalıştığını anlayın.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 50c6b4f309eb78acee0cfa59d1b540adba65cab2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774822"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. 'de erişim denetim listeleri (ACL 'Ler)

Azure Data Lake Storage 2. hem Azure rol tabanlı erişim denetimi (Azure RBAC) hem de POSIX benzeri erişim denetim listelerini (ACL 'Ler) destekleyen bir erişim denetimi modeli uygular. Bu makalede Data Lake Storage 2. içindeki erişim denetim listeleri açıklanmaktadır. Azure RBAC ile ACL 'lerle birlikte nasıl birleştirileceğini ve sistemin bunları yetkilendirme kararları almak için nasıl değerlendirdiği hakkında bilgi edinmek için bkz. [Azure Data Lake Storage 2. erişim denetimi modeli](data-lake-storage-access-control-model.md).

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>ACL 'Ler hakkında

[Güvenlik sorumlusunu](../../role-based-access-control/overview.md#security-principal) , dosyalar ve dizinler için bir erişim düzeyiyle ilişkilendirebilirsiniz. Bu ilişkilendirmeler bir *erişim denetim listesi (ACL)* içinde yakalanır. Depolama hesabınızdaki her dosya ve dizinin bir erişim denetim listesi vardır. Bir güvenlik sorumlusu bir dosya veya dizin üzerinde bir işlem gerçekleştirmeye çalıştığında, bir ACL denetimi, güvenlik sorumlusunun (Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik) işlemi gerçekleştirmek için doğru izin düzeyine sahip olup olmadığını belirler.

> [!NOTE]
> ACL 'Ler yalnızca aynı Kiracıdaki güvenlik sorumluları için geçerlidir ve paylaşılan anahtar veya paylaşılan erişim imzası (SAS) belirteci kimlik doğrulaması kullanan kullanıcılar için de geçerlidir. Bunun nedeni, çağıran ile ilişkili bir kimlik olmadığından ve bu nedenle güvenlik sorumlusu izin tabanlı yetkilendirmenin gerçekleştirilemediği için.  

<a id="set-access-control-lists"></a>

## <a name="how-to-set-acls"></a>ACL 'Leri ayarlama

Dosya ve dizin düzeyindeki izinleri ayarlamak için aşağıdaki makalelerden birine bakın:

| Ortam | Makale |
|--------|-----------|
|Azure Depolama Gezgini |[Azure Data Lake Storage 2. ACL 'Leri ayarlamak için Azure Depolama Gezgini kullanma](data-lake-storage-explorer-acl.md)|
|.NET |[Azure Data Lake Storage 2. ACL 'Leri ayarlamak için .NET kullanın](data-lake-storage-acl-dotnet.md)|
|Java|[Azure Data Lake Storage 2. ACL 'Leri ayarlamak için Java kullanma](data-lake-storage-acl-java.md)|
|Python|[Azure Data Lake Storage 2. ACL 'Leri ayarlamak için Python kullanma](data-lake-storage-acl-python.md)|
|JavaScript (Node.js)|[Azure Data Lake Storage 2. ACL 'Leri ayarlamak için Node.js içindeki JavaScript SDK 'sını kullanın](data-lake-storage-directory-file-acl-javascript.md)|
|PowerShell|[Azure Data Lake Storage 2. ACL 'Leri ayarlamak için PowerShell kullanma](data-lake-storage-acl-powershell.md)|
|Azure CLI’si|[Azure Data Lake Storage 2. ACL 'Leri ayarlamak için Azure CLı 'yi kullanma](data-lake-storage-acl-cli.md)|
|REST API |[Yol-Güncelleştir](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Güvenlik sorumlusu bir *hizmet* sorumlusu ise, ilgili uygulama KAYDıNıN nesne kimliğini değil, hizmet sorumlusunun nesne kimliğini kullanmak önemlidir. Hizmet sorumlusunun nesne KIMLIĞINI almak için Azure CLı 'yı açın ve ardından şu komutu kullanın: `az ad sp show --id <Your App ID> --query objectId` . `<Your App ID>`yer tutucusunu, uygulama kaydlarınızın uygulama kimliğiyle değiştirdiğinizden emin olun.

## <a name="types-of-acls"></a>ACL türleri

İki tür erişim denetim listesi vardır: *erişim ACL 'leri* ve *varsayılan ACL 'ler*.

ACL denetim erişimi bir nesneye erişin. Dosya ve dizinlerin her ikisi de erişim ACL 'Lerine sahiptir.

Varsayılan ACL 'ler, bu dizin altında oluşturulan tüm alt öğeler için erişim ACL 'Lerini tanımlayan bir dizinle ilişkili ACL 'lerin şablonlarıdır. Dosyaların varsayılan ACL 'Leri yok.

Erişim ACL 'Leri ve varsayılan ACL 'Ler aynı yapıya sahiptir.

> [!NOTE]
> Bir üst öğenin varsayılan ACL 'sini değiştirmek, erişim ACL 'sini veya zaten var olan alt öğelerin ACL 'sini etkilemez.

## <a name="levels-of-permission"></a>İzin düzeyleri

Bir kapsayıcıdaki dizinler ve dosyalar üzerindeki izinler **okuma**, **yazma** ve **yürütme**, aşağıdaki tabloda gösterildiği gibi dosyalar ve dizinler üzerinde de kullanılabilir:

|            |    Dosya     |   Dizin |
|------------|-------------|----------|
| **Okuma (R)** | Bir dosyanın içeriğini okuyabilir | Dizinin içeriğini listelemek için **okuma** ve **yürütme** gerektirir |
| **Yazma (W)** | Bir dosyaya yazabilir veya ekleyebilir | Bir dizinde alt öğeler oluşturmak için **yazma** ve **yürütme** gerektirir |
| **Yürütme (X)** | Data Lake Storage 2. bağlamında hiçbir şey anlamına gelmez | Bir dizinin alt öğelerinin çapraz geçişini yapmak için gereklidir |

> [!NOTE]
> Yalnızca ACL 'Leri kullanarak (Azure RBAC olmadan) izin veriyorsanız, bir güvenlik sorumlusu için bir dosyaya okuma veya yazma erişimi vermek istiyorsanız, kapsayıcının kök klasörüne ve dosyaya yol açabilecek klasörlerin hiyerarşisindeki her klasöre güvenlik sorumlusu **yürütme** izinleri vermeniz gerekir.

### <a name="short-forms-for-permissions"></a>İzinlerin kısaltmaları

**RWX**, **Okuma + Yazma + Yürütme** için kullanılır. **Okuma=4**, **Yazma=2** ve **Yürütme=1** olup toplamları izinleri temsil eden daha da kısaltılmış bir sayısal biçim mevcuttur. Bazı örnekler aşağıda verilmiştir.

| Sayısal biçim | Kısa biçim |      Anlamı     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Okuma + Yazma + Yürütme |
| 5            | `R-X`        | Okuma + Yürütme         |
| 4            | `R--`        | Okuma                   |
| 0            | `---`        | İzin yok         |

### <a name="permissions-inheritance"></a>İzin devralma

Data Lake Storage 2. tarafından kullanılan POSIX stili modelde bir öğe için izinler öğenin kendisinde depolanır. Diğer bir deyişle, izinler alt öğe oluşturulduktan sonra ayarlandıysa, öğenin izinleri üst öğeden devralınamaz. İzinler yalnızca, alt öğeler oluşturulmadan önce üst öğelerde varsayılan izinler ayarlandıysa devralınır.

## <a name="common-scenarios-related-to-acl-permissions"></a>ACL izinleriyle ilgili yaygın senaryolar

Aşağıdaki tabloda, bir güvenlik sorumlusunun **işlem** sütununda listelenen işlemleri gerçekleştirmesini sağlamak IÇIN gereken ACL girdileri gösterilmektedir. 

Bu tablo, kurgusal bir dizin hiyerarşisinin her düzeyini temsil eden bir sütun gösterir. Kapsayıcının kök dizini () için bir sütun `\` , **Oregon** adlı bir alt dizin, **İstanbul** adlı MARI dizininin bir alt dizini ve **Data.txt** adlı Portland dizinindeki bir metin dosyası. 

> [!IMPORTANT]
> Bu tablo, hiçbir Azure rol ataması olmadan **yalnızca** ACL 'leri kullandığınızı varsayar. Azure RBAC 'i ACL 'lerle birlikte birleştiren benzer bir tablo görmek için bkz. [izin tablosu: Azure RBAC ve ACL 'Yi birleştirme](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl).

|    İşlem             |    /    | 'Daki | Portland | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Data.txt oku            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Data.txt sonuna ekle       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data.txt Sil          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data.txt oluştur          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Listele                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Liste/Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Liste/Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Önceki iki koşul doğru olduğu sürece, dosyanın üzerine yazma izinleri bunu silmek için gerekli değildir.

## <a name="users-and-identities"></a>Kullanıcılar ve kimlikler

Her dosya ve dizin, bu kimlikler için farklı izinlere sahiptir:

- Sahip olan kullanıcı
- Sahip olan grup
- Adlandırılmış kullanıcılar
- Adlandırılmış gruplar
- Adlandırılmış hizmet sorumluları
- Adlandırılmış Yönetilen kimlikler
- Diğer tüm kullanıcılar

Kullanıcıların ve grupların kimlikleri, Azure Active Directory (Azure AD) kimlikleridir. Bu nedenle, aksi belirtilmediği takdirde, Data Lake Storage 2. bağlamında bir *Kullanıcı* Azure AD kullanıcısına, hizmet sorumlusuna, yönetilen kimliğe veya güvenlik grubuna başvurabilir.

### <a name="the-owning-user"></a>Sahip olan kullanıcı

Öğeyi oluşturan kullanıcı otomatik olarak öğenin sahibi olan kullanıcıdır. Sahip olan kullanıcı şunları yapabilir:

* Sahip olunan bir dosyanın izinlerini değiştirme.
* Sahip olan kullanıcı aynı zamanda hedef grubun bir üyesi oldukça, sahip olunan bir dosyanın sahibi olan grubunu değiştirme.

> [!NOTE]
> Sahip olan Kullanıcı, bir dosyanın veya dizinin sahibi olan *kullanıcısını değiştiremiyor.* Yalnızca süper kullanıcılar bir dosyanın veya dizinin sahibi olan kullanıcısını değiştirebilir.

### <a name="the-owning-group"></a>Sahip olan grup

POSIX ACL 'lerinde, her Kullanıcı bir *birincil grupla* ilişkilendirilir. Örneğin, "Gamze" kullanıcısı "Finans" grubuna ait gelebilir. Gamze birden çok gruba ait olabilir, ancak bir grup her zaman birincil grubu olarak atanır. POSIX’te Gamze bir dosya oluşturduğunda o dosyanın sahibi olan grup birincil grubu olarak ayarlanır (bu örnekte "finans" grubudur). Aksi takdirde sahip olan grup, diğer kullanıcılar/gruplar için atanan izinlere benzer şekilde davranır.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Yeni bir dosya veya dizin için sahip olan grup atanıyor

* **Durum 1**: kök dizin "/". Bu dizin Data Lake Storage 2. bir kapsayıcı oluşturulduğunda oluşturulur. Bu durumda sahip olan Grup, OAuth kullanılarak yapıldıysa kapsayıcıyı oluşturan kullanıcıya ayarlanır. Kapsayıcı paylaşılan anahtar, bir hesap SAS veya hizmet SAS kullanılarak oluşturulduysa, sahip ve sahip grubu **$superuser** olarak ayarlanır.
* **Durum 2** (diğer her durum): yeni bir öğe oluşturulduğunda, sahip olan grup üst dizinden kopyalanır.

#### <a name="changing-the-owning-group"></a>Sahip olan grubu değiştirme

Sahip olan grup aşağıdakiler tarafından değiştirilebilir:
* Herhangi bir süper kullanıcı.
* Sahip olan kullanıcı aynı zamanda hedef grubun üyesi ise sahip olan kullanıcı.

> [!NOTE]
> Sahip olan Grup, bir dosyanın veya dizinin ACL 'Lerini değiştiremiyor.  Sahip olan Grup, yukarıdaki **durum 1** olan kök dizin durumunda hesabı oluşturan kullanıcıya ayarlandığı sürece, sahip olan grup aracılığıyla izin sağlamak için tek bir kullanıcı hesabı geçerli değildir. Uygunsa bu izni geçerli bir kullanıcı hesabına atayabilirsiniz.

## <a name="access-check-algorithm"></a>Erişim denetimi algoritması

Aşağıdaki sözde kod, depolama hesapları için erişim denetimi algoritmasını temsil eder.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
  # Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask isn't used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Maske

Erişim denetimi algoritmasında gösterildiği gibi, maske adlandırılmış kullanıcılar, sahip olan grup ve adlandırılmış gruplar için erişimi sınırlandırır.  

Yeni bir Data Lake Storage 2. kapsayıcısı için, kök dizinin ("/") erişim ACL 'SI için maske, dizinler için 750, dosyalar için **640** olarak varsayılan olarak  . Aşağıdaki tabloda bu izin düzeylerinin sembolik gösterimi gösterilmektedir.

|Varlık|Dizinler|Dosyalar|
|--|--|--|
|Sahip olan kullanıcı|`rwx`|`r-w`|
|Sahip olan grup|`r-x`|`r--`|
|Diğer|`---`|`---`|

Dosyalar yalnızca mağaza sistemindeki dosyalara ait olduğundan, X bitini almaz. 

Maske, arama başına temelinde belirtilebilir. Bu, kümeler gibi farklı tüketim sistemlerinin dosya işlemleri için farklı etkin maskelerle çalışmasına izin verir. Belirli bir istekte bir maske belirtilmişse, varsayılan maskeyi tamamen geçersiz kılar.

### <a name="the-sticky-bit"></a>Yapışkan bit

Yapışkan bit, POSIX kapsayıcısının daha gelişmiş bir özelliğidir. Data Lake Storage 2. bağlamında, yapışkan bitin gerekli olacağı pek olası bir olasılıktır. Özet ' te, bir dizinde yapışkan bit etkinse, alt öğe yalnızca alt öğenin sahibi olan kullanıcı tarafından silinebilir veya yeniden adlandırılabilir.

Yapışkan bit Azure portal gösterilmez.

## <a name="default-permissions-on-new-files-and-directories"></a>Yeni dosya ve dizinlerde varsayılan izinler

Mevcut bir dizin altında yeni bir dosya veya dizin oluşturulduğunda, üst dizindeki varsayılan ACL şunları belirler:

- Alt dizinin varsayılan ACL 'si ve erişim ACL 'SI.
- Alt dosyanın erişim ACL 'SI (dosyaları varsayılan ACL 'ye sahip değildir).

### <a name="umask"></a>umask

Bir dosya veya dizin oluştururken, varsayılan ACL 'Lerin alt öğede nasıl ayarlandığını değiştirmek için umask kullanılır. umask, **sahip olan Kullanıcı**, **sahip olan grup** ve **diğer** için bir RWX değeri içeren üst dizinlerdeki 9 bitlik bir değerdir.

Umask, 007 olarak ayarlanan sabit bir değer Azure Data Lake Storage 2.. Bu değer şunu yapar:

| umask bileşeni     | Sayısal biçim | Kısa biçim | Anlamı |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Sahip olan kullanıcı için, üst öğenin varsayılan ACL 'sini alt öğenin erişim ACL 'sine kopyalayın | 
| umask.owning_group  |    0         |   `---`      | Sahip olan grup için üst öğenin varsayılan ACL 'sini alt öğenin erişim ACL 'sine kopyalayın | 
| uımask. other         |    7         |   `RWX`      | Diğer bir deyişle, alt öğenin erişim ACL 'sindeki tüm izinleri kaldırın |

Azure Data Lake Storage 2. tarafından kullanılan umask değeri, ana dizinde varsayılan bir ACL tanımlanmadığı müddetçe, **diğer** için varsayılan olarak yeni alt klasörlerde hiçbir şekilde iletilmediği anlamına gelir. Bu durumda, umask etkin bir şekilde yok sayılır ve varsayılan ACL tarafından tanımlanan izinler alt öğeye uygulanır. 

Aşağıdaki sözde kod, bir alt öğe için ACL 'Ler oluştururken umask 'in nasıl uygulanacağını gösterir.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="faq"></a>SSS

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL desteğini etkinleştirmem gerekiyor mu?

Hayır. Hiyerarşik ad alanı (HNS) özelliği açık olduğu sürece bir depolama hesabı için ACL 'Ler aracılığıyla erişim denetimi etkinleştirilir.

HNS kapalıysa, Azure Azure RBAC yetkilendirme kuralları yine de geçerlidir.

### <a name="what-is-the-best-way-to-apply-acls"></a>ACL 'Leri uygulamak için en iyi yol nedir?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Azure RBAC ve ACL izinleri nasıl değerlendirilir?

Sistemin depolama hesabı kaynakları için yetkilendirme kararları vermesini sağlamak üzere Azure RBAC ve ACL 'Leri birlikte nasıl değerlendirdiğini öğrenmek için, bkz. [Izinlerin nasıl değerlendirildiği](data-lake-storage-access-control-model.md#how-permissions-are-evaluated).

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Azure rol atamaları ve ACL girdileri sınırları nelerdir?

Aşağıdaki tabloda, Azure RBAC kullanılırken "kaba" izinleri (depolama hesapları veya kapsayıcılar için uygulanan izinler) ve ACL 'Leri kullanarak "hassas" izinleri (dosyalar ve dizinler için uygulanan izinler) yönetmek için göz önünde bulundurmanız gereken limitlerin Özet görünümü verilmektedir. ACL atamaları için güvenlik gruplarını kullanın. Grupları kullanarak, abonelik başına maksimum rol ataması sayısını ve dosya veya dizin başına en fazla ACL girişi sayısını aşmanız daha düşüktür. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Data Lake Storage 2. Azure RBAC devralım işlemini destekliyor mu?

Azure rol atamaları devralınır. Atamalar abonelik, kaynak grubu ve depolama hesabı kaynaklarından, kapsayıcı kaynağına doğru akar.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage 2. ACL 'Leri devralmayı destekliyor mu?

Varsayılan ACL 'ler, üst dizin altında oluşturulan yeni alt dizinler ve dosyalar için ACL 'Ler ayarlamak üzere kullanılabilir. Mevcut alt öğelerin ACL 'Lerini güncelleştirmek için, istenen dizin hiyerarşisi için ACL 'Leri yinelemeli olarak eklemeniz, güncelleştirmeniz veya kaldırmanız gerekecektir. Yönergeler için, bu makalenin [ACL 'leri ayarlama](#set-access-control-lists) bölümüne bakın. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Bir dizini ve içeriğini yinelemeli olarak silmek için hangi izinler gereklidir?

- Çağıranın ' Süper Kullanıcı ' izinleri vardır,

Veya

- Üst dizinin yazma + yürütme izinlerine sahip olması gerekir.
- Silinecek dizin ve içindeki her dizin, okuma + yazma + yürütme izinlerini gerektirir.

> [!NOTE]
> Dizinlerdeki dosyaları silmek için yazma izinlerine sahip olmanız gerekmez. Ayrıca, "/" kök dizini hiçbir şekilde silinemez.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Bir dosya veya dizinin sahibi kim?

Bir dosya veya dizinin Oluşturucusu sahip olur. Kök dizin söz konusu olduğunda, bu kapsayıcıyı oluşturan kullanıcının kimliğidir.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Oluşturma sırasında bir dosyanın veya dizinin sahip olan grubu olarak hangi Grup ayarlandı?

Sahip olan Grup, yeni dosyanın veya dizinin oluşturulduğu üst dizinin sahip olduğu gruptan kopyalanır.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Bir dosyanın sahibi olduğum halde ihtiyacım olan RWX izinlerim yok. Ne yapmalıyım?

Sahip olan kullanıcı kendisine gerekli olan her türlü RWX iznini vermek için dosyanın izinlerini değiştirebilir.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Neden bazen ACL 'lerde GUID 'Leri görüyorum?

Giriş bir kullanıcıyı temsil ediyorsa ve bu kullanıcı artık Azure AD 'de mevcut değilse bir GUID gösterilir. Bu genellikle, kullanıcı şirketten ayrıldığında veya Azure AD’de kullanıcının hesabı silindiğinde gerçekleşir. Ayrıca, hizmet sorumluları ve güvenlik grupları bunları tanımlamak için bir Kullanıcı asıl adına (UPN) sahip değildir ve bu nedenle OID özniteliği (bir GUID) tarafından temsil edilir.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Nasıl yaparım? bir hizmet sorumlusu için ACL 'Leri doğru şekilde ayarladı musunuz?

Hizmet sorumluları için ACL 'Leri tanımladığınızda, oluşturduğunuz uygulama kaydı için *hizmet sorumlusunun* nesne KIMLIĞI (OID) kullanılması önemlidir. Kayıtlı uygulamaların, belirli Azure AD kiracısında ayrı bir hizmet sorumlusu olduğunu unutmayın. Kayıtlı uygulamalar Azure portal görünen bir OID 'ye sahiptir, ancak *hizmet sorumlusu* başka bir (farklı) OID 'ye sahiptir.

Uygulama kaydına karşılık gelen hizmet sorumlusu için OID 'yi almak için `az ad sp show` komutunu kullanabilirsiniz. Uygulama KIMLIĞINI parametre olarak belirtin. İşte uygulama KIMLIĞI = 18218b12-1895-43e9-ad80-6e8fc1ea88ce olan uygulama kaydına karşılık gelen hizmet sorumlusu için OID 'yi edinme örneği. Azure CLı 'de aşağıdaki komutu çalıştırın:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID görüntülenir.

Hizmet sorumlusu için doğru OID 'ye sahip olduğunuzda, OID 'yi eklemek ve OID için uygun izinleri atamak için Depolama Gezgini **erişimi yönet** sayfasına gidin. **Kaydet**’i seçtiğinizden emin olun.

### <a name="can-i-set-the-acl-of-a-container"></a>Kapsayıcının ACL 'sini ayarlayabilir miyim?

Hayır. Kapsayıcıda ACL yok. Ancak, kapsayıcının kök dizininin ACL 'sini ayarlayabilirsiniz. Her kapsayıcının bir kök dizini vardır ve kapsayıcı ile aynı adı paylaşır. Örneğin, kapsayıcı adlandırılmışsa `my-container` kök dizin olarak adlandırılır `myContainer/` . 

Azure depolama REST API, [set CONTAINER ACL](/rest/api/storageservices/set-container-acl)adlı bir işlem içeriyor, ancak bu Işlem kapsayıcının ACL 'sini veya bir kapsayıcının kök dizinini ayarlamak için kullanılamaz. Bunun yerine, bir kapsayıcıdaki Blobların [herkese açık](anonymous-read-access-configure.md)bir şekilde erişilebildiğini göstermek için bu işlem kullanılır. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>POSIX erişim denetimi modeli hakkında daha fazla bilgiyi nereden bulabilirim?

* [Linux üzerinde POSIX Erişim Denetim Listeleri](https://www.linux.com/news/posix-acls-linux)
* [Bir izin Kılavuzu](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX SSS](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu üzerinde POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Linux üzerinde erişim denetim listelerini kullanan ACL](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Data Lake Storage 2. 'de erişim denetimi modeli](data-lake-storage-access-control-model.md)
