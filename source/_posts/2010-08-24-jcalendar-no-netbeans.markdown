---
layout: post
title: "JCalendar no NetBeans"
date: 2010-08-24 13:04
comments: true
categories: [Java]
---

Estou usando Java já faz alguns meses, e por estes dias me perguntei se havia algum datepicker disponível, deveria vir por padrão no Swing. Procurei na internet e encontrei várias soluções, algumas das melhores eram pagas, para variar. Foi então que eu encontrei o JCalendar, este me pareceu o mais bem apresentado dentre os free que encontrei.

A última versão data do início de 2009 mas, pensando bem, quantas melhorias mais um datepicker pode precisar? E o que mais me interessou foi a possibilidade de adicionar o JCalendar na paleta de componentes do Swing, até porque ficar codificando o datepicker de uma tela é perda de tempo.

<!-- more -->

Em primeiro lugar é preciso baixar o JCalendar, isso pode ser feito [aqui](http://www.toedter.com/download/jcalendar-1.4.zip) e, depois de descompactado, o único arquivo necessário será o jcalendar-1.4.jar que está dentro da pasta /lib.

Feito isto, abra o NetBeans e vá em Ferramentas > Paleta > Componentes Swing/AWT. Com isso, deve aparecer o gerenciador de paleta, assim:

![Alt text](/assets/images/jcalendar-1.png)

Com o gerenciador aberto clique em Nova Categoria (aqui New Category) e coloque o nome que preferir para referenciar a pasta onde irão ficar os componentes do JCalendar.

![Alt text](/assets/images/jcalendar-2.png)

Clique em Adicionar do JAR (ou Add from JAR) e selecione o arquivo baixado jcalendar-1.4.jar e clique em Próximo.

![Alt text](/assets/images/jcalendar-3.png)

Vão aparecer todos os componentes disponíveis dentro deste JAR, selecione todos e vá para o Próximo passo.

![Alt text](/assets/images/jcalendar-4.png)

É só escolher a categoria onde quer que estes componentes apareçam na paleta do Swing/AWT e conclui a instalação.

![Alt text](/assets/images/jcalendar-5.png)

O gerenciador da paleta vai aparecer mostrando os componentes adicionados a categoria, é só fechá-lo e ir para um JFrame o JDialog e arrastar os componentes para a tela.

![Alt text](/assets/images/jcalendar-6.png)

O resultado, usando o JDateChooser, deve ficar assim:

![Alt text](/assets/images/jcalendar-7.png)


[Documentação (JCalendar API)](http://www.toedter.com/en/jcalendar/api/index.html)
