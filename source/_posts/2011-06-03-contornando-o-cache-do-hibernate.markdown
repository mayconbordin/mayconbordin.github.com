---
layout: post
title: "Contornando o Cache do Hibernate"
date: 2011-06-03 14:28
comments: true
categories: [Java]
---

Dica rápida: quando você carrega um objeto utilizando Hibernate, modifica o mesmo e salva (utilizando ```update()```, ```merge()``` ou ```saveOrUpdate()```) e em outra funcionalidade de seu aplicativo obtém novamente o mesmo objeto através do Hibernate é comum que seja retornado o objeto em cache, com os valores desatualizados. Admito ter demorado mais de uma hora para encontrar uma alternativa que não fosse desabilitar o cache. Para a minha alegria, isso funcionou:

``` java
Objeto obj = (Objeto) session.get(Objeto.class, id);
session.refresh(obj);
```

Quando o método ```refresh()``` é chamado, ocorre a releitura do objeto no banco de dados, trazendo o último estado do objeto, ignorando o objeto em cache (ou atualizando, desconheço o funcionamento em detalhes). Testei os métodos ```flush()``` (semelhante ao ```commit()```) e ```clear()```, mas nenhum deles conseguiu resgatar o último estado do objeto. Ou é desconhecimento de minha parte, ou o Hibernate não atualiza o cache depois de realizada uma alteração em algum objeto da sessão. Se existe alguma forma de avisar o Hibernate sobre a alteração para que ele atualize o objeto em cache, desconheço.
