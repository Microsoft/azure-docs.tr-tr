---
title: Hizmet hesaplarına Active Directory giriş | Azure Active Directory
description: Active Directory ' deki hizmet hesabı türlerine giriş ve bunların güvenliğini sağlama.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645625"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Active Directory hizmet hesaplarına giriş

Bir hizmetin, yerel ve ağ kaynakları için erişim haklarını belirleyen bir birincil güvenlik kimliği vardır. Bir Microsoft Win32 hizmetinin güvenlik bağlamı hizmeti başlatmak için kullanılan hizmet hesabına göre belirlenir. Bir hizmet hesabı şu şekilde kullanılır:
* bir hizmeti tanımlamak ve doğrulamak
* hizmet başarıyla başlatıldı
* kod veya uygulamaya erişin veya yürütün
* bir işlem başlatın. 

## <a name="types-of-on-premises-service-accounts"></a>Şirket içi hizmet hesabı türleri

Kullanım çalışmanıza göre, bir hizmeti çalıştırmak için yönetilen hizmet hesabı (MSA), bilgisayar hesabı veya Kullanıcı hesabı kullanabilirsiniz. Yönetilen bir hizmet hesabı kullandıklarını onaylamak için hizmetlerin test edilmiş olması gerekir. Mümkünse, bir tane kullanmanız gerekir.

### <a name="group-msa-accounts"></a>MSA hesaplarını Gruplandır

