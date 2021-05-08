---
layout: post
title: Regex para refatoração
---
Uma regex bem montada pode ser de grande ajuda para o processo de refactor, e manutenção de código. Principalmente quando as ferramentas disponíveis nas IDEs, não atendem um caso específico. 

### Possíveis utilizações:
* Remoção de código desnecessário.
  * Métodos Get e Set.
  * Métodos vazios.
  * Métodos que chamam apenas a herança.
* Remoção de código comentado.
* Checagem e remoção de comandos que possam gerar erro.
* Blocos a serem refactorados.
* Substituição de Macros.
* Troca de ordem de parâmetros.

## Características e comandos regex

### Caracteres especiais
Permite pesquisar mais do que simples caracteres
```\ ^$ . | ? * + ( ) [ {```

### Modes Modifiers
Inclua os modificadores no início da regex
Coloque todos juntos para especificar múltiplos modificadores.
Ex. ```(?ismx)```
* ```(?i)``` case insensitive
* ```(?x)``` habilita modo free-spacing
* ```(?s)``` single line mode (dot match \r\n)

### Caracteres não impressos
Permite pesquisar por caracteres não visuais
* ```\t``` Tab
* ```\r``` Carriage Return
* ```\n``` Line Feed
* ```\a``` Bell
* ```\e``` Escape
* ```\f``` Form Feed
* ```\v``` Vertical Tab
* ```\cA``` Control+A (ASCII Control de A-Z)
* ```\u20AC``` Unicode
* ```\x{20AC}``` Unicode

### Shorthand de classes de caracteres
Representação "abreviada" de uma classe de caracteres geralmente utilizada
* ```\b [0-9]``` Dígito 
* ```\w [A-Za-z0-9_]``` Palavra de caractere
* ```\s [ \t\r\n\f]``` Espaço em branco
* ```\D [^\d]``` Exceto Dígito
* ```\W [^\w]``` Exceto Palavra de caractere
* ```\S [^\S]``` Exceto Espaço em branco

### Metacarcteres dentro de classe de caracteres
* ```[+*]``` combina + ou *
* ```[\\x]``` combina \ ou x
* ```[x^]``` combina x ou ^
* ```[]x]``` combina ] ou x
* ```[^]x]``` nega a combinação de ] ou x
* ```[-x]  [x-]``` combina x ou -
* ```[^-x]  [^x-]``` nega a combinação de x ou -

### Backreferences
Combina o mesmo texto previmanete encontrado e capturado pelo grupo indicado
```
<([A-Z][A-Z0-9]*)\b[^>]*>.*?</\1>
Testing <B><I>bold italic</I></B> text

([abc]+)=\1 Combina com cab=cab
([abc])+=\1 Não comnina com cab=cab
```

Suporta até 99 grupos
Parêntesis e backreference não podem ser usados dentro de classe de caracteres
Útil para remover palavras duplicadas
```\b(\w+)\s+\1\b``` Meu cão cão é grande
É possível nomear os grupos capturados
```(?'name'grupo) \k'name'```
```<(?'tag'[A-Z][A-Z0-9]*)\b[^>]*>.*?</\k'tag'>```

### Alternação
Semelhante a classe de caracteres
Combina um regex de um conjunto de regex
A ordem importa
```
cat|dog
cat|dog|mouse|fish
\b(cat|dog\b
\bcat|dog\b
\b(mais|de|seja)\b
```

### Caracteres literais
Um ou mais caracteres sem função espacial
a Jack is a boy
cat About cats and dogs
Observações 
1ª ocorrência a esquerda
Limites da palavra (word boundaries)
Pesquisar próximo

### Lookaround
"Olha" para os lados sem cossumir
Lookahead
```
q(?!u) não é o mesmo que q[^u]
q(?=u) quit
```

### Lookbehind
```
(?<!a)b cab bed debt
(?<=a)b cab bed debt
```

#### Repetições
Exemplos:
```
<.+> This is a <EM>first</EM> test
<.+?> This is a <EM>first</EM> test
<[^>]+> Alternativa sem backtracking
\b[1-9][0-9]{3}\b nº entre 1000 e 9999
```

### Repetições
Indica a quantidade dde repetições do token precedente na regex. Obs: todos greedy
	* ```*``` 0 ou mais vezes
	* ```+``` 1 ou mais vezes
	* ```?``` 0 ou 1 vezes
	* ```{n}``` Exatamente n vezes
	* ```{n,}``` n ou mais vezes
	* ```{n,m}``` no mínimo n e no máximo m vezes

### Itens opcionais
Torna opcional o token precedente
Greedy por padrão
Use ? para tornar Lazy
```
colou?r combina com color ou colour
Nov(ember)? combina com Noc ou November
Feb 23(rd)? Today is Feb 23rd, 2003
Feb 23(rd)?? Today is Feb 23rd, 2003
```

### Âncoras
Âncoras não combinam com nenhum caractere, em vez disso, combinam com a posição antes, depois, ou entre caracteres
* ```^``` começo da linha
* ```\A``` começo do arquivo
* ```$``` final da linha
* ```\Z``` final do arquivo
* Limites de palavra (is This island is beautiful)
  ```
  \bis This island is beautiful
  is\b This island is beautiful
  \bis\b This island is beautiful
  \B Versão negada de \b
  ```

### Classes ou conjuntos de caracteres
Combina apenas um caractere do conjunto
A ordem não importa
```
Metacaracteres ] \ ^ - 
Sou[zs]a combina com Souza ou Sousa
gr[ae]y combina com gray ou grey
[0-9] único dígito entre 0 e 9
[A-Za-z] única letra entre A e Z ou a e z
[^0-9\r\] qualquer caractere exceto dígito e quebra de linha
```

### Branch Reset Groups
Permite comportilhar o mesmo grupo de captura dentro de um branch
Sintaxe (?|regex)
```
(?|(a)|(b)|(c))\1 aa ou bb ou cc
```

### Free-Spacing
No modo free-spacing, espaço em branco, tabulações e quebra de linhas entre os tokens da Regex são ignorados
Também é possível adicionar comentários
```
(?>atomic) (?> ato mi c)
```

### Agrupamento e Captura
Coloque entre parêntesis parte da regex para agrupála e capturar a combinação
Permite aplicar quantificadores para o grupo ou restringir a alteração da parte da regex
```
(\d\d)/(\d\d)/(\d{4}) 02/25/1982
\2-\1-\3 25/02/1982
Set(?:Value)? Sem captura(otimizado)
```

### Ponto
Combina com "quase" qualquer caractere
Não combina com quebra de linha
Alguns editores permitem seleciona a opção para combinal com quebra de linha (não padrão)
Se não houver opção, use a classe de caracteres ```[\s\S]```

Softwares
* grep
* PowerGREP
* RegexBuddy
* RegexMagic
* SGBDs (Oracle, PostgreSQL, MySQL)





