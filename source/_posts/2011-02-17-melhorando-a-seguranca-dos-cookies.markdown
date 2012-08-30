---
layout: post
title: "Melhorando a segurança dos Cookies"
date: 2011-02-17 21:09
comments: true
categories: [PHP]
---

Estou desenvolvendo um sistema de administração básico com o Zend Framework para utilizar em meus projetos, também pretendo escrever algo sobre ele aqui, bem como disponibilizá-lo. Enfim, durante o seu desenvolvimento estava planejado a implementação da funcionalidade de lembrar-se do login no sistema.

Esta é uma funcionalidade muito utilizada e é algo muito prático para quem não aprecia digitar a senha dezenas de vezes (talvez por isso acabem as esquecendo). Apesar de ser uma mão na roda para muita gente, essa funcionalidade pode trazer consigo muitas implicações de segurança, estas citadas por Nicholas Zakas em seu blog e que eu vou resumir aqui.

<!-- more -->

- A primeira delas é o armazenamento de informações importantes sobre o usuário em texto plano, isso os torna vulneráveis a ataques de session hijacking, quando as informações do cookie são interceptadas pela rede (packet sniffing) e utilizadas para recriar o cookie.

- O roubo de cookies também pode ocorrer através de cross-site scripting (XSS). Este método consiste na injeção de código Javascript em uma página através de inclusão de scripts de terceiros que possam conter código malicioso ou através de entradas do usuário não filtradas. Muitos exemplos de XSS podem ser encontrados nesta cheat sheet. E quem não se lembra das falhas do orkut que possibilitavam o roubo de cookies?

- Além destes dois métodos, um terceiro é o cross-site request forgery (CSRF), neste a pessoa que faz o ataque é capaz de convencer o navegador a enviar uma requisição para um site o qual o usuário está logado. Isso pode ser feito tanto usando técnicas de XSS como HTML simples.

## Soluções?

Para evitar roubo de cookies é recomendado enviá-los somente através de conexões seguras (SSL). Como nem todo mundo possui certificado SSL, existem outras alternativas como a criação de uma chave de autenticação com dados do usuário e sempre requisitar a autenticação do usuário em procedimentos de alto nível (como a troca de senha ou email do usuário).

É importante ainda incluir apenas scripts confiáveis em seus sites, além de filtrar todos os dados provenientes dos usuários para evitar a injeção de código malicioso.

## HMAC

Dentre as soluções dispostas acima, uma delas é a HMAC (Hash-based Message Authentication Code), utilizada para construir um código de autenticação de mensagem (MAC) através de uma função hash e uma chave secreta. Através deste método é possível verificar tanto a integridade como a autenticidade da mensagem.Uma aplicação prática deste método pode ser vista no sistema de autenticação da Amazon SQS.

## Implementação

Abaixo é possível visualizar o esquema básico de funcionamento da criação e autenticação dos cookies utilizando HMAC.

![Alt text](/assets/images/security-cookies.jpg "Autenticação de Cookies com HMAC")

A implementação foi feita em PHP, e somente a parte correspondente a criação do cookie foi desenvolvida, sendo que a autenticação depende da arquitetura do sistema.

``` php
<?php

//Inclui as bibliotecas necessárias
require ('./lib/Browser/Browser.php');
require ('./lib/Ip.php');

//Instancia o objeto para obter os dados do navegador
$browser = new Browser();

//Cria uma hash com a challenge do usuário
//Poderia ser ainda a senha do usuário. Neste caso a challenge é uma chave criada
//através do sistema de login CHAP.
//http://en.wikipedia.org/wiki/Challenge-handshake_authentication_protocol
$userChallenge = hash('sha256', "User challenge hash");

//Nome do usuário
$userName = 'admin';

//Gera a chave que será usada para gerar a assinatura do usuário
$key = $browser->getBrowser().
	   $browser->getVersion().
	   $browser->getUserAgent().
	   $browser->getPlatform().
	   Ip::getIp();

//Transforma a chave em hash
//Essa chave precisa ser guardada em associação com o usuário para posterior
//verificação de autenticidade
$key = hash('sha256', $key);

//Une os dados do usuário
$data = $userChallenge . $userName;

//Cria a assinatura do usuário que servirá para autenticação
$signature = hash_hmac("sha256", $data, $key);

//Serializa os dados do cookie
$cookie = serialize(array($userName, $signature));

//Codifica o cookie
$cookie = base64_encode($cookie);

//Armazena o cookie
setcookie('remember_me', $cookie, time()+3600);

//=============================================
//Impressão dos dados
?>
<table border="1">
	<tr>
		<td>Mensagem:</td>
		<td><?= $data ?></td>
	</tr>
	<tr>
		<td>Chave:</td>
		<td><?= $key ?></td>
	</tr>
	<tr>
		<td>Assinatura:</td>
		<td><?= $signature ?></td>
	</tr>
</table>
```

Espero que os comentários estejam claros o suficiente para a compreesão do funcionamento do código. Todos os códigos, incluindo a classe Browser, podem ser baixados aqui.

De forma resumida, através desta implementação os cookies serão uma credencial que só será aberta com uma chave, sendo esta uma combinação de informações do computador do usuário. Isso, entretanto, não irá garantir com 100% de certeza que o cookie não poderá ser usado por outra pessoa em outro computador, mas fazer isso será muito mais complicado. A implementação também limita a "memória" do sistema de login, já que ele usa o IP do usuário como parte da chave para autenticar a credencial. Ao menos os dados do usuário estarão a salvo, desde que outras precauções citadas neste artigo também sejam tomadas.
