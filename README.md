<div align="center">
  
  <h1>Tutorial-Azure-Cognitive-Search</h1>
  
</div>

<div align="center">

  ![image](https://github.com/DevGustavus/Tutorial-Azure-ML/assets/103593279/9e8b97cf-93ac-4daf-b9e3-f0fc656ec883)
  
<hr>
  
  ![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/6d77101a-dc4b-4d37-9889-6c519c530d9a)
  
</div>

<br> 

## Parte 1: Explore um índice de pesquisa de IA do Azure (UI).

Vamos imaginar que você trabalha para a Fourth Coffee, uma rede nacional de cafeterias. Você foi solicitado a ajudar a construir uma solução de mineração de conhecimento que facilite a busca por insights sobre as experiências dos clientes. Você decide construir um índice de pesquisa de IA do Azure usando dados extraídos de avaliações de clientes.

Neste laboratório, você irá:

- Criar recursos do Azure
- Extrair dados de uma fonte de dados
- Enriquecer os dados com habilidades de IA
- Usar o indexador do Azure no portal do Azure
- Consultar seu índice de pesquisa
- Revisar resultados salvos em um Knowledge Store

<hr>

## Parte 2: Recursos do Azure necessários.

A solução que você criará para a Fourth Coffee requer os seguintes recursos em sua assinatura do Azure:

- Um recurso de Azure AI Search, que gerenciará a indexação e a consulta.
- Um recurso de serviços de IA do Azure, que fornece serviços de IA para habilidades que sua solução de pesquisa pode usar para enriquecer os dados na fonte de dados com insights gerados por IA.

> Nota: Seus recursos de Azure AI Search e Azure AI services devem estar na mesma localização!

- Uma conta de armazenamento com containers de blob, que armazenará documentos brutos e outras coleções de tabelas, objetos ou arquivos.

### Criar um recurso de Azure AI Search

1. Faça login no portal do Azure.
2. Clique no botão + Criar um recurso, pesquise por Azure AI Search e crie um recurso de Azure AI Search com as seguintes configurações:
   - Assinatura: Sua assinatura do Azure.
   - Grupo de recursos: Selecione ou crie um grupo de recursos com um nome único.
   - Nome do serviço: Um nome único.
   - Localização: Escolha qualquer região disponível.
   - Camada de preços: Básico
3. Selecione Revisar + criar e, após ver a resposta Validação com sucesso, selecione Criar.
4. Após a conclusão da implantação, selecione Ir para o recurso. Na página de visão geral do Azure AI Search, você pode adicionar índices, importar dados e pesquisar índices criados.

### Criar um recurso de serviços de IA do Azure

Você precisará provisionar um recurso de serviços de IA do Azure que esteja na mesma localização que o seu recurso de Azure AI Search. Sua solução de pesquisa usará esse recurso para enriquecer os dados no datastore com insights gerados por IA.

1. Volte à página inicial do portal do Azure. Clique no botão + Criar um recurso e pesquise por Azure AI services. Selecione criar um plano de serviços de IA do Azure. Você será levado a uma página para criar um recurso de serviços de IA do Azure. Configure-o com as seguintes configurações:
   - Assinatura: Sua assinatura do Azure.
   - Grupo de recursos: O mesmo grupo de recursos que o seu recurso de Azure AI Search.
   - Região: A mesma localização que o seu recurso de Azure AI Search.
   - Nome: Um nome único.
   - Camada de preços: Standard S0
2. Marque a caixa de seleção para aceitar os termos e condições.
3. Selecione Revisar + criar. Após ver a resposta Validação passada, selecione Criar.
4. Aguarde a conclusão da implantação e, em seguida, visualize os detalhes da implantação.

### Criar uma conta de armazenamento

1. Volte à página inicial do portal do Azure e selecione o botão + Criar um recurso.
2. Pesquise por conta de armazenamento e crie um recurso de conta de armazenamento com as seguintes configurações:
   - Assinatura: Sua assinatura do Azure.
   - Grupo de recursos: O mesmo grupo de recursos que o seu recurso de Azure AI Search e Azure AI services.
   - Nome da conta de armazenamento: Um nome único.
   - Localização: Escolha qualquer localização disponível.
   - Desempenho: Padrão
   - Redundância: Armazenamento redundante localmente (LRS)
3. Clique em Revisar e depois em Criar. Aguarde a conclusão da implantação e, em seguida, acesse o recurso implantado.
4. Na conta de armazenamento do Azure que você criou, no painel de menu à esquerda, selecione Configuração (em Configurações).
5. Altere a configuração para Permitir acesso anônimo de Blob para Habilitado e depois selecione Salvar.

<hr>

## Parte 3: Upload de documentos para o Azure Storage.

1. No painel de menu à esquerda, selecione Containers.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/57e54a63-f6a8-4e64-b4b6-417685c9b9db)

