# Classificações

Esta pasta tem ajuda para classificações *supervisionadas* e *não-supervisionadas*:

# Leituras

1. [Unsupervised learning-and-data-clustering](https://towardsdatascience.com/unsupervised-learning-and-data-clustering-eeecb78b422a)

# Tutoriais

1. [Pacote caret](http://topepo.github.io/caret/index.html) - muito boa referencia do pacote caret e como implementar **classificação supervisionada**
1. [Caret package do R](https://cran.r-project.org/web/packages/caret/vignettes/caret.html) - pacote citado no artigo acima
1. [Introdução à Machine Learning no R](https://lgatto.github.io/IntroMachineLearningWithR/index.html)
1. [Outro tutorial](https://quantdev.ssri.psu.edu/tutorials/unsupervised-machine-learning-hclust-pvclust-cluster-mclust-and-more)

# Classificação 

Como classificar objetos? ou como testar uma classificação? 

Técnicas multivariadas e de Machine Learning (Aprendizado de Máquina) permitem *testar* (supervisionada) ou *postular* (não supervisionada) hipóteses de grupamentos para objetos segundo suas similaridades em um ou mais conjuntos de medições de múltiplos atributos. Esses técnicas são muito úteis em taxonomia numérica e na formulação de modelos preditivos, que é o foco desse guia.

[Este tutorial](http://topepo.github.io/caret) é uma importante referência de passos.

## Pré-processamento e AED

AED = análise exploratória de dados. Conheça seus dados, **grafique** seus dados brutos.

Essa uma etapa importante de preparação dos dados. É também o momento de definir os diferentes conjuntos de dados que você tem. Por exemplo, vocês podem definir seus dados:
1. NIR.total - média dos espectros abaxial+adaxial 
1. NIR.abaxial - apenas os espectros abaxial
1. NIR.adaxial - apenas os espectros adaxial
1. NIR.total.pca - PCA de NIR.total
1. morfo.veg - caracteres vegetativos apenas (matriz completa) 
1. morfo.total - todos os caracteres (valores gerado por NMDS da matriz de dissimilaridade `daisy` do pacote (cluster), veja abaixo)
1. morfo.shape - morfometria geométrica de folhas (ou junto com morfo.veg)
1. classificacoes - suas classificacoes a serem testadas

Depois você pode comparar o poder preditivo desses diferentes dados ou de diferentes combinações desses dados.

*Nota* - garanta que todos as tabelas dados tenham uma coluna em comum que seja o identificador das linhas (e.g. coletor+numero) e no R atribua essa coluna também como nome de linhas (`rownames()`) - isso permite você junte esses dados quando precisar ou coloque as linhas facilmente na mesma ordem evitando (entenda indexação de matrizes e data.frames, no Livro do R, se isso não está claro para você). 


### Dicas
1. [Pre-processamento](http://topepo.github.io/caret/pre-processing.html) - leia com atenção os passos e as ferramentas para isso implementadas no pacote Caret;
1. **Gere figuras** que descrevam seus dados. Essa é uma etapa fundamental da AED (análise exploratória de dados). Veja dicas de AED no [Livro do R ](https://labotam.github.io/). Figuras que podem ser utilizadas para gerar um material suplementar que descreva seus dados, ou junto com os dados num repositóiro.
1. Dados NIR são simples, os valores estão todos na mesma escala e são todos numéricos. Os principais problemas são:
  - As variáveis não são independentes, num espectro o valor de uma variável não é independente das variáveis próximas (isso gera colinearidade e isso pode gerar overfit do modelo) - isso pode ser eliminado *reduzindo a dimensão* usando PCA, e/ou fazendo *seleção de variáveis* ou *derivando*
1. Dados morfológicos são mais problemáticos - aqui a AED é muito importante para detectar valores ausentes, outliers ou erros de digitação.
  - Valores ausentes - *não imputar*, ou seja, não estimar valores ausentes, evite isso (você não tem o dado, aceite). Uma forma de incluir todas as variávies (colunas) e não perder nenhum objeto (linha) pode ser:
    - calcular uma *matriz de dissimilaridade* usando a função `daisy` do pacote (Cluster), que trata cada par de forma independente. O índice de *gower* é um bom índice para dados morfológicos;
    - se tua análise de classificação não aceita matrizes de distância, *use  NMDS* para transformar essa matriz em variáveis morfológicas ordenadas, faça o pré-tratamento das variáveis ordenadas e utilize essa matriz como dado morfológico;
  - Variáveis categóricas (fatores) não são aceitas em modelos preditivos como LDA, por exemplo. Pode resolver isso criando uma *variável binária* (0 ou 1) para cada categoria se tiver + de 2 categorias. Se apenas duas categorias converta o fator em numérico que resolve o problema `(as.numeric(as.factor(dados$coluna)))`. **NOTA** - examine antes se suas múltiplas categorias não fazem uma variável semi-quantitativa (ordinal), porque neste caso as categorias podem ser substituidas por números;

### Supervisionada

Numa classificação supevisionada as classes dos objetos (i.e. as categorias a serem testadas) são conhecidas e o objetivo pode ser: (1) gerar um modelo preditivo,  (2) testar e/ou comparar classificações.

#### Usando os recurso do caret
 
- [A lógica explicada no help do pacote](http://topepo.github.io/caret/model-training-and-tuning.html#model-training-and-parameter-tuning). 
- A função _train()_ desse pacote serve para:
1. avaliar, usando resampling (reamostragem) o efeito dos valores dos parametros do modelo escolhido na performance preditiva; a reamostragem é o processo feito na validação cruzada k-fold (repete várias vezes subconjuntos de dados diferentes), validação cruzada leave-one-out, ou bootstraping. Leia [data splitting](http://topepo.github.io/caret/data-splitting.html) as opções de validação cruzada. Importante aqui que todas classes tenham representantes no train e no test. Por isso que o N amostral por classe precisa ser grande. Se temos apenas poucos objetos (individuos) por classe (especies) qualquer data-splitting vai ficar parecido com LEAVE-ONE-OUT (LOOCV). LOO é um data split que pode levar a overfit, mas as vezes é única forma de validação cruzada possível;
2. escolher os parametros do 'melhor modelo';
3. estimar a performance do 'melhor modelo';
Assim, os passos são o seguinte:
1. Para cada conjunto de dados e classificação usa a função _train_ para testar a acurácia de diferentes métodos de classificação supervisionada (e.g. LDA, RandomForest, KNN, etc.). Veja a Figura abaixo extraída de lá que mostra a lógica do procedimento dessa etapa.
2. Compara a acurácia desses diferentes métodos para o dado e classificação testados;
3. Seleciona o melhor modelo (método de classificação e seus parâmetros)
4. Compara a acurácia das diferentes CLASSIFICAÇÕES (se tiver + de uma) segundo os melhores modelos selecionados para cada uma

![](http://topepo.github.io/caret/premade/TrainAlgo.png) 

*Figura mostrando o que o train faz. Link da da imagem: http://topepo.github.io/caret/premade/TrainAlgo.png*


#### Exemplo

```
  #cria o controle para todas as analises
    #leave group out(mais rapido que leave one out)
    myControl_LGOCV <- trainControl(
        method = "LGOCV",  #substitua por LOOCV se quiser leave one out
        number = 100,
        #porcentagem a ser incluida no TRAIN set (apenas para LGOCV)
        p = 0.8,
        
        ## outras definicoes
        # imprime iteracao na tela?
        verboseIter = TRUE,
        # salva as predicoes
        savePredictions = TRUE,
        # salva a probabilidade das classes
        classProb = TRUE
     )
     
     #objeto com seus dados (só variáveis)   
     dado.teste = #seus dados
     grupo.teste = #suas categorias, classes, especies (mesma ordem que dado.teste)
     
     #talvez precise eliminar classes que tenham menos que 3 individuos
      tb = table(grupo.teste)
      filtro = grupo.teste%in%(names(tb)[tb>=3])
      dado.teste = dado.teste[filtro,]
      grupo.teste = grupo.teste[filtro]
     
     #define um inicio para sorteios aleatorios em todos os processos (garante reprodutibilidade)
     set.seed(453)
     
     ##COM SELECAO DE VARIAVEIS - STEPWISE LDA com selecao de variaveis stepwise (ISSO PODE DEMORAR MUITO PARA DADOS NIR)
       sampling = c("none","down","up","smote","rose")
       steplda.par<-expand.grid(maxvar=ceiling(nrow(dados.teste)*0.3),direction='both')
       stepLDA <- train(x=dados.teste,y=grupo.teste,method ="stepLDA", trControl = myControl_LOOCV,tuneGrid=steplda.par)
            
     ##LDA SIMPLES (usando o LGOCV)
       lda <- train(x=dados.teste,y=grupo.teste,method="lda",trControl = myControl_LGOCV)
```

Repita o processo acima para cada CLASSIFICACAO+DADOS. Pode fazer isso num **loop* simples como, por exemplo:

```
#junto meus dados numa lista (com nomes)
meus.dados = list(nir.total=nir.total, morfo.veg=morfo.veg) #tantas quanto tiver
#note que os nomes são os nomes dos objetos
names(meus.dados)
grupo.teste = #isso é apenas a coluna das categorias

#coloque os comando acima dentro de um loop
resultados = list() #lista vazia
for(d in 1:length(meus.dados)) {
   #pega o dado
    dado.teste = meus.dados[[d]]  #duplo colchete
   
   #coloque aqui os scripts acima que interessa
   
   #....
   

   #salva os resultados
   nome = names(meus.dados)[d]  #usando nome você sabe o que o resultado tem dizendo names(resultado)
   resultados[[nome]] = lda

}
```

Compare as acurácias das classificacoes:

```
resamp <- resamples(resultados)
summary(resamp)
#pode plotar com isso
#install.packages('lattice',dependencies=T)
lattice::bwplot(resamp, metric = "Accuracy", cex.axis=0.8)
```

