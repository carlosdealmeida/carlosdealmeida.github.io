## Defensores de padrão

Umas das coisas que menos gosto são os defensores de padrão e arquitetura dizendo que a ou b é melhor que c e d.

O padrão que é bom para o seu projeto é o que você deve usar, por exemplo, um **CRUD** simples não é necessário usar padrões como **DDD**, pode ser um simples **MVC**.

Nos projetos que tenho trabalhado tem pego um pouco de cada padrão. "Mas como assim? Vai ficar uma zona esse seu projeto". Aí que você se engana, tudo depende de como você estrutura a sua base e divide isso com sua equipe.

Aqui tenho buscado usar o conceito do **DDD** que é a separação de responsabilidades, mas sem toda aquela implementação de **command** ou **UnitOfWork**.

Como faço isso? Simples, vou demonstrar. Atualmente eu uso 3 pacotes atualmente em meus projetos, eles são:

1 - General

2 - Infra.Data

3 - UserDirectory

Eles são projetos do tipo classlibrary que são entregues pelo Github Actions aliado ao Github Packages.

O General é compilado em **netstandard2.1**, ou seja ele é compatível com a última versão do **.NET Framework (4.8)** e com o do **.NET Core (.NET6)**, nele contém todas minhas classes puras e simples, funções que são reutilizáveis como validaCPF e Geração de Logs em TXT e constantes em geral.

Já o Infra.Data, fica responsável por toda a parte de banco de dados. Toda alteração que precisa ser feita no banco, é feito lá, pois usamos CodeFirst, com isso temos todos as migrations, mappings, contexts e etc num só pacote.

E por fim o User.Directory, ele basicamente consiste nas implementações dos controllers que usam o Identity para gestão de usuários e perfis no C#. "Mas Carlos, não teria que ter um User.Directory para cada sistema?", aí que está, não necessariamente. Caso você use sempre o básico, sem alterações, no seu Startup.cs (agora no Program.cs a partir do .NET6) você define para qual context você quer que ele olhe. Ou seja, se você quiser uma autenticação única para todos seus sistemas, bastaria você configurar em suas API's o mesmo banco para o IDENTITY e usar o mesmo pacote com as controllers. Com isso irá te poupar bastante tempo de codificação.

Creio que o General e o UserDirectory estão bem explanados, mas o Infra.Data é bem amplo os conceitos que você pode inserir nele. Por isso durante os próximos dias tentarei mostrar como construir o seu Infra.Data da melhor forma.

E você? tem um padrão ou uma arquitetura que goste de usar diferente dos "modinhas"? rsrsrs, comenta aqui embaixo. Se gostou deixa seu curtir para que esse post possa alcançar mais pessoas.