---
title: Design de identidade híbrida - requisitos de gestão de conteúdos Azure [ Microsoft Docs
description: Fornece informações sobre como determinar os requisitos de gestão de conteúdos do seu negócio. Normalmente, quando um utilizador tem o seu próprio dispositivo, também pode ter várias credenciais que serão alternadas de acordo com a aplicação que utilizam. É importante diferenciar o conteúdo criado usando credenciais pessoais versus as criadas usando credenciais corporativas. A sua solução de identidade deve ser capaz de interagir com os serviços na nuvem para proporcionar uma experiência perfeita ao utilizador final, garantindo a sua privacidade e aumentando a proteção contra fugas de dados.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918446"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de gestão de conteúdos para a sua solução de identidade híbrida
Compreender os requisitos de gestão de conteúdos para o seu negócio pode afetar diretamente a sua decisão sobre qual a solução de identidade híbrida a utilizar. Com a proliferação de vários dispositivos e a capacidade dos utilizadores de trazerem os seus próprios dispositivos[(BYOD),](https://aka.ms/byodcg)a empresa deve proteger os seus próprios dados, mas também deve manter a privacidade do utilizador intacta. Normalmente, quando um utilizador tem o seu próprio dispositivo, também pode ter várias credenciais que serão alternadas de acordo com a aplicação que utilizam. É importante diferenciar o conteúdo criado usando credenciais pessoais versus as criadas usando credenciais corporativas. A sua solução de identidade deve ser capaz de interagir com os serviços na nuvem para proporcionar uma experiência perfeita ao utilizador final, garantindo a sua privacidade e aumentando a proteção contra fugas de dados. 

A sua solução de identidade será alavancada por diferentes controlos técnicos de forma a fornecer a gestão de conteúdos, conforme indicado na figura abaixo:

![controlos de segurança](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Controlos de segurança que irão alavancar o seu sistema de gestão de identidade**

Em geral, os requisitos de gestão de conteúdos irão alavancar o seu sistema de gestão de identidade nas seguintes áreas:

* Privacidade: identificar o utilizador que possui um recurso e aplicar os controlos adequados para manter a integridade.
* Classificação de Dados: identifique o utilizador ou grupo e o nível de acesso a um objeto de acordo com a sua classificação. 
* Proteção de Fugas de Dados: os controlos de segurança responsáveis pela proteção de dados para evitar fugas terão de interagir com o sistema de identidade para validar a identidade do utilizador. Isto também é importante para a auditoria do objetivo do trail.

> [!NOTE]
> Leia a [classificação de dados para prontidão](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) na nuvem para obter mais informações sobre as melhores práticas e orientações para a classificação de dados.
> 
> 

Ao planear a sua solução de identidade híbrida, certifique-se de que as seguintes perguntas são respondidas de acordo com os requisitos da sua organização:

* A sua empresa tem controlos de segurança para impor a privacidade dos dados?
  * Se sim, os controlos de segurança poderão integrar-se com a solução de identidade híbrida que vai adotar?
* A sua empresa utiliza a classificação de dados?
  * Se sim, a atual solução é capaz de integrar-se com a solução de identidade híbrida que vai adotar?
* A sua empresa tem atualmente alguma solução para fugas de dados? 
  * Se sim, a atual solução é capaz de integrar-se com a solução de identidade híbrida que vai adotar?
* A sua empresa precisa de auditar o acesso aos recursos?
  * Se sim, que tipo de recursos?
  * Se sim, que nível de informação é necessário?
  * Se sim, onde o registo de auditoria deve residir? No local ou na nuvem?
* A sua empresa precisa de encriptar quaisquer e-mails que contenham dados sensíveis (SSNs, números de cartão de crédito, etc.)?
* A sua empresa precisa de encriptar todos os documentos/conteúdos partilhados com parceiros de negócio externos?
* A sua empresa precisa de fazer cumprir as políticas corporativas em determinados tipos de e-mails (não responde a todos, não reencaminha)?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Definir a Estratégia](plan-hybrid-identity-design-considerations-data-protection-strategy.md) de Proteção de Dados irá passar por cima das opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às suas necessidades de negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar requisitos do controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Veja também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

