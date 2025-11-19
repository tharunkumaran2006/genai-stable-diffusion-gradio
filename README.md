## Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework

### AIM:
To design and deploy a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation.

### PROBLEM STATEMENT:
Design and implement a prototype system that uses the Stable Diffusion model to generate images from text prompts, and integrate it with a Gradio interface so users can interactively enter prompts, generate images, and evaluate the results in a practical, deployment-ready environment.

### DESIGN STEPS:

#### STEP 1:
Build the core image-generation pipeline Load and run the Stable Diffusion model in a Python environment to generate images from user-provided text prompts.
#### STEP 2:
Integrate the pipeline with a Gradio interface Create an interactive UI where users can enter prompts, adjust model settings, and view generated images in real time.
#### STEP 3:
Deploy and test the prototype application Launch the Gradio-based app for users to interact with, evaluate generation quality, and validate the functionality of the overall prototype.
### PROGRAM:

```python
import os
import io
import IPython.display
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']
```

```python
# Helper function
import requests, json

#Text-to-image endpoint
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_TTI_BASE']):
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }   
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL,
                                headers=headers,
                                data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))
```


```python
prompt = "a man riding a bike without helmet"

result = get_completion(prompt)
IPython.display.HTML(f'<img src="data:image/png;base64,{result}" />')
```

<img width="1066" height="619" alt="image" src="https://github.com/user-attachments/assets/9d69dd7b-ed7c-4496-bd4a-90384430565b" />





```python
import gradio as gr 

#A helper function to convert the PIL image to base64
#so you can send it to the API
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

def generate(prompt):
    output = get_completion(prompt)
    result_image = base64_to_pil(output)
    return result_image

gr.close_all()
demo = gr.Interface(fn=generate,
                    inputs=[gr.Textbox(label="Your prompt")],
                    outputs=[gr.Image(label="Result")],
                    title="Image Generation with Stable Diffusion",
                    description="Generate any image with Stable Diffusion",
                    allow_flagging="never",
                    examples=["the spirit of a tamagotchi wandering in the city of Vienna","a mecha robot in a favela"])

demo.launch(share=True, server_port=int(os.environ['PORT1']))
```
### OUTPUT:

<img width="1009" height="591" alt="image" src="https://github.com/user-attachments/assets/ba3063fd-63fa-4590-8d92-0e2d7a584d9b" />


### RESULT:
Thus,designing and deploying a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation has been done successfully.
