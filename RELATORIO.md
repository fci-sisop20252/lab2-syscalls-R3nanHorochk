# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
Pois eles atuam de formas diferentes enquanto o write te mais chamadas de sistema o 
printf funciona de forma mais rapida
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
O write pois você tem maior controle dos dados enviados e cada chamada dele é uma syscall direta
diferente do print na qual pode perder alguns dados 
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
Foi usado o file decriptor 3, e não usamos os outros pois eles são standarts do sistema e atua como
um novo dispositivo de entrada e saida
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Sabemos que ele foi lido completamente quando ele nos retorna 0 
```

**3. Por que verificar retorno de cada syscall?**

```
Para verificar se os dados retornados estão retornando corretamente
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000119 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |         82      |0.000203   |
| 64          |         21      |0.000119   |
| 256         |         6       |0.000070   |
| 1024        |         2       |0.000071   |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Com o  buffer maior ele pode ler mais dados de uma unica vez podemos pensar no buffer como uma
caixa para utilizarmos depois se esta caixa é pequena logo você tera que preenche-la mais vezes
se ela é grande você ira precisar preencher ela menos vezes
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
nem todas pois se o buffer for maior e a quantidade de bytes a ser lidos é menor então nem
sempre ele ira retornar um BUFFER_SIZE de bytes
```

**3. Qual é a relação entre syscalls e performance?**

```
As syscall são as interfaces entre o programa e o kernel para realizar operações como leitura
,escrita,alocação na memoria assim quando utilizamos a syscall temos acesso total ao hardware
pois estamos utilizando o kernel assim podemos melhorar pu atrapalahar a performance por meio 
do tipo de syscall que fazemos
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000228 segundos
- Throughput: 5842.24 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [ ] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Pois so assim temos certeza de que os bytes foram copiados
```

**2. Que flags são essenciais no open() do destino?**

```
 O_WRONLY | O_CREAT | O_TRUNC
- `O_WRONLY`: Abrir apenas para escrita
- `O_CREAT`: Criar arquivo se não existir
- `O_TRUNC`: Truncar arquivo existente (limpa o arquivo)
```

**3. O número de reads e writes é igual? Por quê?**

```
Caso o tamanho do buffer e do bytes lidos sejam iguais a partir do read então sim eles seriam iguais
pois leria a informação e escreve ela no arquivo
```

**4. Como você saberia se o disco ficou cheio?**

```
[Sua análise aqui]
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
Você perdera os dados pois apos terminar a execução ele ira fechar sem salvar os dados
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Elas retornam numeross para indicar se a operação foi realizada corretamente, elas funcionam a partir dos comandos como read() e outros ppara poder acessar o kernel
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
Os file decriptor são importantes pois eles são utilizados para saberem quais arquivos foram abertos funcionando como um indicador
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Quanto maior for o buffer mais espaço poderemos utilizar assim não iremos precisar fazer tantas
syscalls e assim teremos uma melhor perfomance mas temos de ter o equilibrio entre o tamanho do buffer para não fazer com que ele use muito do hardware podendo danificar
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** time ./ex4_copia

**Por que você acha que foi mais rápido?**

```
Pois ele não precisou fazer a chamada do CP
```

---

## 📤 Entrega
Certifique-se de ter:
- [X ] Todos os códigos com TODOs completados
- [X ] Traces salvos em `traces/`
- [X ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
