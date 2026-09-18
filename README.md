# 📸 Automação de Relatórios Fotográficos

Aplicação desktop desenvolvida para automatizar a organização e inserção de registros fotográficos em relatórios corporativos do Microsoft Word.

O sistema permite administrar relatórios de múltiplas empresas, localizar automaticamente as fotografias correspondentes a cada data e atualizar o documento mensal correto, mantendo histórico, backups e controle contra duplicidades.

> 🔒 **Projeto proprietário. O código-fonte e os documentos utilizados em produção são privados. Este repositório apresenta exclusivamente as funcionalidades e conceitos técnicos do projeto para fins de portfólio.**

---

## 🎯 Problema

A elaboração de relatórios fotográficos exigia um processo repetitivo:

```text
Receber fotos
     ↓
Identificar empresa
     ↓
Identificar data
     ↓
Encontrar relatório correto
     ↓
Abrir Microsoft Word
     ↓
Localizar posição adequada
     ↓
Inserir imagens
     ↓
Ajustar imagens
     ↓
Salvar documento
```

Quando esse processo precisa ser repetido para diferentes empresas e diferentes dias ao longo do mês, o volume de trabalho manual cresce rapidamente.

Além disso, é necessário evitar problemas como:

* inserir uma fotografia duas vezes;
* utilizar fotos da data errada;
* atualizar o relatório de outra empresa;
* modificar o relatório do mês incorreto;
* perder a versão anterior do documento após uma alteração.

---

## 💡 Solução

A aplicação centraliza todo o processo em uma interface gráfica.

As empresas e seus respectivos relatórios são cadastrados uma única vez.

No uso diário, o operador informa a data desejada e pode atualizar uma empresa específica ou processar todas as empresas ativas.

```text
        SELECIONAR DATA
               │
               ▼
      LOCALIZAR EMPRESAS
               │
               ▼
       ENCONTRAR AS FOTOS
               │
               ▼
       VALIDAR AS IMAGENS
               │
               ▼
     VERIFICAR DUPLICIDADES
               │
               ▼
       CRIAR UM BACKUP
               │
               ▼
      ATUALIZAR O WORD
               │
               ▼
       REGISTRAR HISTÓRICO
```

---

## ⚙️ Principais funcionalidades

* 🏢 Gerenciamento de múltiplas empresas
* 📁 Detecção automática das pastas de fotografias
* 📅 Identificação das fotos pela data
* 📄 Associação de cada empresa ao seu relatório mensal
* 📸 Processamento automático das imagens
* 🔄 Correção de orientação baseada em EXIF
* 📐 Preservação da proporção das fotografias
* 🗜️ Otimização de imagens grandes antes da inserção
* 🔐 Identificação de imagens através de hash SHA-256
* 🚫 Prevenção de fotografias duplicadas
* 💾 Backup automático do relatório
* 🗃️ Histórico persistente em SQLite
* 📝 Logs para diagnóstico
* 🧪 Modo de simulação
* 📦 Atualização individual ou em lote
* 🖥️ Interface desktop para usuários não técnicos

---

## 🏢 Gerenciamento de empresas

Cada empresa possui sua própria configuração.

Entre as informações administradas estão:

```text
Empresa
├── Pasta de fotografias
├── Relatório Word mensal
├── Competência
└── Status ativo/inativo
```

O cadastro fica armazenado localmente, evitando que essas informações precisem ser configuradas novamente a cada execução.

Também é possível detectar empresas a partir da estrutura de diretórios existente.

---

## 📁 Organização das fotografias

O sistema trabalha com uma estrutura organizada por empresa e data.

Exemplo:

```text
FOTOS/
│
├── EMPRESA A/
│   ├── 18-09-2026/
│   ├── 19-09-2026/
│   └── 20-09-2026/
│
├── EMPRESA B/
│   ├── 18-09-2026/
│   └── 19-09-2026/
│
└── EMPRESA C/
    └── 18-09-2026/
```

A aplicação possui resolução automática de datas e aceita diferentes padrões de nomenclatura das pastas.

Isso reduz a necessidade de seleção manual das fotografias durante a atualização.

---

## 🔐 Prevenção de duplicidades

Cada fotografia processada recebe uma identificação baseada em **SHA-256**.

Conceitualmente:

