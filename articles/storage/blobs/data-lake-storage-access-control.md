---
title: Azure Data Lake Storage 2. | erişim denetimine genel bakış | Microsoft Docs
description: Erişim denetiminin Azure Data Lake Storage 2. nasıl çalıştığını anlama
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: a35cf935d990dbb61f440d2592d59d21f33a2ae8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037236"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. erişim denetimi

Azure Data Lake Storage 2. hem Azure rol tabanlı erişim denetimi (RBAC) hem de POSIX benzeri erişim denetim listelerini (ACL 'Ler) destekleyen bir erişim denetimi modeli uygular. Bu makalede Data Lake Storage 2. için erişim denetimi modelinin temelleri özetlenmektedir.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

RBAC, *güvenlik sorumlularına*izin kümelerini etkili bir şekilde uygulamak için rol atamaları kullanır. *Güvenlik sorumlusu* , Azure kaynaklarına erişim isteyen Azure ACTIVE DIRECTORY (ad) ' de tanımlanan bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliği temsil eden bir nesnedir.

Genellikle, bu Azure kaynakları en üst düzey kaynaklarla sınırlıdır (örneğin, Azure depolama hesapları). Azure depolama söz konusu olduğunda ve sonuç olarak Azure Data Lake Storage 2., bu mekanizma kapsayıcı (dosya sistemi) kaynağına genişletilir.

Depolama hesabınızın kapsamındaki güvenlik sorumlularına roller atamayı öğrenmek için bkz. [Azure Portal Azure Blob 'a erişim verme ve VERILERI RBAC ile sıraya](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)alma.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Rol atamalarının dosya ve dizin düzeyinde erişim denetim listelerindeki etkileri

RBAC rol atamalarının kullanılması, erişim izinlerini denetlemek için güçlü bir mekanizmadır. Bu, ACL 'Lerle ilgili oldukça ayrıntılı bir mekanizmadır. RBAC için en küçük ayrıntı düzeyi kapsayıcı düzeyindedir ve bu, ACL 'Lere göre daha yüksek bir önceliğe göre değerlendirilir. Bu nedenle, bir kapsayıcı kapsamındaki bir güvenlik sorumlusuna bir rol atarsanız, bu güvenlik sorumlusu ACL atamalarından bağımsız olarak o kapsayıcıdaki tüm dizinler ve dosyalar için bu rolle ilişkili yetkilendirme düzeyine sahiptir.

Bir güvenlik sorumlusu [yerleşik bir rol](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)aracılığıyla veya özel bir rol aracılığıyla RBAC veri izinleri verildiğinde, bu izinler bir isteğin yetkilendirilmesine göre önce değerlendirilir. İstenen işlem güvenlik sorumlusunun RBAC atamaları tarafından yetkilendirildiyse, yetkilendirme anında çözülür ve ek ACL denetimleri gerçekleştirilmez. Alternatif olarak, güvenlik sorumlusunun bir RBAC ataması yoksa veya isteğin işlemi atanan izinle eşleşmezse, güvenlik sorumlusunun istenen işlemi gerçekleştirme yetkisine sahip olup olmadığını belirlemesi için ACL denetimleri gerçekleştirilir.

> [!NOTE]
> Güvenlik sorumlusu, Depolama Blobu veri sahibi yerleşik rol ataması ' na atanmışsa, güvenlik sorumlusu bir *Süper Kullanıcı* olarak değerlendirilir ve bir dizin ya da dosyanın sahibini ayarlamanın yanı sıra, sahip olmadıkları dizinler ve dosyalar Için ACL 'ler dahil olmak üzere tüm değiştirici işlemlere tam erişim verilir. Süper Kullanıcı erişimi, bir kaynağın sahibini değiştirmek için tek yetkilendirilmiştir.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Paylaşılan anahtar ve paylaşılan erişim Imzası (SAS) kimlik doğrulaması

Azure Data Lake Storage 2., kimlik doğrulaması için paylaşılan anahtar ve SAS yöntemlerini destekler. Bu kimlik doğrulama yöntemlerinin bir özelliği, çağıran ile hiçbir kimliğin ilişkilendirilmediği ve bu nedenle güvenlik sorumlusu izin tabanlı yetkilendirmenin gerçekleştirilemesinden oluşur.

Paylaşılan anahtar söz konusu olduğunda, çağıran ' Süper Kullanıcı ' erişimini etkin bir şekilde, yani tüm kaynaklardaki tüm işlemlere tam erişim sağlar ve bu da sahip ve ACL 'Leri değiştirme de dahil olmak üzere tüm kaynaklardaki tüm işlemlere

SAS belirteçleri, belirtecin bir parçası olarak izin verilen izinleri içerir. SAS belirtecine dahil edilen izinler, tüm yetkilendirme kararlarında etkili bir şekilde uygulanır, ancak başka bir ACL denetimi gerçekleştirilmez.

## <a name="access-control-lists-on-files-and-directories"></a>Dosya ve dizinlerdeki erişim denetim listeleri

Güvenlik sorumlusunu, dosyalar ve dizinler için bir erişim düzeyiyle ilişkilendirebilirsiniz. Bu ilişkilendirmeler bir *erişim denetim listesi (ACL)* içinde yakalanır. Depolama hesabınızdaki her dosya ve dizinin bir erişim denetim listesi vardır.

Depolama hesabı düzeyinde bir güvenlik sorumlusuna rol atadıysanız, bu güvenlik sorumlusunu belirli dosyalara ve dizinlere yükseltilmiş olarak sağlamak için erişim denetimi listelerini kullanabilirsiniz.

Rol ataması tarafından verilen düzeyinden daha düşük bir erişim düzeyi sağlamak için erişim denetimi listelerini kullanamazsınız. Örneğin, [Depolama Blobu veri katılımcısı](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) rolünü bir güvenlik sorumlusuna atarsanız, bu güvenlik sorumlusunun bir dizine yazmasını engellemek için erişim denetimi listelerini kullanamazsınız.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Erişim denetim listelerini kullanarak dosya ve Dizin düzeyi izinleri ayarlama

Dosya ve dizin düzeyindeki izinleri ayarlamak için aşağıdaki makalelerden birine bakın:

|Bu aracı kullanmak istiyorsanız:    |Şu makaleye bakın:    |
|--------|-----------|
|Azure Storage Gezgini    |[Azure Data Lake Storage 2. ile Azure Depolama Gezgini kullanarak dosya ve Dizin düzeyi izinleri ayarlama](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|REST API    |[Yol-Güncelleştir](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Güvenlik sorumlusu bir *hizmet* sorumlusu ise, ilgili uygulama KAYDıNıN nesne kimliğini değil, hizmet sorumlusunun nesne kimliğini kullanmak önemlidir. Hizmet sorumlusunun nesne KIMLIĞINI almak için Azure CLı 'yı açın ve ardından şu komutu kullanın: `az ad sp show --id <Your App ID> --query objectId`. `<Your App ID>` yer tutucusunu, uygulama kaydlarınızın uygulama KIMLIĞIYLE değiştirdiğinizden emin olun.

### <a name="types-of-access-control-lists"></a>Erişim denetim listelerinin türleri

İki tür erişim denetim listesi vardır: *erişim ACL 'leri* ve *varsayılan ACL 'ler*.

ACL denetim erişimi bir nesneye erişin. Dosya ve dizinlerin her ikisi de erişim ACL 'Lerine sahiptir.

Varsayılan ACL 'ler, bu dizin altında oluşturulan tüm alt öğeler için erişim ACL 'Lerini tanımlayan bir dizinle ilişkili ACL 'lerin şablonlarıdır. Dosyaların varsayılan ACL 'Leri yok.

Erişim ACL 'Leri ve varsayılan ACL 'Ler aynı yapıya sahiptir.

> [!NOTE]
> Bir üst öğenin varsayılan ACL 'sini değiştirmek, erişim ACL 'sini veya zaten var olan alt öğelerin ACL 'sini etkilemez.

### <a name="levels-of-permission"></a>İzin düzeyleri

Bir kapsayıcı nesnesindeki izinler **okuma**, **yazma**ve **yürütme**, aşağıdaki tabloda gösterildiği gibi dosyalar ve dizinler üzerinde de kullanılabilir:

|            |    Dosya     |   Dizin |
|------------|-------------|----------|
| **Okuma (R)** | Bir dosyanın içeriğini okuyabilir | Dizinin içeriğini listelemek için **okuma** ve **yürütme** gerektirir |
| **Yazma (W)** | Bir dosyaya yazabilir veya ekleyebilir | Bir dizinde alt öğeler oluşturmak için **yazma** ve **yürütme** gerektirir |
| **Yürütme (X)** | Data Lake Storage 2. bağlamında hiçbir şey anlamına gelmez | Bir dizinin alt öğelerinin çapraz geçişini yapmak için gereklidir |

> [!NOTE]
> Yalnızca ACL 'Leri kullanarak (RBAC olmadan) izin veriyorsanız, bir güvenlik sorumlusu için bir dosyaya okuma veya yazma erişimi vermek istiyorsanız, kapsayıcıya güvenlik sorumlusu **yürütme** izinleri vermeniz ve dosyaya yol açabilecek klasörler hiyerarşisindeki her bir klasöre sahip olmanız gerekir.

#### <a name="short-forms-for-permissions"></a>İzinlerin kısaltmaları

**RWX**, **Okuma + Yazma + Yürütme** için kullanılır. **Okuma=4**, **Yazma=2** ve **Yürütme=1** olup toplamları izinleri temsil eden daha da kısaltılmış bir sayısal biçim mevcuttur. Bazı örnekler aşağıda verilmiştir.

| Sayısal biçim | Kısa biçim |      Anlamı     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Okuma + Yazma + Yürütme |
| 5            | `R-X`        | Okuma + Yürütme         |
| 4            | `R--`        | Okuma                   |
| 0            | `---`        | İzin yok         |

#### <a name="permissions-inheritance"></a>İzin devralma

Data Lake Storage 2. tarafından kullanılan POSIX stili modelde bir öğe için izinler öğenin kendisinde depolanır. Diğer bir deyişle, izinler alt öğe oluşturulduktan sonra ayarlandıysa, öğenin izinleri üst öğeden devralınamaz. İzinler yalnızca, alt öğeler oluşturulmadan önce üst öğelerde varsayılan izinler ayarlandıysa devralınır.

### <a name="common-scenarios-related-to-permissions"></a>İzinlerle ilgili yaygın senaryolar

Aşağıdaki tabloda, bir depolama hesabında belirli işlemleri gerçekleştirmek için hangi izinlerin gerekli olduğunu anlamanıza yardımcı olacak bazı yaygın senaryolar listelenmektedir.

|    İşlem             |    /    | 'Daki | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Data. txt dosyasını oku            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Data. txt dosyasına Ekle       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data. txt dosyasını Sil          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data. txt oluşturma          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Listele                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Liste/Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Liste/Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Önceki iki koşul doğru olduğu sürece, dosyanın üzerine yazma izinleri bunu silmek için gerekli değildir.

### <a name="users-and-identities"></a>Kullanıcılar ve kimlikler

Her dosya ve dizin, bu kimlikler için farklı izinlere sahiptir:

- Sahip olan kullanıcı
- Sahip olan grup
- Adlandırılmış kullanıcılar
- Adlandırılmış gruplar
- Adlandırılmış hizmet sorumluları
- Adlandırılmış Yönetilen kimlikler
- Diğer tüm kullanıcılar

Kullanıcıların ve grupların kimlikleri, Azure Active Directory (Azure AD) kimlikleridir. Bu nedenle, aksi belirtilmediği takdirde, Data Lake Storage 2. bağlamında bir *Kullanıcı*Azure AD kullanıcısına, hizmet sorumlusuna, yönetilen kimliğe veya güvenlik grubuna başvurabilir.

#### <a name="the-owning-user"></a>Sahip olan kullanıcı

Öğeyi oluşturan kullanıcı otomatik olarak öğenin sahibi olan kullanıcıdır. Sahip olan kullanıcı şunları yapabilir:

* Sahip olunan bir dosyanın izinlerini değiştirme.
* Sahip olan kullanıcı aynı zamanda hedef grubun bir üyesi oldukça, sahip olunan bir dosyanın sahibi olan grubunu değiştirme.

> [!NOTE]
> Sahip olan Kullanıcı, bir dosyanın veya dizinin sahibi olan *kullanıcısını değiştiremiyor.* Yalnızca süper kullanıcılar bir dosyanın veya dizinin sahibi olan kullanıcısını değiştirebilir.

#### <a name="the-owning-group"></a>Sahip olan grup

POSIX ACL 'lerinde, her Kullanıcı bir *birincil grupla*ilişkilendirilir. Örneğin, "Gamze" kullanıcısı "Finans" grubuna ait gelebilir. Gamze birden çok gruba ait olabilir, ancak bir grup her zaman birincil grubu olarak atanır. POSIX’te Gamze bir dosya oluşturduğunda o dosyanın sahibi olan grup birincil grubu olarak ayarlanır (bu örnekte "finans" grubudur). Aksi takdirde sahip olan grup, diğer kullanıcılar/gruplar için atanan izinlere benzer şekilde davranır.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Yeni bir dosya veya dizin için sahip olan grup atanıyor

* **Durum 1**: kök dizin "/". Bu dizin Data Lake Storage 2. bir kapsayıcı oluşturulduğunda oluşturulur. Bu durumda sahip olan Grup, OAuth kullanılarak yapıldıysa kapsayıcıyı oluşturan kullanıcıya ayarlanır. Kapsayıcı paylaşılan anahtar, bir hesap SAS veya hizmet SAS kullanılarak oluşturulduysa, sahip ve sahip grubu **$superuser**olarak ayarlanır.
* **Durum 2** (diğer her durum): yeni bir öğe oluşturulduğunda, sahip olan grup üst dizinden kopyalanır.

##### <a name="changing-the-owning-group"></a>Sahip olan grubu değiştirme

Sahip olan grup aşağıdakiler tarafından değiştirilebilir:
* Herhangi bir süper kullanıcı.
* Sahip olan kullanıcı aynı zamanda hedef grubun üyesi ise sahip olan kullanıcı.

> [!NOTE]
> Sahip olan Grup, bir dosyanın veya dizinin ACL 'Lerini değiştiremiyor.  Sahip olan Grup, yukarıdaki **durum 1** olan kök dizin durumunda hesabı oluşturan kullanıcıya ayarlandığı sürece, sahip olan grup aracılığıyla izin sağlamak için tek bir kullanıcı hesabı geçerli değildir. Uygunsa bu izni geçerli bir kullanıcı hesabına atayabilirsiniz.

### <a name="access-check-algorithm"></a>Erişim denetimi algoritması

Aşağıdaki sözde kod, depolama hesapları için erişim denetimi algoritmasını temsil eder.

```
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
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Maskesi

Erişim denetimi algoritmasında gösterildiği gibi, maske adlandırılmış kullanıcılar, sahip olan grup ve adlandırılmış gruplar için erişimi sınırlandırır.  

> [!NOTE]
> Yeni bir Data Lake Storage 2. kapsayıcısı için, kök dizinin ("/") erişim ACL 'SI için maske, dizinler için 750, dosyalar için 640 olarak varsayılan olarak. Dosyalar yalnızca mağaza sistemindeki dosyalara ait olduğundan, X bitini almaz.
>
> Maske, arama başına temelinde belirtilebilir. Bu, kümeler gibi farklı tüketim sistemlerinin dosya işlemleri için farklı etkin maskelerle çalışmasına izin verir. Belirli bir istekte bir maske belirtilmişse, varsayılan maskeyi tamamen geçersiz kılar.

#### <a name="the-sticky-bit"></a>Yapışkan bit

Yapışkan bit, POSIX kapsayıcısının daha gelişmiş bir özelliğidir. Data Lake Storage 2. bağlamında, yapışkan bitin gerekli olacağı pek olası bir olasılıktır. Özet ' te, bir dizinde yapışkan bit etkinse, alt öğe yalnızca alt öğenin sahibi olan kullanıcı tarafından silinebilir veya yeniden adlandırılabilir.

Yapışkan bit Azure portal gösterilmez.

### <a name="default-permissions-on-new-files-and-directories"></a>Yeni dosya ve dizinlerde varsayılan izinler

Mevcut bir dizin altında yeni bir dosya veya dizin oluşturulduğunda, üst dizindeki varsayılan ACL şunları belirler:

- Alt dizinin varsayılan ACL 'si ve erişim ACL 'SI.
- Alt dosyanın erişim ACL 'SI (dosyaları varsayılan ACL 'ye sahip değildir).

#### <a name="umask"></a>umask

Bir dosya veya dizin oluştururken, varsayılan ACL 'Lerin alt öğede nasıl ayarlandığını değiştirmek için umask kullanılır. umask, **sahip olan Kullanıcı**, **sahip olan grup**ve **diğer**için bir RWX değeri içeren üst dizinlerdeki 9 bitlik bir değerdir.

Umask, 007 olarak ayarlanan sabit bir değer Azure Data Lake Storage 2. Bu değer şunu yapar:

| umask bileşeni     | Sayısal biçim | Kısa biçim | Anlamı |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Sahip olan kullanıcı için, üst öğenin varsayılan ACL 'sini alt öğenin erişim ACL 'sine kopyalayın | 
| umask.owning_group  |    0         |   `---`      | Sahip olan grup için üst öğenin varsayılan ACL 'sini alt öğenin erişim ACL 'sine kopyalayın | 
| umask.Other         |    7         |   `RWX`      | Diğer bir deyişle, alt öğenin erişim ACL 'sindeki tüm izinleri kaldırın |

Azure Data Lake Storage 2. tarafından kullanılan umask değeri, varsayılan ACL 'nin gösterdiği ne olursa olsun, **diğer** için varsayılan olarak yeni alt klasörlerde hiçbir şekilde iletilmediği anlamına gelir. 

Aşağıdaki sözde kod umask bir alt öğesi ACL'leri oluştururken nasıl uygulanacağını gösterir.

```
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Data Lake Storage 2. ACL 'Lerle ilgili genel sorular

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL desteğini etkinleştirmem gerekiyor mu?

Hayır. Hiyerarşik ad alanı (HNS) özelliği açık olduğu sürece bir depolama hesabı için ACL 'Ler aracılığıyla erişim denetimi etkinleştirilir.

HNS kapalıysa, Azure RBAC yetkilendirme kuralları yine de geçerlidir.

### <a name="what-is-the-best-way-to-apply-acls"></a>ACL 'Leri uygulamak için en iyi yol nedir?

Her zaman ACL 'lerdeki atanan sorumlu olarak Azure AD güvenlik gruplarını kullanın. Bireysel kullanıcıları veya hizmet sorumlularını doğrudan atamaya yönelik fırsatı yeniden ölçeklendirin. Bu yapının kullanılması, ACL 'Leri tüm dizin yapısına yeniden uygulama gereksinimi olmadan Kullanıcı veya hizmet sorumluları eklemenize ve kaldırmanıza olanak tanır. ) Bunun yerine, yalnızca uygun Azure AD güvenlik grubundan eklemeniz veya kaldırmanız gerekir. ACL 'Lerin devralındığını aklınızda bulundurun ve bu nedenle, ACL 'Leri yeniden uygulamak için her dosya ve alt dizinde ACL 'nin güncelleştirilmesi gerekir. 

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

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Bir dosyanın sahibiyim, ancak gereken RWX izinlerine sahip değilim. Ne yapmalıyım?

Sahip olan kullanıcı kendisine gerekli olan her türlü RWX iznini vermek için dosyanın izinlerini değiştirebilir.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Neden bazen ACL 'lerde GUID 'Leri görüyorum?

Giriş bir kullanıcıyı temsil ediyorsa ve bu kullanıcı artık Azure AD 'de mevcut değilse bir GUID gösterilir. Bu genellikle, kullanıcı şirketten ayrıldığında veya Azure AD’de kullanıcının hesabı silindiğinde gerçekleşir. Ayrıca, hizmet sorumluları ve güvenlik grupları bunları tanımlamak için bir Kullanıcı asıl adına (UPN) sahip değildir ve bu nedenle OID özniteliği (bir GUID) tarafından temsil edilir.

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Nasıl yaparım? bir hizmet sorumlusu için ACL 'Leri doğru şekilde ayarladı musunuz?

Hizmet sorumluları için ACL 'Leri tanımladığınızda, oluşturduğunuz uygulama kaydı için *hizmet sorumlusunun* nesne KIMLIĞI (OID) kullanılması önemlidir. Kayıtlı uygulamaların, belirli Azure AD kiracısında ayrı bir hizmet sorumlusu olduğunu unutmayın. Kayıtlı uygulamalar Azure portal görünen bir OID 'ye sahiptir, ancak *hizmet sorumlusu* başka bir (farklı) OID 'ye sahiptir.

Uygulama kaydına karşılık gelen hizmet sorumlusu için OID 'yi almak üzere `az ad sp show` komutunu kullanabilirsiniz. Uygulama KIMLIĞINI parametre olarak belirtin. İşte uygulama KIMLIĞI = 18218b12-1895-43e9-ad80-6e8fc1ea88ce olan uygulama kaydına karşılık gelen hizmet sorumlusu için OID 'yi edinme örneği. Azure CLı 'de aşağıdaki komutu çalıştırın:

```
$ az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>
```

Hizmet sorumlusu için doğru OID 'ye sahip olduğunuzda, OID 'yi eklemek ve OID için uygun izinleri atamak için Depolama Gezgini **erişimi yönet** sayfasına gidin. **Kaydet**' i seçtiğinizden emin olun.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage 2. ACL 'Leri devralmayı destekliyor mu?

Azure RBAC atamaları devralınır. Atamalar abonelik, kaynak grubu ve depolama hesabı kaynaklarından, kapsayıcı kaynağına doğru akar.

ACL 'Ler aktarılmaz. Ancak, alt dizinler ve üst dizin altında oluşturulan dosyalar için ACL 'Ler ayarlamak üzere varsayılan ACL 'Ler kullanılabilir. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>POSIX erişim denetimi modeli hakkında daha fazla bilgiyi nereden bulabilirim?

* [Linux üzerinde POSIX Erişim Denetim Listeleri](https://www.linux.com/news/posix-acls-linux)
* [HDFS izin kılavuzu](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX SSS](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu üzerinde POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Linux üzerinde erişim denetim listelerini kullanan ACL](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Data Lake Storage 2. genel bakış](../blobs/data-lake-storage-introduction.md)
