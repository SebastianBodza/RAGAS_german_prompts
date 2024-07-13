# Translated German RAGAS prompts
Ragas offers the option to adjust the prompts to different languages. 
```python
from ragas.testset.generator import TestsetGenerator
from ragas.testset.evolutions import simple, reasoning, multi_context
from langchain_openai import ChatOpenAI, OpenAIEmbeddings

generator_llm = ChatOpenAI(model="gpt-3.5-turbo-0125")
critic_llm = ChatOpenAI(model="gpt-4o")
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
llm = ChatOpenAI(model="gpt-3.5-turbo-0125")
llm_instruct = ChatOpenAI(model="gpt-3.5-turbo-instruct")

generator = TestsetGenerator.from_langchain(generator_llm , critic_llm , embeddings)
language = "german"

generator.adapt(language, evolutions=[simple, reasoning, multi_context])
generator.save(evolutions=[simple, reasoning, multi_context])
```

The main problem is that the adapt function uses the OpenAI services to translate the examples etc.. However, there have been multiple [Issues](#issues) when trying to run the generator. This could also be due to the usage of the `generator_llm` in the adapt function and in this case GPT-3.5.  

# 'Installation'
You can add the adjusted files to `C:\Users\<USER>\.cache\ragas\german` and the prompts will be automatically loaded. 

Only the main issues were fixed to even be able to load the files. If there are still issues present, Let me know. 

# Issues: 
1. The prompt for translation is quite bad. Like: `translate_to: ...`. This results in incosistent translation like:   
```
translate_to: german\ninput: I love chocolate. [...]
output: Ich liebe Schokolade. [...]
``` 
2. Not checked it 100% but it seems like no json forced output was used during translation and the parsing is not properly working. Partially the Code starting ``` are in the example. Sometimes there is also the json tag *(see the following example)*. 
3. Not properly translated scoring json, no real scores are present. Only translated json: 
```json
"output": "```json\n{\n  \"clarity\": \"Klarheit\",\n  \"depth\": \"Tiefe\",\n  \"structure\": \"Struktur\",\n  \"relevance\": \"Relevanz\"\n}\n```"
```
4. Missing outputs e.g.: 
```
original 
        {
            "question": "What is the distance between the Earth and the Moon?",
            "output": "How far is the Moon from Earth?",
        },
```
```
translated
        {
            "question": "Was ist die Entfernung zwischen der Erde und dem Mond?"
        },
```