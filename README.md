# Resolução de Race Condition com Semáforo ou Mutex

## ETAPA 1 - CRIAÇÃO DE CÓDIGO E AVALIAÇÃO CRUZADA

### Análise estática do código do João Carvalho (feita por João Favato):

O código simula uma conta bancária com saldo inicial de 1000, acessada simultaneamente por duas Threads, uma que realiza 10 saques de 50 e outra que realiza 10 depósitos de 50. Como o número de saques e depósitos é igual, o saldo final deveria ser 1000, contudo, devido à falta de proteção no acesso da variável global, o resultado final é corrompido. Tal comportamento é comprovado pela saída no terminal:

### Análise estática do código do João Favato (feita por João Carvalho):

No programa, a operação de reposição adiciona 10 unidades à variável global, enquanto a operação de venda retira 10 unidades. Como elas são executadas a mesma quantidade de vezes, o resultado esperado era de que a variável global não mudasse seu valor, mas a “Race Condition” faz com que isso não seja verdade. Pode-se observar que a thread de vendas se sobrepõe mais vezes à de reposição, fazendo com que o resultado no final seja 20 unidades menor do que o esperado.

## ETAPA 2 - PLANEJAMENTO DE TESTES

### Planejamento de testes do código do João Carvalho (feito por João Favato)

Todos os teste utilizam as mesmas pré-condições: Utilize a placa FRDM-KL25Z, e rode o código no ambiente platform.io com o zephyr 4.2. Certifique-se que os includes estão com os caminhos de diretórios corretos, o prj.conf tenha as configurações para a utilização de logs e que a placa acabou de ser ligada, sem ter nenhum lixo na memória ou variáveis que já estavam em andamento.

#### Teste 1
Etapas: 
1 - Deixe as duas threads com a mesma prioridade (ex: 1).

2 - Coloque a variável ‘saldo_conta’ em 2000.

3 - Coloque o número de operações em 10.

4 - Deixe o valor de transação para ambas as threads em 100.

Pós-condição: Como esperado, o código apresentou ‘race condition’, ou seja, mesmo que o saldo devesse permanecer em 2000, a memória foi sobrescrita em alguns momentos e gerou uma discrepância

#### Teste 2
Etapas: 
1 - Deixe as duas threads com a mesma prioridade (ex: 1). 

2 - Coloque a variável ‘saldo_conta’ em 10000. 

3 - Coloque o número de operações em 20. 

4 - Deixe o valor de transação para ambas as threads em 1000.

Pós-condição: Novamente, o código apresentou ‘race condition’, já que a ação de saque continua sobrescrevendo os depósitos.


#### Teste 3
Etapas: 
1 - Deixe as duas threads com a mesma prioridade (ex: 1).

2 - Coloque a variável ‘saldo_conta’ em 1000. 

3 - Coloque o número de operações em 20.

4 - Deixe o valor de transação para a thread de depósito em 100.

5 - Deixe o valor de transação para a thread de saque em 10.

Pós-condição: Mesmo com a discrepância entre os valores de depósito e saque, é sempre o saque se sobrescreve ao depósito, o que é comprovado pela evidência do terminal:

### Tabela Rotina 1: Planejamento de Testes (Sem Mutex) – Código do João Carvalho (Conta Bancária)

| Caso de Teste | Pré-condição | Etapas de Teste | Pós-condição Esperada / Obtida |
| :--- | :--- | :--- | :--- |
| **1** | Placa FRDM-KL25Z recém-ligada sem lixo na memória, PlatformIO com Zephyr 4.2, prj.conf com logs habilitados e caminhos de includes corretos[cite: 2]. | 1. Deixar as duas threads com a mesma prioridade (ex: 1)[cite: 2];<br>2. Colocar a variável `saldo_conta` em 2000[cite: 2];<br>3. Colocar o número de operações em 10[cite: 2];<br>4. Deixar o valor de transação para ambas as threads em 100[cite: 2]. | Presença de Race Condition: sobrescrita de memória gerando discrepância no saldo final (Esperado: R$ 2000, Real: R$ 1000)[cite: 2]. |
| **2** | Placa FRDM-KL25Z recém-ligada sem lixo na memória, PlatformIO com Zephyr 4.2, prj.conf com logs habilitados e caminhos de includes corretos[cite: 2]. | 1. Deixar as duas threads com a mesma prioridade (ex: 1)[cite: 3];<br>2. Colocar a variável `saldo_conta` em 10000[cite: 3];<br>3. Colocar o número de operações em 20[cite: 3];<br>4. Deixar o valor de transação para ambas as threads em 1000[cite: 3]. | Presença de Race Condition: saques continuam sobrescrevendo depósitos (Esperado: R$ 10000, Real: R$ -10000)[cite: 3]. |
| **3** | Placa FRDM-KL25Z recém-ligada sem lixo na memória, PlatformIO com Zephyr 4.2, prj.conf com logs habilitados e caminhos de includes corretos[cite: 2]. | 1. Deixar as duas threads com a mesma prioridade (ex: 1)[cite: 3];<br>2. Colocar a variável `saldo_conta` em 1000[cite: 3];<br>3. Colocar o número de operações em 20[cite: 3];<br>4. Valor de transação para depósito em 100[cite: 3];<br>5. Valor de transação para saque em 10[cite: 3]. | Presença de Race Condition: saque se sobrescreve ao depósito (Saldo Real obtido: R$ 9800)[cite: 3]. |

