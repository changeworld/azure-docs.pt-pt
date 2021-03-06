---
title: Boas Práticas para usar o Cofre chave - Cofre chave Azure [ Cofre chave ] Microsoft Docs
description: Este documento explica algumas das melhores práticas para usar o Cofre chave
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270995"
---
# <a name="best-practices-to-use-key-vault"></a>Boas práticas para usar o Cofre chave

## <a name="control-access-to-your-vault"></a>Controlar o acesso ao seu cofre

Azure Key Vault é um serviço na nuvem que salvaguarda chaves de encriptação e segredos como certificados, cordas de ligação e senhas. Como estes dados são sensíveis e críticos de negócios, é necessário garantir o acesso aos seus cofres chave, permitindo apenas aplicações e utilizadores autorizados. Este [artigo](key-vault-secure-your-key-vault.md) fornece uma visão geral do modelo de acesso ao Cofre Chave. Explica a autenticação e a autorização e descreve como garantir o acesso aos seus cofres chave.

Sugestões enquanto controla o acesso ao seu cofre são as seguintes:
1. Bloqueie o acesso à sua subscrição, grupo de recursos e cofres-chave (RBAC)
2. Criar políticas de acesso para cada cofre
3. Use o menor privilégio de acesso principal para conceder acesso
4. Ligue os [pontos finais](key-vault-overview-vnet-service-endpoints.md) do serviço firewall e VNET

## <a name="use-separate-key-vault"></a>Use cofre de chave separado

A nossa recomendação é utilizar um cofre por aplicação por ambiente (Desenvolvimento, Pré-Produção e Produção). Isto ajuda-o a não partilhar segredos em ambientes e também reduz a ameaça em caso de violação.

## <a name="backup"></a>Cópia de segurança

Certifique-se de que leva as costas regulares do seu [cofre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) em atualização/eliminação/criação de objetos dentro de um Cofre.

## <a name="turn-on-logging"></a>Ligar a exploração madeireira

[Ligue a exploração de madeira](key-vault-logging.md) para o seu cofre. Também instale alertas.

## <a name="turn-on-recovery-options"></a>Ativar as opções de recuperação

1. Ligue [o Soft Delete](key-vault-ovw-soft-delete.md).
2. Ligue a proteção da purga se quiser proteger-se contra a eliminação da força do segredo/cofre mesmo depois de a eliminação suave ser ligada.