```text
        IMAGEM
           │
           ▼
      SHA-256 HASH
           │
           ▼
   IDENTIFICADOR ÚNICO
           │
      ┌────┴────┐
      │         │
   NOVA      EXISTENTE
      │         │
      ▼         ▼
  INSERIR     IGNORAR
```

O histórico permite verificar se aquela fotografia já foi adicionada ao relatório correspondente.

Assim, executar novamente uma atualização não significa inserir automaticamente as mesmas imagens outra vez.

---

## 📸 Processamento das imagens

Antes de uma fotografia ser adicionada ao relatório, ela passa por etapas de preparação.

Entre elas:

* validação do arquivo;
* leitura de orientação EXIF;
* correção da orientação quando necessário;
* preservação da proporção;
* redução de imagens excessivamente grandes;
* preparação para inserção no documento.

Isso permite padronizar o material antes de atualizar o relatório.

---

## 📄 Automação do Microsoft Word

A aplicação foi projetada para trabalhar com documentos corporativos existentes, inclusive relatórios que possuem estruturas mais complexas.

O sistema possui diferentes estratégias de manipulação:

**Microsoft Word / COM**

Utilizado em ambiente Windows com Microsoft Word instalado, permitindo trabalhar através do próprio Word para preservar documentos corporativos complexos.

**Manipulação DOCX**

Também existe processamento direto da estrutura DOCX, utilizado como alternativa e nos testes automatizados do projeto.

---

## 🛡️ Atualização segura

Uma preocupação importante do projeto é evitar que uma falha durante o processamento danifique o relatório original.

O processo segue uma sequência controlada:

```text
VALIDAR DADOS
      ↓
VALIDAR DOCUMENTO
      ↓
IDENTIFICAR DUPLICIDADES
      ↓
CRIAR BACKUP
      ↓
CRIAR CÓPIA DE TRABALHO
      ↓
PROCESSAR IMAGENS
      ↓
ATUALIZAR DOCUMENTO
      ↓
VALIDAR RESULTADO
      ↓
SUBSTITUIR ORIGINAL
      ↓
REGISTRAR HISTÓRICO
```

Caso determinadas etapas críticas falhem, existem mecanismos para preservar ou restaurar o documento anterior.

---

## 💾 Backups

Antes da alteração de um relatório, uma cópia com identificação temporal é criada.

```text
Relatório atual
       │
       ├──────────────► Backup
       │
       ▼
Processamento
       │
       ▼
Relatório atualizado
```

Isso adiciona uma camada de segurança ao processo automatizado.

---

## 🗃️ Histórico

A aplicação utiliza um banco de dados **SQLite** para manter informações como:

* empresas cadastradas;
* arquivos já processados;
* hashes das fotografias;
* histórico de atualizações.

O histórico permite que decisões importantes do processamento persistam mesmo após fechar a aplicação.

---

## 🧪 Modo de simulação

Antes de alterar os documentos, a aplicação pode executar o processo em modo de simulação.

Nesse modo é possível validar o que seria processado sem modificar:

```text
❌ Documento Word
❌ Banco de histórico
❌ Backups
```

Isso facilita testes e conferências antes de uma atualização real.

---

## 📦 Processamento em lote

Além da atualização individual, o sistema permite processar todas as empresas ativas.

```text
              DATA
                │
        ┌───────┼───────┐
        ▼       ▼       ▼
    EMPRESA A EMPRESA B EMPRESA C
        │       │       │
        ▼       ▼       ▼
      FOTOS   FOTOS   FOTOS
        │       │       │
        ▼       ▼       ▼
      WORD    WORD    WORD
```

Cada empresa é processada de forma isolada.

Dessa maneira, um problema relacionado a uma empresa não precisa interromper automaticamente o processamento das demais.

---

## 🖥️ Interface

A aplicação possui interface gráfica desenvolvida para simplificar a operação diária.

### Tela principal

```text
[ ADICIONAR SCREENSHOT DA INTERFACE ]
```

### Processamento

```text
[ ADICIONAR GIF DO PROCESSAMENTO ]
```

### Resultado no Word

```text
[ ADICIONAR ANTES / DEPOIS DO RELATÓRIO ]
```

> As demonstrações públicas devem utilizar dados fictícios ou anonimizados.

---

