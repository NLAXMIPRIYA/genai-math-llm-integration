# Experiment: Integration of Mathematical Calculations with a Chat Completion System using LLM Function Calling

## AIM

To develop a Python function to calculate the volume of a cylinder and integrate it with a Chat Completion System using LLM Function Calling.

## PROBLEM STATEMENT

To calculate the volume of a cylinder using a Python function and integrate the function with a Chat Completion System. The LLM identifies the user's request, extracts the required parameters, invokes the Python function, and generates a response using the calculated result.

## DESIGN STEPS

**STEP 1: Define the Mathematical Function**

- Import the required libraries: `os`, `json`, `math`, and `openai`.
- Configure the OpenAI API key using an environment variable.
- Define `calculate_cylinder_volume()` with radius and height as parameters.
- Calculate and return the volume of the cylinder.

**STEP 2: Integrate with the Chat Completion System**

- Define the function schema with its name, description, and parameters.
- Specify radius and height as required parameters.
- Send the user's request to the Chat Completion System using the `gpt-4o-mini` model.
- Use `function_call="auto"` to allow the model to decide when to invoke the function.
- Extract the function name and arguments from the model response.

**STEP 3: Execute the Function and Generate the Final Response**

- Convert the function arguments from JSON into Python data.
- Pass the extracted radius and height to the Python function.
- Calculate and display the cylinder's volume.
- Append the function execution result to the conversation.
- Send the updated conversation to the Chat Completion System.
- Display the final natural-language response.

## PROGRAM

```python
import os
import json
import math
import openai

openai.api_key = os.environ['OPENAI_API_KEY']


def calculate_cylinder_volume(radius, height):
    volume = math.pi * radius ** 2 * height
    return volume


radius = 3
height = 8

result = calculate_cylinder_volume(radius, height)

print("Volume of cylinder:", result)


functions = [
    {
        "name": "calculate_cylinder_volume",
        "description": "Calculate the volume of a cylinder using its radius and height.",
        "parameters": {
            "type": "object",
            "properties": {
                "radius": {
                    "type": "number",
                    "description": "Radius of the cylinder"
                },
                "height": {
                    "type": "number",
                    "description": "Height of the cylinder"
                }
            },
            "required": ["radius", "height"]
        }
    }
]


response = openai.ChatCompletion.create(
    model="gpt-4o-mini",
    messages=[
        {
            "role": "user",
            "content": "Calculate the volume of a cylinder with radius 3 and height 8."
        }
    ],
    functions=functions,
    function_call="auto"
)

message = response["choices"][0]["message"]

print(message)


function_call = message["function_call"]

function_name = function_call["name"]

arguments = json.loads(function_call["arguments"])

result = calculate_cylinder_volume(
    arguments["radius"],
    arguments["height"]
)

print("Function result:", result)


messages = [
    {
        "role": "user",
        "content": "Calculate the volume of a cylinder with radius 3 and height 8."
    },
    message,
    {
        "role": "function",
        "name": function_name,
        "content": str(result)
    }
]

final_response = openai.ChatCompletion.create(
    model="gpt-4o-mini",
    messages=messages
)

print(final_response["choices"][0]["message"]["content"])
```

## OUTPUT

**Input:**
- Radius = 3
- Height = 8

**Output:**

```text
Volume of cylinder: 226.1946710584651
Function result: 226.1946710584651
```



<img width="767" height="254" alt="Screenshot 2026-10-27 162125" src="https://github.com/user-attachments/assets/32dbd7f4-5d45-448d-a343-641d546aba53" />


The Chat Completion System generates a final natural-language response using the calculated function result.

## RESULT

Thus, the mathematical calculation of the volume of a cylinder was successfully integrated with the Chat Completion System using LLM Function Calling. The model identified the required function, extracted the radius and height, invoked the Python function, and generated the final response.

The calculated volume of the cylinder was **226.1946710584651 cubic units**.
