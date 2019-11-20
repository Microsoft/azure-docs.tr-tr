---
title: Azure AD Kimlik Koruması ' de riskleri düzeltme ve kullanıcıların engellemesini kaldırma
description: Etkin risk algılamalarını kapatan seçenekler hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61fe0e14fbb6c6251b3bf19843b9c065df60dd86
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887526"
---
# <a name="remediate-risks-and-unblock-users"></a>Riskleri düzeltin ve kullanıcıların engellemesini kaldırın

[Araştırmanızı](howto-identity-protection-investigate-risk.md)tamamladıktan sonra, riski düzeltmek veya kullanıcıların engelini kaldırmak için işlem yapmanız gerekir. Kuruluşlar, [risk ilkelerini](howto-identity-protection-configure-risk-policies.md)kullanarak otomatik düzeltmeyi etkinleştirme seçeneğine de sahiptir. Kuruluşlar, sundukları tüm risk algılamalarını kuruluşunuzun rahat olduğu bir zaman diliminde kapatmayı denemelidir. Microsoft, risk altında çalışırken önemli bir süre olması nedeniyle olayları en kısa sürede kapatmayı öneriyor.

## <a name="remediation"></a>Düzeltmesi

Tüm etkin risk algılamaları, Kullanıcı risk düzeyi adlı bir değerin hesaplamasına katkıda bulunur. Kullanıcı risk düzeyi bir hesap güvenliğinin tehlikeye girdiği olasılığa karşı bir göstergedir (düşük, orta, yüksek). Yönetici olarak, etkilenen kullanıcıların artık risk altında olmaması için tüm risk algılamalarını kapalı olarak almak istersiniz.

Bazı riskler algılamaları kimlik koruması tarafından "kapalı (sistem)" olarak işaretlenebilir, çünkü olaylar artık riskli olarak belirlenmemiştir.

Yöneticiler aşağıdaki seçeneklere sahiptir:

- Risk ilkesiyle kendi kendini düzeltme
- El ile parola sıfırlama
- Kullanıcı riskini kapat
- Bireysel risk algılamalarını el ile kapatın

### <a name="self-remediation-with-risk-policy"></a>Risk ilkesiyle kendi kendini düzeltme

Kullanıcıların, risk ilkelerinizde Azure Multi-Factor Authentication (MFA) ve self servis parola sıfırlama (SSPR) ile kendi kendini düzelmesine izin verirseniz, risk algılandığında kendi engellemesini kaldırabilir. Bu algılamalar daha sonra kapalı olarak değerlendirilir. Risk algılandığında kullanabilmeniz için kullanıcıların daha önce Azure MFA ve SSPR için kayıtlı olmaları gerekir.

Bazı algılamalar, Kullanıcı Kendini düzeltmenin gerekli olduğu, ancak yöneticilerin bu algılamaları yine de değerlendirmesi gereken düzeye kadar risk oluşturmaz. Yöneticiler, [konumlardan erişimi engellemeyi](../conditional-access/howto-conditional-access-policy-location.md) veya ilkelerine karşı kabul edilebilir riski azaltmak gibi ek ölçülerin gerekli olduğunu belirleyebilir.

### <a name="manual-password-reset"></a>El ile parola sıfırlama

Bir Kullanıcı risk ilkesi kullanarak parola sıfırlamayı gerektirmek bir seçenek değilse, yöneticiler el ile parola sıfırlamasına sahip bir kullanıcının tüm risk algılamalarını kapatabilir.

Yöneticiler, kullanıcıları için bir parola sıfırlanırken iki seçenek sunulur:

- **Geçici parola oluşturma** -geçici bir parola oluşturarak, bir kimliği hemen güvenli bir duruma getirebilirsiniz. Bu yöntem, geçici parolanın ne olduğunu bilmeleri gerektiğinden etkilenen kullanıcılara başvurmayı gerektirir. Parola geçici olduğundan, kullanıcıdan bir sonraki oturum açma işlemi sırasında parolayı yeni bir kullanıcı olarak değiştirmesi istenir.

- **Kullanıcının parolayı sıfırlamasını gerektir** -kullanıcıların parola sıfırlamasını sağlamak, yardım masası veya yönetici ile iletişim kurmadan kendinden kurtarmaya olanak sağlar. Bu yöntem yalnızca Azure MFA ve SSPR için kayıtlı kullanıcılar için geçerlidir. Kayıtlı olmayan kullanıcılar için bu seçenek kullanılamaz.

### <a name="dismiss-user-risk"></a>Kullanıcı riskini kapat

Parola sıfırlama sizin için bir seçenek değilse, örneğin Kullanıcı silinmiş olduğu için, Kullanıcı risk algılamalarını kapatmak için seçeneğini belirleyebilirsiniz.