2. Selecione + Container. Uma painel do lado direito se abrirá.

3. Digite as seguintes configurações e clique em Criar:
- Nome: coffee-reviews
- Nível de acesso público: Container (acesso de leitura anônimo para contêineres e blobs)
- Avançado: sem alterações.

4. Em uma nova aba do navegador, baixe as avaliações de café compactadas de https://aka.ms/mslearn-coffee-reviews e extraia os arquivos para a pasta de avaliações.

> ### Os arquivos estão disponíveis na pasta "reviews" do repositório!

6. No portal do Azure, selecione seu contêiner coffee-reviews. No contêiner, selecione Upload.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/8dd46918-00be-4b16-9f49-b57aae756918)

6. No painel Upload de blob, selecione Selecionar um arquivo.

7. Na janela Explorer, selecione todos os arquivos na pasta de avaliações, selecione Abrir e depois selecione Upload.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/029c935c-f15e-4055-93d3-0ab86aa1d3d7)

8. Após o carregamento ser concluído, você pode fechar o painel Upload de blob. Seus documentos agora estão no seu contêiner de armazenamento coffee-reviews.

<hr>

## Parte 4: Indexar os documentos.

Depois de ter os documentos no armazenamento, você pode usar o Azure AI Search para extrair insights dos documentos. O portal do Azure fornece um Assistente de Importação de Dados. Com este assistente, você pode criar automaticamente um índice e um indexador para fontes de dados suportadas. Você usará o assistente para criar um índice e importar seus documentos de pesquisa do armazenamento para o índice do Azure AI Search.

1. No portal do Azure, navegue até o seu recurso de Azure AI Search. Na página Visão geral, selecione Importar dados.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/6d4c90c0-cf7b-4311-8d1b-cc68920d0d59)

2. Na página Conectar-se aos seus dados, na lista Fonte de dados, selecione Armazenamento de Blobs do Azure. Preencha os detalhes do armazenamento de dados com os seguintes valores:
- Fonte de dados: Armazenamento de Blobs do Azure
- Nome da fonte de dados: coffee-customer-data
- Dados a extrair: Conteúdo e metadados
- Modo de análise: Padrão
- String de conexão: *Selecione Escolher uma conexão existente. Selecione sua conta de armazenamento, selecione o contêiner coffee-reviews e clique em Selecionar.
- Autenticação de identidade gerenciada: Nenhum
- Nome do contêiner: esta configuração é preenchida automaticamente após você escolher uma conexão existente.
- Pasta de blob: Deixe em branco.
- Descrição: Avaliações para lojas da Fourth Coffee.

3. Selecione Próximo: Adicionar habilidades cognitivas (Opcional).
4. Na seção Anexar Serviços Cognitivos, selecione seu recurso de serviços de IA do Azure.
5. Na seção Adicionar enriquecimentos:
- Altere o nome do Conjunto de habilidades para coffee-skillset.
- Selecione a caixa de seleção Habilitar OCR e mesclar todo o texto no campo merged_content.
> Nota: É importante selecionar Habilitar OCR para ver todas as opções de campos enriquecidos.

- Certifique-se de que o campo de Dados da fonte esteja definido como merged_content.
- Altere o nível de granularidade do enriquecimento para Páginas (pedaços de 5000 caracteres).
- Não selecione Habilitar enriquecimento incremental
- Selecione os seguintes campos enriquecidos:

