# Implementa-o-de-Criptografia-RSA-em-Rust

# Mini Aula Explicativa:

## 1. Introdução ao RSA

O **RSA** (Rivest-Shamir-Adleman) é um algoritmo de **criptografia assimétrica** desenvolvido em 1977 e é um dos sistemas de chave pública mais antigos e amplamente utilizados.

Suas características principais incluem:

*   **Assimetria:** Utiliza duas chaves diferentes — uma chave pública (compartilhável) e uma chave privada (secreta).
*   **Bidirecionalidade:** Pode ser usado tanto para criptografia quanto para **assinatura digital**.
*   **Fundamento Matemático:** Sua segurança repousa sobre a dificuldade computacional de **fatorar números grandes**.
*   **Adoção Ampla:** Serve como base para protocolos críticos como HTTPS e SSH.

O **Princípio Básico** de uso é que Bob usa a chave pública de Alice para criptografar uma mensagem, mas **somente Alice pode descriptografar** usando sua chave privada correspondente.

## 2. Princípios Matemáticos Fundamentais (A Base da Segurança)

A segurança e a funcionalidade do RSA dependem de conceitos avançados da teoria dos números.

### A. Aritmética Modular
A aritmética modular é a espinha dorsal do RSA.
*   Ela define a relação $a \equiv b \pmod n$, significando que $a \mod n = b \mod n$.
*   As operações de multiplicação e exponenciação modular podem ser calculadas de forma eficiente.

### B. Números Primos e o Problema da Fatoração
O RSA exige a escolha de dois números primos grandes, $p$ e $q$.
*   A segurança do sistema é baseada no **Problema da Fatoração**: Dado um número composto $n = p \times q$, é **computacionalmente difícil** encontrar $p$ e $q$ se $n$ for grande o suficiente (exemplo: 2048 bits).
*   O melhor algoritmo clássico para fatoração é o *General Number Field Sieve (GNFS)*, mas ele ainda é inviável para chaves modernas.

### C. Função Totiente de Euler ($\varphi(n)$)
Esta função é crucial para o cálculo da chave privada.
*   $\varphi(n)$ é a quantidade de números menores que $n$ que são coprimos com $n$.
*   Para o módulo RSA, que é o produto de dois primos distintos ($n = p \times q$), a função Totiente é calculada como: **$\varphi(n) = (p-1) \times (q-1)$**.
*   Este valor **deve ser mantido em segredo**.

### D. Teorema de Euler e Inverso Modular
O Teorema de Euler garante que a descriptografia funcione.
*   Ele estabelece que, se $\text{gcd}(a, n) = 1$, então $a^{\varphi(n)} \equiv 1 \pmod n$.
*   O corolário usado no RSA é: $a^{(k\times\varphi(n) + 1)} \equiv a \pmod n$.
*   O **Inverso Modular** é o mecanismo para gerar a chave privada $d$. $d$ é o inverso modular de $e$ módulo $\varphi(n)$ se: **$e \times d \equiv 1 \pmod {\varphi(n)}$**.
*   Este inverso é calculado utilizando o **Algoritmo Euclidiano Estendido**.

## 3. Funcionamento do Algoritmo RSA

O algoritmo RSA é dividido em três fases: geração de chaves, criptografia e descriptografia.

### Fase 1: Geração de Chaves

1.  **Gere dois primos ($p$ e $q$):** Devem ser grandes e distintos, tipicamente com tamanhos de bits similares (e.g., $p$ e $q \approx n/2$ bits).
2.  **Calcule o Módulo ($n$):** $n = p \times q$. O módulo $n$ define o "tamanho" da chave e é público.
3.  **Calcule $\varphi(n)$:** $\varphi(n) = (p-1) \times (q-1)$. Este valor é secreto.
4.  **Escolha o Expoente Público ($e$):** Escolha $e$ tal que $1 < e < \varphi(n)$. Um valor comum e eficiente é $e = 65537$.
5.  **Calcule o Expoente Privado ($d$):** Encontre $d$ tal que $e \times d \equiv 1 \pmod {\varphi(n)}$ (usando o Algoritmo Euclidiano Estendido).

*   **Chave Pública:** $(n, e)$.
*   **Chave Privada:** $(n, d)$.

### Fase 2: Criptografia
Para criptografar uma mensagem $m$ (representada como um número menor que $n$) usando a chave pública $(n, e)$, o texto cifrado $c$ é calculado como:

$$c = m^e \mod n$$

### Fase 3: Descriptografia
Para descriptografar o texto cifrado $c$ usando a chave privada $(n, d)$, a mensagem original $m$ é recuperada como:

$$m = c^d \mod n$$

**Eficiência Computacional:** Tanto a criptografia quanto a descriptografia usam a **Exponenciação Modular Rápida** (algoritmo "Square-and-Multiply"), que tem uma complexidade de $O(\log \text{exp})$ e é fundamental para viabilizar o RSA, evitando calcular números gigantescos.

## 4. Aplicações Práticas e Considerações de Segurança

O RSA é amplamente usado na segurança da informação, mas possui limitações importantes que moldam seu uso.

### A. Aplicações Típicas (Uso Híbrido e Assinaturas)
Devido à sua complexidade computacional, o RSA é cerca de **1000x mais lento que algoritmos simétricos** como o AES. Por isso, o RSA é tipicamente usado de forma **híbrida**:

1.  **Troca de Chaves Simétricas:** O RSA é usado para criptografar uma chave simétrica (chave de sessão) pequena e aleatória. Uma vez que a chave simétrica é trocada de forma segura, o restante dos dados grandes é criptografado rapidamente usando o algoritmo simétrico.
2.  **Assinatura Digital:** É usado para provar a origem e a integridade de um documento, usando um esquema de padding seguro (PSS).

### B. Segurança e Tamanho de Chave
A segurança do RSA depende diretamente do tamanho do módulo $n$.

| Tamanho Mínimo | Equivalência Simétrica | Status |
| :--- | :--- | :--- |
| **2048 bits** | 112 bits | ✅ Seguro atual |
| **3072 bits** | 128 bits | ✅ Recomendado para 2025 |

Para produção, o uso de **chaves $\geq 2048$ bits** é mandatória. Além disso, a segurança exige o uso de **padding seguro (OAEP)** para criptografia e **PSS** para assinatura digital.

### C. Limitações e Desafios (Ameaças)

1.  **Ataques Quânticos:** O RSA é vulnerável ao **Algoritmo de Shor (1994)**, que pode fatorar $n$ em tempo polinomial se houver um computador quântico funcional. A contramedida futura é a migração para a criptografia pós-quântica (como Kyber ou Dilithium), que já estão em fase de padronização.
2.  **Ataques à Implementação:** Mesmo que a matemática seja sólida, falhas na implementação podem levar a quebras. Isso inclui ataques *Side-Channel* (análise de tempo ou consumo de energia) e o uso de números aleatórios não verdadeiramente aleatórios. Implementações seguras exigem validação e cálculos feitos em **tempo constante** (*constant-time*) para evitar vazamento de informações.
3.  **Complexidade:** O RSA tem muitos detalhes críticos para a segurança e é **fácil de ser implementado incorretamente**. Por isso, recomenda-se o uso de bibliotecas profissionais e testadas (como OpenSSL ou BoringSSL) em vez de implementações educacionais.
