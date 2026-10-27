## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT:
Design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).


### DESIGN STEPS:

#### STEP 1:
Define the Function: Create a Python function to calculate the volume of a cylinder using its radius and height.

#### STEP 2:
Configure Function Schema: Define the function name, description, parameters, and required inputs for the LLM.

#### STEP 3:
Integrate OpenAI API: Connect the Python function to the OpenAI model using the function-calling feature.
Execute and Display Results: Extract the arguments from the model response, execute the function, and return the calculated volume to the model for the final response.

### PROGRAM:
```
import os
import json
import math
from openai import OpenAI

if not os.getenv("OPENAI_API_KEY"):
    print("Set OPENAI_API_KEY in your environment before running API cells.")
else:
    client = OpenAI()

def calculate_cylinder_volume(radius, height):
    """Calculate the volume of a cylinder using V = pi * radius^2 * height."""
    radius = float(radius)
    height = float(height)

    if radius < 0 or height < 0:
        raise ValueError("Radius and height must be non-negative.")

    volume = math.pi * radius**2 * height
    return volume

# Test the Python function without calling the API
print(f"{calculate_cylinder_volume(3, 5):.2f} cubic units")


tools = [
    {
        "type": "function",
        "name": "calculate_cylinder_volume",
        "description": "Calculate the volume of a cylinder using its radius and height.",
        "parameters": {
            "type": "object",
            "properties": {
                "radius": {
                    "type": "number",
                    "description": "Radius of the cylinder."
                },
                "height": {
                    "type": "number",
                    "description": "Height of the cylinder."
                }
            },
            "required": ["radius", "height"],
            "additionalProperties": False
        },
        "strict": True
    }
]

print(json.dumps(tools, indent=2))


messages = [
    {
        "role": "user",
        "content": "Calculate the volume of a cylinder with radius 3 and height 5."
    }
]

print(messages)


response = client.responses.create(
    model="gpt-4.1-mini",
    input=messages,
    tools=tools
)

print(response)


for item in response.output:
    if item.type == "function_call":
        print("Function name:", item.name)
        print("Arguments:", item.arguments)


function_call = next(
    (item for item in response.output
     if item.type == "function_call"
     and item.name == "calculate_cylinder_volume"),
    None
)

if function_call is None:
    print("The model did not request the calculation tool. Response:")
    print(response.output_text)
else:
    args = json.loads(function_call.arguments)
    print("Extracted arguments:", args)


if function_call is not None:
    volume = calculate_cylinder_volume(
        args["radius"],
        args["height"]
    )
    observation = json.dumps({
        "radius": args["radius"],
        "height": args["height"],
        "volume": volume,
        "units": "cubic units"
    })
    print("Function result:", observation)


if function_call is not None:
    final_response = client.responses.create(
        model="gpt-4.1-mini",
        previous_response_id=response.id,
        input=[
            {
                "type": "function_call_output",
                "call_id": function_call.call_id,
                "output": observation
            }
        ],
        tools=tools
    )
    
    print("Name : Dheebash Sai R")
    print("Reg no : 212224040075")
    print(final_response.output_text)    
```

### OUTPUT:
<img width="1950" height="287" alt="image" src="https://github.com/user-attachments/assets/468b81a2-9e32-40a8-8c87-f682d2759a1d" />


### RESULT:
The program to implement a python function for  calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM) is executed successfully.
