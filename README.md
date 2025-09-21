# D2L 9.5 — Tradução Automática e o Conjunto de Dados (eng→fra)

**Fonte de dados usada:** D2L S3  
**MAX_LEN:** 50 | **MIN_FREQ:** 2  
**Tokenização:** nível de palavra (normalização simples + separação de pontuação)  


## Resultados usados

**Tamanho do vocabulário (execução completa):**
- SRC (inglês): 4764
- TGT (francês): 8224

**Varredura de `num_examples` → tamanhos de vocabulário:**

|   num_examples |   vocab_src |   vocab_tgt |
|---------------:|------------:|------------:|
|            500 |         159 |         163 |
|           1000 |         266 |         321 |
|           2000 |         454 |         585 |
|           6000 |        1037 |        1454 |
|          10000 |        1505 |        2252 |
|          20000 |        2459 |        3828 |

**Distribuição de comprimentos:** o alvo em francês aparece levemente deslocado à direita (sequências mais longas), coerente com maior variação morfológica e uso de contrações/acentos.

## 9.5.7 — Pergunta 1

> "Tente valores diferentes do argumento `num_examples`. Como isso afeta os tamanhos do vocabulário do idioma de origem e do idioma de destino?"

Crescimento monotônico e sublinear: ao aumentar `num_examples` de 500 → 20 000, ambos os vocabulários crescem continuamente, mas com ganhos marginais decrescentes, compatíveis com a lei de Zipf. As ocorrências mais frequentes surgem cedo; exemplos extras revelam palavras cada vez mais raras, expandindo o vocabulário gradualmente.

Vocabulário do francês > inglês: em toda a varredura (e na execução completa) o vocabulário TGT supera o SRC. Razões típicas:
- maior variação morfológica do francês;
- contrações e apóstrofos (ex.: l’, c’, qu’), tratados como tokens distintos com tokenização simples;
- acentos e variantes ortográficas (ex.: é, è, ê, ç).

Implicações práticas: controlar o tamanho via `min_freq > 1` e/ou limitar o vocabulário ajuda a reduzir sparsity e casos OOV sem prejudicar o desempenho inicial.

## 9.5.7 — Pergunta 2

> "O texto em alguns idiomas, como chinês e japonês, não tem separadores de palavra. A tokenização em nível de palavra ainda é uma boa ideia? Por quê?"

Não é ideal depender apenas de tokenização em nível de palavra nesses idiomas, pois não há delimitadores explícitos e a segmentação correta exigiria heurísticas complexas ou modelos adicionais. Métodos de subpalavra (BPE, WordPiece, SentencePiece) são preferíveis porque:
- lidam melhor com palavras raras/OOV ao decompor termos em unidades reutilizáveis;
- funcionam bem em escritas contínuas e línguas morfologicamente ricas;
- reduzem o vocabulário efetivo enquanto preservam expressividade;
- são mais robustos a variantes ortográficas e novas composições.

Tokenização por caractere aparece como baseline em alguns cenários, mas aumenta o comprimento das sequências e dificulta modelar dependências longas em comparação a soluções de subpalavra bem treinadas.

**Referências:**
- [Dive into Deep Learning — Seção 9.5](https://d2l.ai/chapter_recurrent-modern/machine-translation-and-dataset.html)
- [Sennrich, Haddow & Birch (2016). *Neural Machine Translation of Rare Words with Subword Units (BPE).*](https://arxiv.org/abs/1508.07909)
- [Kudo (2018). *SentencePiece: A simple and language independent subword tokenizer and detokenizer for Neural Text Processing.*](https://aclanthology.org/D18-2012/)
- [Schuster & Nakajima (2012). *Japanese and Korean voice search.*](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/37842.pdf)