### Planejamento de testes do código do João Favato (feito por João Carvalho)

#### Teste 1
Pré-condição: A placa foi ligada e reiniciada pelo botão de “reset”, para garantir que não houvesse nenhum lixo de memória.

Etapas:
1 - Deixe as duas Threads com a mesma prioridade (7);

2 - Defina o número de operações como 10;

3 - Defina o valor de cada operação como 10;

4 - Defina o estoque disponível como 100.

Pós-condição: Nos testes realizados, foi possível observar que a Thread de vendas sobrescreve a Thread de estoque, deixando o saldo final menor do que o esperado, como é evidenciado abaixo:

#### Teste 2

Pré-condição: A placa foi ligada e reiniciada pelo botão de “reset”, para garantir que não houvesse nenhum lixo de memória[cite: 4].

Etapas: 
1 - Deixe as duas Threads com a mesma prioridade (1);

2 - Defina o número de operações como 20; 

3 - Defina o valor de cada operação como 20;

4 - Defina o estoque disponível como 200.

Pós-condição: Novamente, houve o Race Condition com a Thread de vendas sobrescrevendo a Thread de estoque, deixando o saldo final 100 unidades menor do que o esperado, como pode ser evidenciado abaixo:


#### Teste 3

Pré-condição: A placa foi ligada e reiniciada pelo botão de “reset”, para garantir que não houvesse nenhum lixo de memória[cite: 5].

Etapas:
1 - Deixe as duas Threads com a mesma prioridade (1);

2 - Defina o número de operações como 30;

3 - Defina o valor de cada operação como 60;

4 - Defina o estoque disponível como 1000.

Pós-condição: Novamente, houve o Race Condition com a Thread de vendas sobrescrevendo a Thread de estoque, como pode ser evidenciado abaixo:[cite: 5]

### Tabela Rotina 2: Planejamento de Testes (Sem Mutex) – Código do João Favato (Estoque)

| Caso de Teste | Pré-condição | Etapas de Teste | Pós-condição Esperada / Obtida |
| :--- | :--- | :--- | :--- |
| **1** | A placa foi ligada e reiniciada pelo botão de "reset", para garantir que não houvesse nenhum lixo de memória[cite: 4]. | 1. Deixar as duas Threads com a mesma prioridade (7)[cite: 4];<br>2. Defina o número de operações como 10[cite: 4];<br>3. Defina o valor de cada operação como 10[cite: 4];<br>4. Defina o estoque disponível como 100[cite: 4]. | Presença de Race Condition: Thread de vendas sobrescreve a de estoque (Estoque esperado: 100, Estoque real: 80)[cite: 4]. |
| **2** | A placa foi ligada e reiniciada pelo botão de "reset", para garantir que não houvesse nenhum lixo de memória[cite: 4]. | 1. Deixar as duas Threads com a mesma prioridade (1)[cite: 4];<br>2. Defina o número de operações como 20[cite: 4];<br>3. Defina o valor de cada operação como 20[cite: 4];<br>4. Defina o estoque disponível como 200[cite: 4]. | Presença de Race Condition: Thread de vendas sobrescreve a de estoque (Estoque esperado: 200, Estoque real: 100)[cite: 4, 5]. |
| **3** | A placa foi ligada e reiniciada pelo botão de "reset", para garantir que não houvesse nenhum lixo de memória[cite: 5]. | 1. Deixar as duas Threads com a mesma prioridade (1)[cite: 5];<br>2. Defina o número de operações como 30[cite: 5];<br>3. Defina o valor de cada operação como 60[cite: 5];<br>4. Defina o estoque disponível como 1000[cite: 5]. | Presença de Race Condition: Thread de vendas sobrescreve a de estoque (Estoque esperado: 1000, Estoque real: 820)[cite: 5]. |


## ETAPA 3 - CORREÇÃO E RETESTE

### Justificativa para o uso de Mutex em ambos os códigos

Como pode ser visto nos códigos a seguir, o problema da "Race condition" pode ser resolvido através da implementação do "Mutex", que deriva de Mutual Exclusion (Exclusão Mútua). Ele trata-se de uma chave de acesso, gerenciada pelo kernel do zephyr RTOS, que permite apenas a thread detentora da chave no momento executar determinada porção crítica do código.

