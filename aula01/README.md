# Aula 1 - Dúvidas Comuns

Esta aula tem como principal objetivo discutir e entender algumas práticas comuns no uso de SQL, que
geralmente são fontes de muitas dúvidas no início da carreira profissional. Os tópicos principais
nos quais ela vai focar são:

1. Por que e como usar tabelas intermediárias em bancos de dados?
2. Por que usar tipos especializados para colunas em bancos de dados e para que eles servem?
3. Quais ferramentas posso usar para auxiliar no desenvolvimento relacionado a banco de dados?

Essas perguntas serão respondidas primeiramente com uma curta explicação teórica, seguida de
pequenos exemplos práticos para melhor entendimento.

## 1. Por que e como usar tabelas intermediárias em bancos de dados?

**O que são?**

Tabelas intermediárias são tabelas que representam relações N para N entre duas ou mais entidades (
tabelas "principais").

**Por que não criar várias linhas em cada tabela "principal"?**

Porque isso resulta em três problemas principais:

- Duplicação de dados, consumindo mais armazenamento sem necessidade;
- Inconsistência de dados, onde alguns valores classificadores apenas existem dentro de linhas
específicas de alguma entidade;
- É muito comum que relações N para N não armazenem apenas as referências às entidades, mas também
valores relevantes àquela relação.

Exemplo:

Tabela de Alunos

| ID | Nome | Cursos            |
| :- | :--- | :---------------- |
| 1  | José | Matemática,Física |

Problema: Os cursos Matemática e Física apenas existem nas linhas da Tabela Alunos.

Solução:

Tabela de Alunos

| ID | Nome |
| :- | :--- |
| 1  | José |

Tabela de Cursos

| ID | Nome       |
| :- | :--------- |
| 1  | Física     |
| 2  | Matemática |

Tabela Intermediária Alunos_Cursos

| ID | ID_Aluno | ID_Curso |
| :- | :------- | :------- |
| 1  | 1        | 1        |
| 2  | 1        | 2        |

Dessa forma, os Cursos e os Alunos se tornam entidades diferentes, onde não há dependência
existencial entre elas. Além disso, podemos armazenar dados específicos daquele relacionamento, como
o número de matrícula do aluno no curso e quantidade de faltas, por exemplo.

**Isso não dificulta a consulta do SQL?**

Depende. Existe um momento em que o uso de múltiplas tabelas intermediárias se torna maçante, pois
cada uma delas exige um `JOIN`.

Exemplo:

Consulta dos cursos de um aluno na tabela Alunos inicial

```sql
SELECT cursos FROM alunos WHERE id = ?;
```

Problema: Veja que dessa forma os cursos vão vir praticamente em formato `.csv`, o que geralmente
torna necessário um tratamento por fora do SQL para saber quais os cursos daquele usuário. Algo como
o trecho abaixo:

```js
const resultado_cursos = resultado_consulta;
const cursos = resultado_cursos.split(',');
```

Solução:

Consulta dos cursos de um aluno na tabela Alunos normalizada

```sql
SELECT cursos.nome
FROM alunos
     INNER JOIN alunos_cursos ON alunos_cursos.id_aluno = alunos.id
     INNER JOIN cursos ON cursos.id = alunos_cursos.id_curso
WHERE alunos.id = ?;
```

Assim, conseguimos consultar os cursos de um usuário específico, um por linha, sem precisar de
nenhum tratamento fora do SQL.

**Resumo**

Usamos tabelas intermediárias para lidar com relações N para N de forma eficiente, evitando
redundâncias e inconsistências. Elas facilitam a vida do desenvolvedor pois passam maior parte do
processamento e validação para o próprio banco de dados, que geralmente é extremamente otimizado e
confiável.

**Aprofundamento**

- [Tipos de JOIN][1]
- [Normalização de Bancos de Dados][2]

## 2. Por que usar tipos especializados para colunas em bancos de dados e para que eles servem?

**O que são?**

Os tipos especializados são aqueles que atendem escopos de áreas específicas.

**Resumo**

Usamos tipos especializados para delegar mais responsabilidades ao banco de dados e economizar tempo
de desenvolvimento.

**Aprofundamento**

- [Tipos Específicos do PostgreSQL][3]

## 3. Quais ferramentas posso usar para auxiliar no desenvolvimento relacionado a banco de dados?

**Quais?**

- [dbdiagram][4]: permite modelar bancos de dados numa linguagem própria, exportando para os bancos
de dados mais populares, além de permitir o compartilhamento entre a equipe.
- [DBeaver][5] e [DataGrip][6]: ferramentas gerais de bancos de dados, que permitem manipulação de
bancos de dados, conexão a múltiplos bancos, avaliação de performance, exportação dos dados, geração
de *dump*, entre outros;
- [sqlc][7]: cria interfaces e funções para operações em bancos de dados baseado em código SQL;
- [Prisma][8]: como um ORM, oferece toda uma estrutura de versionamento, migrações, construtor de
consulta, etc;
- [Knex.js][9]: é um construtor de consultas que permite escrever SQL via composição de funções.


<!-- Referências -->

[1]: https://tembo.io/docs/getting-started/postgres_guides/all-possible-joins-in-postgres
[2]: https://medium.com/@ndleah/a-brief-guide-to-database-normalization-5ac59f093161
[3]: https://www.postgresql.org/docs/current/datatype.html
[4]: https://dbdiagram.io/home
[5]: https://dbeaver.io/
[6]: https://www.jetbrains.com/pt-br/datagrip/
[7]: https://sqlc.dev/
[8]: https://www.prisma.io/
[9]: https://knexjs.org/
