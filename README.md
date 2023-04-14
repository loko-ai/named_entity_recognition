<html><p><a href="https://loko-ai.com/" target="_blank" rel="noopener"> <img style="vertical-align: middle;" src="https://user-images.githubusercontent.com/30443495/196493267-c328669c-10af-4670-bbfa-e3029e7fb874.png" width="8%" align="left" /> </a></p>
<h1>Named Entity Recognition</h1><br></html>

A simple example of training, evaluating and predicting a NER model using the **Loko-entity-extractor** component.

The aim of the example is to predict food entities from sentences.

From the **Projects** tab, click on **Import from git** and copy and paste the URL of the current page 
(i.e. https://github.com/loko-ai/named_entity_recognition):
<p align="center"><img src="https://user-images.githubusercontent.com/30443495/230620716-c67e5e58-b71e-4817-9213-39a7e0e9cddd.gif" width="80%" /></p>

### STEP1: Create model

From the Applications tab install and run the loko-entity-extractor (See more here https://github.com/loko-ai/loko-entity-extractor):

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232059873-e697a5fe-ef63-4703-a541-6bd18d18ae7c.gif" width="80%" />
</p>


You can then go back to your project and run it.

In order to start the project remember to press the play button on the right of the project's name:

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232060725-ed95b503-93a4-44bf-9e81-c95e0ef4c598.gif" width="80%" />
</p>

**Loko-entity-extractor** has already some ready-to-use pretrained models, however in this example we want to train a 
model to predict a new entity, named *ALIMENTO*. 

First of all, you have to create the model's *blueprint*, which in this case is:

```
{
    "typology": "trainable_spacy",
    "lang": "it",
    "extend_pretrained": true,
    "n_iter": 30,
    "minibatch_size": 2,
    "dropout_rate": 0.3
}
```

We're starting from the italian spacy model <a href="https://spacy.io/models/it#it_core_news_lg">it_core_news_lg</a>, 
we set 30 iterations, a minibatch-size of 2 and a dropout-rate of 0.3.


You can do it using the **NER GUI** from the **Applications** tab:
<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232069176-d12be701-6340-4bdb-948d-ea434929419b.gif" width="80%" />
</p>

Otherwise, you can create the model using the first flow:

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232070949-6218a703-2994-4931-a2a5-529014457cb0.png" width="60%" />
</p>

### STEP2: Train model

In order to train the model we use the *dataset_train.json* file:

```
 [
   {"text": "Prima di tutto dividete i tuorli dagli albumi e metteteli in due ciotole diverse.", "entities": [[27, 33, "ALIMENTO"], [40, 46, "ALIMENTO"]]},
   {"text": "Unite un cucchiaio di zucchero", "entities": [[22, 30, "ALIMENTO"]]},
   ...
 ]
```

We're using very few samples where we annotated each *ALIMENTO* entity with its *start* and *end* index.

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232074103-3063a41d-b8ad-4914-8987-432c963c402e.png" width="80%" />
</p>

The **File Reader** component reads the dataset, the **Function** component loads the json and the **Entities** component 
fits the model.

### STEP3: Evaluate model

You can now evaluate the model performance obtained on the training data:

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232075214-63bdbb50-91d1-447a-8db2-557a1ef81519.png" width="80%" />
</p>

We save the output of the **Entities** component on a file named *alimenti.eval* that you can visualize using the 
**NER GUI** Report:

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232079455-decbbc7c-8976-4382-98d9-39055907905b.gif" width="80%" />
</p>

### STEP4: Expose service

Finally, you can expose a service to extract the *ALIMENTO* entities:

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232081509-c10f0cf6-68f1-46f1-8ba9-b9e0076163f4.png" width="80%" />
</p>

Use **Route** and **Response** components at the beginning and at the end of the flow producing the output. 
In the **Route** configuration name the service predict and copy the complete url 
(i.e. http://localhost:9999/routes/orchestrator/endpoints/named_entity_recognition/extract). In the **Response** component 
set the *Response Type* to json.

The **Function** component extracts the body from the received request, preprocessing is applied and the **Entities** 
component predicts the output.

You can test it using:

```
curl -d "\"Amo il tiramisù con i savoiardi e la pizza con la mozzarella\"" -X POST http://localhost:9999/routes/orchestrator/endpoints/named_entity_recognition/extract
```

### STEP4: Test service

You can test the extract service directly in your flow using the **HTTP Request** component:

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232083602-90fb4803-710e-476b-b3bf-a760bb5a2820.png" width="80%" />
</p>

In this case you have to change http://localhost:9999 to http://gateway:8080 since the request will be executed in one 
of the containers inside the loko network (i.e. 
http://gateway:8080/routes/orchestrator/endpoints/named_entity_recognition/extract).

The *alimenti_ner* model extracts the following entities:

<p align="center">
<img src="https://user-images.githubusercontent.com/30443495/232085476-9387f192-ee30-4a53-bb99-ccb809ec07f4.png" width="80%" />
</p>

