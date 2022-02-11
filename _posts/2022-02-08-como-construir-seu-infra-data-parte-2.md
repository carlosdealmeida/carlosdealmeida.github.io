## Como construir seu Infra.Data - Parte 2

![c#](/assets/INFRASTRUCTURE.png)

Fala galera blz?

Todo mundo sextando já, fim de dia, rsrsrs. Mas não podia deixar de vir e postar mais uma parte da nossa criação do pacote Infra.Data.

Lembrando que estamos criando do 0 e vamos até a distribuição do pacote através do Github Packages.

Para quem não está acompanhando segue link das partes anteriores para se situar:

[Parte 1](2022-02-07-defensores-de-padrao.md)

[Parte 2](2022-02-08-como-construir-seu-infra-data-parte-1.md)

A publicação excedeu o limite de caracteres do linkedin, rsrsrs, e com isso irei postar em duas partes separadas.

E como alertei anteriormente, não irei postar códigos aqui, pois ficaria grande demais e mal organizado, então estarei postando links para o repositório do github.

Bom vamos lá, já temos o nosso Context criado, hoje vamos mapear uma classe do zero.

Como toda boa tutorial, iremos usar o exemplo de uma lista de Tarefas rsrsrs.

Iremos criar a classe Tarefas na pasta Models, segue o link: 

Com isso já temos a representação para o C#, agora iremos montar para representar no banco de dados a nossa classe.

Antes de mapearmos ela, iremos criar a classe GenericRepository.

GenericRepository é responsável por conter as funções básicas de CRUD do banco afim de não ter repetição de código a cada criação de repository. Pois para cada classe que quisermos mapear no banco, será necessário a criação
de um repository.

Como boa prática, iremos criar ela juntamente com sua interface, o nome será IGenericRepository, lembrando que na convenção o nome de toda interface é o nome da classe precedido da letra "I".

E para deixar um pouco mais completo, nosso GenericRepository conterá os métodos "Async" e "Sync"

# IGenericRepository

```csharp
using Infra.Data.Minimal.Contexts;

namespace Infra.Data.Minimal.Repository.Contracts
{
    public interface IGenericRepository<TContext, TEntity>
        where TEntity : class
        where TContext : AppDbContext
    {
        Task InsertAsync(TEntity obj);
        Task UpdateAsync(TEntity obj);
        Task DeleteAsync(TEntity obj);
        Task<List<TEntity>> FindAllAsync();
        Task<TEntity> FindByIdAsync(Guid id);
        Task<int> CountRecordsAsync();
        bool Exists(Guid id);
        void InsertSync(TEntity obj);
        void UpdateSync(TEntity obj);
        void DeleteSync(TEntity obj);
        List<TEntity> FindAllSync();
        TEntity FindByIdSync(Guid id);
        int CountRecordsSync();
    }
}
```

# GenericRepository

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Infra.Data.Minimal.Contexts;
using Infra.Data.Minimal.Repository.Contracts;
using Microsoft.EntityFrameworkCore;

namespace Infra.Data.Minimal.Repository.Generics
{
    public abstract class GenericRepository<TContext, TEntity> : IGenericRepository<TContext, TEntity>, IDisposable
         where TContext : AppDbContext, new()
         where TEntity : class
    {
        protected AppDbContext _db;
        protected DbSet<TEntity> _dbSet;

        public GenericRepository()
        {
            _db = new TContext();
            _dbSet = _db.Set<TEntity>();
        }
        public virtual async Task InsertAsync(TEntity obj)
        {
            await _dbSet.AddAsync(obj);
            await _db.SaveChangesAsync();
        }
        public virtual void InsertSync(TEntity obj)
        {
            _dbSet.Add(obj);
            _db.SaveChanges();
        }

        public virtual async Task UpdateAsync(TEntity obj)
        {
            _dbSet.Update(obj);
            await _db.SaveChangesAsync();
        }

        public virtual void UpdateSync(TEntity obj)
        {
            _dbSet.Update(obj);
            _db.SaveChanges();
        }

        public virtual async Task DeleteAsync(TEntity obj)
        {
            _dbSet.Remove(obj);
            await _db.SaveChangesAsync();
        }
        public virtual void DeleteSync(TEntity obj)
        {
            _dbSet.Remove(obj);
            _db.SaveChanges();
        }
        public virtual async Task<List<TEntity>> FindAllAsync()
        {
            return await _dbSet.AsNoTracking().Take(0).ToListAsync();
        }
        public virtual List<TEntity> FindAllSync()
        {
            return _dbSet.AsNoTracking().Take(100).ToList();
        }

        public virtual async Task<TEntity> FindByIdAsync(Guid id)
        {
            return await _dbSet.FindAsync(id);
        }

        public virtual TEntity FindByIdSync(Guid id)
        {
            return _dbSet.Find(id);
        }

        public virtual async Task<int> CountRecordsAsync()
        {
            return await _dbSet.CountAsync();
        }

        public virtual int CountRecordsSync()
        {
            return _dbSet.Count();
        }

        public void Dispose()
        {
            _db.Dispose();
        }

        public bool Exists(Guid id)
        {
            return _dbSet.FindAsync(id) == null ? false : true;
        }
    }

}
```

Com essa parte genérica pronta, vamos criar a nossa interface e classe TarefasRepository, ela será bem simples, apenas irá herdar do GenericRepository.

# ITarefasRepository

```csharp
using Infra.Data.Minimal.Contexts;
using Infra.Data.Minimal.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Infra.Data.Minimal.Repository.Contracts
{
    public interface ITarefasRepository : IGenericRepository<AppDbContext, Tarefas>
    {
    }
}
```

# TarefasRepository

```csharp
using Infra.Data.Minimal.Contexts;
using Infra.Data.Minimal.Models;
using Infra.Data.Minimal.Repository.Contracts;
using Infra.Data.Minimal.Repository.Generics;

namespace Infra.Data.Minimal.Repository.Persistence
{
    public class TarefasRepository : GenericRepository<AppDbContext, Tarefas>, ITarefasRepository
    {
    }
}
```

Irá aparecer um problema na implementação da classe TarefasRepository. O que ocorre, por se tratar de implementação com interface e Generic, é necessário que o Context tenha um construtor public sem parâmetros.
Como não precisaremos de construtroes agora iremos remover o construtor do Context.

AppDbContext - https://github.com/learn-minimalApi-NET/Infra.Data.Minimal/blob/main/src/Contexts/AppDbContext.cs

Com isso já temos nosso acesso a tabela do banco de dados, agora iremos mapear. Essa parte é bem chata dependendo do tamanho da sua classe, pois como estamos usando o conceito CodeFirst precisamos representar a classe com todas
as propriedades, primary key, foreigns keys e etc, com isso é temos que "codar" bastante, mas somos desenvolvedores estamos aqui pra isso.

Iremos usar a FluentApi do EntityFrameworkCore, para quem quiser ler mais segue o link de um site bem completo: https://lnkd.in/dUdTCYHz

Como nossa classe é pequena, irá ficar assim:

# TarefasMap

```csharp
using Infra.Data.Minimal.Models;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Infra.Data.Minimal.Repository.Mappings
{
    public class TarefasMap : IEntityTypeConfiguration<Tarefas>
    {
        public void Configure(EntityTypeBuilder<Tarefas> builder)
        {
            builder.ToTable("tarefas");

            builder
                .HasKey(t => t.Id)
                .HasName("PK_tarefas");

            builder
                .Property(t => t.Titulo)
                .HasColumnName("titulo")
                .HasColumnType("varchar")
                .HasMaxLength(100);

            builder
                .Property(t => t.Descricao)
                .HasColumnName("descricao")
                .HasColumnType("varchar")
                .HasMaxLength(2000);

            builder
                .Property(t => t.Feito)
                .HasColumnName("feito")
                .HasColumnType("int")
                .HasDefaultValue(0);
        }
    }
}
```

Perfeito, temos nosso mapeamento, nossos comandos de acesso e agora, iremos fazer com que o Context reconheça tudo isso e possa criar nossa tabela na base de dados através das Migrations e tenha acesso a ela 
para executar os comandos de CRUD.

Iremos criar os métodos OnConfiguring e OnModelCreating, o primeiro serve para que o Context saiba para qual banco apontar e o segundo é para que a aplicação se comunique com o banco de dados de acordo com o nosso
mapeamento.

AppDbContext - https://github.com/learn-minimalApi-NET/Infra.Data.Minimal/blob/main/src/Contexts/AppDbContext.cs

E por fim iremos colocar no inicio do Context a propriedade que irá referenciar a tabela Tarefas:

```csharp
public DbSet<Tarefas> Leads { get; set; }
```

Com isso encerramos a parte de mapear uma classe do 0. Agora precisamos usar esse pacote, no próximo tutorial irei ensinar a como criar um "pipeline" utilizando o Github Actions e distribuindo esse pacote através do Github Packages
para que possamos usar ele em nossos projetos.