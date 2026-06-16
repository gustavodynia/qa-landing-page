# Projeto Prático de Modelagem de Testes - STLC

Este repositório contém a documentação do Ciclo de Vida de Testes de Software (STLC) aplicado a uma Landing Page real com catálogo de IA's para cada segmento de uso (`https://ailearningslp.netlify.app/`). 

O objetivo deste projeto é demonstrar a estruturação de cenários, casos de teste manuais e documentação de aprendizado.

---

## 1. Análise de Requisitos e Cenários de Teste (STLC - Fase 1 a 3)

Como o sistema não possuía uma especificação técnica formal (SDR), foi realizada uma análise exploratória da interface para mapear as regras de negócio implícitas na seção `#catalogo`.

### Cenários Mapeados:
* **Cenário 1:** Validação de integridade e redirecionamento dos links de direcionamento interno.
* **Cenário 2:** Responsividade e adaptabilidade do grid de produtos em dispositivos móveis.
* **Cenário 3:** Validação de resiliência e comportamento da caixa de pesquisa de texto com dados inválidos.

---

## 2. Planejamento e Configuração do Ambiente (STLC - Fase 4)

Para a execução dos testes, foi utilizado o ambiente de homologação pública hospedado na plataforma Netlify através da URL oficial fornecida. 
* **Massa de Dados:** Foram mapeados os cards de ferramentas reais de IA já renderizados na interface.
* **Ferramental:** Utilizou-se o Google Chrome DevTools para emulação de dispositivos móveis (Viewports) e monitoramento de logs de rede/console do navegador (Client-side).

---

## 3. Execução dos Casos de Teste (STLC - Fase 5)

### CT-01: Validar redirecionamento dos botões de ação do catálogo
* **Pré-condição:** Navegador atualizado e conexão com a internet ativa.
* **Tipo de Teste:** Caixa Preta / Funcional.

| Passo | Ação | Resultado Esperado | Status |
| :--- | :--- | :--- | :--- |
| **1** | Acessar a URL `https://ailearningslp.netlify.app/` | A página inicial deve carregar corretamente. | **PASS** |
| **2** | Clicar no botão Catálogo | A tela deve focar na seção `#catalogo` exibindo as opções. | **PASS** |
| **3** | Clicar no botão de ação ("Acessar site oficial") da IA | O navegador deve redirecionar para a página externa da IA em questão. | **PASS** |
| **4** | Validar a URL da nova página aberta | A URL final deve corresponder ao destino correto, sem apresentar quebras ou erro 404. | **PASS** |

### CT-02: Validar responsividade do Grid de opções de IA's (Mobile)
* **Pré-condição:** DevTools aberto em modo de simulação de dispositivo mobile.

| Passo | Ação | Resultado Esperado | Status |
| :--- | :--- | :--- | :--- |
| **1** | Redimensionar a tela para a resolução de um smartphone comum (ex: iPhone 12 ou Pixel 7) | O layout deve se reajustar verticalmente em telas menores. | **PASS** |
| **2** | Avaliar a legibilidade dos cards de opções IA's na seção `#catalogo` | Textos, imagens e botões não devem se sobrepor ou cortar nas bordas da tela. | **FAIL** |

---

### CT-03: Validar busca por caracteres inválidos/especiais na Caixa de Pesquisa
* **Pré-condição:** Estar na seção de busca do catálogo.
* **Tipo de Teste:** Caixa Preta / Fluxo de Exceção (Negativo).

| Passo | Ação | Resultado Esperado | Status |
| :--- | :--- | :--- | :--- |
| **1** | Digitar um caractere especial isolado (ex: `@`) na caixa de texto | O sistema deve tratar o caractere como texto comum e ocultar os cards, exibindo uma mensagem de "Nenhum resultado encontrado". | **FAIL** |

> **Nota do Q.A. (Evidência Técnica):** Ao digitar `@`, o sistema ignorou a busca e manteve a listagem de ferramentas padrão visível, em vez de alertar o usuário sobre a falta de resultados para aquele termo específico. Nenhuma requisição de rede foi disparada (filtro puramente local via JavaScript).

### 4. Encerramento e Conclusão (STLC - Fase 6)

* **Total de Casos de Teste Planejados:** 3

