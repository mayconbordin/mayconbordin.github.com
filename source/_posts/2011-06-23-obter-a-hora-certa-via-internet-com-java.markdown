---
layout: post
title: "Obter a hora certa via Internet com Java"
date: 2011-06-23 18:06
comments: true
categories: [Java]
---

Se você precisa obter a hora atual com confiabilidade, é possível fazer isso através do protocolo NTP (Network Time Protocol). No mundo todo a hora certa é padronizada através do UTC (Coordinated Universal Time), no Brasil a definição da hora fica a cargo o Observatório Nacional, e através de um acordo entre este e o NIC.br a hora certa no Brasil é distribuída gratuitamente via Internet através do projeto NTP.br. Este projeto disponibiliza vários servidores NTP para a consulta da hora certa no Brasil.

Aqui irei mostrar um código muito simples em Java para a consulta da hora certa utilizando um dos servidores do NTP.br. Para utilizar o protocolo NTP vou fazer uso da biblioteca Apache Commons Net, [disponível aqui](https://commons.apache.org/net/download_net.cgi).

A lista de servidores do NTP.br pode ser obtida [neste link](http://ntp.br/). No exemplo abaixo eu estou usando apenas um dos servidores, mas dependendo da necessidade de cada um seria interessante verificar se o servidor está funcionando, e em caso negativo buscar a hora em outros servidores, de preferência servidores diferentes dos do NTP.br. Você pode encontrar servidores NTP [aqui](http://tf.nist.gov/tf-cgi/servers.cgi) e [aqui](http://www.pool.ntp.org/en/).

``` java
    try {
        String ntpServer = "a.st1.ntp.br";

        NTPUDPClient timeClient = new NTPUDPClient();
        InetAddress inetAddress = InetAddress.getByName(ntpServer);
        TimeInfo timeInfo = timeClient.getTime(inetAddress);
        long returnTime = timeInfo.getReturnTime();
        Date time = new Date(returnTime);
        System.out.println("Hora para " + ntpServer + ": " + time);
    } catch (UnknownHostException ex) {
        ex.printStackTrace();
    }   catch (IOException ex) {
        ex.printStackTrace();
    }
```

Como eu disse antes, você pode modificar o código para procurar a hora em vários servidores. É bom tratar estas exceptions também, chamei apenas o print por ser um exemplo simples. Considero muito útil buscar a hora em um servidor confiável principalmente quando da necessidade de se obter a hora correta, algo que pode não ser obtido quando a fonte é a máquina local. Isso é interessante quando pensamos em aplicações Java desktop rodando nas máquinas de clientes.
