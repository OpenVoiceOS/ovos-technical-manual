## OpenVoiceOS Agent Tools Documentation

This documentation describes the **Agent Tools** framework in OpenVoiceOS (OVOS), a standardized mechanism for providing discoverable and executable functions to intelligent agents (e.g., LLM solvers, persona plugins).

The primary goal is to provide a **generic discovery and execution interface** for tools, decoupling the tool logic from the consumption logic. Agents can discover tools and their contracts (input/output schemas) via the MessageBus or call them directly in Python.

-----

### Core Concepts

#### 1. AgentTool

The fundamental unit of the framework. An `AgentTool` is a simple data structure that defines a single, executable function and its contract.

| Attribute | Type | Description |
| :--- | :--- | :--- |
| **`name`** | `str` | The **unique**, snake_case identifier for the tool. This name is typically used by the agent/LLM to reference the function. |
| **`description`** | `str` | A detailed, natural language explanation of the tool's purpose and how it should be used. Essential for LLM reasoning. |
| **`argument_schema`** | `Type[ToolArguments]` | A Pydantic model defining the **required structure and types** for the input arguments. |
| **`output_schema`** | `Type[ToolOutput]` | A Pydantic model defining the **guaranteed structure and types** for the tool's output. |
| **`tool_call`** | `Callable` | The underlying Python function that executes the tool logic. It accepts an instantiated `ToolArguments` model and returns a raw dictionary or an instantiated `ToolOutput` model. |

#### 2. ToolArguments and ToolOutput

These are base classes for Pydantic models that define the data contract for a tool.

  * **`ToolArguments(BaseModel)`**: Tool consumers (agents) must format their input according to the concrete Pydantic model derived from this base. This ensures rigorous input validation.
  * **`ToolOutput(BaseModel)`**: The result of a tool call is guaranteed to conform to the concrete Pydantic model derived from this base. This ensures reliable data handling by the consuming agent.

#### 3. ToolBox (Abstract Base Class)

A **`ToolBox`** is an abstract base class that acts as a **container and service provider** for a group of related `AgentTool` instances. Concrete ToolBoxes are typically implemented as OVOS plugins (e.g., via a setuptools entrypoint).

The `ToolBox` handles:

  * **Tool Discovery:** Implementing the abstract `discover_tools()` method to define the available tools.
  * **Bus Integration:** Registering messagebus handlers to support dynamic discovery and remote calling (`ovos.persona.tools.discover`, `ovos.persona.tools.<toolbox_id>.call`).
  * **Execution and Validation:** Providing the `call_tool(name, tool_kwargs)` method, which is the official interface for executing tools, including mandatory **input and output Pydantic validation**.

-----

### Tool Consumption (For Agents/Solvers)

The framework is designed to support two primary methods for consuming tools: **MessageBus-based** and **Direct Python Call**.

### 1. Direct Python Execution

This method is suitable for agents (or solver plugins) running in the **same Python process** as the `ToolBox` plugin. It provides the highest performance and most robust interface, leveraging static typing and Pydantic validation.

  * **Mechanism:** An agent obtains an instance of a `ToolBox` (or a utility function that wraps the process) and calls the public method `call_tool`.
  * **Benefits:**
      * **Direct Access:** No messagebus latency.
      * **Strong Validation:** Enforces the `argument_schema` and `output_schema` before and after execution, ensuring data integrity.
  * **Usage Flow (Internal to Agent):**
    1.  Get the `ToolBox` instance (e.g., via a plugin loading utility).
    2.  Call: `validated_output = toolbox.call_tool(name="tool_name", tool_kwargs={"arg1": value})`
    3.  The return value is a fully validated **`ToolOutput`** Pydantic object.

### 2. MessageBus Discovery and Calling

This method is crucial for **cross-process communication** and **runtime discovery**, allowing any component with access to the OVOS MessageBus to find and execute tools.

#### A. Tool Discovery