## 🏗️ Arquitetura conceitual

```text
                  INTERFACE
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
     CONFIGURAÇÃO             EMPRESAS
          │                       │
          └───────────┬───────────┘
                      ▼
              SERVIÇO DE RELATÓRIO
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       IMAGENS      BANCO       WORD
          │           │           │
          ▼           ▼           ▼
     PROCESSAMENTO  SQLITE   COM / DOCX
          │           │           │
          └───────────┼───────────┘
                      ▼
               RELATÓRIO FINAL
```

A separação entre aquisição das imagens, processamento, persistência e atualização do documento facilita a manutenção e evolução da aplicação.

---

## 🛠️ Tecnologias utilizadas

**Python** — linguagem principal

**Pillow** — processamento e normalização das imagens

**python-docx** — manipulação de documentos DOCX

**pywin32 / COM Automation** — integração com Microsoft Word no Windows

**SQLite** — persistência de configurações e histórico

**SHA-256** — identificação e prevenção de duplicidade das imagens

**PyInstaller** — geração da aplicação distribuível para Windows

**Pytest** — testes automatizados

---

## 🧪 Qualidade e testes

O projeto possui testes automatizados para diferentes partes do fluxo, incluindo cenários relacionados a:

* processamento de imagens;
* orientação EXIF;
* resolução de datas;
* caminhos com espaços e acentos;
* configurações;
* banco SQLite;
* cadastro de empresas;
* processamento em lote;
* pastas ausentes ou vazias;
* documentos indisponíveis;
* mudança de competência;
* modo de simulação;
* backups;
* prevenção de duplicidades;
* recuperação diante de determinadas falhas.

---

## 📈 Impacto

### Processo manual

```text
Localizar empresa
       ↓
Localizar fotos
       ↓
Conferir data
       ↓
Abrir relatório
       ↓
Localizar seção
       ↓
Inserir fotos
       ↓
Ajustar imagens
       ↓
Salvar
       ↓
Repetir para próxima empresa
```

### Processo automatizado

```text
Selecionar data
       ↓
Executar atualização
       ↓
Sistema processa empresas
       ↓
Relatórios atualizados
```

A aplicação transfere grande parte das tarefas repetitivas para o software e mantém o operador responsável pela conferência do resultado.

---

## 🔐 Código-fonte e dados

Por se tratar de uma solução desenvolvida para um processo operacional real, o código-fonte permanece em repositório privado.

Este repositório público contém apenas documentação e demonstrações do funcionamento da aplicação.

Não são disponibilizados:

* código-fonte proprietário;
* documentos corporativos;
* fotografias reais;
* informações de clientes;
* banco de dados;
* credenciais;
* configurações internas;
* arquivos utilizados em produção.

---

## 👨‍💻 Sobre o projeto

Projeto desenvolvido com foco em:

* automação de processos administrativos;
* processamento de imagens;
* automação do Microsoft Office;
* integridade de dados;
* prevenção de operações duplicadas;
* persistência local;
* aplicações desktop;
* confiabilidade e recuperação de falhas.

---

**Desenvolvido por Leonardo de Carvalho da Costa**


----------------------------------------------------------------------------------------------------------------------------


# 📸 Photographic Report Automation

A desktop application developed to automate the organization and insertion of photographic records into corporate Microsoft Word reports.

The system manages reports for multiple companies, automatically locates photographs corresponding to specific dates, and updates the appropriate monthly document while maintaining processing history, backups, and duplicate protection.

> 🔒 **Proprietary project. Source code and production documents are private. This repository showcases the project's features, architecture, and technical concepts for portfolio purposes only.**

---

## 🎯 The Problem

Creating photographic reports required a repetitive manual workflow:

```text
Receive Photos
      ↓
Identify Company
      ↓
Identify Date
      ↓
Find Correct Report
      ↓
Open Microsoft Word
      ↓
Locate Correct Section
      ↓
Insert Images
      ↓
Adjust Images
      ↓
Save Document
```

When this process has to be repeated for multiple companies and multiple dates throughout the month, the amount of manual work increases significantly.

The workflow also introduces risks such as:

* Inserting the same photograph more than once
* Using photographs from the wrong date
* Updating another company's report
* Modifying a report from the wrong reporting period
* Losing the previous document version after an update

---