Para isso, usam-se os comandos `k_mutex_lock()`, antes de começar a seção crítica do código, e `k_mutex_unlock()` para liberar o recurso para outras threads[cite: 6]. Quando uma thread tenta executar o `k_mutex_lock()` sendo que uma função ainda não deu o "unlock", tal thread ficará em modo "BLOCKED", até que o recurso seja liberado, quando ela se tornará modo "READY".

Com a implementação do `k_mutex`, cada operação ocorre de maneira estritamente sequencial, prevenindo eventuais sobrescrições na memória.

### Análise e reteste do código do João Carvalho (feito por João Favato)

Todos os testes utilizam as mesmas pré-condições: Utilize a placa FRDM-KL25Z, e rode o código no ambiente platform.io com o zephyr 4.2[cite: 6]. Certifique-se que os includes estão com os caminhos de diretórios corretos, o prj.conf tenha as configurações para a utilização de logs e que a placa acabou de ser ligada, sem ter nenhum lixo na memória ou variáveis que já estavam em andamento.

#### Teste 1

Etapas:
1 - Deixe as duas threads com a mesma prioridade (ex: 1).

2 - Coloque a variável 'saldo_conta' em 2000.

3 - Coloque o número de operações em 10.

4 - Deixe o valor de transação para ambas as threads em 100.

Pós-condição: Dessa vez, os resultados do teste mostram que o código devolveu o saldo correto, atestando, assim, que as alterações no código consertaram o problema da 'race condition'.

#### Teste 2

Etapas:
1 - Deixe as duas threads com a mesma prioridade (ex: 1) 

2 - Coloque a variável 'saldo_conta' em 10000.  

3 - Coloque o número de operações em 20. 

4 - Deixe o valor de transação para ambas as threads em 1000.

Pós-condição: Mesmo mudando os valores das variáveis envolvidas nos cálculos, como o saldo da conta, o número de operações e o valor de cada operação da thread, o código continua funcionando corretamente e devolve o valor esperado.

#### Teste 3

Etapas:
1 - Deixe as duas threads com a mesma prioridade (ex: 1).

2 - Coloque a variável 'saldo_conta' em 1000.

3 - Coloque o número de operações em 20.

4 - Deixe o valor de transação para a thread de depósito em 100.

5 - Deixe o valor de transação para a thread de saque em 10.

Pós-condição: Como nesse caso as operações dão valores diferentes, o saldo foi aumentado nos esperados R$ 1800,00, 20 operações com saldo líquido de +90, assim chegando ao resultado final sem 'race condition'.

### Tabela Rotina 3: Correção e Reteste (Com Mutex) – Código do João Carvalho (Conta Bancária)

| Caso de Teste | Pré-condição | Etapas de Teste | Pós-condição Esperada / Obtida |
| :--- | :--- | :--- | :--- |
| **1** | Placa FRDM-KL25Z, Zephyr 4.2 no PlatformIO, logs habilitados, sem lixo de memória e com `k_mutex` implementado[cite: 6]. | 1. Deixar as duas threads com a mesma prioridade (ex: 1)[cite: 6];<br>2. Colocar a variável `saldo_conta` em 2000[cite: 6];<br>3. Colocar o número de operações em 10[cite: 6];<br>4. Deixar o valor de transação para ambas as threads em 100[cite: 6]. | Sucesso: sem Race Condition, saldo correto (Saldo esperado: R$ 2000, Saldo real: R$ 2000)[cite: 6]. |
| **2** | Placa FRDM-KL25Z, Zephyr 4.2 no PlatformIO, logs habilitados, sem lixo de memória e com `k_mutex` implementado[cite: 6]. | 1. Deixar as duas threads com a mesma prioridade (ex: 1)[cite: 7];<br>2. Colocar a variável `saldo_conta` em 10000[cite: 7];<br>3. Colocar o número de operações em 20[cite: 7];<br>4. Deixar o valor de transação para ambas as threads em 1000[cite: 7]. | Sucesso: sem Race Condition, saldo correto (Saldo esperado: R$ 10000, Saldo real: R$ 10000)[cite: 7]. |
| **3** | Placa FRDM-KL25Z, Zephyr 4.2 no PlatformIO, logs habilitados, sem lixo de memória e com `k_mutex` implementado[cite: 6]. | 1. Deixar as duas threads com a mesma prioridade (ex: 1)[cite: 7];<br>2. Colocar a variável `saldo_conta` em 1000[cite: 7];<br>3. Colocar o número de operações em 20[cite: 7];<br>4. Valor de transação para depósito em 100[cite: 7];<br>5. Valor de transação para saque em 10[cite: 7]. | Sucesso: sem Race Condition, saldo correto (Saldo inicial: R$ 1000, Saldo final: R$ 2800)[cite: 7]. |

