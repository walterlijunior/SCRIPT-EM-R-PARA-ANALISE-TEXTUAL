# SCRIPT-EM-R-PARA-ANALISE-TEXTUAL
Scripts em R desenvolvido para realizar análise textual automatizada de documentos em PDF, com foco em conteúdos corporativos e científicos. Disciplina: Análise de Dados, curso de Ciências Contábeis - UFT - Universidade Federal do Tocantins - Campus Palmas

# Análise Textual de PDFs com R e Shiny

## 📋 Descrição

Esta aplicação Shiny fornece uma interface intuitiva para análise textual de documentos PDF, com foco especial em textos em português. A ferramenta permite o upload de arquivos PDF, extração de texto, análise de frequência de palavras, visualização de resultados e busca avançada de termos.



## 🚀 Funcionalidades

- **Extração de texto** de arquivos PDF
- **Análise de frequência** de palavras (individual ou agrupada)
- **Identificação de termos raros**
- **Nuvem de palavras** personalizável
- **Busca inteligente** com reconhecimento de variações de palavras
- **Visualização de texto com destaque** para termos pesquisados

## 📦 Requisitos e Instalação

Para executar o aplicativo, você precisará do R e das seguintes bibliotecas:

```r
# Instalar pacotes necessários
install.packages(c("shiny", "pdftools", "tm", "wordcloud", "ggplot2", 
                 "RColorBrewer", "tidytext", "dplyr", "stringr", 
                 "tibble", "stringdist", "DT", "scales"))
```

## 🔧 Como Usar

1. Clone este repositório
2. Abra o arquivo principal no RStudio
3. Clique em "Run App" para iniciar a aplicação
4. Faça upload de um arquivo PDF e clique em "Analisar Documento"
5. Explore os resultados nas diferentes abas

## 📚 Detalhamento do Código

### Estrutura do Arquivo

O código está organizado como um documento R Markdown com chunks de código R para criar uma aplicação Shiny interativa:

```
- Setup e configuração inicial
- Interface de upload do arquivo
- Funções de processamento de texto
- Definição da interface do usuário (UI)
- Lógica do servidor (Server)
- Instruções de uso
```

### Configuração Inicial

```r
# Linhas 10-23: Configuração do ambiente
knitr::opts_chunk$set(echo = TRUE, warning = FALSE, message = FALSE)
options(shiny.maxRequestSize = 30*1024^2)  # Aumenta limite para 30MB
```

**Como personalizar:**
- Para aumentar o limite de upload, modifique o valor `30*1024^2`. Por exemplo, para 50MB: `options(shiny.maxRequestSize = 50*1024^2)`

### Interface de Upload

```r
# Linhas 26-38: Interface de upload do arquivo
fluidRow(
  column(6, 
    fileInput("arquivo_pdf", "Escolha um arquivo PDF", 
              accept = ".pdf",
              buttonLabel = "Procurar...")
  ),
  column(6,
    actionButton("analisar", "Analisar Documento", 
                icon = icon("play"),
                class = "btn-primary",
                style = "margin-top: 25px;")
  )
)
```

**Como personalizar:**
- Para alterar o botão "Procurar", modifique `buttonLabel = "Procurar..."` 
- Para alterar a cor do botão "Analisar", modifique `class = "btn-primary"`. Opções disponíveis: btn-default, btn-primary, btn-success, btn-info, btn-warning, btn-danger
- Para ajustar o espaçamento, modifique `style = "margin-top: 25px;"`

### Funções de Processamento de Texto

#### Normalização de Palavras (linhas 60-79)

```r
normalizar_palavra <- function(palavra) {
  # Converter para minúsculas
  palavra <- tolower(palavra)
  
  # Remover acentos e caracteres especiais
  palavra <- iconv(palavra, to = "ASCII//TRANSLIT")
  
  # Substituições específicas
  palavra <- gsub("ç", "c", palavra)
  palavra <- gsub("ã", "a", palavra)
  # ...restante do código
}
```

**Como personalizar:**
- Para adicionar novas substituições de caracteres, adicione linhas `palavra <- gsub("caractere_original", "substituto", palavra)`

#### Agrupamento de Palavras Similares (linhas 82-130)

```r
agrupar_palavras_similares <- function(termos, threshold = 0.2) {
  # Se não há termos, retorna uma lista vazia
  if (nrow(termos) == 0) {
    return(termos)
  }
  
  # Normalizar todas as palavras
  termos$termo_normalizado <- sapply(termos$termo, normalizar_palavra)
  
  # Criar um data frame para armazenar o resultado final
  resultado <- data.frame(
    termo = character(),
    frequencia = numeric(),
    variantes = character(),
    stringsAsFactors = FALSE
  )
  
  # Agrupar por termo normalizado
  termos_agrupados <- split(termos, termos$termo_normalizado)
  # ...restante do código
}
```