| Habilidade Cognitiva        | Parâmetro       | Nome do campo   |
|-----------------------------|-----------------|-----------------|
| Extrair nomes de locais     |                 | locais          |
| Extrair frases-chave        |                 | fraseschave     |
| Detectar sentimento         |                 | sentimento      |
| Gerar tags de imagens       |                 | imageTags       |
| Gerar legendas de imagens   |                 | imageCaption    |

6. Em Salvar enriquecimentos em um repositório de conhecimento, selecione:

- Projeções de imagem
- Documentos
- Páginas
- Frases-chave
- Entidades
- Detalhes da imagem
- Referências de imagem
> Nota: Se aparecer um aviso pedindo uma String de Conexão da Conta de Armazenamento.
> ![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/565d9f59-1f6d-411d-8ce6-976ab7e22b28) <br>
> a. Selecione Escolher uma conexão existente. Escolha a conta de armazenamento que você criou anteriormente. <br>
> b. Clique em + Contêiner para criar um novo contêiner chamado knowledge-store com o nível de privacidade definido como Privado e selecione Criar. <br>
> c. Selecione o contêiner knowledge-store e depois clique em Selecionar na parte inferior da tela. <br>

7. Selecione Projeções de blob do Azure: Documento. Uma configuração para Nome do contêiner com o contêiner knowledge-store preenchido automaticamente será exibida. Não altere o nome do contêiner.

8. Selecione Próximo: Personalizar índice de destino. Altere o Nome do índice para coffee-index.

9. Certifique-se de que a Chave esteja definida como metadata_storage_path. Deixe o nome do Sugestor em branco e o modo de Pesquisa autopopulado.

10. Revise as configurações padrão dos campos do índice. Selecione filtrável para todos os campos que já estão selecionados por padrão.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/362af9d6-ad71-499e-a7b2-fdcf170f38ac)

11. Selecione Próximo: Criar um indexador.

12. Altere o Nome do indexador para coffee-indexer.

13. Deixe o Agendamento configurado como Uma vez.

14. Expanda as opções avançadas. Certifique-se de que a opção Codificar Chaves em Base-64 esteja selecionada, pois a codificação de chaves pode tornar o índice mais eficiente.

15. Selecione Enviar para criar a fonte de dados, o conjunto de habilidades, o índice e o indexador. O indexador é executado automaticamente e executa o pipeline de indexação, que:
- Extrai os campos de metadados e conteúdo do documento da fonte de dados.
- Executa o conjunto de habilidades de habilidades cognitivas para gerar mais campos enriquecidos.
- Mapeia os campos extraídos para o índice.

16. Volte para a página do seu recurso de Azure AI Search. No painel esquerdo, em Gerenciamento de Pesquisa, selecione Indexadores. Selecione o indexador coffee-indexer recém-criado. Aguarde um minuto e selecione &orarr; Atualizar até que o Status indique sucesso.

17. Selecione o nome do indexador para ver mais detalhes.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/2d30e13d-cbbe-4e9a-912a-d5106aaee797)

<hr>

## Parte 5: Consulta ao índice.

Utilize o Search explorer para escrever e testar consultas. O Search explorer é uma ferramenta integrada ao portal do Azure que oferece uma maneira fácil de validar a qualidade do seu índice de pesquisa. Você pode usar o Search explorer para escrever consultas e revisar resultados em JSON.

1. Na página Visão geral do seu serviço de pesquisa, selecione Search explorer no topo da tela.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/61238d68-5875-47db-aa54-746543caf476)

2. Observe como o índice selecionado é o coffee-index que você criou. Abaixo do índice selecionado, altere a visualização para o modo JSON.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/de8e9779-40ab-475a-8747-82f04a7be6e3)

No campo de editor de consulta JSON, copie e cole:
````
{
    "search": "*",
    "count": true
}
````

