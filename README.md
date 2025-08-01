# QureGenAI & TyxonQ Platform API Documentation

[English](./README.md) | [中文](./README_zh.md)

## Introduction

Welcome to the QureGenAI 医图生科 and TyxonQ 太玄量子 platforms. Our mission is to provide a comprehensive suite of Quantum Computing + AI for Drug Discovery (Quantum AIDD) services through a powerful, developer-friendly HTTP API and Model Context Protocol (MCP).

Our platform enables researchers, developers, and large models to programmatically access state-of-the-art computational models for molecular simulation, protein structure prediction, and more.

Click To Use [QureGenAI](https://service.quregenai.com)




### Platform Features

#### ✅ Currently Available Models
- **✅ AutoDock GPU Molecular Docking** - A traditional and fast molecular docking algorithm with reliable accuracy, ideal for high-throughput screening. [GitHub](https://github.com/ccsb-scripps/AutoDock-GPU)
- **✅ DiffDock Molecular Docking** - An AI-powered docking model based on diffusion, capable of discovering binding pockets and performing docking without a predefined location. [GitHub](https://github.com/gcorso/DiffDock)
- **✅ Protenix Protein Structure Prediction** - Predicts protein structures and complex models from sequence information alone. A trainable, open-source PyTorch reproduction of AlphaFold 3. [GitHub](https://github.com/bytedance/Protenix)


#### 🚧 Coming Soon Models

- **🔄 Boltz-2** - Next-generation biomolecular structure prediction model for proteins, nucleic acids, and small molecules. [GitHub](https://github.com/jwohlwend/boltz)

- **🔄 ProteinMPNN** - Deep learning method for protein sequence design using message passing neural networks. [GitHub](https://github.com/dauparas/ProteinMPNN)

- **🔄 RoseTTAFold** - Accurate protein structure prediction using attention-based neural networks. [GitHub](https://github.com/RosettaCommons/RoseTTAFold)

- **🔄 SurfDock** - Surface-guided molecular docking for improved binding pose prediction. [GitHub](https://github.com/CAODH/SurfDock)

- **🔄 OpenFE** - Open-source alchemical free energy calculations for drug discovery. [GitHub](https://github.com/OpenFreeEnergy/openfe)

- **🔄 DiffLinker** - Equivariant diffusion model for molecular linker design in drug discovery. [GitHub](https://github.com/igashov/DiffLinker)

- **🔄 MolMap** - Molecular representation learning through 2D molecular maps. [GitHub](https://github.com/shenwanxiang/bidd-molmap)

- **🔄 AI2BMD** - AI-powered biomolecular dynamics simulation and analysis platform. [GitHub](https://github.com/microsoft/AI2BMD)

- **🔄 BioEmu AI** - Advanced biological system emulation using 
artificial intelligence. [GitHub](https://github.com/microsoft/bioemu)


*We are continuously expanding our model collection to provide comprehensive AI-powered drug discovery solutions. Stay tuned for more cutting-edge models!*


## Getting Started

### Base URL
All API endpoints are relative to the following base URL:
```
https://api.quregenai.com
```

### Authentication
All API calls require authentication using an API Key. Include your API key in the `Authorization` header as a Bearer token.

***Token Access***: [Getting a Key](https://service.quregenai.com/) to register and obtain your API key


### Headers
All requests should include the following headers:
```
Content-Type: application/json
Authorization: Bearer your_api_key_here
```

## API Reference

### 1. AutoDock GPU Molecular Docking for High Throughput Screen

Performs molecular docking using the AutoDock algorithm.

- **Endpoint**: `POST /api/autodock_api/autodock_docking`
- **Description**: Submits a job for molecular docking. You must provide a receptor and one of the three ligand input types.

#### Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| `job_name` | string | No | A name for your job. |
| `receptor` | string | Yes | URL or server-accessible path to the receptor file (e.g., PDB format). |
| `center_x`, `center_y`, `center_z` | number/string | Yes | Coordinates for the center of the docking box. |
| `size_x`, `size_y`, `size_z` | number | No | Dimensions of the docking box. Defaults to 15 for each axis. |
| `ligand` | string | Yes (one of three) | Path to a single ligand file (SDF, PDB, URL) single file size should below `10MB`. |
| `ligands` | array of strings | Yes (one of three) | A list of paths to multiple ligand files. |
| `smiles` | string | Yes (one of three) | A SMILES string (use `\n` for multiple), or a path to a `.txt` file with SMILES strings (one per line). |
| `thread` | integer | No | Number of threads. Default is 1200, min is 1000. |

**Note**: You must provide exactly one of `ligand`, `ligands`, or `smiles`.

#### Required Parameters Examples

- `receptor`: Receptor local file/URL
  - Local file:
  ```python
  # Local file path
  path = '/path/to/file/receptor.pdb'
  with open(path, 'r') as f:
      content = f.read()
  receptor = {
    'content': content,
    'name': 'receptor.pdb'
  }
  ```
  - URL:
  ```python
  receptor = 'http://www.receptor.pdb'
  ```

- `center_x`, `center_y`, `center_z`: Docking box center coordinates (can be selected using the web application's molecular structure viewer)
- `size_x`, `size_y`, `size_z`: Docking box dimensions (default is 15 for all)
- Ligand input (choose one of three):
  1. `ligand`: Single ligand, if it's a local file, use key-value (dictionary) object with required `content` field and optional `name` field for easier result viewing; if it's a URL, can be a direct string. (SDF format file, PDB format file, URL link address) File size should not exceed `10MB`
  - Local file: 
    ```python     
    # Local disk path
    path = '/path/to/file/ligand.sdf'
    # Read file content
    with open(path, 'r') as f:
        content = f.read()
    ligand = {
      'content': content,
      'name': 'ligand.sdf'
    }
    ```
  - URL:
    ```python
    ligand = 'http://www.ligand.sdf'
    ```
  
  2. `ligands`: Multiple ligands (multiple files, SDF format, PDB format, URL link addresses), list object, each file size should not exceed `10MB`
  - Local files:
    ```python
    # Local disk multiple ligand file paths
    path1 = '/path/to/file/ligand1.sdf'
    path2 = '/path/to/file/ligand2.sdf'
    # Read file contents
    with open(path1, 'r') as f:
        content1 = f.read()
    with open(path2, 'r') as f:
        content2 = f.read()
    ligands = [
      {'name': 'ligand1.sdf',
       'content': content1 },
       {'name': 'ligand2.sdf',
       'content': content2}
    ]
    ```
  - URLs:
    ```python
    ligands = ['http://www.ligand1.sdf', 
               'http://www.ligand2.sdf']
    ```

  3. `smiles`: SMILES mode
  - SMILES string, use newline `\n` to separate multiple molecules:
    ```python
    smiles = 'CCO\ncccccc'
    ```
  - File path, a `txt` file containing multiple molecular SMILES strings, one molecule SMILES per line:
    ```python
    # Local file
    path = '/path/to/file/smiles.txt'
    with open(path, 'r') as f:
        content = f.read()
    smiles = {
      'content': content
    }
    # URL
    smiles = 'http://www.smiles.txt'
    ```

#### Example 1.1: Single Ligand File

**Python**
```python
import requests

# Read local ligand file content, file size should not exceed 10MB
with open('ligand.sdf', 'r') as f:
    ligand_content = f.read()

# Construct ligand file object
ligand = {
    'content': ligand_content,
    'name': 'ligand.sdf'
}

def submit_autodock_single_ligand():
    url = f"{BASE_URL}/api/autodock_api/autodock_docking"
    
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "ligand": ligand,  # Use file object format
        # "ligand": "https://www.ligand.sdf"  # Or use URL link
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        result = response.json()
        print(f"Task submitted successfully, Task ID: {result.get('task_id')}")
        return result
    else:
        print(f"Request failed: {response.status_code}, {response.text}")
        return None

# Call example
result = submit_autodock_single_ligand()
```

#### Example 1.2: Batch Ligand Docking

**Python**
```python
# Local disk multiple ligand file paths
path1 = '/path/to/file/ligand1.sdf'
path2 = '/path/to/file/ligand2.sdf'
# Read file contents
with open(path1, 'r') as f:
    content1 = f.read()
with open(path2, 'r') as f:
    content2 = f.read()
ligands = [
  {'name': 'ligand1.sdf',
   'content': content1 },
   {'name': 'ligand2.sdf',
   'content': content2}
]

def submit_autodock_multiple_ligands():
    url = f"{BASE_URL}/api/autodock_api/autodock_docking"
    
    data = {
        "job_name": "AutoDock_Multiple_Ligands_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "ligands": ligands,
        # "ligands": ['https://www.ligand1.sdf', 'https://www.ligand2.sdf'],  # URL link list
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        result = response.json()
        print(f"Batch task submitted successfully, Task ID: {result.get('task_id')}")
        return result
    else:
        print(f"Request failed: {response.status_code}, {response.text}")
        return None
```

#### Example 1.3: SMILES String Docking

**Python**
```python
# Local smiles.txt file reading
path = '/path/to/file/smiles.txt'
with open(path, 'r') as f:
    content = f.read()
smiles = {
  'content': content
}

def submit_autodock_smiles():
    url = f"{BASE_URL}/api/autodock_api/autodock_docking"
    
    data = {
        "job_name": "AutoDock_SMILES_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": smiles,  # Local smiles file
        # "smiles": "CC(=O)CC(c1ccccc1)c1c(O)c2ccccc2oc1=O",  # Can also be a molecular smiles string
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        result = response.json()
        print(f"SMILES task submitted successfully, Task ID: {result.get('task_id')}")
        return result
    else:
        print(f"Request failed: {response.status_code}, {response.text}")
        return None
```


---

### 2. DiffDock Molecular Docking

Performs molecular docking using the AI-based DiffDock algorithm.

- **Endpoint**: `POST /api/diffdock_api/diffdock_docking`
- **Description**: Submits a DiffDock job. This is useful when the binding pocket is unknown.

#### Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| `job_name` | string | No | A name for your job. |
| `protein` | string | Yes | Protein file path (e.g. PDB) or sequence string. |
| `ligand_file` | string | Yes (one of two) | Path to a ligand file. |
| `smiles` | string | Yes (one of two) | A SMILES string for the ligand. |
| `diffusion_steps` | integer | No | Number of diffusion steps (1-100). Default: 20. |
| `sample_count` | integer | No | Number of samples to generate (1-100). Default: 10. |

**Note**: You must provide either `ligand_file` or `smiles`.

#### Example: Protein Sequence and SMILES

**Python**
```python
import requests
import os

BASE_URL = "https://api.quregenai.com"
API_KEY = os.environ.get("QUREGENAI_API_KEY", "<your_api_key_here>")

HEADERS = {
    'Content-Type': 'application/json',
    'Authorization': f'Bearer {API_KEY}'
}

# Local file
with open('/path/to/file/receptor.pdb', 'r') as f:
    content = f.read()
protein = {
  'content': content,
  'name': 'protein.pdb'
}

# Can also be a URL
# protein = 'https://files.rcsb.org/download/7RT3.pdb'

def submit_diffdock_sequence_to_smiles():
    url = f"{BASE_URL}/api/diffdock_api/diffdock_docking"
    
    data = {
        "job_name": "DiffDock_Sequence_Test",
        "protein": protein,
        "smiles": "CCO",  # Ethanol
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        print("Task submitted successfully:", response.json())
        return response.json()
    else:
        print(f"Error: {response.status_code}", response.text)
        return None

# submit_diffdock_sequence_to_smiles()
```



---

### 3. Protenix Protein Structure Prediction

Predicts protein structure and complex models using the Protenix algorithm.

- **Endpoint**: `POST /api/protenix_api/protenix_predict`
- **Description**: Submits a job for protein structure prediction, including multi-chain complexes and complexes with DNA/RNA/ligands.

#### Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | A name for the prediction job. |
| `sequences` | array of objects | Yes | An array of sequence objects. See details below. |
| `seeds` | integer | No | Random seed. Default: 42. |
| `n_sample` | integer | No | Number of samples. Default: 5. |
| `n_step` | integer | No | Number of steps. Default: 150. |
| `n_cycle` | integer | No | Number of cycles. Default: 5. |

The `sequences` object has the following structure:
| Key | Type | Required | Description |
|---|---|---|---|
| `moduleType` | string | Yes | Type of sequence: 'proteinChain', 'dnaSequence', 'rnaSequence', 'ligand'. |
| `sequence` | string | Yes | The sequence content (protein sequence, DNA/RNA sequence, or SMILES for ligand). |
| `copy` | integer | Yes | Number of copies of this sequence (1-5). |


#### Example: Protein-Ligand Complex Prediction

**cURL**
```bash
curl -X POST "https://api.quregenai.com/api/protenix_api/protenix_predict" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <your_api_key_here>" \
  -d '{
    "name": "Protein_Ligand_Complex",
    "sequences": [
      {
        "moduleType": "proteinChain",
        "sequence": "MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKREQTGQGWVPSNYITPVN",
        "copy": 1
      },
      {
        "moduleType": "ligand",
        "sequence": "CCO",
        "copy": 1
      }
    ]
  }'
```

**Python**
```python
import requests
import os

BASE_URL = "https://api.quregenai.com"
API_KEY = os.environ.get("QUREGENAI_API_KEY", "<your_api_key_here>")

HEADERS = {
    'Content-Type': 'application/json',
    'Authorization': f'Bearer {API_KEY}'
}

def submit_protenix_protein_ligand():
    url = f"{BASE_URL}/api/protenix_api/protenix_predict"
    protein_sequence = "MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKREQTGQGWVPSNYITPVN"
    
    data = {
        "name": "Protein_Ligand_Complex",
        "sequences": [
            {
                "moduleType": "proteinChain",
                "sequence": protein_sequence,
                "copy": 1
            },
            {
                "moduleType": "ligand",
                "sequence": "CCO",  # Ligand SMILES
                "copy": 1
            }
        ]
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        print("Job submitted successfully:", response.json())
        return response.json()
    else:
        print(f"Error: {response.status_code}", response.text)
        return None

# submit_protenix_protein_ligand()
```

## Asynchronous Tasks and MCP Service

Due to the long computation times for these models, all API calls are **asynchronous**.

1.  **Submit a Job**: When you send a request to an API endpoint, the platform validates your request and queues a job for execution.
2.  **Receive a Task ID**: If the submission is successful, the API returns a `200 OK` response containing a unique `task_id`.

    ```json
    {
        "success": true,
        "message": "Task submitted to remote service, processing asynchronously...",
        "task_id": "1-68775320-15633f89-dc4744aa5c31",
        "job_name": "AutoDock_FAA2F6144C4611F0A525DBA75ABC6F26_1752650526"
    }
    ```
3.  **Monitor Task Status**: You can monitor the status of your job using the `task_id` through the platform's web interface. Log in to the web portal, navigate to the appropriate model section (e.g., AutoDock), and find your task in the history list to view its status and retrieve results.

## Task Query
```python
import requests
# Known task ID
task_id = '1-688c4948-15b5223d-770c7c8b5656'
BASE_URL = 'http://api.quregenai.com'
url = f"{BASE_URL}/api/tasks/{task_id}"

API_KEY = 'sk-4d4de881d792473f9c2baafe1992a0c4'
HEADERS = {
    'Content-Type': 'application/json',
    'Authorization': f'Bearer {API_KEY}'
}

response = requests.get(url, headers=HEADERS)
response.json()
```
**Completed Task Response**

Note that the `status` field value is `completed`
```python
{'success': True,
 'message': 'Task completed',
 'result': {'task_id': '1-688c4948-15b5223d-770c7c8b5656',
  'user_id': 'FAA2F6144C4611F0A525DBA75ABC6F26',
  'task_type': 'protenix',
  'job_name': 'Validation Test',
  'status': 'completed',
  'created_at': '2025-08-01 12:57:44',
  'completed_at': '2025-08-01 13:01:15',
  'parameters': {'complex_count': 1,
   'complex_0_name': 'Validation Test',
   'complex_0_sequence_count': 2,
   'seeds': 42,
   'n_sample': 5,
   'n_step': 150,
   'n_cycle': 5,
   'complex_0_sequence_0_type': 'dnaSequence',
   'complex_0_sequence_0_sequence': 'ATGCGTACGGGGTTTTAAAACCCCGGATCCTTAGGCCTAAGGATCCTTAG',
   'complex_0_sequence_0_count': 1,
   'complex_0_sequence_1_type': 'rnaSequence',
   'complex_0_sequence_1_sequence': 'AUGCGUACGGGGUUUUAAAACCCCGGAUCCUUAGGCCUAAGGAUCCUUAG',
   'complex_0_sequence_1_count': 1},
  'result': 'Result file count: 10'}}
```
**Incomplete Task Response**

Note that the `status` field value is `pending`, indicating that the task is still running and needs to wait a few minutes
```python
{'success': True,
 'message': 'Task is still running, not completed',
 'result': {'task_id': '1-688c7950-1562fcd4-d1bd26f61a59',
  'user_id': 'FAA2F6144C4611F0A525DBA75ABC6F26',
  'task_type': 'protenix',
  'job_name': 'protenix_Sequence_Test',
  'status': 'pending',
  'parameters': {'complex_count': 1,
   'complex_0_name': 'protenix_Sequence_Test',
   'complex_0_sequence_count': 2,
   'seeds': 42,
   'n_sample': 5,
   'n_step': 150,
   'n_cycle': 5,
   'complex_0_sequence_0_type': 'proteinChain',
   'complex_0_sequence_0_sequence': 'MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKREQTGQGWVPSNYITPVN',
   'complex_0_sequence_0_count': 1,
   'complex_0_sequence_1_type': 'ligand',
   'complex_0_sequence_1_ligand': 'CC(=O)Oc1ccccc(=C)c1C(=O)O',
   'complex_0_sequence_1_count': 1}}}
```

## Result File Download
The download interface will package all files into a `.zip` archive
> AutoDock results in the compressed package are sorted by best docking score, with file name prefixes `rank_0`, `rank_1`

```python
import os
import requests

# Known completed task ID
task_id = '1-688c4948-15b5223d-770c7c8b5656'
BASE_URL = 'http://api.quregenai.com'
url = f"{BASE_URL}/api/tasks/{task_id}/results_download"
response = requests.get(url, headers=HEADERS)


def download(response, local_path='./tmp/results/protenix_results.zip'):
    """
    response: HTTP response object
    local_path: Local path where you want to save the results
    """
    if response.status_code == 200:
        # Ensure directory exists
        os.makedirs(os.path.dirname(local_path), exist_ok=True)
        
        # Write response content to local file
        with open(local_path, 'wb') as f:
            for chunk in response.iter_content(chunk_size=8192):
                if chunk:
                    f.write(chunk)
        
        # Verify file creation
        if os.path.exists(local_path):
            file_size = os.path.getsize(local_path)
            print(f"✅ File downloaded successfully: {local_path}")
            print(f"📊 File size: {file_size} bytes")
        else:
            print("❌ File creation failed")
    else:
        try:
            error_info = response.json()
            print(f"❌ Download failed: {error_info}")
        except:
            print(f"❌ Download failed: {response.text}")

# Execute download
download(response)
```
Download success message:
```python
✅ File downloaded successfully: /tmp/results/protenix_results.zip
📊 File size: 295983 bytes
```

## Error Handling

The API uses standard HTTP status codes to indicate the success or failure of a request.

| Status Code | Meaning | Example Error Message |
|---|---|---|
| `400 Bad Request` | The request was malformed, missing parameters, or had conflicting parameters. | `{"success": false, "message": "Missing required parameter: center_y"}` |
| `401 Unauthorized` | The API key is missing, invalid, or the account has insufficient funds. | `{"message":"Invalid or missing Authorization header","success":false}` |
| `500 Internal Server Error` | An unexpected error occurred on the server. | Varies. |

## Common Failure Examples

### a. Insufficient Balance
```bash
Request failed: 401, {"message":"Insufficient QAU balance, current balance: 0, at least 5 QAU required to submit task, please top up first","success":false}
```

### b. API Key Authentication Error
```bash
Request failed: 401, {"message":"Missing Authorization header, please provide API key","success":false}
```


### c. Missing Required Parameters
```python
    # Missing center_y and center_z parameters for pocket center coordinates
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": "CCO",
        "center_x": '-3.52',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```

```bash
Request failed: 400, {
  "success": false,
  "message": "Missing required parameter: center_y (docking box parameter)"
}
```

### d. Redundant Parameters
```python
    # Both smiles and ligand parameters are provided,
    # but only one should be provided
    with open('/path/to/file/ligand.sdf', 'r') as f:
        content = f.read()
    ligand = {
      'content': content,
      'name': 'ligand.sdf'
    }

    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": "CCO",   # redundant
        "ligand": ligand,  # redundant
        "center_x": '-3.52',
        'center_y': '5.57',
        'center_z': '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```

```bash
Request failed: 400, {
  "success": false,
  "message": "Only one of ligand file(ligand/ligands) or SMILES string can be provided"
}
```

### e. Incorrect Ligand File - Protein PDB Instead of Small Molecule
```python
    # Here ligand parameter is given a protein PDB local file instead of a small molecule
    # 1uw6.pdb is a protein file
    with open('/path/to/file/1uw6.pdb', 'r') as f
        content = f.read()
    
    ligand = {
      'content': content,
      'name': '1uw6.pdb'
    }

    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        'ligand': ligand,   # incorrect file
        "center_x": '-3.52',
        'center_y': '5.57',
        'center_z': '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```

```bash
Request failed: 400, {
  "success": false,
  "message": "Detected ligand file '1uw6.pdb' contains multiple amino acid residues (TYR, MET, THR, HIS, ARG...), this appears to be a protein file. This platform only supports protein-small molecule docking, not protein-protein docking. Please check your ligand file content."
}
```

### f. Ligand Structure Error - Unable to Convert to PDBQT Successfully
```python
    # Here an incorrect SMILES structure is provided
    # Need to strictly check the structural correctness of ligands and receptor proteins
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": "CCCCCcccccccccc",
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```

```bash
Request failed: 400, {
  "success": false,
  "message": "Failed to convert ligand structure to PDBQT format. Please verify the SMILES string is chemically valid and properly formatted."
}
```

### g. Query with Incorrect Task ID
```python
{'success': False,
 'message': 'Task does not exist, check if task ID is correct: xx-xxxx'}
```

## Best Practices

- **Sequence Lengths**:
  - Protein: 10-1000 amino acids
  - DNA/RNA: 10-1000 bases
- **File Formats**:
  - Protein: PDB
  - Ligand: SDF, MOL2, MOL
  - SMILES: Standard SMILES strings
- **Performance**:
  - Use appropriate thread counts for AutoDock (1000-2000 recommended).
  - Use URLs for large input files to avoid large request bodies.
- **Task Management**:
  - Store the `task_id` for every submitted job. It is essential for tracking and retrieving results.

## Support

If you encounter any issues or have questions, please contact our technical support team code@quregenai.com or consult the detailed error logs available in the web interface.


#### 微信公众号 | Official WeChat
<img src="docs/images/wechat_offical_qrcode.jpg" alt="TyxonQ 微信公众号" width="200">

#### 开发者交流群 | Developer Community
<img src="docs/images/developer_group_qrcode.png" alt="TyxonQ 开发者交流群" width="200">

*扫码关注公众号获取最新资讯 | Scan to follow for latest updates*  
*扫码加入开发者群进行技术交流 | Scan to join developer community*