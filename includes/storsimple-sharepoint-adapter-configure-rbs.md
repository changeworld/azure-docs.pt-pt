---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184299"
---
> [!NOTE]
> Ao efazer alterações no Adaptador StorSimple para a configuração do SharePoint RBS, deve ser ligado com uma conta de utilizador que pertença ao grupo Dedomínio Admins. Além disso, você deve aceder à página de configuração a partir de um navegador que funciona no mesmo anfitrião que a Administração Central.
> 
> 

#### <a name="to-configure-rbs"></a>Para configurar o RBS
1. Abra a página da Administração Central sharePoint e navegue para **as Definições**do Sistema . 
2. Na secção **Azure StorSimple,** clique em **Configurar o Adaptador Simple**.
   
    ![Configure o adaptador StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Na página **de Adapter Configure StorSimple:**
   
   1. Certifique-se de que a caixa de verificação do caminho de **edição Ativa** está selecionada.
   2. Na caixa de texto, digite o caminho da Convenção Universal de Nomeação (UNC) da loja BLOB.
      
      > [!NOTE]
      > O volume da loja BLOB deve ser alojado num volume iSCSI configurado no dispositivo StorSimple.

   3. Clique no botão **'Ativar'** abaixo de cada uma das bases de dados de conteúdos que pretende configurar para armazenamento remoto.
      
      > [!NOTE]
      > A loja BLOB deve ser partilhada e acessível por todos os servidores frontais web (WFE) e a conta de utilizador configurada para a exploração do servidor SharePoint deve ter acesso à partilha.
      
      ![Ativar o fornecedor RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Quando ativa ou desativa o RBS, verá também a seguinte mensagem.
      
      ![Configurar storSimple Adapter Enable Disable](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Clique no botão **Atualizar** para aplicar a configuração. Quando clicar no botão **'Actualização',** o estado de configuração do RBS será atualizado em todos os servidores WFE, e toda a exploração estará ativada por RBS. Aparece a seguinte mensagem.
      
      ![Mensagem de configuração do adaptador](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Se estiver a configurar o RBS para uma exploração do SharePoint com um número muito grande de bases de dados (superior a 200), a página web da Administração Central do SharePoint poderá esgotar-se. Se isso ocorrer, refresque a página. Isto não afeta o processo de configuração.

4. Verifique a configuração:
   
   1. Inscreva-se no site da Administração Central do SharePoint e navegue na página de **Adapter Configure StorSimple.**
   2. Verifique os detalhes da configuração para se certificar de que correspondem às definições que introduziu. 
5. Verifique se o RBS funciona corretamente:
   
   1. Faça o upload de um documento para o SharePoint. 
   2. Navegue pelo caminho unc que configura. Certifique-se de que a estrutura do diretório RBS foi criada e que contém o objeto carregado.
6. (Opcional) Pode utilizar o `Migrate()` cmdlet Microsoft RBS PowerShell incluído com o SharePoint para migrar o conteúdo BLOB existente para o dispositivo StorSimple. Para mais informações, consulte o [conteúdo migrate para dentro ou para fora do RBS no SharePoint 2013][6] ou [migrar conteúdo para dentro ou para fora do RBS (SharePoint Foundation 2010)][7].
7. (Opcional) Nas instalações de teste, pode verificar se os BLOBs foram retirados da base de dados de conteúdos da seguinte forma: 
   
   1. Inicie o Estúdio de Gestão SQL.
   2. Executar a consulta ListBlobsInDB_2010.sql ou ListBlobsInDB_2013.sql, da seguinte forma.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Se o RBS foi configurado corretamente, deve aparecer um valor NULO na coluna SizeOfContentInDB para qualquer objeto que tenha sido carregado e externo com sucesso com RBS.
8. (Opcional) Depois de configurar o RBS e mover todo o conteúdo BLOB para o dispositivo StorSimple, pode mover a base de dados de conteúdo para o dispositivo. Se optar por mover a base de dados de conteúdos, recomendamos que configure o armazenamento da base de dados de conteúdos no dispositivo como um volume primário. Em seguida, utilize as melhores práticas estabelecidas do SQL Server para migrar a base de dados de conteúdo para o dispositivo StorSimple. 
   
   > [!NOTE]
   > A deslocação da base de dados de conteúdo para o dispositivo só é suportada para a série StorSimple 8000 (não é suportada para a série 5000 ou 7000).
   
   Se armazenar BLOBs e a base de dados de conteúdos em volumes separados no dispositivo StorSimple, recomendamos que os configure no mesmo recipiente de volume. Isto garante que serão apoiados em conjunto.
   
   > [!WARNING]
   > Se não tiver ativado o RBS, não recomendamos que a transferência da base de dados de conteúdos para o dispositivo StorSimple. Esta é uma configuração não testada.
   
9. Vá para o próximo passo: Configure a recolha de [lixo.](#configure-garbage-collection)

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
