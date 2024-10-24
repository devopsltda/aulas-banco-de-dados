# Aula 1 - Dúvidas Comuns

Esta aula tem como principal objetivo discutir e entender algumas práticas comuns no uso de SQL, que
geralmente são fontes de muitas dúvidas no início da carreira profissional. Os tópicos principais
nos quais ela vai focar são:

1. Por que e como usar tabelas intermediárias em bancos de dados?
2. Por que usar tipos específicos para colunas em bancos de dados e para que eles servem?
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

| ID | Nome | Cursos             |
| :- | :--- | :----------------- |
| 1  | José | Matemática, Física |

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
torna necessário um tratamento por fora do SQL para saber quais os cursos daquele usuário.

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

