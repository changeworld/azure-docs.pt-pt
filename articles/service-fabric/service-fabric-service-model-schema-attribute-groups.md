---
title: Modelo de serviço XML grupos de atributos
description: Descreve os grupos de atributos no esquema XML do modelo de serviço Service Fabric.
ms.topic: reference
ms.date: 12/10/2018
ms.openlocfilehash: 95e19dbdeafdd03af56acaeeb06901a36f2e0333
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609388"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-attribute-groups"></a>Modelo de serviço XML grupos de atributos

## <a name="accountcredentialsgroup-attributegroup"></a>AccountCredenciasGroup atributosGroup

|Atributo|Valor|
|---|---|
|conteúdo|2 atributos|
|nome|Grupo Credenciais de Conta|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountCredentialsGroup">
        <xs:attribute name="AccountName" type="xs:string" use="optional">
          <xs:annotation>
            <xs:documentation>User name or Service Account Name (for example, MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
          </xs:annotation>
        </xs:attribute>
        <xs:attribute name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    </xs:attributeGroup>
    
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="accountname"></a>AccountName
Nome do utilizador ou nome da conta de serviço John.Doe@department.contoso.com(por exemplo, MyMachine\JohnDoe ou ).

|Atributo|Valor|
|---|---|
|nome|AccountName|
|tipo|xs:corda|
|utilizar|opcional|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountName" type="xs:string" use="optional">
          <xs:annotation>
            <xs:documentation>User name or Service Account Name (for example, MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
          </xs:annotation>
        </xs:attribute>
        
```

#### <a name="password"></a>Palavra-passe
Palavra-passe para a conta de utilizador.

|Atributo|Valor|
|---|---|
|nome|Palavra-passe|
|tipo|xs:corda|
|utilizar|opcional|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    
```

## <a name="applicationinstanceattrgroup-attributegroup"></a>ApplicationInstanceAttrGroup atribuigroup
Grupo de atributos por exemplo de aplicação.

|Atributo|Valor|
|---|---|
|conteúdo|2 atributos|
|nome|AplicaçõesInstanceAttrGroup|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationInstanceAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application instance.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:attribute name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="nameuri"></a>Nameuri
Nome totalmente qualificado da aplicação.

|Atributo|Valor|
|---|---|
|nome|Nameuri|
|tipo|Fabricuri|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

#### <a name="applicationid"></a>ApplicationID
Identificação desta aplicação.

|Atributo|Valor|
|---|---|
|nome|ApplicationID|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="applicationmanifestattrgroup-attributegroup"></a>ApplicationManifestAttrGroup atribuigroup
Grupo de atributos para manifesto de candidatura.

|Atributo|Valor|
|---|---|
|conteúdo|3 atributos|
|nome|ApplicationManifestAttrGroup|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationManifestAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an unstructured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:anyAttribute processContents="skip"/> <!-- Allow unknown attributes to be used. -->
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="applicationtypename"></a>ApplicationTypeName
O identificador de tipo para esta aplicação.

|Atributo|Valor|
|---|---|
|nome|ApplicationTypeName|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="applicationtypeversion"></a>AplicaçãoTypeVersion
A versão deste tipo de aplicação, uma corda não estruturada.

|Atributo|Valor|
|---|---|
|nome|AplicaçãoTypeVersion|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="manifestid"></a>Manifestoid
O identificador deste manifesto de aplicação, uma corda não estruturada.

|Atributo|Valor|
|---|---|
|nome|Manifestoid|
|utilizar|opcional|
|predefinição||
|tipo|xs:corda|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an unstructured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

## <a name="configoverridesidentifier-attributegroup"></a>ConfigOverridesIdentifier atributoGroup
Identifica substituições de configuração para um pacote de serviço.

|Atributo|Valor|
|---|---|
|conteúdo|2 atributos|
|nome|ConfigOverridesIdentifier|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConfigOverridesIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies configuration overrides for a service package.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServicePackageName" type="xs:string" use="required"/>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="servicepackagename"></a>Nome pacote de serviço

|Atributo|Valor|
|---|---|
|nome|Nome pacote de serviço|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServicePackageName" type="xs:string" use="required"/>
    
```

#### <a name="rolloutversion"></a>Versão rollout
Identificação do lançamento em que foram feitas alterações ao elemento de sobreposições.

|Atributo|Valor|
|---|---|
|nome|Versão rollout|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="connectionstring-attributegroup"></a>ConnectionString atribuiGroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|Linha de conexão|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString">
                <xs:attribute name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Connection string to the Azure storage account. Format: DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="connectionstring"></a>Linha de conexão
Fio de ligação à conta de armazenamento Azure. Formato: DefaultEndpointsProtocol=https; Nome de conta=[]; Chave de Conta=[]

|Atributo|Valor|
|---|---|
|nome|Linha de conexão|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Connection string to the Azure storage account. Format: DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="containername-attributegroup"></a>ContainerName attributeGroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|ContainerName|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName">
    <xs:attribute name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="containername"></a>ContainerName
O nome do contentor no armazenamento de blob Azure onde os dados são carregados.

|Atributo|Valor|
|---|---|
|nome|ContainerName|
|tipo|xs:corda|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="datadeletionageindays-attributegroup"></a>DataDeletionAgeInDays atribuigroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|DataDeletionAgeInDays|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays">
    <xs:attribute name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="datadeletionageindays"></a>DataDeletionAgeInDays
Número de dias após os quais os dados antigos são eliminados deste local.

|Atributo|Valor|
|---|---|
|nome|DataDeletionAgeInDays|
|tipo|xs:corda|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="isenabled-attributegroup"></a>Atributo IsEnabledGroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|IsEnabled|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled">
                <xs:attribute name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="isenabled"></a>IsEnabled
Está ou não habilitada a transferência de dados para este destino. Por defeito, não está ativado.

|Atributo|Valor|
|---|---|
|nome|IsEnabled|
|tipo|xs:corda|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="levelfilter-attributegroup"></a>LevelFilter atribuiGroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|Filtro de Nível|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter">
    <xs:attribute name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="levelfilter"></a>Filtro de Nível
Nível em que os eventos de ETW devem ser filtrados. Todos os eventos no mesmo ou menor nível do que o nível especificado estão incluídos.

|Atributo|Valor|
|---|---|
|nome|Filtro de Nível|
|tipo|xs:corda|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="namevaluepair-attributegroup"></a>NomeValuePair atribuiGroup
Nome e Valor definidos como um atributo.

|Atributo|Valor|
|---|---|
|conteúdo|2 atributos|
|nome|NomeValuePair|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameValuePair">
    <xs:annotation>
      <xs:documentation>Name and Value defined as an attribute.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="name"></a>Nome
O nome do cenário para anular.

|Atributo|Valor|
|---|---|
|nome|Nome|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="value"></a>Valor
O novo valor da configuração.

|Atributo|Valor|
|---|---|
|nome|Valor|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="path-attributegroup"></a>Atributo de caminhoGroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|Caminho|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path">
                <xs:attribute name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the file share. Format: file:[]</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="path"></a>Caminho
Caminho para a partilha de ficheiros. Formato: ficheiro:[]

|Atributo|Valor|
|---|---|
|nome|Caminho|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the file share. Format: file:[]</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="relativefolderpath-attributegroup"></a>Atributo RelativeFolderPathGroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|Caminho-das-pastas relativas|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath">
                <xs:attribute name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="relativefolderpath"></a>Caminho-das-pastas relativas
Caminho para a pasta, em relação ao diretório de registo de aplicações.

|Atributo|Valor|
|---|---|
|nome|Caminho-das-pastas relativas|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="servicemanifestidentifier-attributegroup"></a>ServiceManifestIdentifier atributoGroup
Identifica um manifesto de serviço.

|Atributo|Valor|
|---|---|
|conteúdo|2 atributos|
|nome|ServiceManifestIdentifier|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies a service manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest this is referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest that is referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="servicemanifestname"></a>ServiceManifestName
O nome do manifesto de serviço é referenciado. O nome deve coincidir com o nome declarado no elemento ServiceManifest do manifesto de serviço.

|Atributo|Valor|
|---|---|
|nome|ServiceManifestName|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest this is referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="servicemanifestversion"></a>Manifestação de serviço
A versão do manifesto de serviço que é referenciada. A versão deve coincidir com a versão declarada no manifesto de serviço.

|Atributo|Valor|
|---|---|
|nome|Manifestação de serviço|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest that is referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

## <a name="uploadintervalinminutes-attributegroup"></a>UploadIntervalInMinutes atributoGroup

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|UploadIntervalinMinutes|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes">
    <xs:attribute name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="uploadintervalinminutes"></a>UploadIntervalinMinutes
Intervalo em minutos em que os dados são enviados para este destino.

|Atributo|Valor|
|---|---|
|nome|UploadIntervalinMinutes|
|tipo|xs:corda|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="versioneditemattrgroup-attributegroup"></a>Atribuído do ItemAttrGroup versioned
Atributo grupo para verdição de secções em documentos ApplicationInstance e ServicePackage.

|Atributo|Valor|
|---|---|
|conteúdo|1 ou atributo(s)|
|nome|VersionedItemAttrGroup|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedItemAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for versioning sections in ApplicationInstance and ServicePackage documents.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required"/>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="rolloutversion"></a>Versão rollout

|Atributo|Valor|
|---|---|
|nome|Versão rollout|
|tipo|xs:corda|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required"/>
  
```

## <a name="versionedname-attributegroup"></a>Nome versãoatribuídoGroup
Grupo de atributos que inclui um Nome e uma Versão.

|Atributo|Valor|
|---|---|
|conteúdo|2 atributos|
|nome|Nome versão|

### <a name="xml-source"></a>Fonte XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedName">
    <xs:annotation>
      <xs:documentation>Attribute group that includes a Name and a Version.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalhes do atributo

#### <a name="name"></a>Nome
Nome do item versão.

|Atributo|Valor|
|---|---|
|nome|Nome|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="version"></a>Versão
Versão do item versão, uma corda não estruturada.

|Atributo|Valor|
|---|---|
|nome|Versão|
|utilizar|necessário|

##### <a name="xml-source"></a>Fonte XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