Şirket içi ortamınızda çalışan hizmetler için mümkün olduğunda [Grup yönetilen hizmet hesaplarını](service-accounts-group-managed.md) (gMSAs) kullanın. gMSAs, bir sunucu grubunda çalışan bir hizmet için veya bir ağ yükü dengeleyicinin arkasında tek bir kimlik çözümü sağlar. Tek bir sunucu üzerinde çalışan bir hizmet için de kullanılabilir. [gMSAs, karşılanması gereken belirli gereksinimlere sahiptir](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Tek başına MSA hesapları

GMSA 'yı kullanamıyoruz, [tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md)(sMSA) kullanın. sMSAs, en az Windows Server 2008R2 gerektirir. GMSAs 'ın aksine sMSAs yalnızca bir sunucuda çalışır. Bu sunucu üzerinde birden fazla hizmet için kullanılabilirler.

### <a name="computer-account"></a>Bilgisayar hesabı

MSA kullanamıyoruz, bir [bilgisayar hesabı](service-accounts-computer.md)kullanmayı araştırın. LocalSystem hesabı, yerel bilgisayarda kapsamlı ayrıcalıklara sahip olan ve ağ üzerinde bilgisayar kimliği gibi davranan önceden tanımlanmış bir yerel hesaptır.   
<domain_name>biçimindeki bilgisayar hesabının kimlik bilgilerini kullanarak LocalSystem hesabı erişim ağı kaynağı olarak çalışan hizmetler \<computer_name> .

NT AUTHORITY\SYSTEM, LocalSystem hesabı için önceden tanımlanmış addır. Hizmet başlatmak ve bu hizmet için güvenlik bağlamını sağlamak üzere kullanılabilir.

> [!NOTE]
> Bir bilgisayar hesabı kullanıldığında, bilgisayardaki hangi hizmetin bu hesabı kullandığını söyleyebilir ve bu nedenle hangi hizmetin değişiklik yapmakta olduğunu denetlemez. 

### <a name="user-account"></a>Kullanıcı hesabı

MSA kullanamıyoruz, bir [Kullanıcı hesabı](service-accounts-user-on-premises.md)kullanmayı araştırın. Kullanıcı hesapları, bir etki alanı kullanıcı hesabı veya yerel bir kullanıcı hesabı olabilir.

Bir etki alanı kullanıcı hesabı, hizmetin Windows ve Microsoft Active Directory Domain Services hizmet güvenliği özelliklerinden tam olarak yararlanmasını sağlar. Hizmete yerel ve ağ erişimi hesaba verilmeyecektir. Ayrıca, hesabın üye olduğu tüm gruplar için izinleri de olur. Etki alanı hizmet hesapları Kerberos karşılıklı kimlik doğrulamasını destekler.

Yerel bir kullanıcı hesabı (ad biçimi: ".\UserName") yalnızca ana bilgisayarın SAM veritabanında bulunur; Active Directory Domain Services bir kullanıcı nesnesi yoktur. Etki alanı tarafından yerel bir hesabın kimliği doğrulanamıyor. Bu nedenle, yerel bir kullanıcı hesabının güvenlik bağlamında çalışan bir hizmetin ağ kaynaklarına erişimi yoktur (anonim kullanıcı hariç). Yerel kullanıcı bağlamında çalışan hizmetler, hizmetinin istemcileri tarafından kimlik doğrulaması yaptığı Kerberos karşılıklı kimlik doğrulamasını destekleyemez. Bu nedenlerden dolayı yerel kullanıcı hesapları genellikle dizin altyapılı hizmetler için uygun değildir.

> [!IMPORTANT]
> Ayrıcalıklı Grup üyeliği güvenlik riski oluşturabilecek izinlere sahip olduğundan, hizmet hesapları ayrıcalıklı grupların üyesi olmamalıdır. Her hizmetin denetim ve güvenlik amaçları için kendi hizmet hesabına sahip olması gerekir.

## <a name="choose-the-right-type-of-service-account"></a>Doğru hizmet hesabı türünü seçin


| Ölçütler| gMSA| SMSA 'yı| Bilgisayar hesabı| Kullanıcı hesabı |
| - | - | - | - | - |
| Uygulama tek bir sunucuda çalışır| Evet| Evet. Mümkünse bir gMSA kullan| Evet. Mümkünse MSA kullanın| Evet. Mümkünse MSA kullanın. |
| Uygulama birden çok sunucuda çalışır| Yes| Hayır| Hayır. Hesap sunucuya bağlı| Evet. Mümkünse MSA kullanın. |
| Uygulama yük dengeleyiciler arkasında çalışıyor| Yes| Hayır| Hayır| Evet. Yalnızca gMSA 'Yı kullanamıyoruz kullanın |
| Uygulama Windows Server 2008 R2 üzerinde çalışır| Hayır| Evet| Evet. Mümkünse MSA kullanın.| Evet. Mümkünse MSA kullanın. |
| Windows Server 2012 üzerinde çalışır| Evet| Evet. Mümkünse gMSA kullan| Evet. Mümkünse MSA kullanın| Evet. Mümkünse MSA kullanın. |
| Hizmet hesabını tek sunucuya kısıtlama gereksinimi| Hayır| Evet| Evet. Mümkünse sMSA 'Yı kullan| Hayır. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Araştırmak için sunucu günlüklerini ve PowerShell 'i kullanma

Sunucu günlüklerini, bir uygulamanın hangi sunuculara ve kaç tane sunucuya çalıştığını tespit etmek için kullanabilirsiniz.

Ağınızdaki tüm sunucuların Windows Server sürümünün bir listesini almak için aşağıdaki PowerShell komutunu çalıştırabilirsiniz. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Şirket içi hizmet hesaplarını bulun

"Svc" gibi bir ön ek eklemenizi öneririz. Hizmet hesabı olarak kullanılan tüm hesaplara. Bu adlandırma kuralı, bulmayı ve yönetmeyi kolaylaştırır. Ayrıca, hizmet hesabı ve hizmet hesabı sahibi için bir açıklama özniteliği kullanımını göz önünde bulundurun. Bu bir ekip diğer adı veya güvenlik ekibi sahibi olabilir.

Şirket içi hizmet hesaplarını bulma, güvenliğini sağlamaya yönelik bir anahtardır. MSA olmayan hesaplar için de zor olabilir. Önemli şirket içi kaynaklarınıza erişimi olan tüm hesapların incelemesinin yanı sıra hangi bilgisayarın veya Kullanıcı hesaplarının hizmet hesabı olarak davrandığına karar öneririz. Hesapları bulmak için aşağıdaki yöntemleri de kullanabilirsiniz.

* Her hesap türü için makaleler, bu hesap türünü bulmaya yönelik ayrıntılı adımlara sahiptir. Bu makalelerin bağlantıları için, bu makalenin sonraki adımlar bölümüne bakın.

## <a name="document-service-accounts"></a>Belge hizmeti hesapları

Şirket içi ortamınızda hizmet hesaplarını bulduktan sonra, her hesap hakkında aşağıdaki bilgileri belgeleyin. 

* Sahibi. Hesabı korumak için sorumlu hesap.

* Amaç. Hesabın gösterdiği uygulama veya başka bir amaç. 

* İzin kapsamları. Hangi izinleri vardır ve bu haklara sahip olmalıdır? Herhangi bir grup üyesi ise ne olur?

* Risk profili. Bu hesap tehlikeye atılırsa işletmenizin riski nedir? Yüksek risk varsa, bir MSA kullanın.

* Beklenen ömür ve dönemsel kanıtlama. Bu hesabı ne kadar süre yaşlıyor? Gereksinim incelemesi ve devam etmek için ne sıklıkla sahip olmanız gerekir?

* Parola güvenliği. Kullanıcı ve yerel bilgisayar hesapları için parolanın depolandığı yer. Parolaların güvenli tutulduğundan ve erişimi olan belge bulunduğundan emin olun. Depolanan parolaların güvenliğini sağlamak için [Privileged Identity Management](../privileged-identity-management/pim-configure.md) kullanmayı düşünün. 

  

## <a name="next-steps"></a>Sonraki adımlar

Hizmet hesaplarını güvenli hale getirmek için aşağıdaki makalelere bakın

* [Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)

* [Güvenli grup yönetilen hizmet hesapları](service-accounts-group-managed.md)

* [Güvenli tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md)

* [Güvenli bilgisayar hesapları](service-accounts-computer.md)

* [Güvenli Kullanıcı hesapları](service-accounts-user-on-premises.md)

* [Şirket içi hizmet hesaplarını yönetir](service-accounts-govern-on-premises.md)