1. Selecione Pesquisar. A consulta de pesquisa retorna todos os documentos no índice de pesquisa, incluindo uma contagem de todos os documentos no campo @odata.count. O índice de pesquisa deve retornar um documento JSON contendo seus resultados de pesquisa.
2. Agora vamos filtrar por localização. No campo de editor de consulta JSON, copie e cole:
````
{
 "search": "locations:'Chicago'",
 "count": true
}
````

3. Selecione Pesquisar. A consulta pesquisa todos os documentos no índice e filtra as avaliações com uma localização em Chicago. Você deve ver 3 no campo @odata.count.
4. Agora vamos filtrar por sentimento. No campo de editor de consulta JSON, copie e cole:
````
{
 "search": "sentiment:'negative'",
 "count": true
}
````

5. Selecione Pesquisar. A consulta pesquisa todos os documentos no índice e filtra as avaliações com um sentimento negativo. Você deve ver 1 no campo @odata.count.

> Nota: Observe como os resultados são classificados por @search.score. Esta é a pontuação atribuída pelo mecanismo de pesquisa para mostrar o quão bem os resultados correspondem à consulta fornecida.

6. Um dos problemas que talvez queiramos resolver é por que pode haver certas avaliações. Vamos dar uma olhada nas frases-chave associadas à avaliação negativa. O que você acha que pode ser a causa da avaliação?

<hr>

## Parte 6: Revisar a Knowledge Store.

Vamos ver o poder da Knowledge Store em ação. Quando você executou o Assistente de Importação de Dados, também criou uma Knowledge Store. Dentro da Knowledge Store, você encontrará os dados enriquecidos extraídos pelas habilidades de IA persistindo na forma de projeções e tabelas.

1. No portal do Azure, volte para a sua conta de armazenamento do Azure.

2. No painel de menu à esquerda, selecione Containers. Selecione o container knowledge-store.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/a6484d22-5bec-49b6-b33a-c5625c9d00e3)

3. Selecione qualquer um dos itens e, em seguida, clique no arquivo objectprojection.json.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/03ed9d6f-1427-4ad8-88ee-ba965acda394)

4. Selecione Editar para ver o JSON produzido para um dos documentos do seu armazenamento de dados do Azure.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/c84d10f0-e4a4-4ad7-9ad9-a2d073650f0d)

5. Selecione o breadcrumb de blob de armazenamento no canto superior esquerdo da tela para voltar aos Containers de armazenamento.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/046c8510-457e-4ed8-b8c6-75bc89b2de10)

6. Nos Containers, selecione o container coffee-skillset-image-projection. Selecione qualquer um dos itens.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/f039f65a-1238-42ff-a640-028b5c90203f)

7. Selecione qualquer um dos arquivos .jpg. Selecione Editar para ver a imagem armazenada do documento. Observe como todas as imagens dos documentos são armazenadas dessa maneira.

![image](https://github.com/DevGustavus/Tutorial-Azure-Cognitive-Search/assets/103593279/05e6ed2f-eda7-45a4-829c-e8fb47b34d65)

8. Selecione o breadcrumb de blob de armazenamento no canto superior esquerdo da tela para voltar aos Containers de armazenamento.

9. Selecione Navegador de armazenamento no painel esquerdo e selecione Tabelas. Há uma tabela para cada entidade no índice. Selecione a tabela coffeeSkillsetKeyPhrases.

Observe as frases-chave que a Knowledge Store conseguiu capturar do conteúdo nas avaliações. Muitos dos campos são chaves, então você pode vincular as tabelas como um banco de dados relacional. O último campo mostra as frases-chave que foram extraídas pelo conjunto de habilidades.

<hr>

## Conclusão:

### Para que serve?

As ferramentas de inteligência artificial (IA) do Azure simplificam a análise de documentos, pesquisas e depoimentos, acelerando a obtenção de insights sobre a satisfação de clientes em relação a produtos e serviços. Essa tecnologia permite às empresas automatizar a análise de feedbacks, identificar tendências e padrões, e tomar decisões mais embasadas em dados. No mercado, a IA da Azure impulsiona a competitividade ao oferecer uma análise eficiente e precisa, resultando em melhorias na experiência do cliente e no crescimento do negócio.