**Kullanıcı riskini kapat**' a tıkladığınızda, tüm olaylar kapatılır ve etkilenen Kullanıcı artık risk altında olmaz. Ancak, bu yöntemin var olan parola üzerinde bir etkisi olmadığından, ilgili kimliği güvenli bir duruma getirmez. 

### <a name="close-individual-risk-detections-manually"></a>Bireysel risk algılamalarını el ile kapatın

Bireysel risk algılamalarını el ile kapatabilirsiniz. Risk algılamalarını el ile kapatarak, Kullanıcı risk düzeyini düşürebilirsiniz. Genellikle, risk algılamaları ilgili bir araştırmaya yanıt olarak el ile kapatılır. Örneğin, bir kullanıcıya konuşduğunda etkin bir risk algılamada artık gerekli değildir. 
 
Risk algılamalarını el ile kapatırken, risk algılamanın durumunu değiştirmek için aşağıdaki eylemlerden birini seçebilirsiniz:

- Kullanıcının güvenliğinin aşıldığını Onayla
- Kullanıcı riskini kapat
- Oturum açmayı güvenli Onayla
- Oturum açma güvenliğinin aşıldığını onaylayın

## <a name="unblocking-users"></a>Kullanıcıların engellemesini kaldırma

Bir yönetici, risk ilkesine veya araştırmalarına göre bir oturum açmayı engellemeyi tercih edebilir. Bir blok, oturum açma veya Kullanıcı riski temelinde gerçekleşebilir.

### <a name="unblocking-based-on-user-risk"></a>Kullanıcı riskine bağlı olarak engellemeyi kaldırma

Kullanıcı riski nedeniyle engellenen bir hesabın engelini kaldırmak için Yöneticiler aşağıdaki seçeneklere sahiptir:

1. **Parolayı sıfırlama** -kullanıcının parolasını sıfırlayabilirsiniz.
1. **Kullanıcı riskini kapat** -Kullanıcı risk ilkesi, erişimi engellemek için yapılandırılmış Kullanıcı risk düzeyine ulaşıldığında kullanıcıyı engeller. Kullanıcının risk düzeyini, Kullanıcı riskini ortadan kaldırarak veya bildirilen risk algılamalarını el ile kapatarak azaltabilirsiniz.
1. **Kullanıcıyı Ilkeden hariç tut** -oturum açma ilkenizin geçerli yapılandırmasının belirli kullanıcılar için sorunlara neden olduğunu düşünüyorsanız kullanıcıları bu bilgisayardan dışlayabilirsiniz. Daha fazla bilgi için bkz. [nasıl yapılır: risk Ilkeleri yapılandırma ve etkinleştirme](howto-identity-protection-configure-risk-policies.md#exclusions)makalesindeki bölüm dışlamaları.
1. **Ilkeyi devre dışı bırak** -ilke yapılandırmanızın tüm kullanıcılarınız için sorunlara neden olduğunu düşünüyorsanız, ilkeyi devre dışı bırakabilirsiniz. Daha fazla bilgi için bkz. [nasıl yapılır: risk Ilkeleri yapılandırma ve etkinleştirme](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Oturum açma riskine bağlı olarak engellemeyi kaldırma

Yöneticiler, oturum açma riskini temel alarak bir hesabın engellemesini kaldırmak için aşağıdaki seçeneklere sahiptir:

1. **Tanıdık bir konumdan veya cihazdan oturum açma** -, engellenen şüpheli oturum açma işlemlerinin yaygın bir nedeni, bilmediğiniz konumlardan veya cihazlardan gelen oturum açma çabalarıdır. Kullanıcılarınız, bilinen bir konumdan veya cihazdan oturum açmaya çalışırken bu nedenin engelleme nedeni olup olmadığını hızlı bir şekilde tespit edebilir.
1. **Kullanıcıyı Ilkeden hariç tut** -oturum açma ilkenizin geçerli yapılandırmasının belirli kullanıcılar için sorunlara neden olduğunu düşünüyorsanız kullanıcıları bu bilgisayardan dışlayabilirsiniz. Daha fazla bilgi için bkz. [nasıl yapılır: risk Ilkeleri yapılandırma ve etkinleştirme](howto-identity-protection-configure-risk-policies.md#exclusions)makalesindeki bölüm dışlamaları.
1. **Ilkeyi devre dışı bırak** -ilke yapılandırmanızın tüm kullanıcılarınız için sorunlara neden olduğunu düşünüyorsanız, ilkeyi devre dışı bırakabilirsiniz. Daha fazla bilgi için bkz. [nasıl yapılır: risk Ilkeleri yapılandırma ve etkinleştirme](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Kimlik Koruması genel bakış almak için bkz. [Azure AD kimlik koruması genel bakış](overview-identity-protection.md).
