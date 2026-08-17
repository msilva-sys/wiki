/> https://livemode-projetos.atlassian.net/browse/AIRTABLEGC-34

No projeto do livescript, há uma função, 
```javascript 
recordAirtableCall
```
que extrai da requisição os parâmetros de rota, o método, projeções de campo, e alguns outras métricas. Os campos guardados estão em camelCase.
## Problema
Há algumas diferenças de nome nos campos entre o livescript e no proxy e como esses campos são produzidos. Por exemplo: no livescript, o valor do campo ```airtable_operation``` pode ser ```getEventsPaginated``` enquanto que no proxy esse campo é derivado do método http. 
## TODO
- [x] Adicionar ```hasFilter``` e ```hasProjection``` metrics no proxy.
- [x] Adicionar ```recordBytes``` e ```recordCount``` às métricas coletadas

## Notas
Livescript usa algumas queries de select que vão por meio de um body em uma POST request. Para conseguirmos os logs relacionados ao has_filter e a operação, vamos precisar identificar selects feitos por POST e ler o body.