### Análise e reteste do código do João Favato (feito por João Carvalho)

A pré-condição de todos os testes foi rigorosamente a mesma, ligamos a placa e reiniciamos pelo botão "reset", garantindo que não houvesse nenhum lixo de memória[cite: 7].

#### Teste 1

Etapas:
1 - Deixe as duas Threads com a mesma prioridade (7);

2 - Defina o número de operações como 10;

3 - Defina o valor de cada operação como 10;

4 - Defina o estoque disponível como 100.

Pós-condição: Depois de realizar o primeiro teste, foi verificado que o resultado final foi exatamente o resultado esperado, graças à eliminação do race condition pelo mutex, como pode ser visto pelo output abaixo:


#### Teste 2

Etapas:
1 - Deixe as duas Threads com a mesma prioridade (1);  

2 - Defina o número de operações como 20;

3 - Defina o valor de cada operação como 20;

4 - Defina o estoque disponível como 200.

Pós-condição: Novamente, o valor do estoque final foi exatamente o esperado, evidenciando que a utilização de mutex é válida e funcional para solucionar problemas de Race Condition:

#### Teste 3

Etapas: 
1 - Deixe as duas Threads com a mesma prioridade (1);

2 - Defina o número de operações como 30; 

3 - Defina o valor de cada operação como 60;

4 - Defina o estoque disponível como 1000.

Pós-condição: A saída (output) provou, mais uma vez, que o uso de Mutex é uma solução válida contra Deadlocks e Race Conditions.

### Tabela Rotina 4: Correção e Reteste (Com Mutex) – Código do João Favato (Estoque)

| Caso de Teste | Pré-condição | Etapas de Teste | Pós-condição Esperada / Obtida |
| :--- | :--- | :--- | :--- |
| **1** | A placa foi ligada e reiniciada pelo botão de "reset", com `k_mutex` implementado para exclusão mútua[cite: 7, 8]. | 1. Deixar as duas Threads com a mesma prioridade (7)[cite: 8];<br>2. Defina o número de operações como 10[cite: 8];<br>3. Defina o valor de cada operação como 10[cite: 8];<br>4. Defina o estoque disponível como 100[cite: 8]. | Sucesso: integridade mantida (Estoque esperado: 100, Estoque real: 100)[cite: 8]. |
| **2** | A placa foi ligada e reiniciada pelo botão de "reset", com `k_mutex` implementado para exclusão mútua[cite: 7, 8]. | 1. Deixar as duas Threads com a mesma prioridade (1)[cite: 8];<br>2. Defina o número de operações como 20[cite: 8];<br>3. Defina o valor de cada operação como 20[cite: 8];<br>4. Defina o estoque disponível como 200[cite: 8]. | Sucesso: integridade mantida (Estoque esperado: 200, Estoque real: 200)[cite: 8]. |
| **3** | A placa foi ligada e reiniciada pelo botão de "reset", com `k_mutex` implementado para exclusão mútua[cite: 7, 9]. | 1. Deixar as duas Threads com a mesma prioridade (1)[cite: 8];<br>2. Defina o número de operações como 30[cite: 8];<br>3. Defina o valor de cada operação como 60[cite: 8];<br>4. Defina o estoque disponível como 1000[cite: 9]. | Sucesso: integridade mantida (Estoque esperado: 1000, Estoque real: 1000)[cite: 9]. |

## ETAPA 4 - AVALIAÇÃO INTERNA

### Avaliação (João Favato)

Na primeira versão do meu código, observava-se a ocorrência de 'race condition', já que as duas threads acessam uma mesma variável global e acabavam fazendo alterações que se sobrescreviam. No caso do meu código, a operação de 'venda de estoque' era aquela que sempre atualizava a memória depois, ignorando as alterações realizadas pela 'reposição do estoque'.

Tal comportamento foi corrigido através do uso do mutex, que funciona como uma "chave", garantindo que as duas threads não acessassem uma variável global ao mesmo tempo. Dessa maneira, o código passou a apresentar um comportamento estável, como verificado pelos testes realizados anteriormente.

### Avaliação (João Carvalho)

**O que estava errado antes?**  
Na versão original, ambas as Threads que compunham meu programa disputavam (com a mesma prioridade) a mesma variável global, fazendo com que os resultados fossem incertos, visto que, por muitas vezes, uma Thread sobrescrevia outra.

**O que mudou com a correção?**  
Depois de adicionar o mutex, as Threads pararam de tentar acessar a mesma variável ao mesmo tempo, já que o mutex tem como finalidade bloquear o acesso enquanto outra está acessando. Em suma, o código começou a apresentar saídas coerentes com a lógica do programa.

**O comportamento agora é estável?**  
Sim, agora é possível saber qual será o valor do output mesmo sem acompanhar o monitoramento do programa.
