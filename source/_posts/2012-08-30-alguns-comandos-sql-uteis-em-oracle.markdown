---
layout: post
title: "Alguns comandos SQL úteis em Oracle"
date: 2010-07-09 19:49
comments: true
categories: [Oracle, SQL]
---

Em um dos projetos de faculdade que participei foi utilizado o Oracle 10g XE para o desenvolvimento de um sistema de gestão de pessoas. Acostumado com MySQL tive algumas dificuldades em princípio para me adaptar. Abaixo deixo alguns comandos SQL que utilizei bastante.

<!-- more -->

## Paginação

Para quem está acostumado com o LIMIT e o OFFSET do MySQL e PostgreSQL, usar paginação no Oracle se torna chato, isso porque para limitar a quantidade de resultados que se deseja recuperar é preciso fazer uma sub-query. Assim sendo, o SQL ficaria desta forma:

``` sql
SELECT * FROM ( SELECT codigo, nome, row_number() OVER( ORDER BY codigo ) rn FROM pessoas ) WHERE rn >= 10 AND rn <= 20 ORDER BY rn;
```

Neste comando eu estou selecionando o código e o nome das pessoas ordenadas pelo seu código, à partir do registro 10 até o 20.

## Resgatar datas formatadas e inserir datas

Converter datas não chega a ser algo complicado ou que careça de informações a respeito, de qualquer forma abaixo estão os exemplos para selecionar datas formatadas e para inseri-lás:

``` sql Recuperar a data
SELECT TO_CHAR( campo_data, 'DD/MM/YYYY') FROM nome_tabela;
```

``` sql Recuperar a data e hora
SELECT TO_CHAR( campo_data, 'DD/MM/YYYY HH24:MI:SS') FROM nome_tabela;
```

Para salvar a data é preciso converter a string da seguinte forma:

``` sql
INSERT INTO nome_tabela VALUES( TO_CHAR( '05/10/2010 10:20:55', 'DD/MM/YYYY HH24:MI:SS') );
```

## Converter UNIX EPOCH DATE para Date

O módulo o qual eu estava responsável por desenvolver seria feito todo em PHP, e eu pensando em facilitar minha vida utilizei um sistema de autenticação que eu havia desenvolvido algum tempo atrás usando MySQL. E bom se fosse apenas trocar a connection string e banco utilizado na classe de abstração da aplicação.

Longe disso, meu problema estava no armazenamento das datas em epoch time. Eu precisava visualizar elas em formato de data, mas queria manter no banco de dados apenas o integer(11) para não precisar modificar as classes que faziam cálculos com datas.

Depois de algumas buscas encontrei uma solução:

``` sql
SELECT TO_CHAR( TO_DATE( '1970-01-01', 'YYYY-MM-DD' ) + numtodsinterval( campo_para_converter , 'SECOND' ), 'YYYY-MM-DD' ) AS data FROM nome_tabela GROUP BY TO_CHAR( TO_DATE( '1970-01-01', 'YYYY-MM-DD' ) + numtodsinterval( epoch_para_converter , 'SECOND' ), 'YYYY-MM-DD' )
```

## Auto incremento

Quem começa a usar Oracle sente, no começo, falta do checkbox de auto incremento ou do tipo de dados Serial do PostgreSQL. Este último funciona exatamente como o Oracle, só que de uma forma mais facilitada. No Oracle, é preciso criar uma sequence e depois uma trigger que irá adicionar o valor sequencial ao campo desejado em toda inserção realizada.

Temos a seguinte tabela:

``` sql
CREATE TABLE pessoas(
    codigo Integer NOT NULL,
    nome Varchar2(200) NOT NULL,
)
/
```

Agora precisamos criar uma sequence que servirá para a "criação do campo" auto-incremento:

``` sql
CREATE SEQUENCE seq_pessoas_codigo
 NOMAXVALUE
 NOMINVALUE
 NOCACHE
/
```

No comando acima eu não informei MINVALUE, MAXVALUE, START WITH e INCREMENT BY. Eles servem respectivamente para determinar o valor mínimo da sequência, o valor máximo que ela pode alcançar, por qual valor ela iniciará e de quanto em quanto este valor será incrementado. O cache da sequence serve para armazenar na memória determinada quantidade de valores futuros, o problema é que se ocorrer um problema os valores não utilizados serão perdidos e ocorrerá um gap entre o último valor salvo e o próximo a ser utilizado. Depois de criada a sequence é preciso criar o trigger que vai fazer a inserção do valor sequencial no campo especificado.

``` sql
CREATE OR REPLACE TRIGGER ts_pessoas_codigo_seq BEFORE INSERT
ON pessoas FOR EACH ROW
BEGIN
	SELECT seq_pessoas_codigo.nextval INTO :new.codigo FROM DUAL;
END;
/

CREATE OR REPLACE TRIGGER tsu_pessoas_codigo_seq AFTER UPDATE OF codigo
ON pessoas FOR EACH ROW
BEGIN
	RAISE_APPLICATION_ERROR(-20010,'Não foi possível atualizar a coluna codigo na tabela pessoas pois ela utiliza sequência.');
END;
/
```

No comando acima foram criadas duas triggers. A primeira vai inserir o próximo valor da sequência seq_pessoas_codigo no campo codigo da tabela pessoas antes da inserção de um registro ocorrer. Enquanto que a segunda trigger vai disparar um erro caso alguém tente atualizar  o campo codigo da tabela pessoas.

E  quem precisa resgatar o código inserido pode utilizar o comando abaixo, lembrando que se outros registros forem inseridos ao mesmo tempo corre-se o risco de recuperar o código errado, infelizmente desconheço outra solução.

``` sql
SELECT seq_pessoas_codigo.currval FROM dual;
```

De forma geral, o uso de sequences é bem fácil e na maior parte dos casos funciona muito bem, e pra quem acha chato ficar criando tantos comandos existem ferramentas de modelagem que se encarregam da criação das triggers, a única coisa que precisa ser feita é a vinculação da sequence com o campo desejado. Para quem se interessar, o TOAD Data Modeler faz o trabalho muito bem, pena que não seja gratuito.

Em outros casos o uso de sequences pode ser um problema, principalmente se não pode haver gaps entre os códigos sequenciais. Nestes casos acredito que a melhor forma seja criar uma tabela que armazene os próximos valores e que isso seja controlado através da aplicação.

O Oracle é sem dúvida alguma um dos melhores SGBDs no mercado e vale muito a pena fazer ao menos alguns testes com ele. Apesar dele possuir algumas peculiaridades, de forma alguma isso o torna mais complicado de utilizar. Existe muita documentação e quem quiser testar pode baixar a versão 10g XE que é gratuita para desenvolvimento.


## Links úteis:

- [Oracle 10g XE](http://www.oracle.com/technology/products/database/xe/index.html)
- [Documentações Oracle](http://www.oracle.com/technology/documentation/index.html)
