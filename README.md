# Organizador de comprovantes e mensagens de compras

Página de demonstração do extrator de **loja, cidade, valor, data e código** em
mensagens de confirmação de compra e de entrega, em português.

**Equipe 5** — 5º Seminário de Processamento de Linguagem Natural
Profa. Cláudia Rodrigues de Carvalho — Fatec Osasco, DSM

Integrantes: `_______`, `_______`, `_______`, `_______`, `_______`

🔗 **https://sarahquinteiro.github.io/organizador-comprovantes/**

---

## O que é

Uma mensagem de compra chega assim:

> Compra aprovada na Casa do Parafuso Ltda, em Osasco. Valor de R$ 149,90 pago
> em 12/03/2026. Pedido ABX-99120.

E sai assim:

| campo | saída |
|---|---|
| loja | `Casa do Parafuso Ltda` |
| cidade | `Osasco` |
| valor | `149.90` |
| data | `2026-03-12` |
| codigo | `ABX-99120` |

A página tem quatro abas: o **extrator**, onde se digita ou se cola qualquer
mensagem; as **57 mensagens** do corpus, com o gabarito de cada uma; os
**resultados** das medições; e os **16 testes** comentados, que rodam na hora
em que a aba abre.

Tudo roda dentro do navegador, sem servidor e sem instalar nada. As fontes
estão embutidas no arquivo, então a página também funciona offline — basta
baixar o `index.html` e abrir com um duplo clique.

## O método, dito com precisão

A distinção é fácil de errar, então vai explícita: **a equipe não treinou
modelo nenhum.**

O sistema é híbrido, com duas peças de origens diferentes:

1. Um **modelo estatístico supervisionado** de reconhecimento de entidades, o
   `pt_core_news_lg` do spaCy, treinado pela Explosion nos corpora WikiNER e UD
   Portuguese Bosque. Usamos pronto. Ele reconhece quatro categorias —
   `PER`, `LOC`, `ORG`, `MISC` — e **não tem categoria para dinheiro nem para
   data**. É por isso que valor, data e código não saem do modelo: saem de
   regra, por necessidade e não por conveniência.
2. **Regras escritas pela equipe**: expressões de forma para valor, data e
   código, e listas de consulta para loja e cidade.

### A página não roda o spaCy

O modelo tem cerca de 560 MB e é código Python. Não há como executá-lo no
navegador, e dizer o contrário seria falso. A solução foi honesta e está
visível na própria página:

- as **regras** foram reimplementadas em JavaScript, com o tokenizador do spaCy
  emulado, e conferidas contra a saída do Python em mais de 260 textos;
- as **entidades do modelo** vêm pré-calculadas em posição de caractere, para
  as mensagens que já estavam no corpus;
- para uma mensagem **digitada na hora**, só as regras rodam, e a página avisa
  isso num aviso amarelo em vez de fingir um resultado completo.

O interruptor **"usar o reconhecedor estatístico"**, no alto do extrator,
liga e desliga a contribuição do modelo. É a comparação B contra C da
apresentação, reproduzida ao vivo.

## Os números

Corpus de **57 mensagens inteiramente fictícias**, escritas pela equipe: 45 de
desenvolvimento e 12 de conjunto cego, estas últimas redigidas **depois** de as
regras estarem congeladas, com seis lojas que o extrator nunca tinha visto.
Nenhum dado real de pessoa ou de empresa foi usado.

Nas 45 mensagens de desenvolvimento, em taxa de mensagens com os cinco campos
corretos:

| configuração | taxa exata | F1 de loja |
|---|---|---|
| modelo sozinho, leitura ingênua | 4% | 0,400 |
| só regras | 87% | 0,917 |
| **híbrido (o que entregamos)** | **93%** | **0,960** |

Nas 12 mensagens cegas, cidade, valor, data e código ficaram com precisão e
cobertura iguais a 1,00. O campo loja caiu para F1 de **0,615** — quatro das
nove lojas presentes. Sete das doze mensagens saíram com os cinco campos
corretos, e a **precisão foi 1,00 nos dois conjuntos**: em 57 mensagens o
extrator não preencheu nenhum campo com informação errada.

A queda do campo loja é característica do problema, não defeito do código. Loja
é o único dos cinco campos que depende de **conhecer o mundo** e não de
reconhecer forma: não existe formato de nome de loja. Das 26 lojas do conjunto
de desenvolvimento, 18 estão no cadastro — deliberadamente parcial, para que as
outras 8 dependam do modelo e a comparação meça alguma coisa. Nenhuma das 6
lojas do conjunto cego está cadastrada.

## O que o sistema não faz

- **Não compreende as mensagens.** Reconhece formas e consulta listas. Acertar
  o valor total numa frase com três números é uma regra a respeito da palavra
  "total", não leitura.
- A lista de municípios tem 152 cidades, e o Brasil tem 5.570.
- O domínio é estreito: mensagem de compra brasileira, real como moeda, data no
  formato usado aqui.
- O F1 de 0,9029 que o spaCy declara para o `pt_core_news_lg` foi medido em
  texto da Wikipédia, não em comprovantes. Os números desta página são os
  nossos, medidos no nosso corpus.

## Conteúdo do repositório

| arquivo | o que é |
|---|---|
| `index.html` | a página inteira, com dados, fontes e lógica embutidos |
| `README.md` | este arquivo |

A página não é escrita à mão: os dados que ela carrega — listas, gazetteers,
entidades do modelo, gabaritos e métricas — são exportados do mesmo
`extrator.py` que roda no notebook, por um script de geração. Nenhum número foi
redigitado. O código-fonte do extrator, o notebook, o corpus, a documentação
técnica e o relatório fazem parte da entrega do seminário e não estão neste
repositório, que existe só para hospedar a demonstração.

## Como publicar uma versão nova

```bash
git add index.html
git commit -m "Atualiza a pagina"
git push
```

O GitHub Pages refaz a publicação sozinho, em um ou dois minutos, e o endereço
continua o mesmo.