## 💡 The Solution

The application centralizes the entire workflow through a graphical interface.

Companies and their respective reports are configured once.

During daily use, the operator selects the desired date and can either update a specific company or process all active companies automatically.

```text
          SELECT DATE
               │
               ▼
        LOCATE COMPANIES
               │
               ▼
          FIND PHOTOS
               │
               ▼
        VALIDATE IMAGES
               │
               ▼
       CHECK DUPLICATES
               │
               ▼
         CREATE BACKUP
               │
               ▼
        UPDATE DOCUMENT
               │
               ▼
       REGISTER HISTORY
```

---

## ⚙️ Key Features

* 🏢 Multiple-company management
* 📁 Automatic photo folder detection
* 📅 Date-based photo identification
* 📄 Company-to-monthly-report association
* 📸 Automatic image processing
* 🔄 EXIF-based orientation correction
* 📐 Image aspect ratio preservation
* 🗜️ Optimization of large images before insertion
* 🔐 Image identification using SHA-256
* 🚫 Duplicate photograph prevention
* 💾 Automatic report backups
* 🗃️ Persistent processing history using SQLite
* 📝 Diagnostic logging
* 🧪 Simulation mode
* 📦 Individual and batch processing
* 🖥️ Desktop interface for non-technical users

---

## 🏢 Company Management

Each company has its own configuration.

The application manages information such as:

```text
Company
├── Photo Directory
├── Monthly Word Report
├── Reporting Period
└── Active / Inactive Status
```

Configuration data is stored locally, eliminating the need to configure the same information every time the application is launched.

The application can also detect companies based on the existing directory structure.

---

## 📁 Photo Organization

The system works with photographs organized by company and date.

Example:

```text
PHOTOS/
│
├── COMPANY A/
│   ├── 18-09-2026/
│   ├── 19-09-2026/
│   └── 20-09-2026/
│
├── COMPANY B/
│   ├── 18-09-2026/
│   └── 19-09-2026/
│
└── COMPANY C/
    └── 18-09-2026/
```

The application automatically resolves dates and supports different folder naming patterns.

This significantly reduces the need for manually selecting photographs during each report update.

---

## 🔐 Duplicate Prevention

Each processed photograph receives a unique identification based on **SHA-256**.

Conceptually:

```text
         IMAGE
           │
           ▼
      SHA-256 HASH
           │
           ▼
     UNIQUE IDENTITY
           │
      ┌────┴────┐
      │         │
     NEW     EXISTING
      │         │
      ▼         ▼
   INSERT      SKIP
```

The processing history allows the application to determine whether a photograph has already been inserted into the corresponding report.

Running an update again therefore does not automatically result in the same images being inserted twice.

---

## 📸 Image Processing

Before a photograph is added to a report, it passes through several preparation steps.

These include:

* File validation
* EXIF orientation analysis
* Automatic orientation correction
* Aspect ratio preservation
* Resizing of excessively large images
* Preparation for document insertion

This allows photographs from different sources to be standardized before being inserted into the report.

---

## 📄 Microsoft Word Automation

The application was designed to work with existing corporate documents, including reports containing complex formatting and structures.

The system supports different document-processing strategies.

### Microsoft Word / COM

Used in Windows environments with Microsoft Word installed, allowing the application to interact with Word itself and better preserve complex corporate documents.

### DOCX Processing

Direct DOCX manipulation is also available as an alternative processing strategy and for automated testing scenarios.

---

## 🛡️ Safe Document Updates

A major concern of the project is preventing failures during processing from damaging the original report.

The update follows a controlled workflow:

```text
VALIDATE DATA
      ↓
VALIDATE DOCUMENT
      ↓
CHECK DUPLICATES
      ↓
CREATE BACKUP
      ↓
CREATE WORKING COPY
      ↓
PROCESS IMAGES
      ↓
UPDATE DOCUMENT
      ↓
VALIDATE RESULT
      ↓
REPLACE ORIGINAL
      ↓
REGISTER HISTORY
```

If critical processing stages fail, mechanisms are available to preserve or restore the previous document.

---

## 💾 Automatic Backups

Before a report is modified, a timestamped copy is automatically created.

```text
Current Report
      │
      ├──────────────► Backup
      │
      ▼
  Processing
      │
      ▼
Updated Report
```

