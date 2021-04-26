---
title: 'Azure AD Connect: önceki bir sürümden yükseltme | Microsoft Docs'
description: Yerinde yükseltme ve esnek geçiş dahil olmak üzere Azure Active Directory Connect en son sürümüne yükseltmek için farklı yöntemler açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36b7fce2e2ccb6f331e42e8052ef4fb75d35e831
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729999"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Önceki bir sürümden en son sürüme yükseltme
Bu konuda, Azure Active Directory (Azure AD) Connect yüklemenizi en son sürüme yükseltmek için kullanabileceğiniz farklı yöntemler açıklanmaktadır.  Önemli bir yapılandırma değişikliği yaptığınızda, [esnek geçiş](#swing-migration) bölümündeki adımları da kullanabilirsiniz.

>[!NOTE]
> Sunucularınızın en son Azure AD Connect sürümleriyle güncel tutmanız önemlidir. AADConnect 'e sürekli olarak yükseltmeler yapıyoruz ve bu yükseltmeler güvenlik sorunları ve hataları ve bakım, performans ve ölçeklenebilirlik iyileştirmeleri için düzeltmeler içerir. En son sürümün ne olduğunu görmek ve sürümler arasında yapılan değişikliklerin ne olduğunu öğrenmek için lütfen [sürüm sürümü geçmişine](./reference-connect-version-history.md) başvurun

>[!NOTE]
> Şu anda herhangi bir Azure AD Connect sürümünün geçerli sürüme yükseltilmesi desteklenmektedir. DirSync veya ADSync 'in yerinde yükseltmeleri desteklenmez ve bir esnek geçiş gereklidir.  DirSync 'ten yükseltmek istiyorsanız, bkz. [Azure AD eşitleme aracından yükseltme aracı (DirSync)](how-to-dirsync-upgrade-get-started.md) veya [esnek geçiş](#swing-migration) bölümü.  </br>Uygulamada, son derece eski sürümlerde bulunan müşteriler, Azure AD Connect doğrudan ilişkili olan sorunlarla karşılaşabilir. Birkaç yıl içinde üretimde olan sunucular, genellikle bu dosyalara uygulanan bazı düzeltme eklerine sahip olur ve bunların hepsi için de hesaba katılmaz.  Genellikle, 12-18 ay içinde yükseltmeyen müşteriler, en klasik ve en az riskli seçenektir. bunun yerine bir esnek yükseltme yapmayı düşünmelidir.

DirSync 'ten yükseltmek istiyorsanız, bunun yerine [Azure AD eşitleme aracından (DirSync) yükseltme](how-to-dirsync-upgrade-get-started.md) konusuna bakın.

Azure AD Connect yükseltmek için kullanabileceğiniz birkaç farklı strateji vardır.

| Yöntem | Açıklama |
| --- | --- |
| [Otomatik yükseltme](how-to-connect-install-automatic-upgrade.md) |Bu, Hızlı yüklemeye sahip müşteriler için en kolay yöntemdir. |
| [Yerinde yükseltme](#in-place-upgrade) |Tek bir sunucunuz varsa, aynı sunucuda yüklemeyi yerinde yükseltebilirsiniz. |
| [Swing geçişi](#swing-migration) |İki sunucu ile, yeni sürüm veya yapılandırmaya sahip sunuculardan birini hazırlayabilir ve hazırsanız etkin sunucuyu değiştirebilirsiniz. |

İzin bilgileri için, [yükseltme için gereken izinlere](reference-connect-accounts-permissions.md#upgrade)bakın.

> [!NOTE]
> Azure AD 'de yapılan değişiklikleri eşitlemeye başlamak için yeni Azure AD Connect sunucunuzu etkinleştirdikten sonra, DirSync veya Azure AD Eşitleme kullanmaya geri alma yapmanız gerekir. DirSync ve Azure AD Eşitleme dahil olmak üzere Azure AD Connect eski istemcilere düşürme, desteklenmez ve Azure AD 'de veri kaybı gibi sorunlara yol açabilir.

## <a name="in-place-upgrade"></a>Yerinde yükseltme
Yerinde yükseltme, Azure AD Eşitleme veya Azure AD Connect taşıma için çalışmaktadır. Bu, DirSync 'ten veya Forefront Identity Manager (FIM) + Azure AD Bağlayıcısı ile bir çözüm için çalışmaz.

Bu yöntem, tek bir sunucunuz ve yaklaşık 100.000 nesnesinden daha az olduğunda tercih edilir. Kullanıma hazır eşitleme kurallarında herhangi bir değişiklik varsa, yükseltmeden sonra tam içeri aktarma ve tam eşitleme gerçekleşmelidir. Bu yöntem, yeni yapılandırmanın sistemdeki tüm var olan nesnelere uygulanmasını sağlar. Bu çalıştırma, eşitleme altyapısının kapsamındaki nesne sayısına bağlı olarak birkaç saat sürebilir. Normal Delta eşitleme Zamanlayıcısı (varsayılan olarak her 30 dakikada bir eşitlenir) askıya alınır, ancak parola eşitleme devam eder. Bir hafta sonu sırasında yerinde yükseltmeyi yapmayı düşünebilirsiniz. Yeni Azure AD Connect sürümünde kullanıma hazır yapılandırma değişikliği yoksa, bunun yerine normal bir Delta içeri aktarma/eşitleme başlar.  
![Yerinde yükseltme](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Kullanıma hazır eşitleme kurallarında değişiklik yaptıysanız, bu kurallar yükseltme sırasında varsayılan yapılandırmaya geri ayarlanır. Yapılandırmanızın yükseltmeler arasında tutulduğundan emin olmak için, [varsayılan yapılandırmayı değiştirmek Için en iyi yöntemler](how-to-connect-sync-best-practices-changing-default-configuration.md)bölümünde açıklandıklarından emin olun.

Yerinde yükseltme sırasında, yükseltme tamamlandıktan sonra yürütülmesi gereken belirli eşitleme etkinliklerini (tam Içeri aktarma adımı ve tam eşitleme adımı dahil) gerektiren değişiklikler olabilir. Bu tür etkinlikleri ertelemek için, [yükseltmeden sonra tam eşitlemeyi erteleme](#how-to-defer-full-synchronization-after-upgrade)bölümüne bakın.

Standart olmayan bağlayıcıyla Azure AD Connect kullanıyorsanız (örneğin, genel LDAP Bağlayıcısı ve genel SQL Bağlayıcısı), yerinde yükseltmeden sonra [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) ilgili bağlayıcı yapılandırmasını yenilemeniz gerekir. Bağlayıcı yapılandırmasını yenileme hakkında daha fazla bilgi için, bkz. makale bölümü [bağlayıcı sürümü yayın geçmişi-sorun giderme](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Yapılandırmayı yenilemeyin, içeri ve dışarı aktarma çalıştırma adımları bağlayıcı için doğru çalışmaz. Uygulama olay günlüğünde, *"AAD bağlayıcı yapılandırmasındaki derleme sürümü (&quot; x. x. xxx. x ")," C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll "öğesinin gerçek sürümünden (" x. x. xxx. x ") daha önceki bir* hata iletisiyle karşılaşırsınız.

## <a name="swing-migration"></a>Swing geçişi
Karmaşık bir dağıtımınız veya çok sayıda nesneniz varsa, canlı sistemde yerinde yükseltme yapmak pratik olabilir. Bazı müşteriler için, bu işlem birden çok gün alabilir ve bu süre boyunca hiçbir Delta değişikliği işlenmez. Bu yöntemi Ayrıca, yapılandırmanızda önemli değişiklikler yapmayı planladığınızda ve buluta gönderilmeden önce onları denemek istediğinizde de kullanabilirsiniz.

Bu senaryolar için önerilen yöntem, esnek geçiş kullanmaktır. Tek bir etkin sunucu ve bir hazırlama sunucusu olmak üzere iki sunucu (en az) gerekir. Etkin üretim yüküyle birlikte etkin sunucu (aşağıdaki resimdeki düz mavi satırlarla gösterilir) sorumludur. Hazırlama sunucusu (kesikli mor çizgilerle gösterilir) yeni sürüm veya yapılandırmayla hazırlanır. Tam olarak hazırsanız, bu sunucu etkin hale gelir. Artık eski sürümü veya yapılandırması yüklü olan önceki etkin sunucu, hazırlama sunucusunda yapılır ve yükseltilir.

İki sunucu farklı sürümleri kullanabilir. Örneğin, yetkisini almayı planladığınız etkin sunucu Azure AD Eşitleme kullanabilir ve yeni hazırlama sunucusu Azure AD Connect kullanabilir. Yeni bir yapılandırma geliştirmek için esnek geçiş geçişi kullanıyorsanız, iki sunucu üzerinde aynı sürüme sahip olmak iyi bir fikirdir.  
![Hazırlama sunucusu](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Bazı müşteriler bu senaryo için üç veya dört sunucu bulundurmayı tercih eder. Hazırlama sunucusu yükseltildiğinde, [olağanüstü durum kurtarma](how-to-connect-sync-staging-server.md#disaster-recovery)için bir yedekleme sunucusu yoktur. Üç veya dört sunucu ile, yeni sürüme sahip bir birincil/hazır sunucu kümesi hazırlayabilir, bu da her zaman bir hazırlama sunucusu olmasını sağlar.

Bu adımlar, FIM + Azure AD Bağlayıcısı ile Azure AD Eşitleme veya bir çözümden geçiş yapmak için de çalışır. Bu adımlar DirSync için çalışmaz, ancak DirSync adımları ile aynı esnek geçiş yöntemi (paralel dağıtım olarak da bilinir) [yükseltme Azure Active Directory eşitleme (DirSync)](how-to-dirsync-upgrade-get-started.md)içinde bulunur.

### <a name="use-a-swing-migration-to-upgrade"></a>Yükseltmek için bir esnek geçiş geçişi kullanın
1. Her iki sunucuda Azure AD Connect kullanırsanız ve yalnızca bir yapılandırma değişikliği yapmayı planlıyorsanız, etkin sunucu ve hazırlama sunucunuzun aynı sürümü kullandığınızdan emin olun. Bu, farkları daha sonra karşılaştırmayı kolaylaştırır. Azure AD Eşitleme yükseltme yapıyorsanız, bu sunucuların farklı sürümleri vardır. Azure AD Connect eski bir sürümünden yükseltiyorsanız, aynı sürümü kullanan iki sunucu ile başlamak iyi bir fikirdir, ancak bu gerekli değildir.
2. Özel bir yapılandırma yaptıysanız ve hazırlama sunucunuzda yoksa, [Active Server 'dan hazırlama sunucusuna özel bir yapılandırma taşıma](#move-a-custom-configuration-from-the-active-server-to-the-staging-server)bölümündeki adımları izleyin.
3. Azure AD Connect önceki bir sürümünden yükseltiyorsanız, hazırlama sunucusunu en son sürüme yükseltin. Azure AD Eşitleme taşıyorsanız, hazırlama sunucunuza Azure AD Connect yükleyebilirsiniz.
4. Eşitleme altyapısının hazırlama sunucunuzda tam içeri aktarma ve tam eşitleme çalıştırmasına izin verin.
5. Yeni yapılandırmanın, [bir sunucunun yapılandırmasını doğrulama](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server)bölümünde "Doğrula" altındaki adımları kullanarak beklenmeyen değişikliklere neden olmadığını doğrulayın. Bir şeyler beklenmiyorsa, bunu düzeltin, içeri aktar ve Eşitle ' yi çalıştırın ve adımları izleyerek iyi görünene kadar verileri doğrulayın.
6. Hazırlama sunucusunu etkin sunucu olacak şekilde değiştirin. Bu, [sunucu yapılandırmasını doğrulama](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server)bölümünde "etkin sunucu Değiştir" son adımıdır.
7. Azure AD Connect yükseltiyorsanız, hazırlama modunda olan sunucuyu en son sürüme yükseltin. Verileri ve yapılandırmayı yükseltmeden önce aynı adımları izleyin. Azure AD Eşitleme yükselttiyseniz, artık eski sunucunuzu kapatabilir ve yetkisini alabilirsiniz.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Etkin sunucudan hazırlık sunucusuna özel bir yapılandırma taşıyın
Etkin sunucuda yapılandırma değişiklikleri yaptıysanız, hazırlama sunucusuna aynı değişikliklerin uygulandığından emin olmanız gerekir. Bu taşıma konusunda yardımcı olmak için [Azure AD Connect yapılandırma belgegir](https://github.com/Microsoft/AADConnectConfigDocumenter)' i kullanabilirsiniz.

PowerShell kullanarak oluşturduğunuz özel eşitleme kurallarını taşıyabilirsiniz. Diğer değişiklikleri her iki sistemde de aynı şekilde uygulamanız gerekir ve değişiklikleri geçiremezsiniz. [Yapılandırma belgeno](https://github.com/Microsoft/AADConnectConfigDocumenter) , aynı olduklarından emin olmak için iki sistemi karşılaştıralmanıza yardımcı olabilir. Araç ayrıca bu bölümde bulunan adımların otomatikleştirilmesine yardımcı olabilir.

Aşağıdaki şeyleri her iki sunucuda da aynı şekilde yapılandırmanız gerekir:

* Aynı ormanlarla bağlantı
* Herhangi bir etki alanı ve OU filtreleme
* Parola eşitleme ve parola geri yazma gibi isteğe bağlı özellikler

**Özel eşitleme kurallarını taşıma**  
Özel eşitleme kurallarını taşımak için aşağıdakileri yapın:

1. Etkin sunucunuzda **eşitleme kuralları düzenleyicisini** açın.
2. Özel bir kural seçin. **Dışarı aktar**'a tıklayın. Bu, bir not defteri penceresi getirir. Geçici dosyayı bir PS1 uzantısıyla kaydedin. Bu, bir PowerShell betiği oluşturur. PS1 dosyasını hazırlama sunucusuna kopyalayın.  
   ![Eşitleme kuralını dışarı aktarma](./media/how-to-upgrade-previous-version/exportrule.png)
3. Bağlayıcı GUID 'SI, hazırlama sunucusunda farklı olduğundan, bunu değiştirmeniz gerekir. GUID 'yi almak için, **eşitleme kuralları düzenleyicisini** başlatın, aynı bağlı sistemi temsil eden hazır kuralların birini seçin ve **dışarı aktar**' a tıklayın. PS1 dosyanızdaki GUID 'yi, hazırlama sunucusundan GUID ile değiştirin.
4. Bir PowerShell isteminde PS1 dosyasını çalıştırın. Bu, hazırlama sunucusunda özel eşitleme kuralını oluşturur.
5. Tüm özel kurallarınız için bunu tekrarlayın.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Yükseltmeden sonra tam eşitlemeyi erteleme
Yerinde yükseltme sırasında, belirli eşitleme etkinliklerinin (tam Içeri aktarma adımı ve tam eşitleme adımı dahil) yapılması gereken değişiklikler olabilir. Örneğin, bağlayıcı şeması değişiklikleri **tam içeri aktarma** adımı ve kullanıma hazır eşitleme kuralı değişikliklerinin, etkilenen bağlayıcılarda **tam eşitleme** adımının yürütülmesini gerektirir. Yükseltme sırasında, Azure AD Connect hangi eşitleme etkinliklerinin gerekli olduğunu belirler ve bunları *geçersiz kılma* olarak kaydeder. Aşağıdaki eşitleme çevriminde, eşitleme Zamanlayıcı bu geçersiz kılmaları alır ve yürütür. Bir geçersiz kılma başarıyla yürütüldükten sonra kaldırılır.

Bu geçersiz kılmaların yükseltmeden hemen sonra gerçekleşmesini istemediğiniz durumlar olabilir. Örneğin, çok sayıda eşitlenmiş nesneniz var ve bu eşitleme adımlarının iş saatlerinden sonra gerçekleşmesini istiyorsunuz. Bu geçersiz kılmaları kaldırmak için:

1. Yükseltme sırasında, **yapılandırma tamamlandığında eşitleme Işlemini Başlat** seçeneğinin **işaretini kaldırın** . Bu, eşitleme zamanlayıcısını devre dışı bırakır ve geçersiz kılmalar kaldırılmadan önce eşitleme döngüsünün otomatik olarak yapılmasını önler.

   ![Yapılandırma tamamlandığında eşitleme işlemini başlat seçeneğini vurgulayan, temizlemeniz gereken ekran görüntüsü.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Yükseltme tamamlandıktan sonra, hangi geçersiz kılmaların eklendiğini bulmak için aşağıdaki cmdlet 'i çalıştırın: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Geçersiz kılmalar bağlayıcıya özeldir. Aşağıdaki örnekte, hem şirket içi AD Bağlayıcısı hem de Azure AD bağlayıcısına tam Içeri aktarma adımı ve tam eşitleme adımı eklenmiştir.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Eklenen var olan geçersiz kılmaları unutmayın.
   
4. Rastgele bir bağlayıcıda tam içeri aktarma ve tam eşitleme için geçersiz kılmaları kaldırmak üzere aşağıdaki cmdlet 'i çalıştırın: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Tüm bağlayıcılarda geçersiz kılmaları kaldırmak için aşağıdaki PowerShell betiğini yürütün:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Scheduler 'ı yeniden başlatmak için aşağıdaki cmdlet 'i çalıştırın: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Gerekli eşitleme adımlarını en erken rahatınız üzerinde yürütmeyi unutmayın. Synchronization Service Manager kullanarak bu adımları el ile yürütebilir ya da Set-ADSyncSchedulerConnectorOverride cmdlet 'ini kullanarak geçersiz kılmaları geri ekleyebilirsiniz.

Rastgele bir bağlayıcıda tam içeri aktarma ve tam eşitleme için geçersiz kılmaları eklemek üzere aşağıdaki cmdlet 'i çalıştırın:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Sorun giderme
Aşağıdaki bölümde, Azure AD Connect yükseltme sorunuyla karşılaşırsanız kullanabileceğiniz sorun giderme ve bilgiler yer almaktadır.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Azure AD Connect yükseltmesi sırasında bağlayıcı Azure Active Directory hatası yok

Önceki bir sürümden Azure AD Connect yükselttiğinizde, yükseltmenin başlangıcında aşağıdaki hatayla karşılaşabilirsiniz 

![Hata](./media/how-to-upgrade-previous-version/error1.png)

Bu hata, b891884f-051e-4A83-95af-2544101c9083 tanıtıcısına sahip Azure Active Directory Bağlayıcısı geçerli Azure AD Connect yapılandırmasında bulunmadığından oluşur. Bunun durum olduğunu doğrulamak için bir PowerShell penceresi açın, cmdlet 'ı çalıştırın `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

PowerShell cmdlet 'ı **BELIRTILEN ma 'nın bulunamadığını** hata bildiriyor.

Bunun oluşma nedeni, geçerli Azure AD Connect yapılandırmasının yükseltme için desteklenmediği nedenidir. 

Azure AD Connect daha yeni bir sürümünü yüklemek istiyorsanız: Azure AD Connect Sihirbazı 'nı kapatın, var olan Azure AD Connect kaldırın ve yeni Azure AD Connect temiz bir yüklemesini gerçekleştirin.



## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory tümleştirme](whatis-hybrid-identity.md)hakkında daha fazla bilgi edinin.