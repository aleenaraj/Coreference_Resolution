## **Coreference Resolution for a text using Stanford CoreNLP**

This is a Python wrapper for Stanford University's NLP group's Java-based CoreNLP tools. It can either be imported as a module or run as a JSON-RPC server. Because it uses many large trained models (requiring 3GB RAM on 64-bit machines and usually a few minutes loading time), most applications will probably want to run it as a server.

1. Python interface to Stanford CoreNLP tools: ***Tagging***, ***Phrase-Structure Parsing***, ***Dependency Parsing***, ***Named-entity recognition***, and ***Coreference resolution***.

2. Runs an JSON-RPC server that wraps the Java server and outputs JSON.

3. Outputs parse trees which can be used by nltk.

For download and installation follow the below mentioned repository by Dustin smith

https://github.com/dasmith/stanford-corenlp-python

### Coref Resolution
**Coreference resolution** is the task of finding all expressions that refer to the same 
entity in a text. It is an important step for a lot of higher level NLP tasks that 
involve natural language understanding such as document summarization, question answering, and information extraction.

**Stanford CoreNLP** system implements the **Multi-Pass Sieve Coreference Resolution** (or anaphora resolution) system as described in https://nlp.stanford.edu/pubs/coreference-emnlp10.pdf


So given a text say:

**Text**: "Obama is the the president of US. Florida is a nice place. **It** is good. **He** lives in Florida.
            Trump is the current president. **He** owns Trump tower"

The system is expected to detect the corresponding Antecedent for the highlighted Anaphora. Hence the Coreferenced Text should
be:

**Coreferenced Text**:  “Obama is the the president of US. Florida is a nice place. ***Florida*** is good. ***Obama*** lives in Florida. Trump is the current president. ***Trump*** owns Trump tower”



### Implementation

            >>text = """Obama is the the president of US. Florida is a nice place. It is good. He lives in Florida.
                        Trump is the current president. He owns Trump tower""".



           >>server = jsonrpc.ServerProxy(jsonrpc.JsonRpc20(),
                             jsonrpc.TransportTcpIp(addr=("127.0.0.1", 8080)))

           >>result = loads(server.parse(text))
           >>pprint(result['coref'])
           
This gives the corresponding anaphora resolution for any given text. Coreference Resolution for the above text is:

        [[[[u'the the president of US', 0, 4, 2, 7], [u'Obama', 0, 0, 0, 1]],
        [[u'He', 3, 0, 0, 1], [u'Obama', 0, 0, 0, 1]]],
        [[[u'a nice place', 1, 4, 2, 5], [u'Florida', 1, 0, 0, 1]],
        [[u'It', 2, 0, 0, 1], [u'Florida', 1, 0, 0, 1]],
        [[u'Florida', 3, 3, 3, 4], [u'Florida', 1, 0, 0, 1]]],
        [[[u'the current president', 4, 4, 2, 5], [u'Trump', 4, 0, 0, 1]],
        [[u'He', 5, 0, 0, 1], [u'Trump', 4, 0, 0, 1]],
        [[u'Trump', 5, 2, 2, 3], [u'Trump', 4, 0, 0, 1]]]]

Pass the text through the coref_rephrase() function. Functionality in the coref_rephrase includes running corenlp coref on the text, tokenizing the given sentence to the most granular level that being word tokenization and then replacing the anaphora in the text with the corresponding antecedent and hence the final result.

The final result after rephrasing the text with its coreference is:

        [['Obama is the the president of US .',
        'Florida is a nice place .',
        u'Florida is good .',
        u'Obama lives in Florida .',
        'Trump is the current president .',
        u'Trump owns Trump tower']]