**Como personalizar:**
- Para ajustar o limite de similaridade, modifique o valor `threshold = 0.2` (valores menores resultam em agrupamentos mais restritos)

#### Busca Inteligente de Palavras (linhas 133-281)

```r
buscar_palavra_inteligente <- function(texto, termo, case_sensitive = FALSE) {
  # Verificar se o termo está vazio
  if (trimws(termo) == "") {
    return(list(total = 0, por_pagina = rep(0, length(texto)), 
                posicoes = list(), palavras_encontradas = character(0)))
  }
  
  # Normalizar o termo de busca
  termo_norm <- normalizar_palavra(termo)
  
  # Preparar o padrão de busca para correspondência parcial
  # ...restante do código
}
```

**Como personalizar:**
- Para modificar a sensibilidade a maiúsculas/minúsculas padrão, altere `case_sensitive = FALSE` para `case_sensitive = TRUE`
- Para ajustar as variantes de busca, modifique o vetor `variantes` nas linhas 155-162

### Interface Principal (UI)

A interface do usuário está organizada em abas:

#### Contador de Palavras (linhas 287-296)

```r
div(
  class = "well well-sm",
  style = "text-align: center; background-color: #f0f8ff; border-left: 4px solid #007bff;",
  h4(
    icon("info-circle"), 
    textOutput("contador_palavras", inline = TRUE)
  )
)
```

**Como personalizar:**
- Para mudar a cor de fundo, modifique `background-color: #f0f8ff`
- Para mudar a cor da borda esquerda, modifique `border-left: 4px solid #007bff`

#### Abas da Interface (linhas 298-492)

```r
tabsetPanel(
  tabPanel("Texto Extraído", 
    h4("Texto do PDF (por página):"),
    uiOutput("paginas_pdf")
  ),
  tabPanel("Termos Frequentes",
    # ...código da aba
  ),
  # ...outras abas
)
```

**Como personalizar:**
- Para adicionar uma nova aba, adicione um novo bloco `tabPanel("Nome da Aba", conteúdo...)`
- Para reordenar as abas, reordene os blocos `tabPanel`

#### Tabela de Termos Frequentes (linhas 331-338)

```r
DTOutput("tabela_termos_dt")
```

**Como personalizar:**
- As opções da tabela estão definidas na função `renderDT` nas linhas 519-526
- Para alterar o número de linhas exibidas, modifique `pageLength = 10`
- Para alterar as opções de exportação, modifique `buttons = c('copy', 'csv', 'excel', 'pdf')`

#### Gráfico de Barras (linhas 339-340)

```r
plotOutput("grafico_barras")
```

**Como personalizar:**
- O estilo do gráfico é definido nas linhas 543-568
- Para mudar a cor das barras, modifique `fill = "steelblue"` na linha 556 ou 578

#### Nuvem de Palavras (linhas 413-428)

```r
fluidRow(
  column(3, 
    radioButtons("tipo_contagem_nuvem", "Tipo de contagem:", 
               choices = c("Individual" = "individual", 
                          "Agrupada (palavras similares)" = "agrupada"),
               selected = "agrupada", inline = TRUE)
  ),
  # ...outras colunas
)
```

**Como personalizar:**
- A nuvem de palavras é gerada nas linhas 610-664
- Para mudar as cores, modifique `color_palette` nas linhas 634-638
- Para ajustar o tamanho, modifique `scale = c(4, 0.5)` na linha 651

### Lógica do Servidor (Server)

As funções do servidor processam os dados e renderizam as saídas:

#### Processamento do PDF (linhas 49-57)

```r
output_text <- reactive({
  req(input$arquivo_pdf)
  req(analise_realizada$valor)
  # Extrair todas as páginas do PDF e manter a formatação por página
  texto_pdf <- pdftools::pdf_text(input$arquivo_pdf$datapath)
  return(texto_pdf)  # Retorna um vetor com cada página como um elemento
})
```

#### Tokenização e Contagem (linhas 284-327)

```r
texto_tokenizado <- reactive({
  req(output_text())
  
  # Combinar todas as páginas
  texto_completo <- paste(output_text(), collapse = " ")
  
  # Usar tibble e unnest_tokens para processamento mais preciso
  tokens <- tibble(texto = texto_completo) %>%
    unnest_tokens(palavra, texto) %>%
    # ...restante do código
})
```

#### Renderização de Tabelas e Gráficos (linhas 499-664)

```r
output$tabela_termos_dt <- renderDT({
  req(input$min_freq)
  
  if (input$tipo_contagem == "individual") {
    # ...código para contagem individual
  } else {
    # ...código para contagem agrupada
  }
  
  datatable(df, rownames = FALSE, options = list(
    pageLength = 10,
    lengthMenu = c(10, 25, 50),
    dom = 'Blfrtip',
    buttons = c('copy', 'csv', 'excel', 'pdf')
  ))
})
```