Agents can broadcast a discovery message to find all available tools across all running ToolBoxes.

  * **Discovery Message:**
    ```json
    {"type": "ovos.persona.tools.discover"}
    ```
  * **ToolBox Response:** Each registered `ToolBox` responds with its tools, providing a complete description that includes the Pydantic schemas converted to **JSON Schema** (via the `tool_json_list` property). This format is ideal for submitting to LLMs (e.g., for **Function Calling** or **Tool Use**).
  * **Tool Schema Format (Simplified):**
    ```json
    {
        "name": "web_search",
        "description": "Searches the web for information...",
        "argument_schema": {
            "type": "object",
            "properties": {
                "query": {"type": "string", "description": "The search query."}
            },
            "required": ["query"]
        },
        // ... (output_schema also as JSON Schema)
    }
    ```

#### B. Tool Execution (Remote Call)

To execute a tool remotely, an agent sends a message targeted at the specific `ToolBox`.

  * **Tool Call Message:**
    ```json
    {
        "type": "ovos.persona.tools.<toolbox_id>.call",
        "data": {
            "name": "tool_name",
            "kwargs": {"arg1": "value1", "arg2": 123} // Raw argument dict
        }
    }
    ```
  * **ToolBox Execution:** The target `ToolBox` receives the message, runs the **`call_tool`** logic (which includes validation), and emits a response message.
  * **ToolBox Response Message (Success):**
    ```json
    {
        "type": "ovos.persona.tools.<toolbox_id>.call.response",
        "data": {
            "toolbox_id": "web_search_tools",
            "result": {"field1": "output_value", "field2": 456} // Raw dictionary output
        }
    }
    ```
  * **ToolBox Response Message (Error):**
    ```json
    {
        "type": "ovos.persona.tools.<toolbox_id>.call.response",
        "data": {
            "toolbox_id": "web_search_tools",
            "error": "ValueError: Invalid input for 'tool_name'..."
        }
    }
    ```

-----

## Implementation Guide

This guide provides the steps for implementing a concrete **ToolBox** plugin and includes a toy example for file operations.

To create a new set of discoverable tools, you must implement a class that inherits from `ToolBox` and fulfill its abstract requirements, primarily the `discover_tools` method.

### Step 1: Define Input and Output Schemas

For each tool, you must first define its data contract using Pydantic models. These models must inherit from `ToolArguments` or `ToolOutput` and define the expected fields.

```python
from pydantic import Field
from typing import Optional
from ovos_plugin_manager.templates.agent_tools import ToolArguments, ToolOutput

# 1. Input Schema for Reading
class FileReadArguments(ToolArguments):
    """Arguments for the read_file tool."""
    path: str = Field(description="The absolute path to the file to be read.")
    encoding: str = Field(default="utf-8", description="The character encoding of the file (e.g., 'utf-8').")

# 2. Output Schema for Reading
class FileReadOutput(ToolOutput):
    """Output structure for the read_file tool."""
    content: str = Field(description="The full content of the file as a string.")
    file_size_bytes: int = Field(description="The size of the file in bytes.")
    success: bool = Field(default=True)

# 3. Input Schema for Writing
class FileWriteArguments(ToolArguments):
    """Arguments for the write_file tool."""
    path: str = Field(description="The absolute path to the file to be written or overwritten.")
    content: str = Field(description="The string content to write to the file.")
    append: bool = Field(default=False, description="If True, append to the file; otherwise, overwrite/create it.")

# 4. Output Schema for Writing
class FileWriteOutput(ToolOutput):
    """Output structure for the write_file tool."""
    path: str = Field(description="The path to the file that was written.")
    bytes_written: int = Field(description="The number of bytes successfully written.")
    success: bool = Field(default=True)
    error: Optional[str] = None
```

### Step 2: Implement the Tool Execution Logic

Write the raw Python functions that perform the actual task. These functions **must accept an instantiated Pydantic Argument Model** and **return a `dict` or an instantiated Pydantic Output Model**.

```python
import os
from ovos_plugin_manager.templates.agent_tools import ToolCallReturn

# Tool Call Function for Reading
def read_file_logic(args: FileReadArguments) -> ToolCallReturn:
    """The core logic for reading a file."""
    if not os.path.exists(args.path):
        # We can return an error dict/model if the tool logic fails
        return {"content": "", "file_size_bytes": 0, "success": False}
        
    with open(args.path, 'r', encoding=args.encoding) as f:
        content = f.read()
    
    return {
        "content": content,
        "file_size_bytes": len(content.encode(args.encoding)),
        "success": True
    }

# Tool Call Function for Writing
def write_file_logic(args: FileWriteArguments) -> ToolCallReturn:
    """The core logic for writing a file."""
    mode = 'a' if args.append else 'w'
    
    try:
        content_bytes = args.content.encode('utf-8')
        with open(args.path, mode, encoding='utf-8') as f:
            f.write(args.content)
        
        return FileWriteOutput(
            path=args.path,
            bytes_written=len(content_bytes),
            success=True
        )
    except Exception as e:
        # Returning an output model on failure with an error message
        return FileWriteOutput(
            path=args.path,
            bytes_written=0,
            success=False,
            error=str(e)
        )
```