This provides an additional layer of protection for automated document operations.

---

## 🗃️ Processing History

The application uses a **SQLite** database to persist information such as:

* Registered companies
* Previously processed files
* Photograph hashes
* Update history

This allows important processing decisions to persist even after the application is closed.

---

## 🧪 Simulation Mode

Before modifying actual documents, the application can execute the workflow in simulation mode.

This allows the operator to verify what would be processed without modifying:

```text
❌ Word Documents
❌ Processing History
❌ Backups
```

This feature makes it easier to validate operations before performing an actual report update.

---

## 📦 Batch Processing

In addition to individual updates, the system can process all active companies automatically.

```text
              DATE
                │
        ┌───────┼───────┐
        ▼       ▼       ▼
    COMPANY A COMPANY B COMPANY C
        │       │       │
        ▼       ▼       ▼
      PHOTOS   PHOTOS   PHOTOS
        │       │       │
        ▼       ▼       ▼
       WORD     WORD     WORD
```

Each company is processed independently.

This means that an issue related to one company does not necessarily need to interrupt the processing of all remaining companies.

---

## 🖥️ User Interface

The application includes a graphical interface designed to simplify daily operation.

### Main Interface

```text
[ ADD APPLICATION SCREENSHOT HERE ]
```

### Processing Demo

```text
[ ADD PROCESSING GIF / VIDEO HERE ]
```

### Report Result

```text
[ ADD BEFORE / AFTER SCREENSHOTS HERE ]
```

> Public demonstrations should only contain fictional or properly anonymized data.

---

## 🏗️ Conceptual Architecture

```text
                 USER INTERFACE
                       │
           ┌───────────┴───────────┐
           ▼                       ▼
     CONFIGURATION              COMPANIES
           │                       │
           └───────────┬───────────┘
                       ▼
                REPORT SERVICE
                       │
           ┌───────────┼───────────┐
           ▼           ▼           ▼
        IMAGES      DATABASE      WORD
           │           │           │
           ▼           ▼           ▼
      PROCESSING     SQLITE    COM / DOCX
           │           │           │
           └───────────┼───────────┘
                       ▼
                 FINAL REPORT
```

The separation between image acquisition, processing, persistence, and document manipulation makes the application easier to maintain and evolve.

---

## 🛠️ Technologies

**Python** — Core application language

**Pillow** — Image processing and normalization

**python-docx** — DOCX document manipulation

**pywin32 / COM Automation** — Microsoft Word integration on Windows

**SQLite** — Local configuration and processing history

**SHA-256** — Image identification and duplicate prevention

**PyInstaller** — Windows executable distribution

**Pytest** — Automated testing

---

## 🧪 Quality & Testing

The project includes automated tests covering different parts of the workflow, including scenarios related to:

* Image processing
* EXIF orientation
* Date resolution
* Paths containing spaces and special characters
* Configuration management
* SQLite persistence
* Company registration
* Batch processing
* Missing or empty folders
* Unavailable documents
* Reporting period changes
* Simulation mode
* Backup creation
* Duplicate prevention
* Recovery from specific processing failures

---

## 📈 Impact

### Manual Workflow

```text
Find Company
      ↓
Find Photos
      ↓
Verify Date
      ↓
Open Report
      ↓
Locate Section
      ↓
Insert Photos
      ↓
Adjust Images
      ↓
Save Document
      ↓
Repeat for Next Company
```

### Automated Workflow

```text
Select Date
      ↓
Run Update
      ↓
System Processes Companies
      ↓
Reports Updated
```

The application transfers most repetitive tasks to software while keeping the operator responsible for validating the final result.

---

## 🔐 Source Code & Data

Because this solution was developed for a real operational workflow, the source code remains in a private repository.

This public repository contains only documentation and demonstrations of the application's functionality.

The following are **not publicly available**:

* Proprietary source code
* Corporate documents
* Real photographs
* Client information
* Databases
* Credentials
* Internal configurations
* Production files

---

## 👨‍💻 About the Project

This project was developed with a focus on:

* Business process automation
* Image processing
* Microsoft Office automation
* Data integrity
* Duplicate operation prevention
* Local persistence
* Desktop application development
* Failure recovery and reliability

---

**Developed by Leonardo de Carvalho da Costa**
