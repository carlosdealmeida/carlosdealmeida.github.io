## Como construir seu Infra.Data - Parte 1

![c#](/assets/INFRASTRUCTURE.png)

Fala aí galera blz?

Esses dias tem sido bem corridos, sprint review e planning, bugs urgentes e tudo mais rsrsrs.

Mas hoje vou conseguir iniciar com você a criação do pacote Infra.Data que mencionei no post anterior. Se você não leu a publicação anterior clique no link abaixo para entender o contexto:

[Parte 1](2022-02-07-defensores-de-padrao.md)

Bom vamos lá.

## Atenção


Para esta tutorial estarei usando o .NET6, EntityFrameworkCore e o SQLite

O primeiro passo é criar seu projeto, para isso execute o comando abaixo

```csharp
dotnet classlib -n Infra.Data.Minimal -o src
```

O comando acima está criando o projeto do tipo Class Library, com o nome Infra.Data.Minimal e na pasta src (eu já tinha criado uma pasta Infra.Data.Minimal para poder executar esse comando)

Vamos abrir o nosso projeto no VSCode ou Visual Studio.

O segundo passo é criar os diretórios. Iremos criar seguindo o exemplo abaixo:

**Contexts** (Onde iremos guardar todos os nossos contextos)

**Models** (Onde iremos guardar models que usem pacotes que não podem ser instalados no General)

**Repository**

**Contracts** (Interfaces de implementação das persistências)

**Generics** (Classe genérica para implementação das persistências)

**Mappings** (Mapeamento das classes para o banco de dados)

**Persistence** (Implementação das persistências)

Após isso iremos instalar os pacotes do EntityFrameworkCore necessários:

```csharp
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 6.0.1

dotnet add package Microsoft. EntityFrameworkCore. Design --version 6.0.1
```

Com os pacotes instalados vamos criar o nosso primeiro context. Os Contexts são responsáveis pela conexão com o banco de dados. Você ter múltiplos contexts olhando para bancos diferentes ou para 
mesmos bancos com diferentes schemas.

Não vou colocar os códigos aqui pois senão ficará extenso, irei linkar no repositório do github onde estarei montando esse projeto com vocês e também deixarei um print em anexo nessa publicação

Link do context: https://github.com/learn-minimalApi-NET/Infra.Data.Minimal/blob/main/src/Contexts/AppDbContext.cs

No próximo iremos mapear uma classe e criar a parte da persistência, lembrando que para facilitar irei criar as classes no pacote inicialmente, e no final iremos criar o General.

Deixa aqui seu comentário, vamos trocar uma idéia, se tiverem sugestões ou quiserem contribuir só abrir um PR no repositório.

Abraços e até a próxima