### Step 3: Create the ToolBox Implementation

Implement the concrete `FileToolBox` inheriting from `ToolBox` and define the `discover_tools` method.

```python
from ovos_plugin_manager.templates.agent_tools import ToolBox, AgentTool
from typing import List
from ovos_bus_client import MessageBusClient
from ovos_utils.fakebus import FakeBus

class FileToolBox(ToolBox):
    """
    A concrete ToolBox implementation providing file reading and writing tools.
    """
    def __init__(self, bus: Optional[Union[MessageBusClient, FakeBus]] = None):
        super().__init__(toolbox_id="ovos_file_tools", bus=bus)

    def discover_tools(self) -> List[AgentTool]:
        """
        Defines and returns all AgentTools provided by this ToolBox.
        """
        file_read_tool = AgentTool(
            name="read_file",
            description="Reads the content of a text file from the local filesystem at a specified absolute path.",
            argument_schema=FileReadArguments,
            output_schema=FileReadOutput,
            tool_call=read_file_logic
        )

        file_write_tool = AgentTool(
            name="write_file",
            description="Writes or appends content to a text file on the local filesystem at a specified absolute path.",
            argument_schema=FileWriteArguments,
            output_schema=FileWriteOutput,
            tool_call=write_file_logic
        )

        return [file_read_tool, file_write_tool]

# Note: The ToolBox is now ready to be loaded by an OVOS plugin loader
# and automatically bound to the bus if one is provided to the constructor.
```

-----

## 2. Agent Consumption Example (Direct Call)

A consuming Agent, such as a **Solver Plugin**, can use the `call_tool` method directly after obtaining the `FileToolBox` instance.

### Example Tool Consumption Logic

```python
from ovos_utils.fakebus import FakeBus
import tempfile
import atexit

# Setup: Create a temporary environment and the ToolBox instance
BUS = FakeBus()
file_toolbox = FileToolBox(bus=BUS) # Binds the toolbox to the bus

# Create a temporary file for testing
temp_dir = tempfile.gettempdir()
test_file_path = os.path.join(temp_dir, "ovos_tool_test.txt")

# Ensure the file is cleaned up on exit
def cleanup():
    if os.path.exists(test_file_path):
        os.remove(test_file_path)
atexit.register(cleanup)


def solve_file_problem():
    # 1. Agent needs to write a file
    print(f"Agent attempting to WRITE to: {test_file_path}")
    write_args = {
        "path": test_file_path,
        "content": "Hello, OpenVoiceOS Tools!\n",
        "append": False
    }
    
    try:
        # Call the tool directly using the validated interface
        write_result: FileWriteOutput = file_toolbox.call_tool(name="write_file", tool_kwargs=write_args)
        
        if write_result.success:
            print(f"WRITE SUCCESS: Wrote {write_result.bytes_written} bytes.")
        else:
            print(f"WRITE FAILED: {write_result.error}")
            return
        
        # 2. Agent needs to read the file
        print(f"\nAgent attempting to READ from: {test_file_path}")
        read_args = FileReadArguments(path=test_file_path) # Can pass an instantiated Pydantic model too

        read_result: FileReadOutput = file_toolbox.call_tool(name="read_file", tool_kwargs=read_args)

        if read_result.success:
            print("READ SUCCESS:")
            print(f"  Content: '{read_result.content.strip()}'")
            print(f"  Size: {read_result.file_size_bytes} bytes")
        else:
            print(f"READ FAILED: (File not found? {read_result.success})")

    except ValueError as e:
        print(f"\nExecution Failed (Input Validation): {e}")
    except RuntimeError as e:
        print(f"\nExecution Failed (Tool Logic/Output Validation): {e}")

# solve_file_problem()
```