* **Total de Casos de Teste Executados:** 3

* **Testes com Sucesso (PASS):** 2

* **Bugs Encontrados (FAIL):** 1

### Resumo da Execução:

* **CT-01 (Redirecionamento do Botão de Ação):** **PASS** – O link "Acessar site oficial" de cada IA redirecionou o usuário perfeitamente para as URLs externas correspondentes.

* **CT-02 (Responsividade do Grid / Caixa de Pesquisa):** **FAIL** – Foi identificada uma quebra de layout (overflow de texto) quando o usuário digita strings longas sem espaços na barra de busca, afetando dispositivos de viewports específicas (como Pixel 7 e iPhone SE).

* **CT-03 (Busca por Caracteres Inválidos):** **PASS** – O sistema não gerou erros de exceção no console ao receber caracteres especiais. A falta do feedback visual de "Nenhum resultado encontrado" entra como oportunidade de melhoria técnica.

## 5. Relatório de Bug Encontrado (Bug Report)

* **ID do Bug:** BUG-001
* **Título:** Quebra de layout (Overflow) na mensagem de feedback da busca em dispositivos mobile.
* **Gravidade:** Baixa (Não impede o uso, mas afeta severamente a usabilidade).
* **Componente:** Caixa de Pesquisa (`#catalogo` / Mobile View).

### Descrição do Problema:
Ao realizar uma busca contendo uma string longa sem espaços (ex: mais de 30 caracteres contínuos), o texto de feedback *"Nao encontrei uma correspondencia exata para..."* estoura o limite lateral do container invisível, estendendo-se para fora da área visível da tela do dispositivo móvel.
Obs: Necessário ajuste de pontuação na mensagem de feedback.

### Ambiente de Teste:
* **Dispositivo:** Emulação de Mobile (Pixel 7 - 412x915).
* **Navegador:** Google Chrome (v149).

### Passos para Reproduzir:**
1. Acessar o site em modo de visualização mobile (Pixel 7).
2. Rolar até o campo de pesquisa.
3. Digitar uma sequência longa de caracteres sem espaços (Ex: `gijhi2yu98475#############################`).
4. Observar a mensagem de feedback que surge logo abaixo do botão "Limpar".

### Resultado Esperado:
O texto deve respeitar as margens do celular e quebrar automaticamente para a linha de baixo (wrap), mantendo-se totalmente visível dentro do bloco azul.

### Resultado Atual:
O texto foge do enquadramento lateral direito da tela, gerando quebra visual do layout.

---

### Sugestão de Correção Técnica (Para o Desenvolvedor):
Inserir uma validação no campo de busca para haver a quebra de linha a partir de um número de caracteres seguros, evitando o avanço além das margens.

---

* **ID do Bug:** BUG-002
* **Título:** Instabilidade no Scroll da página ao digitar no campo de busca (Apenas no modo Desktop).
* **Gravidade:** Média (Prejudica severamente a usabilidade e a experiência do usuário).
* **Componente:** Caixa de Pesquisa (`#catalogo` / Desktop View).

### Descrição do Problema:
No modo Desktop, ao digitar qualquer termo na caixa de pesquisa, a página executa um scroll automático e abrupto para baixo a cada caractere inserido. Isso faz com que a tela fique oscilando ("subindo e descendo") rapidamente enquanto o usuário digita, gerando um comportamento instável e desconfortável.

### Passos para Reproduzir:
1. Acessar o site em modo Desktop.
2. Rolar até o campo de pesquisa do catálogo.
3. Digitar pausadamente a palavra `imagem`.
4. Observar o movimento involuntário da barra de rolagem a cada tecla pressionada.

### Resultado Esperado:
O usuário deve conseguir digitar o termo completo sem que a página mova o scroll sozinha. O filtro dos cards deve acontecer de forma silenciosa abaixo do campo de texto, mantendo a tela estática.

### Resultado Atual:
O sistema força um redirecionamento de foco/scroll a cada caractere, fazendo a página saltar repetidamente entre a caixa de pesquisa e os resultados abaixo dela.

---

### Sugestão de Correção Técnica (Para o Desenvolvedor):
Desabilitar uma possível pesquisa em tempo real. Disparar a requisição apenas após o Enter do usuário. 