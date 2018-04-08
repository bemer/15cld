# Orquestração e Automação de Serviços - 15CLD

![fiap logo](/images/fiap_logo.jpg)

Bem vindo ao material de laboratório elaborado para o curso de **Orquestração e Automação de Serviços** da turma **15CLD**!

Aqui você vai encontrar todo o conteúdo e passos para a execução dos laboratórios em sala de aula, utilizando os conceitos apresentados pela disciplina.

## Importante

* Este laboratório é realizado de forma incremental, ou seja, para que o passo 2 seja executado o passo 1 precisa ter sido finalizado, e assim sucessivamente, portanto: **siga o laboratório na ordem exata**.
* Caso durante a execução do laboratório você encontre alguma dúvida ou problema, não exite em **chamar o professor**.
* Estes laboratórios foram criados levando em consideração a última versão disponível de cada uma das ferramentas.
* O objetivo dos laboratórios não é simplesmente ter tudo funcionando em nosso ambiente, mas sim explorar um pouco o tema: **Como as coisas funcionam**.
* A troca de conhecimentos é um dos ativos mais importantes em qualquer curso, desta maneira sugestões e feedbacks sobre o material escrito são sempre bem vindos.

## Índice

Você deverá executar o laboratório na seguinte ordem:


= Deploying Microservices on AWS Cloud
:toc:

This repo contains a simple application that consists of three microservices. The sample application uses three services:

. `webapp`: Web application microservice calls `greeting` and `name` microservice to generate a greeting for a person.
. `greeting`: A microservice that returns a greeting.
. `name`: A microservice that returns a person’s name based upon `{id}` in the URL.