**Como personalizar:**
- Para alterar opções de tabelas, modifique o objeto `options` nas funções `renderDT`
- Para mudar cores dos gráficos, encontre os comandos `ggplot` e modifique parâmetros como `fill`, `color`, etc.
- Para a nuvem de palavras, ajuste parâmetros como `scale`, `rot.per` e `colors` na função `wordcloud` (linhas 645-652)

#### Destaque de Texto (linhas 781-841)

```r
destacar_texto <- function(texto, palavras, case_sensitive = FALSE) {
  texto_html <- texto
  
  # Gerar cores para cada palavra encontrada
  num_palavras <- length(palavras)
  cores <- colorRampPalette(brewer.pal(min(8, max(3, num_palavras)), "Set1"))(num_palavras)
  
  # Para cada palavra, adicionar destaque com uma cor específica
  for (i in seq_along(palavras)) {
    palavra <- palavras[i]
    cor <- cores[i]
    padrao <- paste0("\\b", palavra, "\\b")
    
    texto_html <- gsub(
      pattern = padrao, 
      replacement = paste0('<span style="background-color: ', cor, 
                          '; color: white; font-weight: bold; padding: 0 2px; border-radius: 3px;">', 
                          "\\0", '</span>'),
      x = texto_html,
      ignore.case = !case_sensitive
    )
  }
  # ...restante do código
}
```

**Como personalizar:**
- Para mudar o estilo do destaque, modifique os atributos CSS em `replacement` nas linhas 791-794
- Para alterar a paleta de cores, modifique `brewer.pal(min(8, max(3, num_palavras)), "Set1")` na linha 786

## 🎨 Guia de Personalização Rápida

### Cores e Estilos

- **Botão "Analisar"**: Linha 35, altere `class = "btn-primary"` para uma das classes Bootstrap: btn-default, btn-success, btn-info, btn-warning, btn-danger
- **Botões de busca**: Linhas 329 e 443, altere `class = "btn-info"` ou `class = "btn-primary"`
- **Cor do contador de palavras**: Linha 290, modifique `background-color: #f0f8ff; border-left: 4px solid #007bff;`
- **Cores do gráfico de barras**: Linhas 556 e 578, altere `fill = "steelblue"`
- **Paleta da nuvem de palavras**: Linhas 634-638, modifique `brewer.pal(n_cores, "Dark2")` ou `brewer.pal(n_cores, "Pastel1")`

### Tamanhos e Limites

- **Limite de upload**: Linha 12, altere `options(shiny.maxRequestSize = 30*1024^2)` para ajustar o tamanho máximo do arquivo
- **Número de palavras na nuvem**: Linha 426, modifique o valor padrão em `value = 100`
- **Linhas na tabela**: Linha 520, altere `pageLength = 10`
- **Frequência mínima padrão**: Linha 336, modifique `value = 1`

### Textos e Rótulos

- **Título da aplicação**: Linha 3, altere `title: "Análise Textual de PDF com Agrupamento de Palavras"`
- **Rótulo do botão de upload**: Linha 29, modifique `buttonLabel = "Procurar..."`
- **Rótulo do botão de análise**: Linha 34, altere `"Analisar Documento"`
- **Nomes das abas**: Linhas 299, 303, 349, 379, 408, altere os primeiros argumentos dos `tabPanel`

## 📈 Exemplos de Uso Comum

### Como adicionar uma nova substituição de acentos:

```r
# Na função normalizar_palavra (linhas 60-79), adicione:
palavra <- gsub("caractere_original", "substituto", palavra)
```

### Como mudar a cor do botão "Analisar":

```r
# Na linha 35, altere:
actionButton("analisar", "Analisar Documento", 
            icon = icon("play"),
            class = "btn-success",  # Alterado de btn-primary para btn-success
            style = "margin-top: 25px;")
```

### Como adicionar uma nova aba:

```r
# Após a linha 444, adicione:
tabPanel("Minha Nova Aba",
  h4("Conteúdo da nova aba"),
  # Adicione aqui o conteúdo da nova aba
)
```

### Como modificar a aparência do destaque de texto:

```r
# Na função destacar_texto (linha 793), altere:
replacement = paste0('<span style="background-color: ', cor, 
                    '; color: black; font-weight: bold; padding: 2px 5px; border-radius: 5px;">', 
                    "\\0", '</span>'),
```




## 🤝 Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou enviar pull requests.

## 📄 Licença

Este projeto está licenciado sob a [MIT License](LICENSE).

## 👤 Autor

- **Autor Original**: [Walterli Junior](https://github.com/walterlijunior)
- **Repositório**: [SCRIPT-EM-R-PARA-ANALISE-TEXTUAL](https://github.com/walterlijunior/SCRIPT-EM-R-PARA-ANALISE-TEXTUAL)
