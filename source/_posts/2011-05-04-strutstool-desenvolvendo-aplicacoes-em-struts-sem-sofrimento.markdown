---
layout: post
title: "StrutsTool: Desenvolvendo Aplicações em Struts sem Sofrimento"
date: 2011-05-04 17:21
comments: true
categories: 
---

Um dos aspectos que mais chama a atenção no desenvolvimento de aplicações com Struts é a quantidade de passos necessários para criar aplicações relativamente simples. Quem já usou sabe como é custoso criar um simples CRUD.

Isso trás grande frustração para quem desenvolve aplicações web, inclusive eu. Isso se dá principalmente pois o Struts necessita de extensa configuração. Cada action que você cria precisa ser mapeada em um arquivo xml, se você usar Tiles o negócio é pior ainda, é xml para todo lado.

O pior é que isso acontece em outras frameworks Java, como o Spring. Porém esta adotou o uso de anotações ao invés de xml, mas não deixa de ser configuração. Fato é que com as anotações você não precisa criar tantos arquivos, mas ainda assim precisa escrever muitas linhas para fazer a coisa funcionar.

Com uma rápida busca na internet é possível encontrar diversas discussões sobre quando utilizar Java para web ou qual é a melhor dentre as frameworks para Java (a Wikipedia [lista](http://en.wikipedia.org/wiki/Comparison_of_web_application_frameworks#Java) 35 frameworks para Java). Kelby Zorgdrager faz em um artigo uma [comparação entre frameworks Java](http://olex.openlogic.com/wazi/2010/choosing-the-right-java-web-development-framework/), com tabelas de pontuação para os pontos mais relevantes de uma framework. Para encurtar a história, de modo geral (não conclusivo) Struts e JSF tem as melhores notas. Entretanto, frameworks como Stripes e Tapestry tem melhores notas quando falamos em RAD, isso porque ambas se utilizam de convenção ao invés de configuração.

Nessas horas muitos diriam para trocar de linguagem, mas isso depende muito das necessidades e recursos disponíveis para o projeto. Não vou entrar nesta discussão, a internet já está cheia delas, tendo destaque as com títulos como: “linguagem A vs linguagem B”.

<!-- more -->

## Porque Struts?

Não vou dizer que adoro esta framework, pois estaria mentindo. Digamos que a escolha desta framework foi mais uma questão de necessidade (obrigação), do contrário teria escolhido outra framework, ou outra linguagem, como Ruby ([comparação interessante entre as linguagens](http://gaurav-asktome.blogspot.com/2011/03/java-vs-ruby-on-rails-it-is-dead-heat.html)).

Bom, se eu tenho que utilizar Struts para desenvolver uma aplicação, ao menos posso usar meus meios para reduzir o sofrimento durante a jornada. E é exatamente o que estou fazendo, construindo uma aplicação de linha de comando inspirada em Rails para Struts 2.

## StrutsTool

Pois bem, esta aplicação se chama StrutsTool (não estava muito inspirado, então me baseei no Zend_Tool mesmo) e está hospedada no [Google Code](http://code.google.com/p/strutstool/).

A idéia de fazer tal ferramenta surgiu, obviamente, de uma necessidade, que era não me arrebentar para programar funcionalidades simples. E por simples, me refiro ao CRUD. Além disso, muitos arquivos precisam ser criados e modificados para se obter uma aplicação funcional em Struts.

Quer uma amostra prática disto? Justin Gehtland [reescreveu uma aplicação Java em Ruby on Rails](http://www.theserverside.com/news/thread.tss?thread_id=33120) e obteve os seguintes resultados:

```
Linhas de Código:
Rails: 1164
Java: 3293

Número de Classes:
Rails: 55
Java: 62

Número de Métodos:
Rails: 126
Java: 549

Linhas de Configuração:
Rails: 113
Java: 1161
```

Não é para menos. Quer outro exemplo, então dê uma olhada [neste projeto](https://github.com/mayconbordin/JavaWebExample) que hospedei há algum tempo no Github, é apenas um exemplo simples de CRUD utilizando Struts 2, Tiles, Hibernate e Hibernate Validator. Em poucas palavras : muito código para pouca funcionalidade.

Para deixar claro o quanto de trabalho um CRUD pode dar, você precisa:

- criar os arquivos .jsp
- criar um controlador
- mapeá-lo no arquivo struts.xml indicando os arquivos .jsp que irão ser exibidos
- se usar tiles, definir os arquivos .jsp no tiles.xml e referenciá-los no struts.xml
- criar a classe POJO
- adicionar validadores de dados ao POJO
- mapear a classe POJO para o Hibernate, podendo ser com anotações ou xml
- criar classe de acesso a dados
- criar classe de serviço
- você pode ainda querer criar um arquivo properties para armazenar as mensagens que serão usadas pelo controlador

Foi baseado nesta aplicação de exemplo que eu comecei a criar o que viria a ser uma aplicação de base para o desenvolvimento de minhas aplicações em Struts. Se trata de um projeto com as configurações básicas para funcionar e em cima dele eu poderia adicionar as funcionalidades específicas de minhas aplicações.

De forma simples, esta aplicação (StrutsTool) cria um projeto base e conforme você for criando novas funcionalidades (models, controladores, views, …) a ferramenta irá adicioná-las a este projeto e executar toda a configuração necessária. Ou seja, é um gerador para Struts.

## Funcionalidades

Esta ferramenta provê, por enquanto, poucas funcionalidades, porém extremamente úteis. Mas antes de falar destas funcionalidades gostaria de listar as frameworks/APIs suportadas pelos projetos criados com esta ferramenta. São elas:

- Struts 2.2.1.1
- Struts2 jQuery Plugin 2.5.3
- Hibernate 3.6.3
- Hibernate Validator 4.1.0
- Hibernate Search 3.4.0
- Tiles 2.2.2
- HTML Compressor 1.1
- Closure Compiler
- wro4j 1.3.6
- YUICompressor 2.4.2
- Spring Framework 3.1.0
- displaytag 1.2

Agora sim vamos as funcionalidades, basicamente elas são quatro: criação de projeto, scaffolding, criação de controlador e criação de modelo. Nos próximos itens estarei fazendo uma descrição mais detalhada de cada uma destas funções.

### Criação de Projeto

A criação de um projeto consiste apenas em descomprimir o arquivo que contém o projeto de base para aplicações. Neste projeto não existem controladores, views ou modelos. Apenas a estrutura básica de um projeto para a IDE NetBeans, além de uma série de classes utilizadas como base para a geração de aplicações e alguns arquivos de configuração para as frameworks/APIs.

### Scaffolding

Talvez uma das funcionalidades mais interessantes da ferramenta, apesar de ser pouco flexível. Assim como em Rails, é necessário apenas informar o nome da classe e os seus atributos e tipos correspondentes. Adicionalmente, a ferramenta permite adicionar o tamanho das variáveis e em caso de relacionamentos com outras classes, indicá-los.

### Criação de Controlador e Criação de Modelo

A criação de controlador e model é o que o nome diz, não existe mistério. Exceto que a criação de controladores com suas actions inclui a criação das views. Em uma versão futura adicionarei a opção de não criar uma view, o que algumas vezes pode ser conveniente. Quanto a criação de modelo, ele gera a entidade com validações, mapeamento, classe de acesso a dados e de serviços.

## Instalação do StrutsTool

O StrutsTool foi desenvolvido em Java, para fazer o download dele basta ir na [seção de downloads](http://code.google.com/p/strutstool/downloads/list) do projeto e fazer o download dos arquivos StrutsTool-x.x.tar.gz (baixe sempre a última versão) e o arquivolib.zip.

Descompacte o primeiro arquivo na pasta que achar mais conveniente. Dentro desta pasta haverá outra pasta chamada resources, coloque o arquivo lib.zip dentro dela. Para executar a ferramenta utilize o arquivostrutstool (Linux) ou strutstool.bat (Windows). Antes de executá-lo, abra-o e edite as variáveis nele declaradas, sendo elas: INSTALL_PATH (caminho da instalação do StrutsTool) e JAVA_BIN (caminho da pasta bin do Java (JRE ou JDK) ).

Para executar o comando sem precisar estar na pasta da ferramenta você precisa, no Linux, editar o arquivo .bashrc e adicionar as seguintes linhas:

``` bash
PATH=$PATH:/caminho/completo/para/strutstool
export PATH
```

No windows basta adicionar o caminho para o StrutsTool na variável de ambiente path.

## Criando um Aplicativo

Feita a instalação, é hora de começar a utilizar a ferramenta para criar um aplicativo de exemplo, para demonstrar o que a ferramenta é capaz de fazer. Nosso aplicativo vai ser algo simples, com duas tabelas, uma de produtos e outra de marcas.

Neste exemplo estarei utilizando o banco de dados PostgreSQL, mas qualquer outro banco suportado pelo Hibernate irá funcionar (contanto que sejam feitas as modificações no mapeamento, se necessário). O SQL para criar as tabelas é este:

``` sql
CREATE TABLE produto(
    id Serial NOT NULL,
    nome Character varying(100) NOT NULL,
    marca Integer NOT NULL
) WITH (OIDS=FALSE);

ALTER TABLE produto ADD CONSTRAINT pk_produto PRIMARY KEY (id);

CREATE TABLE marca(
    id Serial NOT NULL,
    nome Character varying(100) NOT NULL
) WITH (OIDS=FALSE);

ALTER TABLE marca ADD CONSTRAINT pk_marca PRIMARY KEY (id);

ALTER TABLE produto ADD CONSTRAINT fk_marca_produto FOREIGN KEY (marca) REFERENCES marca (id) ON DELETE NO ACTION ON UPDATE NO ACTION;
```

Criado o banco de dados, iremos agora criar um novo projeto com o StrutsTool. O comando para realizar esta operação é simples:

    strutstool new project loja com.app.web

Este comando cria o projeto base dentro da pasta loja/. Agora vamos criar o que realmente interessa nesta aplicação através do scaffolding. Primeiro vamos criar os providers (view, modelo e controlador) para a classe Produto, assim:

    strutstool scaffold Produto nome:string:100 marca:Marca

Após executar o comando deverá aparecer na tela todos os arquivos e pastas criados e/ou modificados. Vale ressaltar que o método scaffold parte da premissa de que o identificador da tabela é chamado id, sendo este um valor inteiro e auto-incremento (ou que use uma sequence, como neste caso (consulte a documentação do Hibernate)).

Outro aspecto importante é o das convenções adotadas pela ferramenta. No caso do Produto, é assumido (por padrão, você pode alterar) que o nome da tabela é produto e ela tem, além da chave primária id, um campo chamado nome de tamanho 100. Além disso, é criado um controlador chamado ProdutoControllercom as actions: index, edit, add e delete.

E nas views (pasta web/WEB-INF/) é criada a pasta produto com as páginas: index.jsp, edit.jsp e add.jsp (delete utiliza jQuery). A url para acesso a action index, por exemplo, ficaria assim:http://localhost:8084/loja/produto/index.action.

Estas são convenções básicas adotadas em várias frameworks conhecidas, exceto o Struts (além de outros frameworks Java), mas que através desta ferramenta poupam o desenvolvedor de configurar mais do que o necessário.

Agora vamos gerar os providers da classe Marca. Para tanto:

    strutstool scaffold Marca nome:string:100 produtos:set:Produto

Aqui adicionamos mais um atributo a classe, produtos:set:Produto, que corresponde ao relacionamento um para muitos (one-to-many) com a classe de Produto (uma marca tem vários produtos). O set do atributo corresponde ao tipo de Collection do Java java.util.Set. Este é o único tipo de coleção suportado pela ferramenta (não consegui fazer List funcionar com o Hibernate).

Por padrão, a ferramenta gera relacionamentos em duas direções, portanto a classe Produto poderá acessar a marca a qual pertence, bem como uma marca poderá acessar todos os seus produtos. Para quem notou, a ferramenta não checa os tipos de dados utilizados nos parâmetros. A bem da verdade, ela possui uma lista com os tipos básicos suportados e quando um tipo é desconhecido ela trata-o como um novo tipo, portanto cuidado ao informar os tipos de dados.

Outra funcionalidade que preferi desativar era a checagem das classes existentes. Neste exemplo a classe Produto referencia a classe Marca, sendo que esta última nem sequer existe ainda. Porém, me pareceu mais prático deixar a cargo do programador cuidar destes detalhes.

Por equanto esta ferramenta suporta apenas relacionamentos um para muitos (1:n) e muitos para um (n:1). Relacionamentos muitos para muitos (n:m) podem ser feitos com dois relacionamentos um para muitos.

Agora que a aplicação foi gerada, é hora de abrí-la com o NetBeans e fazer algumas modificações. Basta ir em “Abrir Projeto...” e selecionar a pasta loja. É provável que o NetBeans exiba um erro sobre o arquivo build-impl.xml, isto ocorre devido a uma modificação que é feita durante a criação do projeto. Basta clicar em Regenerar e prosseguir normalmente.

Com o projeto aberto precisamos fazer algumas coisas. Primeiro, adicionar o driver do banco de dados utilizado às bibliotecas do projeto. Depois disso, é preciso abrir o arquivo hibernate.cfg.xml e modificar as configurações do banco de dados com o qual o Hibernate irá se comunicar.

Ainda no arquivo de configurações do Hibernate, é preciso modificar o valor da propriedade com nome hibernate.search.default.indexBase. O valor desta propriedade é o caminho absoluto para a pasta onde o Lucene irá criar os índices para as buscas. Você deve criar a pasta (dar as permissões para escrita na pasta) e informar na propriedade o caminho completo para ela, no meu arquivo a propriedade ficou assim:

    /home/maycon/lucene/indexes

Seguindo todos estes passos e com o banco de dados tal qual o deste exemplo, basta executar a aplicação e você terá criado um pequeno aplicativo web com Struts sem muito sofrimento e com pouca configuração.

## Alguns Ajustes

Para aparar um pouco as arestas deste exemplo vou sugerir algumas modificações no código, especificamente na parte visual. A primeira diz respeito ao campo autocomplete que é criado no formulário de Produto e referencia Marca. Abra o arquivo form.jsp na pasta web/WEB-INF/produto/ e na tag troque o valor do atributo listValue de id para nome. Se você adicionou alguma marca poderá ver a lista de marcas, e a medida que for digitando as marcas irão sendo filtradas.

Talvez vocês tenham notado que na listagem de produtos, na coluna marca, ao invés de imprimir o nome da Marca está imprimindo algo como: com.app.web.model.entity.Marca@... Para resolver isto basta abrir a entidade Marca e sobreescrever o método toString, assim:

``` java
@Override
public String toString() {
    return nome;
}
```

A segunda modificação é apenas para dar um exemplo de como se criam links para actions. Vou colocar os links na página inicial. Para fazer isso abra o arquivo redirect.jsp na pasta web/WEB-INF/. Substitua o conteúdo do arquivo por isto:

``` html
<%@taglib uri="/struts-tags" prefix="s"%>
<h1>Hello World</h1>

<!-- generator:index -->
<s:url id="marcaIndex" action="index" namespace="/marca"></s:url>
<s:a href="%{marcaIndex}">Marca Index</s:a>

<s:url id="produtoIndex" action="index" namespace="/produto"></s:url>
<s:a href="%{produtoIndex}">Produto Index</s:a>
```

Acho que não há necessidade de maiores explicações sobre o código acima, para maiores detalhes existe a documentação do Struts 2, mais especificamente a referência das tags dele. Ainda sobre o arquivo redirect.jsp, se você precisar exibir como página inicial uma action, usê-o da seguinte forma:

``` html
<%
  response.sendRedirect("login/index.action");
%>
```

Assim você estará redirecionando o usuário para, neste exemplo, a action index do controlador de login.

### Lucene Search

O Lucene é uma engine de busca para textos muito poderosa, escrita em Java. Como foi colocado ateriormente, é preciso informar uma pasta onde o Lucene irá criar os índices para as buscas. Estes índices são criados automaticamente quando dados são inseridos, modificados ou removidos. Entretanto, caso você já possua dados no seu banco, terá de executar um comando uma vez na aplicação para criar o índice inicial. Para fazer isso com a tabela de marcas, por exemplo, você precisaria executar o seguinte código:

``` java
new MarcaRepositoryHibernate().createInitialLuceneIndex();
```

Pronto, índice criado, já é possível remover o código da aplicação. Espero futuramente inserir esta funcionalidade no StrutsTool.

## Para finalizar

Acredito que até o fim deste semestre estarei empenhado no desenvolvimento desta ferramenta, adicionando novas funcionalidades (testes unitários com JUnit e JMock, por exemplo) e, é claro, corrigindo eventuais (provavéis) bugs. Não sei se esta ferramenta terá grande utilidade para outras pessoas, estou desenvolvendo ela de acordo com minhas necessidades.

Espero que a ferramenta tenha alguma utilidade para quem esteja lendo isto. E para quem vai utilizá-la no desenvolvimento de aplicações recomendo logo abaixo alguns sites de referência para consulta, até porque a existência de um gerador não significa que você não deva saber como as coisas funcionam por baixo.

## Links Recomendados

- <http://www.mkyong.com/> – o mais completo site de tutoriais para Spring, Hibernate, Struts, …
- <http://vaannila.com/> – outro site com muitos bons tutoriais para Struts, Tiles, Validator e Hibernate
- <http://www.weinfreund.de/struts2-jquery-showcase/index.action> – vários exemplos de como utilizar o plugin jQuery para Struts
- <http://www.hibernate.org/docs>
- <http://www.hibernate.org/subprojects/search/docs>
- <http://www.hibernate.org/subprojects/validator/docs>
